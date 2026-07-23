# Record Stream to Block Stream Migration

This document describes what changes between [record streams](./glossary.md#record-stream) and [block streams](./glossary.md#block-stream) from a Mirror Node operator's perspective, so an operator can assess migration impact and plan their upgrade. The companion document for the consensus-side cutover narrative - phase sequence, [Wrapped Record Block (WRB)](./glossary.md#wrb-wrapped-record-block) construction, [Jumpstart Data](./glossary.md#jumpstart-data), error handling - is [Cutover-Process.md](./Cutover-Process.md). For the configuration steps to subscribe a Mirror Node to a Block Node, see [Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md).

---

## Why record streams are being replaced

The record stream was the consensus network's original output format. It split the network's per-block output across four separate file types - record files for transactions, sidecar files for trace data, signature files for per-node RSA signatures, and event stream files for hashgraph events. Verifying a record file required collecting and verifying RSA signatures from a majority of consensus nodes, an operation whose cost scales linearly with the node count. State changes were not part of the stream at all.

The block stream, specified in [HIP-1056](https://hips.hedera.com/hip/hip-1056), unifies the four record-stream artifacts into a single ordered feed of `BlockItem` messages and adds two capabilities that did not exist in the record stream: each block carries its own self-contained cryptographic proof (the Block Proof) signed with a constant-size [TSS](./glossary.md#tss-hintsts) aggregated signature ([HIP-1200](https://hips.hedera.com/hip/hip-1200)), and each block includes its state-change deltas so a consumer can rebuild state in lockstep with the network without replaying transactions from genesis. [HIP-1193](https://hips.hedera.com/hip/hip-1193) defines the network-level transition from record streams to block streams.

---

## What changes for a Mirror Node

The shift is in the source. A Mirror Node ingests the same logical content - transactions, results, sidecar trace data, signatures, state - but the transport, encoding, and verification mechanism all change.

|     Dimension      |          Before cutover (record stream)          |                                                                        After cutover (block stream)                                                                        |
|--------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Transport          | Poll cloud-storage buckets (GCS / S3)            | Subscribe to one or more Block Nodes over gRPC                                                                                                                             |
| Encoding           | Record file (`.rcd`) + sidecar + signature files | `BlockItem` messages in a single stream                                                                                                                                    |
| Verification       | Per-node RSA signatures, majority verification   | Single TSS aggregated signature per block, verified against the network's Ledger ID and the WRAPS verification key (the same key is used across nearly all Hiero networks) |
| Source discovery   | Operator-configured cloud-bucket source          | On-chain registry per [HIP-1137](https://hips.hedera.com/hip/hip-1137) or operator-supplied list                                                                           |
| Trigger for switch | Manual reconfiguration                           | Automatic per [HAPI version](./glossary.md#hapi-version) (Mirror Node v0.155+)                                                                                             |

For configuration steps and the property table, see [Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md). For the operator-visible behaviour of a Block Node subscription, see [Mirror Node Integration](./mirror-node-integration.md).

---

## Data equivalence

The Mirror Node importer composes `BlockItem` messages back into the same transaction-and-result shape it consumed from record files. The mapping below traces the importer's `BlockStreamReaderImpl` and `BlockFileTransformer` for the current `hiero-mirror-node@fb39e329b8`.

|        Record stream artifact         |                        Block stream item(s)                        |                                                                                                                                                                                                                         Notes                                                                                                                                                                                                                          |
|---------------------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Record file (`.rcd`) - block envelope | `BlockHeader` + per-round event/transaction items + `BlockProof`   | A record file historically captured one round of consensus. A block contains one round of consensus by default; the rounds-per-block ratio is configurable per [HIP-1056](https://hips.hedera.com/hip/hip-1056).                                                                                                                                                                                                                                       |
| Transaction body                      | `signed_transaction` field inside a `SignedTransaction` block item | The `TransactionBody` is decoded from `SignedTransaction.bodyBytes`, same as in the record stream.                                                                                                                                                                                                                                                                                                                                                     |
| Transaction record                    | `TransactionResult` item + zero or more `TransactionOutput` items  | The single `TransactionRecord` in the record stream is split: status, fees, and consensus timestamp live in `TransactionResult`; type-specific outputs (token mint serials, contract call results, etc.) live in `TransactionOutput`.                                                                                                                                                                                                                  |
| Sidecar records (trace data)          | `TraceData` items, inline                                          | Sidecars were a separate file in the record stream; trace data is now interleaved with the transaction's other items in the block stream.                                                                                                                                                                                                                                                                                                              |
| Signature file (`.rcd.sig`)           | `BlockProof` item                                                  | The per-node RSA signature file is replaced by a single TSS aggregated signature carried in `BlockProof`.                                                                                                                                                                                                                                                                                                                                              |
| Event stream file                     | `EventHeader` block items + associated event data, inline          | Per `block_item.proto`, a `block_header` is followed by an `event_header` and that event's transactions; event metadata is interleaved with transactions in the block rather than published as a separate file.                                                                                                                                                                                                                                        |
| Running-hash chain                    | Per-block merkle-tree linkage signed by `BlockProof`               | The record-stream running-hash chain across files is replaced by a per-block merkle tree whose hash is signed by the `BlockProof`'s TSS signature; block N's merkle tree reuses the hash computed for block N−1 per [HIP-1056](https://hips.hedera.com/hip/hip-1056). The first post-cutover block binds the WRB root hash of the final pre-cutover record file as its previous-block hash, per [HIP-1193](https://hips.hedera.com/hip/hip-1193) §3.2. |

State change data does not appear above because it has no record-stream equivalent - it is new in the block stream and listed in the next section.

---

## What is new in block streams

Items present in the block stream that had no record-stream counterpart:

- **State changes per block** - every block carries the state deltas produced by the round of consensus that built it, as `StateChanges` block items. Consumers can rebuild state in step with the network without replaying transactions from genesis.
- **Block Proof** - a single self-contained cryptographic proof for each block, signed with a TSS aggregated signature ([HIP-1200](https://hips.hedera.com/hip/hip-1200)). One signature replaces the per-node RSA signatures the record stream used.
- **Per-block merkle tree signed by the Block Proof** - each block is structured as a merkle tree whose root is signed by the TSS aggregated signature carried in `BlockProof`, enabling per-item inclusion proofs ([HIP-1424](https://hips.hedera.com/hip/hip-1424#section-14)).
- **Unified format** - events, transactions, results, trace data, signatures, and state changes are all `BlockItem` messages in one stream rather than four separate file types.
- **On-chain endpoint discovery** - Block Nodes register their service endpoints on-chain per [HIP-1137](https://hips.hedera.com/hip/hip-1137), enabling Mirror Nodes to discover them through the network rather than from a hard-coded list. See [Block Node On-Chain Registration](./block-node-on-chain-registration.md).

---

## Cutover sequence

[HIP-1193](https://hips.hedera.com/hip/hip-1193) defines the cutover in phases rather than at fixed release numbers. The phases below follow the HIP's specification. The HAPI-version trigger that the Mirror Node uses to detect the boundary is `0.76.0` by default in `hiero-mirror-node@fb39e329b8` - operators should not change this without coordination with the network.

### Preparation phase

This is the release on the consensus network before the cutover boundary. Two things happen.

- **Ledger ID generation.** Consensus nodes perform the TSS ceremony specified in [HIP-1398](https://hips.hedera.com/hip/hip-1398) and externalize the Ledger ID via a synthetic `LedgerIdPublicationTransaction` (defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200)). The Ledger ID is the hash of the genesis TSS Roster and is what consumers use to verify the network's TSS signature on every subsequent block.
- **Mirror Node v0.155+ ships the auto-detect logic.** Operators on earlier versions must upgrade in order for automatic switching to occur.

The block stream is not yet produced during this phase; the Mirror Node continues ingesting from cloud storage.

### Cutover boundary

The boundary is a single network-update event, described in [HIP-1193](https://hips.hedera.com/hip/hip-1193) §2. The sequence at the boundary is:

1. The consensus network executes a Freeze Upgrade transaction and stops accepting new transactions.
2. The freeze round comes to consensus.
3. All record-stream signatures are collected and the final record file is finalized.
4. The final record file is wrapped into a Wrapped Record Block (WRB).
5. The first block of the block stream is produced. It binds the root hash of the final-record WRB as its previous-block hash (carried via the `BlockProof`'s signed merkle linkage), providing cryptographic continuity across the format change.
6. Consensus nodes stop producing record, event, and signature streams and start pushing block streams to configured Block Nodes.

From the Mirror Node side: the importer's cutover service polls for block-stream data once the network's HAPI version reaches the configured threshold (`cutover.hapiVersion` default `0.76.0`). When block-stream data appears, the Mirror Node automatically switches its source from cloud storage to Block Node subscription.

### Post-cutover

The network produces only the block stream. Mirror Nodes consume it from Block Nodes via gRPC. The legacy record-stream cloud buckets are no longer written to. The Mirror Node continues to operate without further operator intervention.

> Note, an operator is not expected to set `cutover.enabled`, `cutover.firstStage.*`, or any other cutover property on a stock Mirror Node v0.155+ deployment. The defaults are correct for the network the Mirror Node is connected to.

---

## Historical data after cutover

After the cutover boundary, the network no longer writes new record-stream files. Pre-cutover history remains accessible in two places.

- **Legacy record-stream files in cold storage.** The existing publicly accessible cloud buckets containing record files, sidecars, and signature files are preserved per [HIP-1193](https://hips.hedera.com/hip/hip-1193) §3.1. A Mirror Node that needs to ingest pre-cutover blocks can continue to download from these buckets.
- **Wrapped Record Blocks on Block Nodes.** Block Nodes that hold full history maintain an archive of WRBs covering pre-cutover blocks. Block Nodes serve WRBs alongside post-cutover blocks; an existing Mirror Node ingest path does not need to treat them differently - per [HIP-1193](https://hips.hedera.com/hip/hip-1193) §3.1, "Existing mirror nodes will not be affected by the introduction of WRBs in the block stream."

A Mirror Node operator starting a fresh Mirror Node after the cutover boundary does not need to choose between the two; subscribing to a Block Node provides both pre-cutover (WRB) and post-cutover (BlockStream) data through a single transport.

Consensus nodes retain only a minimal recent buffer of block data and cannot serve historical blocks. Any historical catch-up reads WRBs from a Block Node or legacy record files from cold storage.
