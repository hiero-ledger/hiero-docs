# Network Ports and Protocols

This document defines the network ports, traffic directions, and TLS posture of a Block Node, so an operator can configure firewalls, security groups, and Kubernetes `NetworkPolicy` correctly before deploying.

## Key terms

**Initiator**

The component that opens the TCP connection. For every gRPC flow in this document the initiator is the gRPC client; the Block Node accepts the connection on a listening port.

**Direction**

Relative to the Block Node. Inbound traffic terminates on a Block Node port; outbound traffic originates from the Block Node and terminates on a port elsewhere.

**TLS-in-process**

Whether the Block Node binary itself terminates TLS. The Block Node does not terminate TLS in-process for any port; TLS is terminated upstream by a Kubernetes Ingress, load balancer, or similar.

**Production exposure**

Whether the port is intended to be reachable from outside the Kubernetes cluster in a production deployment. Internal-cluster ports are still subject to `NetworkPolicy` within the cluster.

---

## Port summary

The ports listed in this table are **defaults**. All ports are configurable, and the API-to-port mapping may vary by Block Node deployment. As of Block Node 0.36, each gRPC API uses a dedicated port. In a production Kubernetes deployment using the Helm chart, the MetalLB LoadBalancer provides a unified external entry point at port `40840`; intra-cluster traffic uses the per-service ports.

|              API / Function              | Default port |     Protocol     | Direction (vs BN) |               Initiator                | TLS in-process |           TLS upstream typical           |            Production exposure            |
|------------------------------------------|--------------|------------------|-------------------|----------------------------------------|----------------|------------------------------------------|-------------------------------------------|
| Publish API                              | `40984`      | gRPC over HTTP/2 | Inbound           | Consensus Node                         | No (h2c)       | Yes, at Ingress / LB                     | External (LoadBalancer / Ingress)         |
| Subscribe API                            | `40980`      | gRPC over HTTP/2 | Inbound           | Mirror Node / peer Block Node          | No (h2c)       | Yes, at Ingress / LB                     | External (LoadBalancer / Ingress)         |
| Status API                               | `40982`      | gRPC over HTTP/2 | Inbound           | Any Block Node client                  | No (h2c)       | Yes, at Ingress / LB                     | External (LoadBalancer / Ingress)         |
| Health and readiness probes              | `40983`      | HTTP/1.1 GET     | Inbound           | Kubelet                                | No (h2c)       | n/a (intra-cluster)                      | Internal (ClusterIP only)                 |
| Block Access API                         | `40981`      | gRPC over HTTP/2 | Inbound           | Authorized clients                     | No (h2c)       | Yes, at Ingress / LB                     | External (LoadBalancer / Ingress)         |
| MetalLB LoadBalancer (front-end)         | `40840`      | TCP              | Inbound           | External clients                       | No             | Yes, at Ingress / LB                     | External (Kubernetes LoadBalancer)        |
| Prometheus metrics                       | `16007`      | HTTP             | Inbound           | Prometheus / monitoring                | No             | Typically internal only                  | Internal (ClusterIP / NodePort)           |
| JVM remote debug (dev/test)              | `5005`       | TCP / JDWP       | Inbound           | Debugger                               | n/a            | n/a                                      | **Dev / test only - never in production** |
| Backfill (peer Block Node Subscribe API) | `40980`      | gRPC over HTTP/2 | **Outbound**      | This Block Node (when backfill loaded) | No (h2c)       | Optional, gated by `BACKFILL_ENABLE_TLS` | External (peer Block Node)                |

---

## Common confusions

Operators familiar with the Hedera consensus network may reach for the wrong port number. The two networks use different defaults.

|          Component          |                                        Default gRPC port(s)                                        |
|-----------------------------|----------------------------------------------------------------------------------------------------|
| **Block Node (LFH)**        | `40984` (Publish), `40980` (Subscribe), `40982` (Status), `40983` (Health), `40981` (Block Access) |
| **Block Node (base chart)** | `40840` (all services share `service.port` when `blockNode.ports.*` are unset)                     |
| **Hedera Consensus Node**   | `50211`                                                                                            |

`50211` is the Hedera Consensus Node's public gRPC port and is unrelated to the Block Node. In a Tier 1 LFH deployment, Mirror Nodes subscribe to the Block Node on port `40980`, not `50211`. See the [Port summary](#port-summary) for the full per-service breakdown.

---

## Port reference

### gRPC Block Stream APIs

The Block Node exposes gRPC APIs that form its primary network surface. In the LFH production profile each API runs on its own dedicated port; the API name is the stable identifier — port numbers are configurable defaults.

- **Publish API** — Consensus Nodes stream finalized blocks into the Block Node via `BlockStreamPublishService`. Initiator: Consensus Node.
- **Subscribe API** — Mirror Nodes and downstream Block Nodes consume the block stream via `BlockStreamSubscribeService.subscribeBlockStream`. Initiator: subscriber.
- **Status API** — clients query block-range availability, available services, and response latency via `BlockNodeService.serverStatus`. Initiator: any Block Node client.

Health probes run on their own dedicated HTTP port (see [Health and readiness probes](#health-and-readiness-probes)).

|      API      | LFH default port | Helm value (`blockNode.ports.*`) |       Env var        | Allowed range  |        Protocol        | Direction |
|---------------|------------------|----------------------------------|----------------------|----------------|------------------------|-----------|
| Publish API   | `40984`          | `publisher`                      | `PRODUCER_PORT`      | `1024`–`65535` | gRPC over HTTP/2 (h2c) | Inbound   |
| Subscribe API | `40980`          | `subscriber`                     | `SUBSCRIBER_PORT`    | `1024`–`65535` | gRPC over HTTP/2 (h2c) | Inbound   |
| Status API    | `40982`          | `serverStatus`                   | `SERVER_STATUS_PORT` | `1024`–`65535` | gRPC over HTTP/2 (h2c) | Inbound   |
| Block Access  | `40981`          | `blockAccess`                    | `BLOCK_ACCESS_PORT`  | `1024`–`65535` | gRPC/HTTP over HTTP/2  | Inbound   |

> **Base chart defaults.** When `blockNode.ports.*` entries are `null` (base `values.yaml` defaults for `publisher`, `subscriber`, `blockAccess`, `serverStatus`), all services share `service.port` (default `40840`). The LFH profile (`lfh-values.yaml`) sets all per-service ports explicitly. Non-LFH deployments (development, testing) typically use the shared `40840` port.

#### Notes

- The Block Node forwards block items to subscribers as they arrive from the publisher, without first verifying the block. Verification is self-contained at the consumer via the [Block Proof](../glossary.md#block-proof) carried with each block; see [HIP-1056](https://hips.hedera.com/hip/hip-1056).
- The connection is closed by the server when the requested block range is fully served, or on internal error. A stream-maximum-duration close condition is defined but is not currently enforced; firewalls should accommodate long-lived streams.
- For the operator-facing companion view of who connects from the Mirror Node side, see [Connecting a Mirror Node to a Block Node](./connecting-a-mirror-node-to-a-block-node.md).

### 16007 - Prometheus metrics

The Block Node exposes OpenMetrics-format counters and gauges via Helidon's metrics HTTP server. Scraped by Prometheus or a compatible collector.

|     Field      |                                         Value                                          |
|----------------|----------------------------------------------------------------------------------------|
| Default port   | `16007`                                                                                |
| Protocol       | HTTP (cleartext)                                                                       |
| Direction      | Inbound                                                                                |
| Initiator      | Prometheus / monitoring collector                                                      |
| TLS in-process | No                                                                                     |
| Path           | `/metrics` (default `metrics.exporter.openmetrics.http.path`)                          |
| Helm value     | `blockNode.metrics.port` (also `blockNode.metrics.hostname`, `blockNode.metrics.path`) |
| JVM property   | `metrics.exporter.openmetrics.http.port`                                               |

#### Notes

- The metrics endpoint is typically reachable only from within the cluster. Most deployments scrape it via a sidecar or a `ServiceMonitor`; exposing it externally is rarely needed and increases attack surface.
- The Prometheus convention of suffixing counter names with `_total` is applied at scrape time; the underlying metric name in the Block Node is registered without the suffix.
- Port `16007` is dedicated exclusively to metrics and is independent of all Block Node gRPC services and web servers. It does not share a port with any other service in any deployment profile — including base-chart deployments where all gRPC services share `service.port`.

### 5005 - JVM remote debug (dev/test only)

JDWP for attaching a Java debugger. **Must not be enabled in production.**

|       Field        |                                                                                                       Value                                                                                                        |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Default port       | `5005`                                                                                                                                                                                                             |
| Protocol           | JDWP over TCP                                                                                                                                                                                                      |
| Direction          | Inbound                                                                                                                                                                                                            |
| Initiator          | Debugger                                                                                                                                                                                                           |
| TLS in-process     | n/a                                                                                                                                                                                                                |
| Enabled where      | `block-node/app/docker/docker-compose.yml` (debug profile only)                                                                                                                                                    |
| Enabled how        | `JAVA_TOOL_OPTIONS=… -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=*:5005`                                                                                                                         |
| Helm chart default | **Not enabled.** The chart's `JAVA_TOOL_OPTIONS` helper injects only logging and metrics flags; `-agentlib:jdwp` is never added by chart defaults. Verified by rendering `helm template charts/block-node-server`. |

#### Notes

- The docker-compose debug profile binds JDWP on `*:5005` - every network interface. This is acceptable on a single-developer host but unacceptable anywhere reachable from a network the operator does not control.
- The Helm chart does not inject the `-agentlib:jdwp` argument. An operator who enables JDWP in a chart-managed deployment must also constrain the pod's network exposure with a `NetworkPolicy`; there is no in-process authentication on JDWP.
- Enabling JDWP significantly reduces the performance of the software. Do not leave it enabled outside of debug sessions.

### Backfill egress - peer Block Node connection

When the `backfill` plugin is enabled, the Block Node acts as a gRPC client to a peer Block Node to pull historical blocks. This is the only flow in this document where the Block Node is the initiator.

|     Field     |                                                                              Value                                                                              |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Target port   | Subscribe API port on each peer Block Node (`40980` in LFH profile; `40840` in base-chart default)                                                              |
| Protocol      | gRPC over HTTP/2; subscribes via `BlockStreamSubscribeService.subscribeBlockStream`                                                                             |
| Direction     | **Outbound** from this Block Node                                                                                                                               |
| Initiator     | This Block Node                                                                                                                                                 |
| TLS           | Off by default (`BACKFILL_ENABLE_TLS=false`); see [TLS requirements](#tls-requirements)                                                                         |
| Sources list  | `BACKFILL_BLOCK_NODE_SOURCES_PATH` points to a JSON file on disk; default empty (`""`)                                                                          |
| Plugin loaded | The `backfill` plugin is loaded by chart defaults. Egress occurs only if `BACKFILL_BLOCK_NODE_SOURCES_PATH` is set to a non-empty file listing peer Block Nodes |

#### Notes

- A Block Node with no `BACKFILL_BLOCK_NODE_SOURCES_PATH` file mounted makes no outbound gRPC connections of this kind. The plugin, if present, loads but stays idle.
- The list of peer Block Nodes is operator-supplied via a JSON file mounted into the pod. Firewalls and security groups must permit egress to every listed peer's Subscribe API port (`40980` in LFH; `40840` in base-chart default).

### Health and readiness probes

Kubernetes probes query the Block Node's dedicated health HTTP server with HTTP/1.1 GET requests. This service always runs on a separate dedicated port.

|     Field      |                               Value                                |
|----------------|--------------------------------------------------------------------|
| Port           | `40983`                                                            |
| Helm value     | `blockNode.ports.health`                                           |
| Env var        | `HEALTH_PORT`                                                      |
| Liveness path  | `/healthz/livez` (default; `blockNode.health.liveness.endpoint`)   |
| Readiness path | `/healthz/readyz` (default; `blockNode.health.readiness.endpoint`) |
| Protocol       | HTTP/1.1 GET                                                       |
| Direction      | Inbound                                                            |
| Initiator      | Kubelet                                                            |

#### Notes

- Probe traffic is intra-cluster only - kubelet to pod IP. Cluster-external firewalls do not need a rule for it.
- A `NetworkPolicy` that restricts ingress to specific ports must explicitly allow the kubelet to reach the health port (`40983` default), or the probes will fail and Kubernetes will restart the pod.

---

## Traffic flows by node tier

The `tier` of a Block Node describes where its block stream originates. [Tier 1](../glossary.md#tier-1-block-node) nodes receive data via the Publish API port and [Tier 2](../glossary.md#tier-2-block-node) nodes request data via the Subscribe API port. The operator-visible difference is which API is used. For full tier and type taxonomy, see [Block Node Types](../../Block-Node-Types.md).

Block Node tiers and types are visualised in the network architecture diagram at [block-node-network-architecture.svg](../../assets/block-node-network-architecture.svg).

> **Port values in the tables below** are for the LFH production profile. Base-chart default is `40840` for all gRPC services and `40983` for health probes when `blockNode.ports.*` entries are unset.

### Tier 1 Block Node

Receives the block stream **directly from Consensus Nodes**. The Consensus Node is the gRPC client; the Block Node accepts on the Publish API port (`40984` in LFH). A Tier 1 deployment is the connection point between the consensus network and downstream block-stream consumers.

|                 Flow                  | Direction |       Port        |                                                     Notes                                                      |
|---------------------------------------|-----------|-------------------|----------------------------------------------------------------------------------------------------------------|
| Publish (Consensus Node → Block Node) | Inbound   | `40984` gRPC      | One stream per active Consensus Node publisher                                                                 |
| Subscribe (Mirror / Tier 2 → BN)      | Inbound   | `40980` gRPC      | Multiple long-lived subscribers                                                                                |
| Status (clients → BN)                 | Inbound   | `40982` gRPC      | Publishers and subscribers query the Status API for available blocks, available services, and response latency |
| Block Access (clients → BN)           | Inbound   | `40981` gRPC/HTTP | Block retrieval API                                                                                            |
| Metrics scrape                        | Inbound   | `16007` HTTP      | Intra-cluster                                                                                                  |
| Probes                                | Inbound   | `40983` HTTP/1.1  | Intra-cluster from kubelet                                                                                     |
| Backfill (optional)                   | Outbound  | `40980` to peer   | Only if `backfill` plugin present and enabled; targets the peer's Subscribe API port                           |

A Tier 1 Block Node typically declares the `PUBLISH`, `SUBSCRIBE_STREAM`, and `STATUS` APIs on its registered endpoint. `STATE_PROOF` is uncommon at Tier 1; it is usually offered at Tier 2 nodes that serve clients directly.

### Tier 2 Block Node

Receives the block stream **from another Block Node** - typically a Tier 1, but a Tier 2 may also pull from another Tier 2. The publish path is replaced by a subscribe-from-upstream path; otherwise the network surface matches Tier 1.

|                 Flow                 |  Direction   |           Port            |                                                       Notes                                                       |
|--------------------------------------|--------------|---------------------------|-------------------------------------------------------------------------------------------------------------------|
| Pull from upstream Block Node        | **Outbound** | `40980` to peer (typical) | Mechanism is operator-dependent.                                                                                  |
| Subscribe (Mirror / downstream → BN) | Inbound      | `40980` gRPC              | Same shape as Tier 1                                                                                              |
| Status (clients → BN)                | Inbound      | `40982` gRPC              | Subscribers and other clients query the Status API for available blocks, available services, and response latency |
| Block Access (clients → BN)          | Inbound      | `40981` gRPC/HTTP         | Block retrieval API                                                                                               |
| Metrics scrape                       | Inbound      | `16007` HTTP              | Intra-cluster                                                                                                     |
| Probes                               | Inbound      | `40983` HTTP/1.1          | Intra-cluster from kubelet                                                                                        |
| Backfill (optional)                  | Outbound     | `40980` to peer           | Only if `backfill` plugin present and enabled; targets the peer's Subscribe API port                              |

A Tier 2 Block Node typically declares `SUBSCRIBE_STREAM` and `STATUS` on its registered endpoint, and may add `STATE_PROOF` if it serves proofs to clients.

#### Notes for both tiers

- The block stream travelling between Block Nodes is forwarded unverified. This affects what TLS gives and does not give (next section), but does not change the tier model.
- "Archive Server" and other deployment types can run at either tier and use the same ports. See [Block Node Types](../../Block-Node-Types.md) for the type taxonomy.
- For server sizing alongside firewall planning (NIC throughput, network targets), see [Block Node Hardware Specifications](./block-node-hardware-specifications.md).

---

## TLS requirements

The Block Node process does not terminate TLS for any inbound port. TLS is terminated upstream by a Kubernetes Ingress, a service mesh sidecar, or a load balancer. This is true today and is the documented deployment posture.

|                            Connection                            |   TLS in-process at BN    |          TLS upstream (typical)          |                                                 Notes                                                 |
|------------------------------------------------------------------|---------------------------|------------------------------------------|-------------------------------------------------------------------------------------------------------|
| gRPC publish (CN → BN, `40984` LFH / `40840` default)            | No                        | Yes, at Ingress / LB                     | CN connects to the Ingress hostname; Ingress strips TLS and forwards h2c to the pod                   |
| gRPC subscribe (MN / Tier 2 → BN, `40980` LFH / `40840` default) | No                        | Yes, at Ingress / LB                     | Same path as publish                                                                                  |
| Backfill egress (BN → peer BN, Subscribe API port)               | No (Block Node is client) | Optional, gated by `BACKFILL_ENABLE_TLS` | Default `false`. When `true`, the Block Node initiates a TLS-wrapped connection to the peer's Ingress |
| Metrics scrape (Prometheus → BN, `16007`)                        | No                        | Typically intra-cluster, often plaintext | If exposed beyond the cluster, terminate TLS at the collector or at an Ingress                        |
| Probes (kubelet → BN, `40983`)                                   | No                        | n/a (intra-cluster)                      | Kubelet uses HTTP/1.1 GET against the pod IP; `40983` is set in both base `values.yaml` and LFH       |
| Debug attach (debugger → BN, `5005`)                             | n/a                       | n/a                                      | Dev/test only; do not expose                                                                          |

Two cautions worth surfacing:

- **TLS at the transport is not the same as block verification.** The Block Node forwards block items as they arrive from the Consensus Node; the consumer verifies each block self-contained from its Block Proof. A TLS-encrypted stream does not turn into a verified stream by virtue of being encrypted. See [HIP-1056](https://hips.hedera.com/hip/hip-1056) for the Block Proof structure and [HIP-1200](https://hips.hedera.com/hip/hip-1200) for the [TSS](../glossary.md#tss-hintsts) threshold signature scheme that signs each block.
- **Self-signed certificates are acceptable for testing but not for production.** A Block Node operator registering an endpoint on-chain via [HIP-1137](https://hips.hedera.com/hip/hip-1137) signals TLS expectations to clients via the `requires_tls` field on each `RegisteredServiceEndpoint`. See [Block Node On-Chain Registration](../block-node-on-chain-registration.md).

---

## Firewall policy requirements

The Block Node Helm chart does not ship a `NetworkPolicy` template or any other firewall manifest. Operators express the policy in whichever primitive their environment uses - Kubernetes `NetworkPolicy`, a cloud-provider security group, a host-level firewall, or a service mesh. The list below states what any such policy must allow or deny for a Block Node deployment, derived row-by-row from the [Port summary](#port-summary).

### Must allow (LFH production profile)

- **Inbound TCP `40984`** from Consensus Node source IPs only — restrict this port to known publisher IPs; deny all other inbound.
- **Inbound TCP `40980`** from Mirror Nodes and peer Block Nodes that subscribe to this Block Node.
- **Inbound TCP `40981`** from authorized block-access clients.
- **Inbound TCP `40982`** from monitoring and operator tooling (server-status API).
- **Inbound TCP `40983`** from the Kubernetes kubelet (liveness and readiness probes: `/healthz/livez`, `/healthz/readyz`). Probe traffic is intra-cluster only.
- **Inbound TCP `16007`** from the monitoring system that scrapes Prometheus metrics. Typically intra-cluster; rarely needs external exposure.
- **Outbound TCP `40980`** to each peer Block Node listed in `BACKFILL_BLOCK_NODE_SOURCES_PATH`, if the `backfill` plugin has been configured with a non-empty sources file. A Block Node without a backfill sources file makes no such outbound connections.

> **Additional outbound connections.** The backfill egress rule above covers the most common explicitly-configured outbound flow. Depending on profile and enabled plugins, a Block Node may also make outbound connections for Mirror Node integration, RSA and TSS bootstrap, and (for the Remote Full History profile) S3-compatible object storage. Solo Provisioner will manage inbound and outbound firewall rules automatically in a future release when traffic shaping support is enabled.
>
> **Base-chart default (non-LFH deployments).** When per-service ports are not set, replace the per-port rules above with a single **Inbound TCP `40840`** rule covering all services, and **Outbound TCP `40840`** for backfill egress. The kubelet health probe still uses **Inbound TCP `40983`** and Prometheus metrics still uses **Inbound TCP `16007`** — both ports are set explicitly in the base `values.yaml` regardless of whether the LFH profile is active.

### Must deny

- **Inbound TCP `5005`** in any production deployment. JDWP has no in-process authentication; the Helm chart does not enable it by default and a production cluster should not open it.

### Selecting the right primitive

- On Kubernetes, a `NetworkPolicy` scoped to the Block Node pod (typically by `app.kubernetes.io/name: block-node-server` label) expresses the above. Restrict `from:` and `to:` to specific namespace or pod selectors rather than `{}` open-to-all.
- On bare-metal or cloud-VM deployments, a host firewall or cloud security group enforces the same rules. Cloud security groups vary in stateful vs stateless semantics; consult the provider's documentation.
- DNS egress to a resolver is required if `BACKFILL_BLOCK_NODE_SOURCES_PATH` lists peer Block Nodes by hostname (the JSON file accepts either hostnames or IPs).
