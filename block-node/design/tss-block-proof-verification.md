# TSS Block Proof Verification

## Purpose

Cryptographically confirms that blocks were signed by a threshold of network stake using TSS
([HIP-1200](https://hips.hedera.com/hip/hip-1200)).
For the broader block verification design, see
[`docs/design/block-verification.md`](block-verification.md).

## Goals

- Verify every TSS block proof with the TSS library, given only the network's ledger ID, the
  block signature and the signed hash.
- Treat the ledger ID and the block signature as opaque: only the TSS library decides whether
  they are well formed.
- Notice at start, not at the first TSS block, that the TSS library cannot verify in the node's
  environment, and stop instead of rejecting valid blocks.
- Ship only the part of the TSS library that verifies.

## Terms

**Ledger ID**: the identity of a network's chain of trust. The network publishes it in a
`LedgerIdPublicationTransactionBody` (`LedgerIdPublication`), normally in block 0. The Block
Node treats it as an opaque byte sequence and hands it to the TSS library with every
verification.

**TssSignedBlockProof**: a `BlockProof` whose `signed_block_proof` carries a `block_signature`
produced by the TSS library of the Consensus Nodes. Every block, including block 0, carries the
same kind of signature. The Block Node treats the signature as opaque; the TSS library checks its
format.

**WRAPS**: the recursive proof system that keeps block signatures verifiable against the ledger
ID as the Roster changes (see the [glossary](../block-node/glossary.md#wraps)). The TSS library
checks WRAPS whenever it verifies a block signature and embeds what it checks WRAPS against, so
the Block Node configures, downloads or installs nothing for it.

**TSS library**: hiero-cryptography, Maven group `com.hedera.cryptography`, version 3.22.0: the
modules `hedera-cryptography-wraps` and `hedera-cryptography-hints`, plus
`com.hedera.common:hedera-common-nativesupport`.

**Verifier variant**: the build of `hedera-cryptography-wraps` published with the classifier
`verifier`. It leaves out the parameters needed only to construct WRAPS, so the jar is about
16 MB instead of 83 MB and those parameters are never loaded into memory.

## Design

### Library dependency

- Gradle: `hiero-dependency-versions/build.gradle.kts` pins one `hederaCryptographyVersion` for
  the three modules, and `gradle/modules.properties` maps the JPMS module
  `com.hedera.cryptography.wraps` to `com.hedera.cryptography:hedera-cryptography-wraps|verifier`,
  so every module that requires it resolves the verifier variant. `hedera-cryptography-hints`
  and `hedera-common-nativesupport` have no verifier variant.
- Maven (Helm): the chart's `resolve-plugins` init container resolves plugin jars with
  `mvn dependency:copy-dependencies` from the published POMs, and a POM cannot express a Gradle
  variant. `block-node/block-verification/build.gradle.kts` therefore writes
  `<classifier>verifier</classifier>` into the `hedera-cryptography-wraps` dependency of the
  published `block-verification` POM, so pods download the same jar Gradle selects.

### Library gateway

`NativeVerificationLibrary` is the only production class that calls the TSS library. It
exposes:

- `warmUp()`: loads the native code and the embedded verification key and reports whether TSS
  block proofs can be verified in this process.
- `verifyTss(ledgerId, signature, hash)`: a thin call of `TSS.verifyTSS`.
- `libraryVersion()`: the version of the TSS library module, for log messages.

Nothing is installed into the library: the ledger ID is passed with every call, and the library
embeds everything else it verifies against. Earlier library versions needed the Roster and a
WRAPS verification key installed before verifying; the current API has neither.

`TSS.verifyTSS` remembers the outcome of its WRAPS check for the last eight distinct combinations
of WRAPS proof, ledger ID and hinTS verification key. Consecutive blocks signed under the same
WRAPS therefore skip that check, the most expensive part of a verification, while the hinTS check
of the signed hash runs on every call. That cache, which the library synchronizes itself, is its
only mutable state on the verification path.

The gateway runs every call into the library under one process-wide fair lock, so TSS
verifications run one at a time, in the order their sessions ask for the lock. The library does not
need the serialization. It narrows a start-up ordering gap of the plugin: on a node without any
verified block, the first session to complete establishes where strict ordering begins (see
`ResultOrderingManager`), and verifying one TSS proof at a time makes that the session that reached
the library first, usually the lowest block. It does not close the gap, since sessions reach the
library in any order and RSA proofs do not take the lock. Throughput is therefore one TSS
verification at a time. Measured on a laptop, a verification costs about 8 ms when its WRAPS
was among the recently verified ones and about 75 ms otherwise (the first block signed under a new
WRAPS); small CI runners are slower.

### Start-up warm-up

`VerificationServicePlugin.start()` calls `NativeVerificationLibrary.warmUp()` before anything
else. The warm-up obtains `HintsLibraryBridge.getInstance()` and `WRAPSLibraryBridge.getInstance()`,
which load the native libraries, and then calls `WRAPSLibraryBridge.isVerificationSupported()`,
which loads the verification key embedded in the jar (about 2 seconds). The order matters: asking
for the key before both instances exist fails with an `UnsatisfiedLinkError` that leaves the
library unusable for the rest of the process.

The library extracts its native libraries (a few MB) into the directory named by the
`java.io.tmpdir` system property. That directory must be writable and allow executing mapped
files, so it must not be mounted `noexec`. With a read-only root filesystem, mount a writable
`emptyDir` at `/tmp`.

- On success the plugin logs INFO
  `TSS library <version> ready to verify block proofs, loaded in <N> ms`, loads the TSS data held
  by the application state facility, registers its handlers and starts.
- When the library throws, the gateway logs a WARNING with the stack trace,
  `The TSS library <version> could not be loaded (temp directory <dir>)`. When the library
  reports the key as not loaded, it logs its own reason.
- On either failure the plugin logs ERROR
  `The TSS library <version> cannot verify block proofs in this process, shutting down` and calls
  `serverHealth().shutdown(...)` without registering its handlers. The failure is permanent for
  the JVM, and a node that stayed up would answer every valid TSS block with a failure that blames
  the publisher. Kubernetes then restarts the pod.

The warm-up always runs; there is no configuration property for it.

### Verification

`TSSVerifier` runs for each block whose proof is a `signed_block_proof`, and `StateProofVerifier`
uses it for the signed proof inside a block state proof. It reads the current `TssData` from
`VerificationDataProvider` and calls `TSS.verifyTSS(ledgerId, blockSignature, signedHash)`
through the gateway:

- No TSS data held: `MISSING_VERIFICATION_DATA`.
- The signature verifies: success.
- The signature does not verify: `BAD_BLOCK_PROOF`.
- The library throws `IllegalArgumentException` (or `IllegalStateException`) because it
  considers the ledger ID or the signature malformed: `BAD_BLOCK_PROOF`, and a WARNING with the
  stack trace,
  `The TSS library <version> rejected the block signature or the ledger id of the TSS data valid from block <N>`,
  logged for the first such rejection under that TSS data; the following ones are logged at DEBUG,
  since the rejection repeats for every block until the data changes. This usually means the TSS
  data comes from a network that used an earlier TSS library (see
  [Migration across TSS library changes](#migration-across-tss-library-changes)) or the publisher
  signed with another TSS library. The Block Node cannot tell these two causes apart, so the failure
  stays `BAD_BLOCK_PROOF`, which ends only the stream that supplied the block.

An `Error` thrown by the library completes the verification session exceptionally and is
reported as `UNKNOWN_ERROR`, not as a bad block proof.

### TSS data

`TssData` (see `node_service.proto`) carries:

- `ledger_id`: the only verification input besides the block signature and the signed hash.
- `valid_from_block`: the first block the data applies to.
- `current_roster`: the Roster that published the ledger ID; informational and optional, it is
  not needed to verify.
- `wraps_verification_key`: no longer used. Producers leave it empty and consumers ignore it; it
  stays in the message for wire compatibility.

`TssData` reaches the node from three sources:

1. **Block 0 of the stream.** While the node holds no TSS data, `BlockHasher` scans the signed
   transactions of block 0 for a `LedgerIdPublicationTransactionBody`. `VerificationHelper` maps
   it to `TssData` (ledger ID, node contributions as the informational Roster, valid from block
   0; the publication's `history_proof_verification_key` is not read), and
   `VerificationDataProvider` installs it and forwards it to the application state facility.
   Block 0 provisions TSS data only when the node holds none.
2. **Bootstrap file.** The application state facility (`ApplicationStateFacilityPlugin` in the
   `facility-app-state` module) loads `app.state.tssBootstrapFilePath` (default
   `/opt/hiero/block-node/application-state/tss-bootstrap-roster.json`, `TssData` as JSON) at
   start and rewrites it whenever the data changes.
3. **Peer Block Nodes.** The `roster-bootstrap-tss` plugin periodically queries the peers listed
   in `roster.bootstrap.tss.blockNodeSourcesPath` through `serverStatusDetail` and hands each
   answer to the application state facility.

The application state facility and `VerificationDataProvider` both install data only when it is
valid from a strictly later block than the data they hold (or when they hold none); the facility
then persists it and sends a `TssDataNotification`. Both ignore data without a ledger ID, since TSS
data SHALL carry one (`node_service.proto`). Neither inspects the ledger ID beyond requiring one,
and neither needs a Roster.

For a node joining an existing network where block 0 is never replayed, place the bootstrap file
in the application state volume; the `blocks validate` command of the Block Stream tool
(`tools-and-tests/tools`) writes `tss-bootstrap-roster.json` from a stream that contains the
publication.

### Migration across TSS library changes

A TSS library change can alter the proof format and the ledger ID of every TSS network. The move
to hiero-cryptography 3.22.0 (Block Node 0.45.0, with Consensus Node 0.79.0) is such a change: the Block Node cannot verify
TSS block proofs produced with the earlier library, and earlier Block Nodes cannot verify the new
ones. A node upgraded across the change still holds the old ledger ID in its bootstrap file, so
every new TSS block fails with `BAD_BLOCK_PROOF`, the first one with the WARNING above. The verifier does not
filter such data, since the ledger ID is opaque; the operator resets the TSS data instead (delete
or replace the bootstrap file, or let block 0 of the new stream provision it) and resets the block
store if it holds TSS blocks from before the change. See
[Block Node Version Compatibility](../block-node/operations/version-compatibility.md) and
[Resetting and Upgrading the Block Node](../block-node/operations/resetting-and-upgrading-the-block-node.md#resetting-tss-data-across-a-tss-library-change).

## Configuration

|                  Property                   |                               Default                               |               Description               |
|---------------------------------------------|---------------------------------------------------------------------|-----------------------------------------|
| `app.state.tssBootstrapFilePath`            | `/opt/hiero/block-node/application-state/tss-bootstrap-roster.json` | Where `TssData` is persisted and loaded |
| `roster.bootstrap.tss.blockNodeSourcesPath` | empty                                                               | Peer Block Nodes to query for `TssData` |

The verification plugin has no TSS property and the warm-up has no switch. The TSS library reads
no configuration: it embeds what it verifies against and extracts its native libraries into
`java.io.tmpdir`.

## Metrics

`verification_proof_total{proof_type="tss", result=...}` counts TSS verification successes and
failures (see [Metrics](../block-node/metrics.md)).

## Exceptions

- The TSS library cannot be loaded at start: WARNING with the stack trace, ERROR, and the node
  shuts down (see [Start-up warm-up](#start-up-warm-up)).
- `IllegalArgumentException` or `IllegalStateException` from `TSS.verifyTSS`: `BAD_BLOCK_PROOF`
  and a WARNING with the stack trace for the first rejection under a TSS data, DEBUG afterwards.
- An `Error` from the library during verification: `UNKNOWN_ERROR`.

## Acceptance Tests

- `NativeVerificationLibraryTest` (also run alone in a fresh JVM): the warm-up reports that TSS
  block proofs can be verified; a valid signature verifies; changing the first or the last byte of
  the ledger ID makes verification fail; a truncated ledger ID makes the library throw
  `IllegalArgumentException`; concurrent callers get the same results as sequential ones.
- `SignerTssVerificationTest` and `VerificationDataProviderTest`: tampered signatures fail, TSS
  data without a Roster is installed, TSS data without a ledger ID is ignored, the strictly later
  `valid_from_block` rule holds, and a library rejection is reported once per TSS data.
- `ApplicationStateFacilityPluginTest`: the facility follows the same strictly later rule, does not
  persist data for a block it already holds data for, and ignores TSS data (or a bootstrap file)
  without a ledger ID.
- The test blocks are signed by the deterministic `TssBlockSigner.create()`
  (`tools-and-tests/block-signing`) with a committed genesis WRAPS. Regenerating it needs the full
  library jar, which the build swaps in only for
  `GENERATE_WRAPS_PROOF=true ./gradlew :block-signing:test --tests '*WrapsProofGeneratorTest*'`.
