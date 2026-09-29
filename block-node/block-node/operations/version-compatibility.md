# Block Node Version Compatibility

This document defines the version compatibility requirements between Block Node (BN),
Consensus Node (CN), and Mirror Node (MN) releases.

It is intended for Block Node operators planning upgrades and for integration developers
who need to know which component versions are required to run together.

---

## Version compatibility table

|   Block Node    | Consensus Node | Mirror Node |
|-----------------|----------------|-------------|
| 0.41.0+         | 0.77.0+        | 0.162.0+    |
| 0.38.x - 0.40.x | 0.76.x         | -           |
| 0.36.x - 0.37.x | 0.75.x         | -           |

Mirror Node versions are tracked from BN 0.41.0 onwards. For version combinations not listed
here, check the [Block Node releases page](https://github.com/hiero-ledger/hiero-block-node/releases)
or contact your Hashgraph point of contact.

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
