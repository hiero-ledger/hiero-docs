# Block Node Version Compatibility

This document defines the version compatibility requirements between Block Node (BN),
Consensus Node (CN), and Mirror Node (MN) releases.

It is intended for Block Node operators planning upgrades and for integration developers
who need to know which component versions are required to run together.

---

## Version compatibility table

|   Block Node    |   Consensus Node    |     Mirror Node      |
|-----------------|---------------------|----------------------|
| 0.45.0+         | 0.79.0+ (tentative) | 0.166.0+ (tentative) |
| 0.41.x - 0.44.x | 0.77.x - 0.78.x     | 0.162.x - 0.165.x    |
| 0.38.x - 0.40.x | 0.76.x              | -                    |
| 0.36.x - 0.37.x | 0.75.x              | -                    |

Mirror Node versions are tracked from BN 0.41.0 onwards. The 0.45.0 row is tentative until those
releases exist; see the [compatibility fence at BN 0.45.0](#compatibility-fence-at-bn-0450--cn-0790--mn-01660).
Pre-releases on the earlier TSS library pair like the 0.41.x - 0.44.x row: the Block Node
pre-releases 0.45.0-rc1, 0.45.0-rc2 and 0.45.0-sha256.*, and the Consensus Node pre-releases of
0.79.0 and 0.80.0 published so far (the alpha pre-releases). The Block Node carries the new library
from the first 0.45.0 release candidate after rc2.
For version combinations not listed here, check the
[Block Node releases page](https://github.com/hiero-ledger/hiero-block-node/releases)
or open an issue on the [hiero-block-node repository](https://github.com/hiero-ledger/hiero-block-node/issues).

---

## Compatibility fence at BN 0.45.0 / CN 0.79.0 / MN 0.166.0

> **Tentative:** these versions are planned and may change until the releases exist.

BN 0.45.0 moves block proof verification to hiero-cryptography 3.22.0, the TSS library release
that the Consensus Node adopts with
[hiero-consensus-node PR 27506](https://github.com/hiero-ledger/hiero-consensus-node/pull/27506)
(it uses 3.20.1, which is wire compatible with 3.22.0; the change is cherry-picked into CN 0.79.0)
and the Mirror Node with
[hiero-mirror-node PR 14399](https://github.com/hiero-ledger/hiero-mirror-node/pull/14399). The
library changes the TSS block signature and the ledger ID of every TSS network, so this is a hard
compatibility break for TSS block proofs:

- BN 0.45.0+ cannot verify TSS block proofs produced by a CN on an earlier TSS library (every CN
  release before 0.79.0, and the 0.79.0 and 0.80.0 alpha pre-releases, use hiero-cryptography
  3.15.0 or older), and a BN older than 0.45.0 (including the 0.45.0-rc1, 0.45.0-rc2 and
  0.45.0-sha256.* pre-releases) cannot verify TSS block proofs produced by CN 0.79.0+.
- An MN older than 0.166.0 cannot verify TSS block proofs produced by CN 0.79.0+.
- Wrapped Record Blocks (RSA block proofs) are not affected.

No public network (previewnet, testnet, mainnet) produces TSS signed blocks yet. Solo, CI and other
test environments that run with TSS enabled must upgrade BN, CN and MN together and reset the
Block Node's TSS data at the same time as their Consensus Nodes:

- Without that reset, the BN keeps the old ledger ID from its TSS bootstrap file and every new TSS
  block fails verification with `BAD_BLOCK_PROOF`. The node starts normally; the first failing block
  logs a WARNING `The TSS library 3.22.0 rejected the block signature or the ledger id of the TSS data valid from block <N>`
  (the following blocks log the library's reason at DEBUG, and each one is still logged as a failed
  verification session).
- A BN that already stored TSS signed blocks from before the change must also reset its block
  store: those blocks do not verify on BN 0.45.0+.

See [Resetting TSS data across a TSS library change](./resetting-and-upgrading-the-block-node.md#resetting-tss-data-across-a-tss-library-change)
for the procedure.

---

## Compatibility fence at BN 0.41.0 / CN 0.77.0 / MN 0.162.0

BN 0.41.0 and MN 0.162.0 changed the Merkle Mountain Top hasher (defined by [HIP-1424](https://hips.hedera.com/hip/hip-1424)) to
always use a fixed 16-leaf tree, which changes how block hashes are computed. All three
components must cross this fence together:

- Blocks stored by a BN older than 0.41.0 will fail verification on BN 0.41.0+. You will see
  WARNING-level log entries and the `verification_blocks_failed` metric will increase — see
  [Metrics](../metrics.md) for how to monitor this.
- Block hashes computed by an MN older than 0.162.0 will not match those computed by BN 0.41.0+.
- CN 0.77.0 is required for protocol compatibility with BN 0.41.0.

When CN 0.77 ships, upgrade BN, CN, and MN at the same time. Block Node operators are encouraged to reset
the block store when upgrading to BN 0.41.0 - see
[Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md).

---

## If blocks are not reaching the Block Node

If your Block Node is running but not receiving blocks, check these two CN node properties
before investigating the network or BN configuration:

- `blockStream.streamMode` - default is `BOTH` (stream both blocks and records) since CN 0.75.0.
  If set to `RECORDS`, the CN does not send any blocks to the Block Node.
- `blockStream.writerMode` - default is `FILE_AND_GRPC` (write to both the local file system
  and the Block Node gRPC stream) since CN 0.75.0. If set to `FILE`, no blocks are streamed
  to the Block Node over gRPC.

Neither property needs to be set explicitly if the defaults are acceptable. For the full list
of CN block stream configuration properties and how to set them, see
[Consensus Node to Block Node Configuration](./consensus-node-to-block-node-configuration.md).

---

## Further reading

- [Preparing Your Block Node for WRB Cutover](./preparing-your-block-node-for-wrb-cutover.md)
- [Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md)
- [Block Node Hardware Specifications](./block-node-hardware-specifications.md)
- [Block Node releases](https://github.com/hiero-ledger/hiero-block-node/releases)
