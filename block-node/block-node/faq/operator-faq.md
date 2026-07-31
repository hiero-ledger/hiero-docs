# Block Node Operator FAQ

Common operational questions for Block Node operators, with answers sourced directly
from the codebase, configuration files, and protocol documentation. Each answer links
to the canonical reference for full detail.

For term definitions, see the [Glossary](../glossary.md).

---

## Hardware and sizing

### What hardware specs do I need?

Requirements depend on your deployment tier and network:

|             Deployment              |                      CPU                      |  RAM   | Fast NVMe |          Bulk storage           |
|-------------------------------------|-----------------------------------------------|--------|-----------|---------------------------------|
| Tier 1 mainnet (Local Full History) | 24 cores / 48 threads, single-socket ≥2.0 GHz | 256 GB | 7.5 TB    | 100 TB HDD (500 TB recommended) |
| Tier 2 Remote Full History          | 16 cores / 32 threads                         | 128 GB | —         | 100 GB+                         |
| Testnet / previewnet                | 16 vCPU                                       | 32 GB  | —         | Sized to retention window       |

Network: minimum 2 × 10 Gbps NICs for Tier 1 mainnet.

> See [Block Node Hardware Specifications](../operations/block-node-hardware-specifications.md) for full details.

### What are the minimum NVMe IOPS requirements?

Fast NVMe storage must sustain (aggregate across all drives, random-access):

- **350,000** random write IOPS
- **900,000** random read IOPS
- **1,000,000** random read AIO IOPS
- P99 write latency < 300 µs; P99 read latency < 200 µs

> See [Block Node Hardware Specifications](../operations/block-node-hardware-specifications.md) for full details.

### Can I run a Block Node on a VM?

Yes. Solo Provisioner supports VM deployment on GCP, AWS, and Azure. For
testnet/previewnet, a GCP `e2-standard-16` (16 vCPU, 32 GB RAM) or equivalent is
sufficient. For mainnet Tier 1, bare-metal deployment is strongly recommended due to
NVMe IOPS requirements and the risk of noisy-neighbour effects on VMs.

> See [Deploy with Solo Provisioner](../operations/solo-weaver-single-node-k8s-deployment.md) for full details.

### How do I size the archive PVC relative to my bulk storage disk?

Set `blockNode.persistence.archive.size` to approximately **80% of your available bulk
disk capacity**. For example, with 100 TiB of HDD, set the archive PVC to around 80 TiB.

The reasoning:
- Leaving 20% headroom means when the PVC eventually fills, there is hardware space
immediately available to relieve pressure while more storage is provisioned or data
is migrated.
- Drive performance generally degrades slightly above 80% utilisation.

The 80% figure is a recommendation, not a hard requirement — operators may choose a
different value based on their own retention and capacity policies.

---

## Networking and security

### What ports does the Block Node use, and which need to be open?

|        Port         |                                   Purpose                                    |                    Direction                    |                                 Production exposure                                 |
|---------------------|------------------------------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------|
| **40840** (default) | gRPC — Publish, Subscribe, Status, Block Access APIs                         | Inbound from CNs, MNs, peer BNs; kubelet probes | Public (Tier 1: CNs + authorised subscribers; Tier 2: upstream BNs + subscribers)   |
| **16007**           | Prometheus / OpenMetrics scrape                                              | Inbound from monitoring                         | Internal cluster only                                                               |
| **5005**            | JVM remote debug (JDWP)                                                      | Inbound from debugger                           | **Must be denied in production** — enabling JDWP significantly degrades performance |
| Outbound (dynamic)  | Backfill plugin dials peer Block Node subscribe API (destination port 40840) | Outbound to peer BNs                            | Required if backfill plugin is enabled                                              |
| Outbound (dynamic)  | RSA Bootstrap Plugin fetches RSA address book from Mirror Node               | Outbound to Mirror Node                         | Required if roster-bootstrap-rsa plugin is enabled                                  |

The Block Node does not terminate TLS in-process; TLS is handled upstream by a Kubernetes
Ingress or load balancer.

> See [Network Ports and Protocols](../operations/network-ports-and-protocols.md) for full details.

### What are the expected inbound and outbound traffic flows?

**Inbound (port 40840):**
- Consensus Nodes push block streams (Tier 1 only — requires `stream-publisher` plugin)
- Mirror Nodes and downstream Block Nodes subscribe to block streams
- All clients call `serverStatus` to discover available block ranges
- Kubernetes kubelet issues HTTP GET liveness and readiness probes

**Inbound (port 16007):**
- Prometheus / monitoring scrapes the OpenMetrics endpoint

**Outbound (dynamically assigned port):**
- Backfill plugin dials peer Block Nodes to fetch missing historical blocks
- RSA Bootstrap Plugin dials a Mirror Node to fetch RSA address book data for WRB block proof verification

> See [Network Ports and Protocols](../operations/network-ports-and-protocols.md) for full details.

### What bandwidth should I plan for?

Estimates from the hardware specifications at 2,000 TPS sustained load:

|                 Traffic type                 |  Estimated bandwidth   |
|----------------------------------------------|------------------------|
| Ingress from Consensus Node                  | ~1.8 MB/s steady-state |
| Egress to 33 subscribers                     | ~60 MB/s steady-state  |
| Egress to 33 subscribers (4× catch-up burst) | ~67 MB/s               |

At 20,000 TPS the egress estimate reaches ~580 MB/s steady-state, requiring 10+ Gbps
NICs. These are well-informed estimates based on block-size modelling, confirmed by the
block node team as current targets. The 33-subscriber figure may change as the network
topology evolves — verify the expected subscriber count with your Hashgraph PoC before
finalising hardware procurement.

> See [Block Node Hardware Specifications](../operations/block-node-hardware-specifications.md) for full details.

### Does the Block Node support TLS or authentication on its endpoints?

**TLS:** The Block Node process does not terminate TLS in-process. TLS is terminated
upstream by a Kubernetes Ingress, load balancer, or service mesh. TLS support varies
by port:

|              Port               |                                                                                                   TLS at ingress                                                                                                   |
|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Publisher (CN → BN, 40840)**  | **Not currently supported.** The Consensus Node PBJ client disables TLS globally; enabling TLS upstream on this port will break CN streaming. Expected to become configurable in a future CN release (~0.78/0.79). |
| **Subscriber (MN → BN, 40840)** | Permitted if the operator desires it for privacy or compliance.                                                                                                                                                    |
| **Status (40840)**              | Not supported until a qualified CN release is available.                                                                                                                                                           |
| **Metrics (16007)**             | Internal only — do not expose publicly. TLS is not relevant.                                                                                                                                                       |

**Authentication:** There is no built-in authentication mechanism, and there are no
plans to add any. Security is enforced at the network and transport layer.

> See [Network Ports and Protocols](../operations/network-ports-and-protocols.md) and [Block Node On-Chain Registration](../block-node-on-chain-registration.md) for full details.

### How should I secure the Block Node if there is no built-in authentication?

The Block Node has no authentication and there are no plans to add any. This is by
design: **trust is in the data, not in the node.** Every block carries a
[Block Proof](../glossary.md#block-proof) that cryptographically verifies the block's
authenticity — subscribers verify the data themselves rather than trusting the node
delivering it.

**TLS** is advisory for subscriber-facing ports only. Do **not** enable TLS on the
Publisher port (CN → BN) until CN support is qualified (targeted ~0.78/0.79) — doing
so will break all Consensus Node ingest.

**Practical network controls:**
- Restrict port 16007 (metrics) to internal cluster access only — never expose it publicly.
- Deny port 5005 (JDWP) in all production firewall rules — enabling JDWP significantly
degrades performance.
- Use Kubernetes `NetworkPolicy` to limit which pods can reach port 40840 if your
deployment environment requires it.

---

## Deployment and configuration

### What is the difference between Tier 1 and Tier 2?

|                           |                Tier 1                |                         Tier 2                         |
|---------------------------|--------------------------------------|--------------------------------------------------------|
| Block stream source       | Directly from Consensus Nodes        | From an upstream Block Node (Tier 1 or another Tier 2) |
| Who runs it               | Governing Council / trusted entities | Community operators, enterprises — permissionless      |
| `stream-publisher` plugin | **Required**                         | **Must be removed** from `plugins.names`               |
| Hardware                  | Mainnet bare-metal specs             | Lower — sized to retention window                      |

> See [Block Node Types and Tiers](../../Block-Node-Types.md) and [Configuration Reference](../configuration.md) for full details.

### What is the difference between node types (Full Node, Rolling-History, Light Node, Archive Server)?

|        Type         |                     History retained                      |                          Typical use                          |
|---------------------|-----------------------------------------------------------|---------------------------------------------------------------|
| **Full Node**       | All history from genesis on local storage                 | Tier 1 mainnet — `plugin-profile-lfh` or `plugin-profile-all` |
| **Rolling-History** | Recent history only (configurable window, e.g. 7–90 days) | Tier 2 — low-cost redistribution                              |
| **Light Node**      | Minimal — health and status only                          | Development, testing, testnet                                 |
| **Archive Server**  | Cold storage — no live streaming                          | Offline archival                                              |

> See [Block Node Types and Tiers](../../Block-Node-Types.md) for full details.

### How do I deploy a Block Node?

Three paths are available:

1. **Solo Provisioner (recommended for mainnet Tier 1 and testnet/evaluation):** Single
   command handles Kubernetes setup and Helm installation. Solo Provisioner also automates
   networking and traffic shaping tasks based on dynamic values from the managed Block Node.
   See [Deploy with Solo Provisioner](../operations/solo-weaver-single-node-k8s-deployment.md).

2. **Direct Single Node Kubernetes (an option for operators with an existing cluster):** Manual
   Helm install on a pre-existing single-node cluster using the `task helm-release` Taskfile target.
   See [Direct Single Node Kubernetes Deployment](../operations/single-node-k8s-deployment.md).

3. **Existing Kubernetes cluster:** Apply the Block Node Helm chart directly with
   `-f charts/block-node-server/values-overrides/plugin-profile-lfh.yaml` (or your
   chosen profile) and any site-specific overrides.

### What plugin configuration do I need?

Select a pre-built Helm values override from
[`charts/block-node-server/values-overrides/`](https://github.com/hiero-ledger/hiero-block-node/tree/main/charts/block-node-server/values-overrides):

|         Profile          |                                                   Use                                                    |
|--------------------------|----------------------------------------------------------------------------------------------------------|
| `plugin-profile-lfh`     | Tier 1 — full history on local NVMe + HDD                                                                |
| `plugin-profile-rfh`     | Remote archival — cloud storage backend                                                                  |
| `plugin-profile-all`     | Full history local + cloud backup *(testing only — plugins may conflict and produce unexpected results)* |
| `plugin-profile-minimal` | Development / testnet — health and status only                                                           |

For **Tier 2**, start from `plugin-profile-lfh` and remove `stream-publisher` from
`plugins.names`. The presence of `stream-publisher` is the key difference between
Tier 1 and Tier 2.

> See [Configuration Reference](../configuration.md) for full details.

### How do I configure the API ports?

**Single port (default):** All APIs share port 40840. Override with the `SERVER_PORT`
environment variable.

**Per-service ports:** Set individual ports via the `blockNode.ports` Helm values section:

```yaml
blockNode:
  ports:
    publisher: 40840     # PRODUCER_PORT
    subscriber: 40841    # SUBSCRIBER_PORT
    blockAccess: 40842   # BLOCK_ACCESS_PORT
    health: 40843        # HEALTH_PORT
    serverStatus: 40844  # SERVER_STATUS_PORT
```

When set in Helm, do not also set these in `blockNode.config` — they are injected
automatically as environment variables.

> See [Configuration Reference](../configuration.md) for full details.

### How do I configure traffic control and message size limits?

Key environment variables:

|                 Variable                  |       Default        |           Purpose            |
|-------------------------------------------|----------------------|------------------------------|
| `SERVER_MAX_MESSAGE_SIZE_BYTES`           | 131,072,000 (125 MB) | Max HTTP/2 message size      |
| `SERVER_SOCKET_SEND_BUFFER_SIZE_BYTES`    | 131,072              | TCP send buffer              |
| `SERVER_SOCKET_RECEIVE_BUFFER_SIZE_BYTES` | 8,388,608            | TCP receive buffer           |
| `SERVER_MAX_TCP_CONNECTIONS`              | 1,000                | Max simultaneous connections |
| `BACKFILL_MAX_INCOMING_BUFFER_SIZE`       | 104,857,600 (100 MB) | Backfill gRPC receive buffer |

> See [Configuration Reference](../configuration.md) for full details.

---

## Health and monitoring

### What telemetry and metrics does the Block Node emit?

The Block Node exposes Prometheus-compatible metrics on port 16007 (`/metrics`), using
the `blocknode_` prefix. Metric categories include:

|        Category        |           Prefix examples           |                  What it covers                   |
|------------------------|-------------------------------------|---------------------------------------------------|
| Application state      | `blocknode_app_state_status`        | Node lifecycle (starting / running / stopping)    |
| Publisher (CN → BN)    | `blocknode_publisher_*`             | Connections, latency, stream errors, open streams |
| Subscriber (MN → BN)   | `blocknode_subscriber_*`            | Open subscriptions, errors                        |
| Verification           | `blocknode_verification_*`          | Blocks verified, failed, error counts             |
| Persistence (recent)   | `blocknode_files_recent_*`          | Write latency, blocks stored                      |
| Persistence (historic) | `blocknode_files_historic_*`        | Archive metrics                                   |
| Backfill               | `blocknode_backfill_*`              | Fetch errors, blocks backfilled                   |
| Messaging              | `blocknode_messaging_*`             | Internal queue utilisation                        |
| Cloud storage archive  | `blocknode_cloud_storage_archive_*` | Upload success/failure, bytes stored              |
| Cloud storage expanded | `blocknode_cloud_expanded_*`        | Per-block upload metrics                          |

> See [Metrics and Monitoring](../metrics.md) for the complete metric catalogue with
> descriptions and types.

### How do I check if my Block Node is healthy?

Three methods:

1. **HTTP health probes:**

   ```
   GET http://<host>:40840/healthz/livez   → 200 OK (running) / 503 (not running)
   GET http://<host>:40840/healthz/readyz  → 200 OK (ready)  / 503 (not ready)
   ```
2. **Block range via gRPC:**

   ```bash
   grpcurl -plaintext -d '{}' <host>:40840 \
     org.hiero.block.api.BlockNodeService/serverStatus
   ```

   Check that `lastAvailableBlock` is advancing.

3. **Metrics:**

   ```bash
   curl http://<host>:16007/metrics | grep blocknode_app_state_status
   ```

   Value 1 = Running; 0 = Starting; 2 = Shutting Down.

> See [Network Ports and Protocols](../operations/network-ports-and-protocols.md) and [Metrics and Monitoring](../metrics.md) for full details.

### What are the liveness and readiness probe URLs?

|   Probe   |             Default URL              | HTTP method |
|-----------|--------------------------------------|-------------|
| Liveness  | `http://<host>:40840/healthz/livez`  | GET         |
| Readiness | `http://<host>:40840/healthz/readyz` | GET         |

Both paths are configurable via `blockNode.health.liveness.endpoint` and
`blockNode.health.readiness.endpoint` in Helm values.

> See [Configuration Reference](../configuration.md) for full details.

### What metrics should I alert on?

**Medium severity — page on call:**

|                        Metric                        |     Threshold     |
|------------------------------------------------------|-------------------|
| `blocknode_app_state_status`                         | ≠ 1 (RUNNING)     |
| `blocknode_publisher_receive_latency_ns`             | > 10 seconds      |
| `blocknode_verification_blocks_error`                | > 3 in 60 s       |
| `blocknode_publisher_block_send_response_failed`     | > 5 in 60 s       |
| `blocknode_publisher_stream_errors`                  | > 5 in 60 s       |
| `blocknode_files_recent_persistence_time_latency_ns` | > 20 milliseconds |

**Low severity — investigate next business day:**

|                    Metric                     |  Threshold  |
|-----------------------------------------------|-------------|
| `blocknode_publisher_open_connections`        | > 40        |
| `blocknode_messaging_item_queue_percent_used` | > 60%       |
| `blocknode_backfill_fetch_errors`             | > 3 in 60 s |

> See [Metrics and Monitoring](../metrics.md) for full details.

### What log level should I run in production?

Set `org.hiero.block.level = INFO` in production. Use `FINE` only when actively
debugging — it generates significant volume.

> **Note:** The current chart default in `values.yaml` is `FINE` with a comment
> "temporarily while testing is ongoing." Override this to `INFO` in your production
> Helm values.

Also: **do not schedule maintenance tasks (log rotation, cron jobs, tmpwatch) at UTC
midnight.** Block Node I/O load peaks at midnight when network processing is highest.

> See [Configuration Reference](../configuration.md) for full details.

---

## Connectivity

### How do I connect a Mirror Node to my Block Node?

Configure the Mirror Node importer in `application.yml`:

```yaml
hiero:
  mirror:
    importer:
      block:
        enabled: true
        nodes:
          - host: <block-node-host>
            port: 40840
            priority: 0
```

Then restart the Mirror Node importer. Verify by checking that `lastAvailableBlock`
advances in the Block Node `serverStatus` response and that the Mirror Node logs show
subscribe activity.

> See [Connecting a Mirror Node to a Block Node](../operations/connecting-a-mirror-node-to-a-block-node.md) for full details.

### Does the Block Node reconnect to the Consensus Node automatically?

**No.** The Block Node is the server — it does not initiate connections. The Consensus
Node is the client and opens the publish stream to the Block Node. If the stream closes
(for any reason), the Block Node sends an `EndOfStream` response and waits passively.
The Consensus Node is responsible for reconnecting.

### Does the Consensus Node reconnect to the Block Node automatically?

**Yes.** The Consensus Node has built-in reconnection logic. Key configuration
properties in `application.properties`:

|                   Property                   | Default |                    Purpose                    |
|----------------------------------------------|---------|-----------------------------------------------|
| `blockNode.streamResetPeriod`                | 24 h    | Proactive periodic connection reset           |
| `blockNode.highLatencyThreshold`             | 30 s    | Latency threshold before considering a switch |
| `blockNode.highLatencyEventsBeforeSwitching` | 5       | Events before switching to next BN            |
| `blockNode.globalCoolDownSeconds`            | 10 s    | Minimum time between BN switches              |

> See [Configure Consensus Node Streaming](../operations/consensus-node-to-block-node-configuration.md) for full details.

---

## Upgrades and resets

### How do I upgrade my Block Node with minimal downtime?

**Via Solo Provisioner:**

```bash
sudo solo-provisioner block node upgrade
```

This preserves block data, updates the Helm chart, and restarts the pod. Downtime is
the pod restart window only (typically < 60 s). Subscribers reconnect automatically
once the pod passes readiness.

**Via Taskfile (manual):**
1. Update the `VERSION` in your Helm override or `.env` file.
2. Run `task helm-upgrade`.

Both methods preserve local block storage (PVCs are not deleted). If the upgrade
requires a block store reset (e.g. format change), add `--with-reset` for Solo
Provisioner or run `task reset-upgrade`.

> See [Resetting and Upgrading the Block Node](../operations/resetting-and-upgrading-the-block-node.md) for full details.

### How do I reset the Block Node state?

> ⚠️ **Destructive operation.** A reset permanently deletes all locally stored block data.
> After a reset, the node must backfill from a peer Block Node — on mainnet this can
> take days or weeks. Back up PVC contents before proceeding.

**Reset only (same version):**

```bash
task reset-file-store
```

**Reset + upgrade:**

```bash
task reset-upgrade
```

After a reset, `serverStatus` returns `firstAvailableBlock = lastAvailableBlock = uint64_max`
(empty node). Configure backfill sources and enable greedy backfill to recover history.

> See [Resetting and Upgrading the Block Node](../operations/resetting-and-upgrading-the-block-node.md) for full details.

### How do I enable or disable plugins after deployment?

Edit `plugins.names` in your Helm values (comma-separated plugin identifiers), then run
`helm upgrade` or `task helm-upgrade`:

```yaml
blockNode:
  config:
    # Example: Tier 2 — stream-publisher removed
    PLUGINS_NAMES: "backfill,block-access-service,blocks-file-recent,blocks-file-historic,facility-messaging,health,roster-bootstrap-rsa,roster-bootstrap-tss,server-status,stream-subscriber,block-verification"
```

**Note:** Removing a plugin name skips loading on the next pod start but does not
delete the JAR from the plugins volume. Adding a name causes the init container to
download and load it on the next start.

> See [Configuration Reference](../configuration.md) for full details.

---

## Backfill

### What is backfill and when does it run?

[Backfill](../glossary.md#backfill) is the automatic process of fetching missing
historical blocks from peer Block Nodes. The backfill plugin runs continuously,
scanning every `BACKFILL_SCAN_INTERVAL` (default 60 s) for gaps in local block storage
and fetching from configured peer sources.

Backfill triggers in two scenarios:
1. **Startup gaps** — blocks missing from storage when the pod starts.
2. **Live-tail gaps** — gaps detected during normal operation (e.g. after a reset or
network interruption).

### How do I tune backfill retry behavior?

|             Variable             |     Default     |                                       Purpose                                        |
|----------------------------------|-----------------|--------------------------------------------------------------------------------------|
| `BACKFILL_MAX_RETRIES`           | 3               | Max retries per fetch attempt                                                        |
| `BACKFILL_MAX_BACKOFF_MS`        | 300,000 (5 min) | Max backoff between retries                                                          |
| `BACKFILL_FETCH_BATCH_SIZE`      | 10              | Blocks fetched per gRPC call                                                         |
| `BACKFILL_DELAY_BETWEEN_BATCHES` | 1,000 ms        | Delay between successive batch requests                                              |
| `BACKFILL_GREEDY`                | false           | Set `true` to continuously fetch without delay (recommended during initial backfill) |

> See [Configuration Reference](../configuration.md) for full details.

### How do I configure or change what Block Nodes are used as backfill sources?

Set `BACKFILL_BLOCK_NODE_SOURCES_PATH` to the path of a JSON file listing peer Block
Nodes:

```json
{
  "nodes": [
    {
      "address": "tier1-bn.example.com",
      "port": 40840,
      "priority": 0,
      "name": "Primary archive"
    }
  ]
}
```

All Tier 1 Block Nodes should have at least one source configured. More than one is
recommended for redundancy — the backfill plugin selects by earliest available block,
then priority, then health score.

> See [Preparing for WRB Cutover](../operations/preparing-your-block-node-for-wrb-cutover.md) for full details.

---

## Costs and economics

### Who pays for ingress and egress costs?

**Operators pay infrastructure costs directly** — their cloud provider credit card is on
file and billed for all ingress, egress, and compute.

**Hedera provides daily rewards** intended to offset operational expenses including
bandwidth and hardware costs. These rewards are not guaranteed to cover all costs —
if the reward amount does not cover actual spend, the operator absorbs the difference.

**Co-location is strongly recommended.** Placing a Block Node in the same data center
or cloud region as the Consensus Node it streams from significantly reduces cross-region
egress costs. This is one of the reasons the team advises operators to co-locate.

---

## Kubernetes resources

### What Kubernetes resources does the Helm chart create?

The `block-node-server` Helm chart creates the following resources in the target namespace.
Resource names are based on the Helm release name (default: `block-node-server`):

|               Kind               |                          Purpose                          |
|----------------------------------|-----------------------------------------------------------|
| `StatefulSet`                    | Runs the Block Node pod with stable network identity      |
| `Service` (ClusterIP)            | Internal cluster endpoint on port 40840                   |
| `Service` (LoadBalancer)         | External endpoint (if `service.type: LoadBalancer`)       |
| `ServiceAccount`                 | Pod identity for RBAC                                     |
| `ConfigMap` (config)             | Environment variables injected into the pod               |
| `ConfigMap` (logging)            | Java logging configuration                                |
| `ConfigMap` (sources)            | `block-node-sources.json` for backfill and roster queries |
| `Secret`                         | Credentials (e.g. S3 keys)                                |
| `ServiceMonitor`                 | Prometheus scrape configuration (port 16007)              |
| `Ingress`                        | TLS termination (if `ingress.enabled: true`)              |
| `ConfigMap` (Grafana dashboard)  | Pre-built Grafana dashboard (if monitoring enabled)       |
| `ConfigMap` (Grafana datasource) | Grafana datasource pointing at the metrics endpoint       |

> See `charts/block-node-server/templates/` in the repository for the full template set.

---

## Protocols and tooling

### Where are the protocol buffers defined?

The Block Node public API protos live in `protobuf-sources/src/main/proto/block-node/api/`:

|               Proto file               |                                           Services / messages defined                                           |
|----------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| `block_stream_publish_service.proto`   | `BlockStreamPublishService.publishBlockStream` — CN → BN ingestion                                              |
| `block_stream_subscribe_service.proto` | `BlockStreamSubscribeService.subscribeBlockStream` — MN / Tier 2 consumption                                    |
| `block_access_service.proto`           | `BlockAccessService.getBlock` — random-access single-block retrieval                                            |
| `node_service.proto`                   | `BlockNodeService.serverStatus` / `serverStatusDetail` — metadata and health                                    |
| `state_service.proto`                  | `StateService.stateSnapshot` — *(defined; not yet implemented)*                                                 |
| `proof_service.proto`                  | `ProofService` — block content and state proofs *(not yet implemented)*                                         |
| `reconnect_service.proto`              | `ReconnectService.reconnect()` — provides state + block data to lagging Consensus Nodes *(not yet implemented)* |
| `network-data.proto`                   | Shared network endpoint message types: `NetworkData`, `NetworkConnection`                                       |
| `shared_message_types.proto`           | Shared message types: `BlockItemSet`, `BlockProof`, `EndOfStream`, etc.                                         |

The BN API protos are defined locally in `protobuf-sources/src/main/proto/block-node/api/`
within this repository. The Consensus Node protos (pulled for combined artifact generation)
originate from the
[hiero-ledger/hiero-consensus-node](https://github.com/hiero-ledger/hiero-consensus-node)
repository and are fetched by `protobuf-sources/scripts/build-bn-proto.sh`.

The Block Node also publishes a release artifact for every release containing the full set of `.proto` files supported by that release. See the [releases page](https://github.com/hiero-ledger/hiero-block-node/releases) for downloads.

### What tooling and scripts are provided in the repository?

The `tools-and-tests/` directory contains:

|                  Tool                  |                  Location                  |                                                                                                    Purpose                                                                                                    |
|----------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **`bn-endpoint-checker.sh`**           | `tools-and-tests/scripts/node-operations/` | Health checker: verifies TCP reachability, calls `serverStatus` and `serverStatusDetail`, and optionally fetches the latest block proof type. Primary operator health-check script.                           |
| **`Taskfile.yml`** (operations)        | `tools-and-tests/scripts/node-operations/` | Taskfile targets: `helm-upgrade`, `reset-file-store`, `reset-upgrade`, `helm-release`. Used for lifecycle management of deployed nodes.                                                                       |
| **`generate-rsa-roster-bootstrap.sh`** | `tools-and-tests/scripts/node-operations/` | Generates an RSA roster bootstrap JSON file for WRB cutover preparation.                                                                                                                                      |
| **`run-k6-tests.sh`**                  | `tools-and-tests/k6/`                      | Runs k6 load tests against a deployed Block Node.                                                                                                                                                             |
| **Block Stream Simulator**             | `tools-and-tests/simulator/`               | Publishes synthetic block streams to a Block Node without a real Consensus Node. Used for local testing. See [Testing with the Simulator](../operations/testing-a-deployed-block-node-using-the-simulator.md). |

### Which plugins provide which features?

Each plugin is identified by its `plugins.names` key (used in Helm configuration):

|       Plugin name        |                                       Feature provided                                       |             Tier required             |
|--------------------------|----------------------------------------------------------------------------------------------|---------------------------------------|
| `stream-publisher`       | Accepts block streams from Consensus Nodes (`publishBlockStream` RPC)                        | Tier 1 only — **remove for Tier 2**   |
| `stream-subscriber`      | Serves block streams to Mirror Nodes and downstream Block Nodes (`subscribeBlockStream` RPC) | Tier 1 and Tier 2                     |
| `block-access-service`   | Single-block random-access retrieval (`getBlock` RPC)                                        | Tier 1 and Tier 2                     |
| `server-status`          | `serverStatus` and `serverStatusDetail` RPCs — block range, version, plugin list             | All deployments                       |
| `health`                 | Kubernetes liveness (`/healthz/livez`) and readiness (`/healthz/readyz`) probes              | All deployments                       |
| `block-verification`     | Verifies block proofs before persistence (TSS and RSA/WRB)                                   | All deployments                       |
| `blocks-file-recent`     | Short-term block persistence on local NVMe with configurable retention policy                | LFH and RFH profiles                  |
| `blocks-file-historic`   | Long-term block persistence on local HDD (archive tier)                                      | LFH profile                           |
| `cloud-storage-archive`  | Archives blocks to S3-compatible cloud storage (group files)                                 | RFH and cloud-backup profiles         |
| `cloud-storage-expanded` | Uploads each verified block individually to S3-compatible storage                            | Optional                              |
| `backfill`               | Fetches missing historical blocks from peer Block Nodes                                      | All production deployments            |
| `roster-bootstrap-rsa`   | Loads the RSA node address book at startup for WRB block proof verification                  | Required for WRB cutover              |
| `roster-bootstrap-tss`   | Loads TSS roster data for post-cutover block proof verification                              | Required post-cutover                 |
| `facility-messaging`     | Internal LMAX Disruptor event bus — distributes block items to all plugins                   | All deployments (core infrastructure) |

> See [Configuration Reference](../configuration.md) for `plugins.names` syntax and profile examples.

### Is a fully-qualified domain name (FQDN) required?

No FQDN is strictly required, but you need either a resolvable hostname or an IP address in two places:

**For `block-nodes.json` (CN → BN wiring):**
The `address` field accepts any hostname or IP that is DNS-resolvable from the Consensus
Node's host. If DNS resolution is unreliable in your environment, use an IP address
directly.

**For on-chain registration (HIP-1137):**
Each `service_endpoint` requires either:
- `domain_name` — an FQDN of up to 250 ASCII characters, OR
- `ip_address` — an IPv4 or IPv6 address in big-endian byte order.

The two are mutually exclusive per endpoint. For production deployments a stable hostname
is recommended so that IP address changes do not require a registration update.

> See [Configure Consensus Node Streaming](../operations/consensus-node-to-block-node-configuration.md)
> and [Block Node On-Chain Registration](../block-node-on-chain-registration.md) for full details.

---
