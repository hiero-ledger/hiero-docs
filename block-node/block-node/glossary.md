# Block Node Glossary

Terms used across Hiero Block Node documentation, listed alphabetically.
Terms marked *(planned)* describe capabilities that are not yet implemented.
Terms marked *(theoretical)* describe services that are architecturally possible
but not currently provided by any known deployment.

---

## A

### Aggregated Signatures

***

A constant-size BLS threshold signature produced by the hinTS Threshold Signature Scheme
(TSS). Requires partial signatures from nodes holding more than half the network's consensus
weight (staked HBAR). Replaces the per-node RSA multi-signatures used in the legacy record
stream. Defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200).
See also: [TSS (hinTS)](#tss-hintsts), [WRAPS](#wraps).

### Archive Server

***

A Block Node type that provides cold storage of historical block data without exposing live
streaming or consumer APIs.
Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

---

## B

### Backfill

***

The process by which a Block Node retrieves missing historical blocks from one or more peer
Block Nodes. Consensus Nodes retain only a minimal recent buffer and cannot supply history;
backfill always targets another Block Node. On a mature network, full backfill can take days
or weeks. Configured via `BACKFILL_BLOCK_NODE_SOURCES_PATH`.

### Block Footer

***

The last non-proof item in a block. Signals that the block is complete. Exactly one
`BlockFooter` appears per block, followed by one or more `BlockProof` items and then
`end_of_block`.
See also: [Block Proof](#block-proof), [Block Item](#block-item).

### Block Header

***

The first [Block Item](#block-item) in every block. Contains the HAPI protocol version,
software version, [block number](#block-number), previous block hash, and the consensus
timestamp of the first transaction in the block. Defined in
[HIP-1056](https://hips.hedera.com/hip/hip-1056).

### Block Item

***

An individual unit within a block stream. Block Items include `BlockHeader`,
`EventHeader`, `RoundHeader`, `EventTransaction`, `TransactionResult`,
`TransactionOutput`, `StateChanges`, `TraceData`, `BlockFooter`, and `BlockProof`.
Defined in [HIP-1056](https://hips.hedera.com/hip/hip-1056).

### Block Node

***

A software system that ingests, verifies, stores, and serves block streams for a Hiero
network. Block Nodes receive block streams from Consensus Nodes (Tier 1) or other Block
Nodes (Tier 2), verify each block's integrity, store valid blocks, and serve blocks to
subscribers such as Mirror Nodes. Defined in
[HIP-1081](https://hips.hedera.com/hip/hip-1081).
See also: [Tier 1 Block Node](#tier-1-block-node), [Tier 2 Block Node](#tier-2-block-node).

### Block Node Type

***

The deployment shape of a Block Node, describing which services it provides and how much
history it retains. Types include [Rolling-History](#rolling-history),
[Full Node](#full-node), [Light Node](#light-node), [Private-Cloud](#private-cloud),
[Archive Server](#archive-server), and [Community Node](#community-node).
Not to be confused with [Block Node Tier](#tier-1-block-node).

### Block Number

***

A monotonically increasing integer assigned by consensus to each block produced by the
network. Block numbers start at 0 (genesis) and increment by 1 per block. Used in
`serverStatus` (`first_available_block`, `last_available_block`), subscription requests
(`start_block_number`, `end_block_number`), and backfill configuration.

### Block Proof

***

A cryptographic proof attached to each block that allows any consumer to independently
verify the block's authenticity. A block may contain multiple `BlockProof` items; they
always appear after the [Block Footer](#block-footer). Defined in
[HIP-1056](https://hips.hedera.com/hip/hip-1056).

### Block Stream

***

A continuous, ordered feed of finalized block data produced by Consensus Nodes and served
by Block Nodes. Each block consists of a stream of `BlockItem` messages containing
transactions, state changes, events, EVM trace data, and cryptographic proofs. Delivered
over gRPC in Protocol Buffer format. Replaces the legacy
[Record Stream](#record-stream). Defined in
[HIP-1056](https://hips.hedera.com/hip/hip-1056).

---

## C

### Community Node

***

A Block Node type operated by community members or ecosystem participants to provide
general-purpose block stream access to the broader network.
Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

### Consensus Node

***

A node in the Hiero consensus network. Consensus Nodes execute the consensus algorithm,
process transactions, maintain current network state, and produce the Block Stream.
They retain only a minimal recent buffer of block data and cannot supply historical blocks
to a recovering Block Node.

### Cutover Boundary

***

The point in the network's release timeline at which Consensus Nodes stop producing
[Record Streams](#record-stream) and start producing [Block Streams](#block-stream).
Activates at Consensus Node release 0.77.0. Defined in
[HIP-1193](https://hips.hedera.com/hip/hip-1193).
See also: [WRB (Wrapped Record Block)](#wrb-wrapped-record-block), [Jumpstart Data](#jumpstart-data).

### Cutover Release

***

The Consensus Node software release at which production of [Record Streams](#record-stream)
ends and production of [Block Streams](#block-stream) simultaneously begins. This release
also enables TSS signatures and shifts data access from cloud storage buckets to Block Nodes.
See [Cutover-Process.md](./Cutover-Process.md) for the full phase sequence.

---

## F

### Full Node

***

A Block Node type that retains all history and state, offering the broadest range of
services including state management, state proofs, content proofs, and query APIs.
Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

---

## H

### HAPI Version

***

The Hedera API protocol version reported by the consensus network. Mirror Nodes use the
HAPI version to detect when the [Cutover Boundary](#cutover-boundary) has been crossed
and automatically switch their data source from cloud storage to block-stream subscription.

### HIP (Hiero Improvement Proposal)

***

A formal specification document that proposes changes to the Hiero protocol, network
behaviour, or ecosystem standards. HIPs are the authoritative source for features such as
Block Streams ([HIP-1056](https://hips.hedera.com/hip/hip-1056)), Block Nodes
([HIP-1081](https://hips.hedera.com/hip/hip-1081)), on-chain registration
([HIP-1137](https://hips.hedera.com/hip/hip-1137)), TSS signatures
([HIP-1200](https://hips.hedera.com/hip/hip-1200)), and the record-to-block-stream cutover
([HIP-1193](https://hips.hedera.com/hip/hip-1193)). Browse all HIPs at
[hips.hedera.com](https://hips.hedera.com).

---

## J

### Jumpstart Data

***

A small amount of configuration data (under 1.5 KB) containing the last wrapped block
number, its root hash, and the streaming Merkle tree state. Consensus Nodes are configured
with this data at the release that activates WRB production, enabling them to resume block
hash calculation without scanning millions of historical blocks.
See [WRB CLI Runbook](./operations/wrb-cli-runbook.md).

---

## L

### Ledger ID

***

The hash of the genesis TSS Roster. Used alongside the [WRAPS](#wraps) verification key
to verify [Aggregated Signatures](#aggregated-signatures) on block streams.
Network-specific — each Hiero network has its own Ledger ID.
Defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200).

### Light Node

***

A Block Node type suitable for development, testing, or services that do not require full
history. A variant of [Rolling-History](#rolling-history) with a focus on lightweight
deployments. Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

### Local Full History (LFH)

***

A Block Node that retains the complete block history of the network on its local storage
volumes. Always written as "Local Full History (LFH)" — never "Long-Form-History."

---

## M

### Mirror Node

***

A Hiero service that provides extensive historical data, query capabilities, and analytics
for the network. Mirror Nodes subscribe to Block Nodes to receive the block stream and
index it into their own storage. After the [Cutover Boundary](#cutover-boundary), Mirror
Nodes connect to Block Nodes instead of downloading record files from cloud storage.

---

## N

### NLG (Network Load Generator)

***

A Hiero test tool that generates high-volume transaction load against a network of
Consensus Nodes. Used in conjunction with [Solo](#solo-provisioner) to drive
realistic production-scale traffic against a Block Node before connecting it to the
live network. See [Load Testing with Solo and NLG](./operations/load-testing-a-deployed-block-node-using-solo-and-nlg.md).

---

## P

### Partial History

***

A service provided by some Block Nodes that make available a subset of the network's
history — for example, only the most recent 30 days, or blocks after a specific height.
See also: [Rolling-History](#rolling-history).

### Plugin

***

A composable unit of functionality in the Block Node. All major Block Node features
(verification, storage, publishing, subscribing, backfill, health checks) are implemented
as plugins conforming to the `BlockNodePlugin` interface. The set of active plugins
determines what services a deployed Block Node provides.
See [Architecture Overview](./architecture/architecture-overview.md).

### Private Archive

***

A service provided by some Block Nodes that store block stream data in an archive with no
public access. This may be for the benefit of a private entity, or may be a form of
disaster recovery support for the public network. Example storage locations include private
cloud buckets, long-term tape, or replicated local disks.

### Private-Cloud

***

A Block Node type deployed within a private network or cloud environment, typically
serving a single organisation's internal needs rather than the broader public network.
Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

### Private Sphere

***

A Hiero network operated on behalf of a private entity.

### Publisher

***

An entity that publishes block data to a Block Node via the `publishBlockStream` gRPC API.
In a typical Hiero network, publishers are Consensus Nodes.

---

## R

### Record Block History (RBH) Block Node

***

A Block Node customised to hold no retention limit, receive [Wrapped Record Blocks](#wrb-wrapped-record-block)
only through an offline out-of-process load, and serve as a backfill source for live Block
Nodes to pre-load WRB history before the [Cutover Release](#cutover-release).
See [Cutover-Process.md](./Cutover-Process.md).

### Record Stream

***

The legacy output format of a Hiero Consensus Node, superseded by the
[Block Stream](#block-stream). Published as a sequence of files (`.rcd`, `.rcd.sig`,
sidecar, and event stream files) to public cloud storage. Mirror Nodes historically
downloaded these files to ingest network data.

### Reconnect Services *(planned)*

***

APIs and data streams that will allow Consensus Nodes to catch up to the current network
state after downtime by requesting recent blocks and state snapshots from Block Nodes.
Service interfaces are defined; this capability is planned for a future release and is not
currently in active development.

### Rolling-History

***

A Block Node type that retains only recent history — for example, the last 24 hours or
30 days — rather than full history from genesis. Most Tier 2 nodes are expected to be
this type. Provides a [Partial History](#partial-history) service.
Defined in [Block Node Types and Tiers](./../../Block-Node-Types.md).

### Roster / TSS Roster

***

The current set of Consensus Nodes and their consensus weights used by the TSS scheme.
The genesis roster's hash is the [Ledger ID](#ledger-id). Use "Roster" in documentation
— "Address Book" is an older, largely obsolete term.

---

## S

### serverStatus

***

A gRPC endpoint (`BlockNodeService.serverStatus`) that returns metadata about a Block
Node's current state: `first_available_block`, `last_available_block`, and
`only_latest_state`. These three fields are the only fields on `ServerStatusResponse`.
The extended `ServerStatusDetailResponse` provides extended detail, including
`version_information`, `node_address_book`, `available_ranges`, `stored_ranges`, `tss_data`,
and `ranged_address_book_history`. Note: the extended response does not carry the fields
returned in the base response.

### `StateChanges`

***

A [Block Item](#block-item) type that carries explicit CRUD operations applied to the
network's named states (maps, queues, singletons) as part of a block. Block Nodes apply
`StateChanges` to maintain their local copy of network state. For batch transactions,
`StateChanges` appear at the batch boundary rather than per individual transaction.
Defined in [HIP-1056](https://hips.hedera.com/hip/hip-1056).

### Solo Provisioner

***

The recommended tool for provisioning Block Nodes on cloud VMs or bare-metal Kubernetes
clusters. Formerly known as Solo Weaver, and only referred to as `Solo Provisioner` in
descriptions. The repository URL and filenames may still contain references to `solo-weaver`.
See [Deploy with Solo Provisioner](./operations/solo-weaver-single-node-k8s-deployment.md).

### State Snapshot *(planned)*

***

A point-in-time capture of the complete network state (accounts, balances, smart contract
storage, and so on) at a specific block height. Block Nodes will produce state snapshots
from their locally managed state once state management is implemented. This capability is
planned for a future release.

### Subscriber

***

An entity that subscribes to a block stream from a Block Node via the
`subscribeBlockStream` gRPC API. Subscribers include Mirror Nodes, Tier 2 Block Nodes,
and any application consuming live or historical block data.
See also: [Publisher](#publisher).

---

## T

### Tier 1 Block Node

***

A Block Node that receives the block stream directly from one or more Consensus Nodes.
Tier 1 nodes are typically operated by trusted or Council-affiliated entities and form the
first layer of the block-stream distribution network.

### Tier 2 Block Node

***

A Block Node that receives the block stream from one or more upstream Block Nodes (Tier 1
or another Tier 2). Tier 2 nodes are permissionless and are commonly used to redistribute
the stream to downstream clients such as Mirror Nodes or to provide value-added
application-specific services.

### TSS (hinTS)

***

Threshold Signature Scheme — specifically the hinTS construction used by Hiero. Produces
a constant-size, constant-time BLS aggregate signature from partial signatures by nodes
holding more than half the network's consensus weight. Replaces per-node RSA signatures.
Defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200).
See also: [Aggregated Signatures](#aggregated-signatures), [WRAPS](#wraps).

### TSS Ceremony

***

A secure multi-party computation (Powers-of-Tau) that produced the Universal Structured
Reference String (SRS) for the WRAPS proving system. The ceremony is complete; artifacts
ship with TSS library releases.
Specified in [HIP-1398](https://github.com/hiero-ledger/hiero-improvement-proposals/pull/1398).

---

## V

### Verified Block

***

A block for which a [Block Proof](#block-proof) has been received and the
[Aggregated Signature](#aggregated-signatures) against the network's [Ledger ID](#ledger-id)
and [WRAPS](#wraps) verification key is valid. Block Nodes persist only verified blocks;
blocks delivered from the live stream (before verification completes) are unverified and may
contain errors, be repeated, or be incomplete — the receiver is responsible for verification.
See also: [Block Proof](#block-proof).

---

## W

### WRB (Wrapped Record Block)

***

A block-stream-format wrapper around a historical record file. Each WRB embeds the
original record stream content in a `BlockItem`, adds a block header, footer, and block
proof derived from the original per-node RSA signatures. WRBs make pre-cutover history
accessible through the post-cutover block-stream API without losing cryptographic
provenance. Specified in HIP-1427 (draft; implemented).
See [WRB CLI Runbook](./operations/wrb-cli-runbook.md).

### WRB Catch Up

***

The process by which a Consensus Node combines [Jumpstart Data](#jumpstart-data) with
timestamp and subtree root hashes to produce a valid WRB hash for the block following the
jumpstart block, then continues block-by-block until it is current and can resume normal
transaction processing. See [Cutover-Process.md](./Cutover-Process.md).

### WRAPS

***

Weighted Roster Attestation Proof System. A recursive SNARK proof mechanism that attests
the active [Roster](#roster--tss-roster) is a valid descendant of the genesis roster,
ensuring [Aggregated Signatures](#aggregated-signatures) remain verifiable as the roster
evolves. The WRAPS verification key is constant across nearly all Hiero networks.
Defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200).
