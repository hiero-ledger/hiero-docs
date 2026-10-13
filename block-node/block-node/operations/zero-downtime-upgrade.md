# Zero-Downtime Block Node Upgrade

A standard upgrade restarts the Block Node pod, which briefly interrupts block ingestion and
subscriber streams for the duration of the pod restart (typically 1-2 minutes). If a secondary
Block Node is available and the Consensus Node is configured to fail over to it, blocks continue
flowing during the upgrade window - no blocks are missed and the Block Node's block range has no gap.

Zero-downtime upgrade does not use a different upgrade command. The difference is entirely in the
CN configuration: `block-nodes.json` lists the secondary as a lower-priority fallback, and the CN
switches to it automatically when the primary pod goes down. No manual re-pointing is needed.

## Prerequisite - secondary Block Node and priority failover

You need two things in place before starting:

1. A secondary Block Node that is running and reachable by the CN on its streaming (publish) port.
2. The CN's `block-nodes.json` must include both Block Nodes with distinct `priority` values:

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

`priority: 0` is highest priority - the CN streams to this node by default. `priority: 1` is the
fallback. When the primary pod goes down, the CN detects the connection drop and switches to the
secondary automatically. When the primary returns, the CN switches back.

> **Port note:** `streamingPort` and `servicePort` depend on your deployment profile. LFH-profile:
> `streamingPort 40984`, `servicePort 40982`. Base-chart: both `40840`. Confirm your values in your
> Block Node's Helm values before adding the entry.

`block-nodes.json` supports live reload - no CN restart is needed to add or change entries.

See [Configuring a Consensus Node to Stream Blocks to a Block Node](./consensus-node-to-block-node-configuration.md)
for the full `block-nodes.json` schema and live-reload details.

## Step 1 - Record baseline on both Block Nodes

Set these shell variables to your deployment values before running any commands in this procedure:

```bash
export PRIMARY_BN_HOST="<primary-BN-address>"     # e.g. bn-primary.example.com
export SECONDARY_BN_HOST="<secondary-BN-address>" # e.g. bn-secondary.example.com
export NAMESPACE="block-node"                      # your deployment namespace
export SERVICEPORT=40982                           # servicePort from block-nodes.json (LFH: 40982, base-chart: 40840)
```

Capture `firstAvailableBlock` and `lastAvailableBlock` on both the primary and the secondary.
These commands require `grpcurl` and the Block Node protobuf bundle extracted to `~/bn-proto` -
see [Block Node gRPC API Quickstart](../api-quickstart.md) for download instructions.

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$PRIMARY_BN_HOST:$SERVICEPORT" \
  org.hiero.block.api.BlockNodeService/serverStatus

grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$SECONDARY_BN_HOST:$SERVICEPORT" \
  org.hiero.block.api.BlockNodeService/serverStatus
```

Record `lastAvailableBlock` on the primary - call this **N**. After the upgrade the primary must
backfill from N+1 up to the block where the CN resumed streaming to it.

Also verify the secondary is actively ingesting before proceeding. Run this command twice, 10
seconds apart, and confirm the value increases:

```bash
curl -s "http://$SECONDARY_BN_HOST:16007/metrics" | grep blocknode_publisher_block_items_received_total
```

If the value is zero or not increasing, the CN is not streaming to the secondary - do not proceed
until the failover path is confirmed working.

## Step 2 - Upgrade the primary

Run the standard upgrade on the primary. The CN switches to the secondary automatically when the
primary pod terminates.

**Solo Provisioner (mainnet / production):**

```bash
sudo solo-provisioner block node upgrade \
  --profile=mainnet \
  --values=<UPDATED_VALUES_FILE> \
  --no-reuse-values \
  --chart-version=<X.Y.Z>
```

**Taskfile (manual deployment):**

```bash
task helm-upgrade
```

Monitor the pod restart:

```bash
kubectl get pods -n "$NAMESPACE" -w
```

While the primary pod is terminating and restarting, the secondary receives all CN-published blocks.

## Step 3 - Verify recovery

**Confirm the primary is ingesting blocks again:**

Run this command twice, 10 seconds apart, and confirm the value increases:

```bash
curl -s "http://$PRIMARY_BN_HOST:16007/metrics" | grep blocknode_publisher_block_items_received_total
```

If the value is not increasing after the pod reaches Running, the CN has
not yet reconnected to the primary. The CN reconnects automatically: after the primary's connection
cooldown expires, the CN's connection monitor (which runs every 200 ms) detects the primary as
available and switches back. This typically resolves within seconds to a minute of the pod becoming
Ready. If the metric is still not incrementing after a few minutes, temporarily remove the secondary
entry from `block-nodes.json` to force the CN to connect to the primary, confirm
`blocknode_publisher_block_items_received_total` on the primary starts incrementing, then restore
the secondary entry.

**Monitor backfill of the upgrade-window gap:**

During the upgrade window the primary missed blocks N+1 through M (the blocks the secondary
received while the primary was restarting). The backfill plugin closes this gap automatically - but
only if `backfill.blockNodeSourcesPath` (`BACKFILL_BLOCK_NODE_SOURCES_PATH` env var) is set to a
file listing the secondary (or another peer that holds the upgrade-window blocks). The default value
is empty, which means backfill is disabled unless explicitly configured.

```bash
curl -s "http://$PRIMARY_BN_HOST:16007/metrics" | grep -E "backfill_pending_blocks|backfill_blocks_backfilled"
```

- `blocknode_backfill_pending_blocks` declining to `0` and `blocknode_backfill_blocks_backfilled_total` increasing confirms the gap is closing.
- If both remain at `0` with no movement, the backfill source is not configured. Set both
  `blockNode.backfill.sources` in your Helm values **and** `BACKFILL_BLOCK_NODE_SOURCES_PATH`
  under `blockNode.config` (e.g. `/opt/hiero/block-node/backfill/block-node-sources.json`).
  The chart creates the sources file at that path but does not set the environment variable
  automatically - both are required to close the upgrade-window gap.

**Re-run `serverStatus` on the primary:**

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$PRIMARY_BN_HOST:$SERVICEPORT" \
  org.hiero.block.api.BlockNodeService/serverStatus
```

- `firstAvailableBlock` must equal the pre-upgrade value - no older blocks were lost.
- `lastAvailableBlock` must equal or exceed N and must continue advancing.

**Verify subscriber reconnection:**

```bash
curl -s "http://$PRIMARY_BN_HOST:16007/metrics" | grep blocknode_subscriber_open_connections
```

`blocknode_subscriber_open_connections` should return to its pre-upgrade level as Mirror Nodes
re-establish their subscribe streams.

## If there is no secondary Block Node

Without a secondary, there is no failover path. Blocks produced by the network while the primary
pod is restarting are not captured, leaving a gap in the primary's block range. Schedule the
upgrade during a low-traffic period and use the standard upgrade procedure in
[Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md#upgrading-the-block-node).

## Related

- [Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md)
- [Configuring a Consensus Node to Stream Blocks to a Block Node](./consensus-node-to-block-node-configuration.md)
- [Block Node gRPC API Quickstart](../api-quickstart.md)
- [Metrics Reference](../metrics.md)
