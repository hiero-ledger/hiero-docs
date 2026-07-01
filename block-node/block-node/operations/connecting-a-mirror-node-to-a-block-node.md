# Connecting a Mirror Node to a Block Node

This guide walks a Mirror Node operator through the steps required to subscribe a Mirror Node to one or more Block Nodes, verify the connection is healthy, and handle common failure modes.

For the rationale, subscription model, status codes, and the gap / reconnection behaviour Mirror Node operators must understand, see the companion [Mirror Node Integration](../mirror-node-integration.md) concept doc.

## Overview

A Mirror Node opens a long-lived gRPC server-streaming call to each Block Node's `BlockStreamSubscribeService.subscribeBlockStream` RPC. The Block Node streams `SubscribeStreamResponse` messages containing block items, terminates each block with an `end_of_block` marker, and closes the stream with a single terminal `status` message.

The Mirror Node ships with built-in multi-Block-Node support: configure one or more Block Nodes under `hiero.mirror.importer.block.nodes[]` and the Mirror Node selects between them by priority and measured latency, failing over automatically if a node becomes inactive.

This guide shows how to:

1. Confirm each target Block Node is reachable and serving the desired block range.
2. Configure the Mirror Node to subscribe to one or more Block Nodes.
3. Verify the connection through logs, metrics, and the Block Node's `serverStatus` endpoint.
4. Diagnose the most common failure modes.

> **Most Mirror Node operators consume the block stream from a public Block Node and do not run their own.** The exception is an operator running many Mirror Nodes, who may run a Tier 2 Block Node as a local buffer that pre-validates and redistributes the stream to multiple Mirror Nodes from a single connection upstream. This guide covers both cases; sections specific to running your own Block Node are marked accordingly.

## Prerequisites

Before you begin, ensure you have:

- One or more Block Nodes to connect to, using one of the following sources:
  - [**Block Nodes published on the network**](https://hips.hedera.com/all#hip-1137) - for production use against production networks (the typical case).
  - [**Bare Metal Single Node Kubernetes Deployment**](./single-node-k8s-deployment.md) - if you are running your own Block Node.
  - [**Virtual Machine Single Node Kubernetes Deployment**](./solo-weaver-single-node-k8s-deployment.md) - if you are running your own Block Node.
  - [**Block Node Dev Quickstart (Docker)**](../quickstart.md) - for local development and testing.
- A running Mirror Node deployment ready to be reconfigured to consume from a Block Node:
  - [**Mirror Node Installation Guide**](https://github.com/hiero-ledger/hiero-mirror-node/blob/main/docs/installation.md) - local or Docker Compose install paths.
  - [**Mirror Node Configuration Reference**](https://github.com/hiero-ledger/hiero-mirror-node/blob/main/docs/configuration.md) - full property reference for the Mirror Node services.
- Network connectivity between the Mirror Node host and each Block Node host on the gRPC port (default `40840`).
- A gRPC client capable of HTTP/2 server-streaming calls:
  - The Mirror Node's built-in gRPC stack (for production integration), **or**
  - [**`grpcurl`**](https://github.com/fullstorydev/grpcurl) - for ad-hoc verification from a shell (optional but recommended). Install via `brew install grpcurl` on macOS, following the [official guide](https://grpcurl.com/how-do-i-install-grpcurl-on-my-system/) on Windows, or your distribution's package manager on Linux.

The table below summarises the Mirror-Node-side technical requirements. For Block Node version, port, transport, and block-availability requirements when running your own Block Node, see the [Block Node Configuration](../configuration.md) reference and the deployment guides linked above.

### On the Mirror Node host

|    Requirement    |                                                                                                                                                                                                                                                              Details                                                                                                                                                                                                                                                              |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Network access    | TCP connectivity from the Mirror Node host to each Block Node host on its gRPC port (default `40840`).                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Proto definitions | `block_stream_subscribe_service.proto`, `node_service.proto`, and `shared_message_types.proto` from [`protobuf-sources/src/main/proto/block-node/api/`](https://github.com/hiero-ledger/hiero-block-node/tree/main/protobuf-sources/src/main/proto/block-node/api). For ad-hoc `grpcurl` use, the matching versioned bundle from the [Block Node releases](https://github.com/hiero-ledger/hiero-block-node/releases) page is the easiest source — see [Step 1](#step-1-confirm-each-block-node-is-reachable-and-serving-blocks). |
| gRPC reflection   | The Block Node does **not** enable gRPC server reflection on the public port. Clients must supply protobuf descriptors explicitly.                                                                                                                                                                                                                                                                                                                                                                                                |

## Configuration

> **If you operate your own Block Node:** tuning the subscriber-facing settings (`subscriber.*`) and server-level limits (`server.*`, `server.http2.*`) is covered in [Block Node Configuration](../configuration.md). Mirror Node operators consuming a public Block Node do not need to touch these.

### Mirror Node properties

Configure the Mirror Node via `application.yml` (or equivalent Spring property source). All keys live under the `hiero.mirror.importer.block.*` namespace. See the [Mirror Node Configuration Reference](https://github.com/hiero-ledger/hiero-mirror-node/blob/main/docs/configuration.md) for the full table.

#### Required

|                 Property                 | Default |                                                             Set to                                                              |
|------------------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------|
| `hiero.mirror.importer.block.enabled`    | `false` | `true` — master switch for the block-stream source.                                                                             |
| `hiero.mirror.importer.block.sourceType` | `AUTO`  | `BLOCK_NODE` to subscribe exclusively, or `AUTO` to try Block Node first and fall back to record-file ingestion if unavailable. |

#### Block Node endpoints

Declare one entry under `hiero.mirror.importer.block.nodes[]` per target Block Node:

|                     Property                      | Default |                                                Effect                                                 |
|---------------------------------------------------|---------|-------------------------------------------------------------------------------------------------------|
| `hiero.mirror.importer.block.nodes[].host`        | —       | Host or IP of the Block Node gRPC service. **Required.**                                              |
| `hiero.mirror.importer.block.nodes[].port`        | `40840` | gRPC port of the Block Node.                                                                          |
| `hiero.mirror.importer.block.nodes[].priority`    | `0`     | Selection priority. **Lower value is higher priority.** Highest-priority reachable node is preferred. |
| `hiero.mirror.importer.block.nodes[].requiresTls` | `false` | Set to `true` if the Block Node endpoint is fronted by TLS termination.                               |

Example YAML for two Block Nodes with the second as failover:

```yaml
hiero:
  mirror:
    importer:
      block:
        enabled: true
        sourceType: BLOCK_NODE
        nodes:
          - host: bn-primary.example.com
            port: 40840
            priority: 0
            requiresTls: true
          - host: bn-fallback.example.com
            port: 40840
            priority: 10
            requiresTls: true
```

#### Selection and readmit behaviour

The Mirror Node will not block on a single unhealthy Block Node. Tune the readmit logic with:

|                         Property                          |         Default         |                                                                     Effect                                                                      |
|-----------------------------------------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| `hiero.mirror.importer.block.scheduler.type`              | `PRIORITY_THEN_LATENCY` | Selection strategy: `LATENCY`, `PRIORITY`, or `PRIORITY_THEN_LATENCY`. The default picks by priority and uses measured latency as a tiebreaker. |
| `hiero.mirror.importer.block.stream.maxSubscribeAttempts` | `3`                     | Consecutive failed subscribe attempts before a Block Node is marked inactive.                                                                   |
| `hiero.mirror.importer.block.stream.readmitDelay`         | `1m`                    | How long an inactive Block Node stays out before being retried.                                                                                 |
| `hiero.mirror.importer.block.stream.responseTimeout`      | `400ms`                 | `serverStatus` request timeout.                                                                                                                 |

#### Migration cutover

During the records-to-block-streams cutover ([HIP-1193](https://github.com/hiero-ledger/hiero-improvement-proposals/blob/main/HIP/hip-1193.md)), the Mirror Node can switch automatically from record-file ingestion to block-stream subscription as the network rolls forward:

|                     Property                      |                                                      Default                                                       |                             Effect                              |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| `hiero.mirror.importer.block.cutover.enabled`     | unset; network default is `true` for mainnet/testnet, `false` for others. When set, overrides the network default. | Enables the auto-switch.                                        |
| `hiero.mirror.importer.block.cutover.hapiVersion` | `0.76.0`                                                                                                           | The HAPI version following which the final cutover will happen. |

Cutover detection and handling are automatic in Mirror Node v0.155 and later; operators are not expected to set these properties under normal conditions.

## Step-by-Step Guide

### Step 1: Confirm each Block Node is reachable and serving blocks

For each Block Node entry you plan to configure, run the checks below.

#### Reachability

```bash
nc -vz <BLOCK_NODE_HOST> 40840
```

- **Expected output**: `Connection to <BLOCK_NODE_HOST> port 40840 [tcp/*] succeeded!`
- **If this fails**: investigate firewall rules, security groups, and that the Block Node process is running.

#### Available block range

Because the Block Node does not enable gRPC reflection, the protobuf descriptors must be supplied explicitly. Download the matching protobuf release bundle once and reuse it for all checks:

```bash
# 1. Discover the URL of the latest protobuf bundle release.
BUNDLE_URL=$(curl -s https://api.github.com/repos/hiero-ledger/hiero-block-node/releases/latest \
  | grep "browser_download_url.*block-node-protobuf.*tgz" \
  | head -1 | cut -d '"' -f 4)

# 2. Download and extract into a working directory.
mkdir -p ~/bn-proto && cd ~/bn-proto
curl -sL -O "$BUNDLE_URL"
tar -xzf block-node-protobuf-*.tgz

# 3. Call serverStatus. The bundle extracts to the current directory, so use `-import-path .`.
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  <BLOCK_NODE_HOST>:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

> **Note:** The download uses `curl -LO` rather than `wget` because `wget` is not installed on macOS by default. On Linux either tool works. The extracted tarball lays out `block/`, `block-node/`, `platform/`, `services/`, and `streams/` directly in the current directory — there is no version-prefixed top-level folder.

- **Expected output** (active node with blocks ingested):

  ```json
  {
    "firstAvailableBlock": "1",
    "lastAvailableBlock": "123456",
    "onlyLatestState": false
  }
  ```
- **Expected output** (freshly started node, no blocks yet):

  ```json
  {
    "firstAvailableBlock": "18446744073709551615",
    "lastAvailableBlock": "18446744073709551615",
    "onlyLatestState": false
  }
  ```

  When both values are `uint64_max`, the Block Node has not yet ingested any blocks. The Mirror Node will receive `NOT_AVAILABLE (6)` if it tries to subscribe now.

  Without `-emit-defaults`, `grpcurl` elides `"onlyLatestState": false` from the output; both forms are semantically equivalent.

### Step 2: Configure the Mirror Node and restart

1. Edit the Mirror Node importer's `application.yml` (or equivalent override).
2. Set the [required properties](#required) and add a `nodes[]` entry per Block Node, as shown in the example above.
3. Restart the importer.

The Mirror Node selects an active Block Node by `scheduler.type` (default `PRIORITY_THEN_LATENCY`) and opens a `subscribeBlockStream` gRPC call against it. On failure, it tries the next eligible Block Node, marking failed nodes inactive after `maxSubscribeAttempts` consecutive failures and readmitting them after `readmitDelay`.

### Step 3: Smoke-test the subscribe call from the shell *(optional)*

If you want to confirm the Block Node will accept a subscribe call before the Mirror Node restarts, use the same protobuf bundle from Step 1:

```bash
cd ~/bn-proto
grpcurl -plaintext \
  -import-path . \
  -proto block-node/api/block_stream_subscribe_service.proto \
  -d '{"start_block_number": "1", "end_block_number": "18446744073709551615"}' \
  <BLOCK_NODE_HOST>:40840 \
  org.hiero.block.api.BlockStreamSubscribeService/subscribeBlockStream
```

- **Expected behaviour on a Block Node that has ingested blocks**: `grpcurl` prints a continuous stream of `SubscribeStreamResponse` messages alternating between `block_items` (batched block data) and `end_of_block` (one per completed block). The stream remains open until you cancel with `Ctrl-C` or the Block Node returns a terminal `status`.
- **Expected behaviour on a freshly started Block Node with no blocks**: a single terminal status, then the stream closes:

  ```json
  { "status": "NOT_AVAILABLE" }
  ```

### Step 4: Handle disconnects and gaps

The Block Node closes the stream when the finite range is fully served, when the connection reaches the Block Node's connection lifetime limit, when an internal error occurs, or when the client disconnects. The Mirror Node handles reconnection automatically: it will retry against the highest-priority reachable Block Node, governed by `maxSubscribeAttempts` and `readmitDelay`.

Two operator-visible patterns are worth knowing:

- **Gap in `end_of_block.block_number`**: the flow of data from Consensus Node to Block Node arrived out-of-order or required a resend due to verification or persistence failure on the upstream stream. The Mirror Node may reconnect to backfill the missing range from history. The Mirror Node detects this and re-subscribes with `start_block_number = (last_committed_block + 1)`. If the gap is not available on the current Block Node (terminal `NOT_AVAILABLE`), failover to a higher-priority Tier-1 archive node is required - configure such a node as a low-priority entry under `nodes[]` so the Mirror Node can fall over automatically.
- **Repeated `ERROR (3)` from one Block Node**: the Block Node is failing internally. The Mirror Node will mark it inactive after `maxSubscribeAttempts` and try the next configured node.

See [Gaps and out-of-order blocks come from the unverified stream](../mirror-node-integration.md#gaps-and-out-of-order-blocks-come-from-the-unverified-stream) in the concept doc for the rationale.

## Verification

### Verify on the Mirror Node side

- The Mirror Node's last-committed block number advances monotonically.
- Importer logs show subscribe activity against the configured `nodes[]` entries; no Block Node remains continuously marked inactive.
- Block-processing latency (time from `end_of_block` received to block committed) stays below the block interval.

### Optional: verify on the Block Node side (if you operate it)

The checks in this section run against the Block Node and are only relevant if you are operating one yourself. Mirror Node operators consuming a public Block Node should rely on the Mirror-Node-side checks above.

#### Logs

When a session ends in error, the Block Node logs at `INFO` level (the `%(,d` format specifier expands to the numeric client identifier):

```
Subscriber session <clientId> failed due to <cause>.
```

When a session ends with `SUCCESS`, it logs at `TRACE` level:

```
Subscriber session <clientId> completed successfully.
```

Enable `TRACE` for the `org.hiero.block.node.stream.subscriber` logger if you want positive confirmation per session.

#### Metrics

The Block Node exposes Prometheus-format metrics on the default metrics endpoint:

```bash
curl -s http://<BLOCK_NODE_HOST>:16007/metrics | grep blocknode_subscriber
```

- **Expected output** with one Mirror Node connected:

  ```
  # TYPE blocknode_subscriber_open_connections gauge
  blocknode_subscriber_open_connections 1
  # TYPE blocknode_subscriber_errors counter
  blocknode_subscriber_errors_total 0
  ```

  The counter is exposed with the Prometheus-conventional `_total` suffix even though the underlying metric is registered as `subscriber_errors`. The `_open_connections` gauge is updated lazily: a closed session's decrement is processed only when the next subscriber attempts to connect. In low-traffic windows the gauge can appear stuck on the previous value. Treat the gauge as approximate; use the Mirror Node side (last committed block, reconnect rate) and infrastructure-level connection counts (load balancer, ingress) for precise observation.

#### Status

Re-run `serverStatus` while the Mirror Node is connected and confirm `lastAvailableBlock` advances as Consensus Nodes publish new blocks:

```bash
grpcurl -plaintext -d '{}' \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  <BLOCK_NODE_HOST>:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

## Troubleshooting

|                                           Symptom                                            |                                                    Likely cause                                                    |                                                                                                        Resolution                                                                                                        |
|----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `grpcurl` returns `Failed to dial: connection refused`                                       | Block Node is not listening on the expected port.                                                                  | Verify `server.port` in the Block Node configuration and that the process is running. On Linux: `ss -tlnp \| grep 40840`. On macOS: `lsof -nP -iTCP:40840 -sTCP:LISTEN`.                                                 |
| `grpcurl` returns `Failed to list services: ... malformed header: missing HTTP content-type` | The Block Node does not enable gRPC server reflection on the public port; `grpcurl` cannot self-discover services. | Supply protobuf descriptors explicitly with `-import-path` and `-proto`, as shown in [Step 1](#step-1-confirm-each-block-node-is-reachable-and-serving-blocks).                                                          |
| `nc -vz` succeeds but `subscribeBlockStream` immediately closes with `NOT_AVAILABLE (6)`     | `start_block_number` is below `first_available_block`, or the Block Node has not ingested any blocks yet.          | Call `serverStatus`; if both `first_available_block` and `last_available_block` equal `uint64_max`, wait for ingest. Otherwise set `start_block_number >= first_available_block`.                                        |
| Immediate close with `INVALID_START_BLOCK_NUMBER (4)`                                        | `start_block_number` exceeds `last_available_block + subscriber.maximumFutureRequest`.                             | Wait for the Block Node to advance, or reduce `start_block_number`.                                                                                                                                                      |
| Immediate close with `INVALID_END_BLOCK_NUMBER (5)`                                          | `end_block_number < start_block_number`.                                                                           | Set `end_block_number >= start_block_number`, or use `18446744073709551615` for an indefinite stream.                                                                                                                    |
| Repeated terminal `ERROR (3)` from a single Block Node                                       | Block Node internal failure.                                                                                       | Check that Block Node's logs at `INFO` for `failed due to ...`; review its health (CPU, memory, disk). The Mirror Node will mark this node inactive after `maxSubscribeAttempts`.                                        |
| All configured Block Nodes marked inactive                                                   | Network reachability problem, or all Block Nodes simultaneously unhealthy.                                         | Verify host/port for each `nodes[]` entry; check that `requiresTls` matches the actual termination setup; inspect each Block Node's metrics endpoint.                                                                    |
| `blocknode_subscriber_open_connections` does not increment after Mirror Node connects        | Connection is not reaching the Block Node.                                                                         | Re-check firewall rules; verify the Mirror Node is connecting to the correct host and port.                                                                                                                              |
| **Gap in `end_of_block.block_number` after going live**                                      | The live block stream experienced interruptions or errors and blocks were received out of order or were resent.    | The Mirror Node will reconnect to backfill from history.                                                                                                                                                                 |
| Stream stalls with no new `block_items` after going live                                     | Block Node has not received new blocks from Consensus Nodes.                                                       | Check `blocknode_publisher_open_connections` and Consensus Node logs; this is a publisher-side issue, not a subscriber one. See [Block Node Troubleshooting](../troubleshooting.md#block-node-not-receiving-new-blocks). |
| High latency between block production and Mirror Node receipt                                | Live queue is polled at up to `MAX_LIVE_POLL_DELAY = 500 ms`.                                                      | This is the worst-case poll latency in the current implementation.                                                                                                                                                       |
| Session fails shortly after reconnect with `NOT_AVAILABLE (6)`                               | Mirror Node reconnected before the Block Node re-indexed the requested range after a restart.                      | Add a short delay and re-query `serverStatus` before each reconnect (already handled by the Mirror Node's readmit logic via `readmitDelay`).                                                                             |
| Cutover does not switch to block-stream source                                               | `hiero.mirror.importer.block.cutover.enabled` is `false`, or the network has not reached `cutover.hapiVersion`.    | Set `cutover.enabled=true`; confirm the network HAPI version meets `cutover.hapiVersion`.                                                                                                                                |

## Related documentation

- [Mirror Node Integration](../mirror-node-integration.md) - concept doc explaining the rationale, subscription model, status codes, and the gap / reconnection behaviour Mirror Node operators must understand.
- [Mirror Node Configuration Reference](https://github.com/hiero-ledger/hiero-mirror-node/blob/main/docs/configuration.md) - full property reference including the `hiero.mirror.importer.block.*` namespace.
- [Block Node Overview](../block-node-overview.md)
- [Block Node Configuration](../configuration.md)
- [Block Node Metrics](../metrics.md)
- [Block Node Troubleshooting](../troubleshooting.md)
