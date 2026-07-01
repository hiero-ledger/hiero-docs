# TSS Block Proof Verification

## Purpose

Cryptographically confirms that blocks were signed by a threshold of network stake using TSS.
For the broader block verification design, see
[`docs/design/block-verification.md`](block-verification.md).

## Terms

**LedgerId** — Network identity used as root of trust for `TSS.verifyTSS()`. Published in
block 0 via `LedgerIdPublicationTransactionBody`.

**TssSignedBlockProof** — A `BlockProof` whose `blockSignature` layout depends on WRAPS
availability:

- **Pre-settled** (WRAPS not yet available):
  `vk (1,096) || blsSig (1,632) || aggregate_schnorr_sig (192)` = 2,920 bytes
- **Post-settled** (WRAPS available):
  `vk (1,096) || blsSig (1,632) || wraps_compressed_proof (704)` = 3,432 bytes

Both variants are handled internally by `TSS.verifyTSS()`.

## Design

`VerificationServicePlugin` owns TSS state (`activeLedgerId`, `activeTssPublication`) as public
static fields and passes the ledger ID to each `ExtendedMerkleTreeSession` via constructor.
Block items stream into the session, which hashes them into five subtree hashers by kind.

For block 0, `SIGNED_TRANSACTION` items are scanned for `LedgerIdPublicationTransactionBody`.
When found, the session calls `VerificationServicePlugin.initializeTssParameters()` which sets
the native TSS state (address book, WRAPS VK) and updates the plugin's static fields.
Block 0 can overwrite TSS parameters on each attempt until they are persisted after successful
verification.

On end-of-block, the session computes the block root hash and calls
`TSS.verifyTSS(ledgerId, signature, hash)`. The library handles dispatch between the genesis
Schnorr path and the settled WRAPS path internally, and rejects malformed signatures.

## TSS Parameters Bootstrap

TSS verification requires three components: the **ledger ID**, the **address book** (node public
keys, weights, and IDs), and the **WRAPS verification key**. All three are published together in
a `LedgerIdPublicationTransactionBody` in block 0 and persisted as a single protobuf file.

`activeLedgerId` and `activeTssPublication` are public static fields on
`VerificationServicePlugin`, initialized from one of two sources:

1. **Persisted file** (`verification.tssParametersFilePath`) — written after block 0 is
   verified, loaded on restart. Contains a serialized `LedgerIdPublicationTransactionBody`
   which fully restores all TSS state (address book + WRAPS VK + ledger ID).
2. **Block Stream** — `LedgerIdPublicationTransactionBody` found in a `SIGNED_TRANSACTION`
   item during block 0 processing. The session calls
   `VerificationServicePlugin.initializeTssParameters()` directly. When block 0 is verified
   successfully, the publication is persisted to the file.

TSS parameters can be overwritten by any block 0 until they are persisted (after successful
verification or file bootstrap). Once persisted, `initializeTssParameters()` becomes a no-op
(`tssParametersPersisted` guard).

For nodes joining an existing network where block 0 will never be replayed, the TSS parameters
file can be placed manually into the volume.

## Configuration

|               Property               |                           Default                            |           Description           |
|--------------------------------------|--------------------------------------------------------------|---------------------------------|
| `verification.tssParametersFilePath` | `/opt/hiero/block-node/application-state/tss-parameters.bin` | TSS parameters persistence path |
