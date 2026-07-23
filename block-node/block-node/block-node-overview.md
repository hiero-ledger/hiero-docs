# Block Node Overview

Block Nodes represent a new class of nodes in a Hiero network designed to increase decentralization and network data distributions. They enable operators to assume responsibility for long-term block and state storage while supporting the security and performance characteristics of a Hiero network.

This overview document provides operators with the essential concepts needed to understand Block Node roles and responsibilities before diving into deployment.

## What is a Block Node?

A Block Node is a special kind of server that keeps a complete, trustworthy copy of what is happening on a Hiero network.
It receives a stream of already-agreed blocks from Consensus Nodes, delivers the block stream via the subscribe API, checks that each block is valid, stores valid blocks, and serves single blocks via API query. In the future Block Nodes will also maintain an accurate copy of the current network state and will serve state related queries via new APIs.

Instead of pushing this data into centralized cloud storage, Block Nodes act as a decentralized data layer for the network.
They stream blocks to Mirror Nodes and other Block Nodes, answer questions from apps and services about past blocks or current state, and provide cryptographic proofs so users can independently verify that the data is correct.

## How Block Nodes differ from other nodes

|           **Aspect**           |                                        **Consensus Node**                                         |                                                   **Block Node**                                                   |                                                          **Mirror Node**                                                           |
|--------------------------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Primary role                   | Reach consensus and update canonical state.                                                       | Ingest, verify, store, and serve blocks, and state.                                                                | Provide value‑added access to historical data and analytics.                                                                       |
| Produces blocks                | Yes – produces finalized block streams per **([HIP-1056](https://hips.hedera.com/hip/hip-1056))** | No – consumes and verifies blocks from Consensus Nodes or upstream Block Nodes.                                    | No – will consume data from Block Nodes once the cutover lands; today Mirror Nodes still download record files from cloud storage. |
| Maintains full consensus state | Yes – authoritative state, optimized for consensus.                                               | (planned) Manages an active copy of network state locally, updated with `State Changes`; supports reconnect flows. | No – maintains data in an indexed form as needed for queries and analytics.                                                        |
| Data APIs                      | gRPC for transactions/queries; no history.                                                        | Streaming gRPC APIs for live and historical blocks, random-access retrieval, state, and proofs.                    | Public REST and custom APIs for queries and observability.                                                                         |
| Who runs it                    | Governing Council and approved operators.                                                         | Tier 1: Council / trusted; Tier 2: permissionless operators, service providers, app teams, and infra providers.    | Permissionless operators, service providers, and app teams.                                                                        |

## Role in the Hiero Network

Block Nodes act as the trusted historians and data providers for a Hiero network. They receive block streams from Consensus Nodes, verify that each block and its data are correct, apply the included `State Changes` to maintain a local copy of network state, and durably store both the blocks and the state snapshots they produce.
By doing so, Block Nodes replace old methods of storing network data in centralized cloud buckets and instead make this data reliably available in a decentralized way.

Block Nodes distribute blocks to downstream clients—including Mirror Nodes, other Block Nodes, and applications—so anyone can access real-time or historical data.
They also generate cryptographic proofs for transactions and state, making it possible for users and applications to independently verify the accuracy of the network's history without relying on a single provider.

Finally, Block Nodes help the network scale and run smoothly.
When a Consensus Node needs to catch up with its peers or recover from downtime, it can rely on Block Nodes to provide the latest data and state snapshots—partially relieving Consensus Nodes from heavy data distribution duties and improving overall efficiency.

## Core Block Node Services

Block Nodes provide several core services that turn block streams into reliable, consumable data for the network:

- [Block Stream](./glossary.md#block-stream) ingestion, verification, and distribution.
- Cryptographic proofs for transactions and network state.
- Durable storage of blocks, and consensus state.
- Real-time and historical data streaming to downstream clients.
- Random-access retrieval of blocks and state at specific block heights.
- State snapshot creation, plus reconnect services (not currently in active development) to help Consensus Nodes catch up or recover.

## Example Use Cases

- Running an API server for wallets, explorers, or dApps to access verified blockchain data.
- Operating analytics or aggregation pipelines using live and historical block streams.
- Providing specialized compliance, archival, or network recovery support for Hiero services.

## Block Node Tiers

A Block Node's tier describes where it gets its block stream from. The same core software runs at every tier; the differences are operational.

| **Tier** |                         **Description**                          |        **Typical Operators**        |               **Key Focus**                |
|----------|------------------------------------------------------------------|-------------------------------------|--------------------------------------------|
| Tier 1   | Receive streams directly from Consensus Nodes; high reliability. | Governing Council, trusted entities | Verification, reconnect, state management. |
| Tier 2   | Receive streams from Tier 1 or another Tier 2; permissionless.   | Community, enterprises              | Streaming, proofs, geographic redundancy.  |

Beyond tiers, operators can deploy a Block Node in different types — for example Full Node, Rolling-History, Light Node, Private-Cloud, Archive Server, or Community Node — by combining different sets of plugins. Rolling-History provides a Partial History service, retaining only recent blocks rather than full history. Archive Server provides cold storage without live streaming. See [Block-Node-Types.md](../Block-Node-Types.md) for the full taxonomy.

## High-Level Architecture

Block Nodes follow a modular design, receiving **block streams**, verifying integrity using **aggregated signatures** and cryptographic mechanisms defined in related HIPs ([HIP-1200](https://hips.hedera.com/hip/hip-1200) and [HIP-1056](https://hips.hedera.com/hip/hip-1056)), and persisting blocks and state to local disk and (optionally) S3-compatible archival storage.
They may provide four common functions:

- **Block Stream ingestion and verification** - Receives block streams from Consensus Nodes and verifies their integrity using aggregated signatures and Merkle proofs.
- **State management and snapshot generation** (planned) - Block Nodes will maintain an active local copy of network state by applying `State Changes` from the block stream and generate state snapshots served through dedicated APIs to support reconnect and recovery flows. State management is a prerequisite for reconnect support; neither is currently implemented.
- **Durable storage** - Persists blocks and saved states to local disk or to S3-compatible archival storage for long-term, tamper-evident history.
- **Data services** - Exposes gRPC/REST APIs providing real-time block streaming, random-access block retrieval, state queries at specific block heights, and cryptographic proofs to Mirror Nodes, other Block Nodes, and applications.

Block Nodes fan out block streams to Mirror Nodes and Tier 2 Block Nodes while also serving reconnect and state snapshot services back to Consensus Nodes, creating a scalable data availability layer between consensus and downstream consumers.

![block-node-network-architecture](../../docs/assets/block-node-network-architecture.svg)

### How Data Flows Through the Network

The diagram above illustrates the complete data flow:

1. **Consensus Nodes produce blocks** - Users submit transactions via gRPC to Consensus Nodes, which reach consensus through Hashgraph and produce finalized blocks with block proofs containing aggregated signatures.
2. **Block Nodes receive and verify** - Tier 1 Block Nodes receive block streams directly from Consensus Nodes, verify block integrity using aggregated signatures (as defined in [HIP-1200](https://hips.hedera.com/hip/hip-1200)), and store verified blocks and state to local disk and (optionally) S3-compatible archival storage.
3. **Block Nodes distribute downstream** - block streams fan out to:
   - **Mirror Nodes** - for public REST APIs and explorer services
   - **Tier 2 Block Nodes** - for geographic redundancy and permissionless participation
   - **Applications** - via gRPC/REST APIs for custom integrations
4. **Block Nodes support Consensus Node recovery** - When a Consensus Node experiences downtime or falls behind, it requests reconnect data (recent blocks and state snapshots) from Block Nodes to quickly resynchronize without burdening other Consensus Nodes.

## Key Responsibilities

Block Node operators are responsible for:

- Keeping their Block Node online, monitored, and synchronized with the latest network state.
- Managing storage and retention for blocks and state snapshots.
- Securing access to APIs and infrastructure, including authentication, authorization, and network boundaries.
- Applying software upgrades in line with Hedera/Hiero releases and Block Node compatibility guidance.
- Choosing and configuring which services to expose (streaming, random access, proofs, reconnect/state snapshots) for their consumers.

## Benefits for Operators

- Replaces legacy batch file downloads from centralized cloud buckets with continuous low-latency gRPC streaming.
- Lower costs via local-first storage instead of paid reads from centralized cloud buckets.
- Enhanced confidence via aggregated network signatures and cryptographic block proofs.
- Enhanced Mirror Node integration with random-access and proofs.
- Plugin extensibility without forking.

## Choosing Your Block Node Configuration

Use this decision guide to determine which Block Node configuration suits your needs:

### Tier 1 vs Tier 2

**Choose Tier 1 if:**

- You are a member of the Hiero network's governance structure or trusted network partner.
  - In the public Hiero network a governance member is an Hiero Governing Council member.
- You have authorization to peer directly with Consensus Nodes.
- You can commit to high-availability SLAs (99.9%+ uptime).
- You want to provide reconnect services to Consensus Nodes.
- You are able to operate a bare‑metal server that meets the recommended hardware specifications described in the [Block Node Hardware Specifications](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/block-node/operations/block-node-hardware-specifications.md).

**Choose Tier 2 if:**

- You are a community operator, enterprise, or infrastructure provider.
- You want to participate without special permissions (permissionless).
- You can receive block streams from existing Tier 1 or Tier 2 Block Nodes.
- Your focus is on streaming verified data to applications or Mirror Nodes.
- Your goals include developing and providing value-added services based on Block Stream data.

**Note:** Tier 2 nodes are truly permissionless—anyone can deploy one without approval or registration. You simply configure your node to connect to one or more existing Block Nodes (Tier 1 or Tier 2) for upstream block streams.

### Helm deployment profile

Once you know your tier, select the plugin profile that matches your storage strategy. Profiles are pre-built Helm values overrides shipped in the
[`charts/block-node-server/values-overrides/`](https://github.com/hiero-ledger/hiero-block-node/tree/main/charts/block-node-server/values-overrides)
directory and applied with the `-f` flag during Helm install, or selected interactively by Solo Provisioner.

|                      Goal                      |                                                                                                  Profile                                                                                                  |                 Storage strategy                  |                                                 Hardware sizing                                                 |
|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Tier 1 — full block history on local disk      | [`plugin-profile-lfh`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-lfh.yaml)                                                      | Local NVMe (recent) + local HDD (archive)         | [LFH spec](./operations/block-node-hardware-specifications.md#local-full-history-lfh)                           |
| Tier 1 — recent blocks local, history in cloud | [`plugin-profile-rfh`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-rfh.yaml)                                                      | Local NVMe (recent) + S3-compatible cloud archive | [RFH spec](./operations/block-node-hardware-specifications.md#remote-full-history-rfh)                          |
| Tier 1 — local history and cloud backup        | [`plugin-profile-all`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-all.yaml)                                                      | Local NVMe + local HDD + S3 archive               | [LFH spec](./operations/block-node-hardware-specifications.md#local-full-history-lfh)                           |
| Tier 2 — full history on local disk            | [`plugin-profile-lfh`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-lfh.yaml) with `stream-publisher` removed from `plugins.names` | Local NVMe (recent) + local HDD (archive)         | [LFH spec](./operations/block-node-hardware-specifications.md#local-full-history-lfh)                           |
| Tier 2 — recent blocks local, history in cloud | [`plugin-profile-rfh`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-rfh.yaml) with `stream-publisher` removed from `plugins.names` | Local NVMe (recent) + S3-compatible cloud archive | [RFH spec](./operations/block-node-hardware-specifications.md#remote-full-history-rfh)                          |
| Development, testing, or testnet / previewnet  | [`plugin-profile-minimal`](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values-overrides/plugin-profile-minimal.yaml)                                              | No block storage (health and status only)         | [Testnet / previewnet sizing](./operations/block-node-hardware-specifications.md#testnet-and-previewnet-sizing) |

> **Note:** For Tier 2, no dedicated profile file exists. Start from `plugin-profile-lfh` (local history) or `plugin-profile-rfh` (cloud archive) and remove `stream-publisher` from `plugins.names`. See [configuration.md](./configuration.md#plugin-management) for the full `plugins.names` reference.

For CPU, RAM, disk, and NIC requirements for each profile, see
[Block Node Hardware Specifications](./operations/block-node-hardware-specifications.md).

## Getting Started

**To start running a Block Node, read:**

- **Virtual Machine Single Node Kubernetes Deployment Guide** – [step‑by‑step instructions for deploying a single Block Node instance with the Solo Provisioner in a Kubernetes cluster, including environment preparation, deployment, and basic verification.](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/block-node/operations/solo-weaver-single-node-k8s-deployment.md)
- **Bare Metal Single Node Kubernetes Deployment** – [instructions for deploying the Block Node Server Helm chart in a single‑node Kubernetes environment, suitable for production setups on bare metal or cloud VMs.](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/block-node/operations/single-node-k8s-deployment.md)
- **Load Testing a Deployed Block Node Using Solo and NLG** – [realistic production-scale load testing using real Consensus Nodes and the Network Load Generator, to validate Block Node capacity before connecting to the live network.](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/block-node/operations/load-testing-a-deployed-block-node-using-solo-and-nlg.md)
