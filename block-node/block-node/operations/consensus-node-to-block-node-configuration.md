# Configuring a Consensus Node to Stream Blocks to a Block Node

## Overview

> **Scope:** On Hedera mainnet and testnet, `application.properties` is overwritten on each release by the Network Management Tool (NMT), and `block-nodes.json` is generated and installed by NMT at deployment time. Manual edits to either file on managed nodes will be overwritten. This guide applies to private or permissioned networks where operators manage configuration directly.
>
> Private networks should also be aware that certain CN releases will introduce new default values - for example, the default streaming behaviour may change from writing records to local disk to streaming blocks to Block Nodes automatically. When a private network upgrades to such a release, a `block-nodes.json` file still needs to be installed to identify which Block Node(s) to stream to.

A Consensus Node (CN) produces a stream of block data. When block streaming is enabled but no
Block Node targets are reachable - because `block-nodes.json` is absent or all configured nodes
are unavailable - the CN behaves in one of three ways depending on its settings:

1. Write blocks to disk and continue.
2. Proceed normally and discard blocks not sent to a Block Node.
3. Enter a `CHECKING` state and pause the network until a Block Node becomes reachable.

Once Record Streams are fully replaced by Block Streams, only option (3) will apply - a missing
or misconfigured `block-nodes.json` will halt consensus.

To route the block stream to a Block Node, two CN settings must point to streaming-enabled values
and a `block-nodes.json` configuration file must exist on disk.

This guide shows how to:

1. Verify the two stream configuration settings that gate block streaming.
2. Create the `block-nodes.json` file that identifies which Block Node(s) to stream to.
3. Confirm the streaming connection is active using CN logs and Block Node metrics.
4. Optionally tune advanced connection behaviour.

## Prerequisites

Before you begin, ensure you have:

- A deployed and healthy Block Node:
  - [Bare Metal Single Node Kubernetes Deployment](./single-node-k8s-deployment.md)
  - [Virtual Machine Single Node Kubernetes Deployment](./solo-weaver-single-node-k8s-deployment.md)
- A running Consensus Node with permission to edit its `application.properties` and the
  `data/config` directory.
  See [hiero-consensus-node releases](https://github.com/hiero-ledger/hiero-consensus-node/releases)
  for available versions.
- Network access from the CN host to the Block Node host on the Block Node's gRPC port.
  Port `40840` is the historical default, but starting with Block Node 0.36 each service uses a separate port - consult your Helm values or release notes for the current publish-service port.
  Confirm reachability with `nc -vz <BN_HOST> <PORT>` before proceeding.

## Step 1 - Verify stream configuration on the Consensus Node

Two CN settings control whether blocks are streamed at all.
Both must be set to streaming-enabled values or the `block-nodes.json` file is completely ignored.

### blockStream.writerMode

Controls where the CN writes blocks.
Set in `application.properties` as `blockStream.writerMode=<value>`.

|      Value      |                                 Behaviour                                  | Streams to Block Node? |
|-----------------|----------------------------------------------------------------------------|------------------------|
| `FILE`          | Write blocks to local disk only. `block-nodes.json` is completely ignored. | No                     |
| `FILE_AND_GRPC` | Write blocks to local disk **and** stream to Block Nodes via gRPC.         | **Yes**                |
| `GRPC`          | Stream to Block Nodes via gRPC only. No local block files are written.     | **Yes**                |

The current default is `FILE_AND_GRPC`.
If your CN was configured with `FILE` (the value used before WRB streaming was enabled), change it to `FILE_AND_GRPC` or `GRPC`.

> Note: The defaults for both `writerMode` and `streamMode` match the Hedera mainnet configuration and are hard-coded in `BlockStreamConfig.java`. Verify your CN's effective values rather than assuming the default applies.

### blockStream.streamMode

Controls which stream type the CN produces.
Set in `application.properties` as `blockStream.streamMode=<value>`.

|   Value   |                             Behaviour                              |
|-----------|--------------------------------------------------------------------|
| `RECORDS` | Produce record streams only. Block streaming is disabled entirely. |
| `BLOCKS`  | Produce block streams only.                                        |
| `BOTH`    | Produce both record streams and block streams.                     |

The current default is `BOTH`.
If this is set to `RECORDS`, block streaming is disabled regardless of `writerMode`.
Ensure it is `BOTH` or `BLOCKS`.

## Step 2 - Create block-nodes.json

The CN reads target Block Node addresses from a JSON file on startup and watches it continuously for changes.

### File location

The file must be named exactly `block-nodes.json` and placed in the directory configured by:

```
blockNode.blockNodeConnectionFileDir = data/config   # default
```

The path is relative to the CN working directory. On mainnet and testnet this directory is managed by NMT and may change between releases; confirm the active path in the CN process environment or release notes before proceeding.

### File schema

The file is a JSON object with a single `nodes` array. Each entry has the following fields:

|            Field            |  Type   | Required |                                                                                                                                                                                          Description                                                                                                                                                                                          |
|-----------------------------|---------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `address`                   | string  | Yes      | Hostname or IP address of the Block Node. Must be resolvable by the CN's OS DNS stack.                                                                                                                                                                                                                                                                                                        |
| `streamingPort`             | integer | Yes      | TCP port the Block Node listens on for incoming block streams. LFH (Local Full History) profile default: `40984` (`PRODUCER_PORT`). Base-chart default: `40840`.                                                                                                                                                                                                                              |
| `servicePort`               | integer | No       | TCP port for Block Node service APIs (e.g., `serverStatus`). Defaults to `streamingPort` if omitted. **Set this explicitly when using per-service ports** (BN 0.36+ or LFH profile) where the publisher and status ports differ - the CN uses this port for readiness checks and will fail to connect if it points to the wrong service. LFH profile default: `40982` (`SERVER_STATUS_PORT`). |
| `priority`                  | integer | Yes      | Connection priority. **Lower value = higher priority.** `0` is the highest priority. Nodes with the same priority are selected randomly among available candidates.                                                                                                                                                                                                                           |
| `messageSizeSoftLimitBytes` | integer | No       | Soft limit on per-request payload size in bytes. Requests are packed up to this size; an oversized single item may exceed it. Defaults to `2,097,152` (2 MB) if omitted.                                                                                                                                                                                                                      |
| `messageSizeHardLimitBytes` | integer | No       | Hard limit on per-item payload size in bytes. Items larger than this value are rejected. Defaults to `131,072,000` (125 MB) if omitted.                                                                                                                                                                                                                                                       |

> **Note:** Both size limits must not exceed what the Block Node is configured to accept. Setting either limit higher than the Block Node's own `server.maxMessageSizeBytes` (or equivalent) will cause streaming errors on oversized items.

### Example: single Block Node

```json
{
  "nodes": [
    {
      "address": "10.0.0.5",
      "streamingPort": 40984,
      "servicePort": 40982,
      "priority": 0
    }
  ]
}
```

### Example: two Block Nodes with failover

The CN connects to the highest-priority node available.
If that node becomes unreachable, it falls back to the next priority group.

```json
{
  "nodes": [
    {
      "address": "bn-primary.example.com",
      "streamingPort": 40984,
      "servicePort": 40982,
      "priority": 0
    },
    {
      "address": "bn-secondary.example.com",
      "streamingPort": 40984,
      "servicePort": 40982,
      "priority": 1
    }
  ]
}
```

### Live reload

The CN watches `block-nodes.json` for create, modify, and delete events.
Changes take effect immediately - **no CN restart is required.**
If the file is deleted, the CN clears its Block Node list and stops establishing new connections until a valid file is recreated.
If a reload fails to parse, the CN logs a warning and continues using the previous configuration.

### How assignment and failback work

The CN selects a streaming target whenever it starts or needs to re-establish a connection:

1. **Initial selection** - At startup, the CN contacts each listed BN via its `servicePort` to confirm readiness, then selects the BN with the **lowest `priority` value** that is available. When two or more BNs share the same priority, one is chosen at random.
2. **High-latency trigger** - The CN measures acknowledgement latency for each request sent to the active BN. If the BN returns acknowledgements slower than `blockNode.highLatencyThreshold` (default: 30 s) for `blockNode.highLatencyEventsBeforeSwitching` (default: 5) consecutive requests, the CN considers the connection high-latency and switches to the next-best available BN.
3. **Switching** - After switching, the CN waits at least `blockNode.globalCoolDownSeconds` (default: 10 s) before making another switch. The CN logs `Selected new block node for streaming: HOST:PORT (wantedBlock: N)` each time it switches. The failed BN also enters a per-node cooldown (default: 15–30 s depending on failure type) before it is eligible for selection again.
4. **Higher-priority recovery** - Every ~200 ms, the connection monitor checks whether any BN with a lower priority number than the current active connection has become available (cooldown expired and no active connection). When the preferred primary recovers and its cooldown expires, the CN switches back automatically - typically within seconds of recovery, not at the daily reset.
5. **Periodic reset** - Every `blockNode.streamResetPeriod` (default: 24 h, with up to 30 min of jitter), the CN proactively resets its streaming connection to prevent long-lived connection drift, then re-selects among all available BNs by priority.

> **Note:** The CN logs `No block nodes available for streaming` when every listed BN is unreachable. In this state, the CN does not stream blocks until a BN becomes reachable. Ensure at least one BN in `block-nodes.json` is always reachable.

## Step 3 - Confirm streaming is active

### On the Consensus Node

After creating `block-nodes.json`, watch the CN application log for these messages in order:

|                             Log message                              |                                                               Meaning                                                               |
|----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `Starting block node connection manager...`                          | CN has seen the config file and is initialising.                                                                                    |
| `Block node configuration loaded (version: N)`                       | `block-nodes.json` parsed successfully. `N` is a monotonically increasing counter that starts at `1` and increments on each reload. |
| `Block node configuration watcher started`                           | CN is now watching the file for future changes.                                                                                     |
| `Block node connection manager started`                              | Connection manager is active.                                                                                                       |
| `[HOST:PORT] Block node is available for streaming (wantedBlock: N)` | CN reached the BN's status API and confirmed it is ready.                                                                           |
| `Selected new block node for streaming: HOST:PORT (wantedBlock: N)`  | Active streaming connection established.                                                                                            |

If you see instead:

|                                  Log message                                  |                                                            Action                                                             |
|-------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| `Streaming is not enabled; block node connection manager will not be started` | `blockStream.writerMode` is `FILE`. Change it to `FILE_AND_GRPC` or `GRPC`.                                                   |
| `Block node configuration file does not exist at PATH`                        | `block-nodes.json` is missing or in the wrong directory. Check `blockNode.blockNodeConnectionFileDir`.                        |
| `No block nodes available for streaming`                                      | CN cannot reach any listed Block Node. Check network connectivity and firewall rules on the `streamingPort` (`40984` in LFH). |

### On the Block Node

Once the CN is streaming, the Block Node metric `blocknode_publisher_block_items_received_total` will begin incrementing.
Monitor it via the Block Node metrics endpoint (default port `16007`) or via Grafana.

```bash
curl -s http://<BN_HOST>:16007/metrics | grep blocknode_publisher_block_items_received_total
```

A steadily increasing value confirms the Block Node is receiving blocks from the CN.
A value of `0` or no metric present means the CN has not yet established a streaming connection.

See [Block Node Metrics](../metrics.md) for the full metrics reference and [Block Node Troubleshooting](../troubleshooting.md) if the connection does not establish.

## Optional - Tune connection behaviour

The following `blockNode.*` settings in the CN's `application.properties` control how the CN manages Block Node connections.
The defaults are appropriate for most deployments.

|                   Setting                    | Default |                                                         Description                                                         |
|----------------------------------------------|---------|-----------------------------------------------------------------------------------------------------------------------------|
| `blockNode.streamResetPeriod`                | `24h`   | How often the CN proactively resets its streaming connection. Periodic resets prevent long-lived connections from drifting. |
| `blockNode.highLatencyThreshold`             | `30s`   | Block acknowledgement latency above which the CN considers the connection high-latency.                                     |
| `blockNode.highLatencyEventsBeforeSwitching` | `5`     | Number of consecutive high-latency acknowledgements before the CN considers switching to another Block Node.                |
| `blockNode.globalCoolDownSeconds`            | `10`    | Minimum time in seconds between connection switches, regardless of cause.                                                   |
| `blockNode.grpcOverallTimeout`               | `30s`   | gRPC client connection timeout.                                                                                             |

### Pairing strategy

These guidelines help choose which BN to assign as primary and how to tune the connection settings for your deployment:

- **Minimise round-trip latency to the primary BN.** Block acknowledgement latency - how long the CN waits for the BN to confirm each request - determines how often the high-latency circuit breaker fires. Place your primary BN (priority 0) on the lowest-latency network path from the CN: ideally the same data centre or availability zone. Acknowledgement latency well below `highLatencyThreshold` (default 30 s) is normal; values near or above the threshold during normal operation indicate a network or BN performance problem worth investigating before relying on automatic failover.
- **Assign distinct priorities for distinct failure domains.** If you have BNs in different regions or data centres, assign `priority: 0` to the closest one and `priority: 1` (or higher) to the others. The CN switches to a higher-priority-number BN only when the lower-priority-number BN fails or becomes high-latency.
- **Reserve equal-priority groups for equivalent BNs.** BNs with the same priority are selected at random. Use same-priority groups only when the BNs are genuinely interchangeable - for example, two replicas in the same cluster. Do not use same-priority groups to implement load balancing: the CN sends the entire block stream to one BN at a time.
- **Watch for frequent switches.** The CN switches once per `streamResetPeriod` (default: daily) as a normal periodic reset. If it logs `Selected new block node for streaming` more often than that - more than once a day outside of planned CN restarts - the primary BN's acknowledgement latency is approaching `highLatencyThreshold`. Investigate the BN's CPU, memory, and disk I/O. As a temporary measure, increase `highLatencyEventsBeforeSwitching` to tolerate more transient spikes before switching.
- **Return to the preferred primary automatically.** After a failover, the primary BN enters a per-node cooldown (default: 15–30 s depending on failure type). Once the cooldown expires, the connection monitor proactively checks every ~200 ms for a higher-priority available BN and switches back without waiting for the daily `streamResetPeriod` reset. If the primary does not come back automatically, verify it is reachable on its `servicePort` - the log message `[HOST:PORT] Block node is available for streaming` confirms a successful reachability check.

## Troubleshooting

|                                                       Symptom                                                       |                                                                   Likely cause                                                                    |                                                                                                             Resolution                                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `blocknode_publisher_block_items_received_total` stays at `0` after setup                                           | The CN has not established a streaming connection. Any of the three causes below may apply.                                                       | Check the CN logs for the error messages listed in Step 3. Confirm `blockStream.writerMode` is not `FILE` and `block-nodes.json` is present in the correct directory.                                                               |
| CN log: `Streaming is not enabled; block node connection manager will not be started`                               | `blockStream.writerMode` is set to `FILE`.                                                                                                        | Set `blockStream.writerMode=FILE_AND_GRPC` in `application.properties` and restart the CN.                                                                                                                                          |
| CN log: `Block node configuration file does not exist at PATH`                                                      | `block-nodes.json` is absent or in the wrong directory.                                                                                           | Create the file at the path shown in the log, or set `blockNode.blockNodeConnectionFileDir` to the directory that contains the file.                                                                                                |
| CN log: `No block nodes available for streaming`                                                                    | The CN cannot reach any Block Node listed in `block-nodes.json` - either the address/port is wrong or a firewall is blocking the connection.      | Verify the `address` and `streamingPort` values in `block-nodes.json`. Run `nc -vz <address> <streamingPort>` from the CN host. In LFH deployments the publish port is `40984`; open that inbound on the BN host if the test fails. |
| `nc -vz <BN_HOST> <streamingPort>` fails from the CN host                                                           | The publish port is blocked between the CN and BN hosts.                                                                                          | Check host firewall rules on both the CN and BN hosts. Open TCP inbound on `streamingPort` (`40984` in LFH) on the BN host. If running in Kubernetes, check network policy and security group rules.                                |
| The `address` in `block-nodes.json` resolves to the wrong host or not at all                                        | The hostname is not resolvable from the CN host's DNS.                                                                                            | Run `nslookup <address>` or `dig <address>` from the CN host. Use an IP address instead of a hostname if DNS resolution is unreliable.                                                                                              |
| CN logs show a successful connection but the BN still shows `blocknode_publisher_block_items_received_total` at `0` | The CN connected to the `servicePort` (status API) but may be streaming to the wrong `streamingPort`, or the BN's publisher plugin is not loaded. | Confirm `streamingPort` in `block-nodes.json` matches the BN's publisher port (`40984` in LFH profile). Check the BN logs for publisher plugin startup messages.                                                                    |
| `block-nodes.json` was updated but the CN did not reload the configuration                                          | Some editors replace files atomically (write to a temp file then rename), which may not trigger the inotify create event the CN watches for.      | Check CN logs for file-watcher errors. If the reload did not fire, delete the file and recreate it - the CN also watches for create events and will reload on the new file.                                                         |
| The CN frequently switches between Block Nodes                                                                      | The primary Block Node's acknowledgement latency is exceeding `blockNode.highLatencyThreshold` (`30s` by default) on consecutive blocks.          | Check Block Node performance (CPU, memory, disk I/O). Increase `blockNode.highLatencyEventsBeforeSwitching` to tolerate more high-latency events before switching. See [Block Node Troubleshooting](../troubleshooting.md).         |
