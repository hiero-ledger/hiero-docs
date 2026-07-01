# Enabling Block Node Streaming in hiero-local-node

## Overview

[hiero-local-node](https://github.com/hiero-ledger/hiero-local-node) provides a Docker-based
local Hiero network for development and testing. The `--enable-block-node` flag starts a Block
Node container alongside the Consensus Node, but **two manual configuration steps are required
before the CN actually streams blocks to the Block Node**.

> **Deprecation notice:** Hiero Local Node is in a 6-month deprecation period ending September
> 2026 and will be removed after that date. [Solo](https://solo.hiero.org) is the supported
> replacement for local development and testing.

By default, the CN's `application.properties` ships with:

```
blockStream.streamMode = RECORDS
blockStream.writerMode = FILE
```

Both settings disable block streaming entirely - the Block Node container runs but receives
nothing. This guide shows how to fix both settings and verify that blocks are flowing.

This guide covers:

1. Single-node mode: start the stack and fix the CN configuration.
2. Multinode mode: start a 4-CN / 2-BN stack and understand the default topology.
3. Verify blocks are flowing using logs and the metrics endpoint.
4. Common issues and fixes.

## Prerequisites

Before you begin, ensure you have:

- **Node.js 20.11.0 or later** and **Docker Desktop** installed and running.
- **hiero-local-node** available - either:
  - Installed via npm: `npm install -g @hashgraph/hedera-local`
  - Or cloned locally: `git clone https://github.com/hiero-ledger/hiero-local-node.git`
  - See [hiero-local-node releases](https://github.com/hiero-ledger/hiero-local-node/releases)
    for versioned packages.
- Permission to edit files inside the cloned `hiero-local-node` directory (required for the
  `application.properties` fix).

## Single-node mode

### Step 1 - Start the stack with Block Node enabled

From the cloned `hiero-local-node` directory:

```bash
npm run start:block-node
```

Or, using the globally installed package from any directory:

```bash
npx @hashgraph/hedera-local start --enable-block-node
```

**What `--enable-block-node` does:**

When the flag is omitted, `DockerService.ts` injects `docker-compose.block-node.yml` into the
compose stack. That file replaces the `block-node` service with a no-op stub that prints a
disabled message. Passing `--enable-block-node` skips that injection, so the base
`docker-compose.yml` `block-node` service runs as defined:

|       Endpoint        | Host port |                Purpose                 |
|-----------------------|-----------|----------------------------------------|
| gRPC (CN → BN stream) | `40840`   | Block Node receives blocks from the CN |
| Prometheus metrics    | `16007`   | Block Node metrics scrape endpoint     |

The CN container (`network-node`) and Block Node container (`block-node`) are on the same Docker
bridge network (`network-node-bridge`), so the CN can reach the Block Node at hostname
`block-node` on port `40840` without going through the host.

### Step 2 - Fix application.properties

The file at `compose-network/network-node/data/config/application.properties` in the
`hiero-local-node` directory controls the CN's block streaming behaviour. Open it and change the
two `blockStream` lines:

**Before (default - streaming disabled):**

```properties
blockStream.streamMode = RECORDS
blockStream.writerMode = FILE
```

**After (streaming enabled):**

```properties
blockStream.streamMode = BOTH
blockStream.writerMode = FILE_AND_GRPC
```

`streamMode = RECORDS` disables block streaming entirely regardless of `writerMode`.
`writerMode = FILE` ignores `block-nodes.json` and the Block Node entirely.
Both must be changed before the CN streams any blocks.

> **Note:** `writerMode` cannot be hot-reloaded. You must restart the stack after editing
> `application.properties` for this change to take effect.

### Step 3 - Fix block-nodes.json

The file at `compose-network/network-node/data/config/block-nodes.json` tells the CN which Block
Node(s) to stream to. The file ships with an outdated port value. Replace its contents with:

```json
{
  "nodes": [
    {
      "address": "block-node",
      "streamingPort": 40840,
      "priority": 0
    }
  ]
}
```

`address` is the Docker service name, which is resolvable by the CN container within the
Docker network. `streamingPort` must match the Block Node's gRPC listen port (`40840` - the
default when `SERVER_PORT` is not overridden in the compose file).

The CN watches `block-nodes.json` for changes and reloads it without a restart. However,
because `writerMode` requires a restart (Step 2), restart the full stack once after making both
edits:

```bash
npm run stop
npm run start:block-node
```

## Multinode mode

### Start the stack

```bash
npm run start:multinode:block-node
```

This combines `docker-compose.yml`, `docker-compose.multinode.yml`, and
`docker-compose.multinode.blocknode.yml` into a single stack.

### Default topology

The default multinode stack runs 4 Consensus Nodes and 2 Block Nodes in a 2:1 mapping:

|  Consensus Node  |   Block Node   |    BN internal port    | Host port |
|------------------|----------------|------------------------|-----------|
| `network-node`   | `block-node`   | `40840` (default)      | `40840`   |
| `network-node-1` | `block-node`   | `40840` (default)      | `40840`   |
| `network-node-2` | `block-node-1` | `8083` (`SERVER_PORT`) | `8083`    |
| `network-node-3` | `block-node-1` | `8083` (`SERVER_PORT`) | `8083`    |

The `block-nodes.json` for each CN is injected via Docker volume mounts defined in
`docker-compose.multinode.yml`:

```yaml
# CNs network-node-1 → block-node (port 40840)
x-first-block-node-volumes: &first-block-node-volumes
  - "${APPLICATION_CONFIG_PATH}/CNtoBNConfig/2to1/CN1/block-nodes.json:\
/opt/hgcapp/data/config/block-nodes.json"

# CNs network-node-2 and network-node-3 → block-node-1 (port 8083)
x-second-block-node-volumes: &second-block-node-volumes
  - "${APPLICATION_CONFIG_PATH}/CNtoBNConfig/2to1/CN2/block-nodes.json:\
/opt/hgcapp/data/config/block-nodes.json"
```

The config files live at:

- `compose-network/network-node/data/config/CNtoBNConfig/2to1/CN1/block-nodes.json`
- `compose-network/network-node/data/config/CNtoBNConfig/2to1/CN2/block-nodes.json`

> **Note:** These files ship with an outdated `"port"` field that the CN's PBJ JSON parser does not
> recognise. The parser silently ignores unknown fields, so without the fix the CN resolves the port
> as `0` and cannot connect. Replace both files before starting the multinode stack.

**`CNtoBNConfig/2to1/CN1/block-nodes.json`** (used by `network-node-1` → `block-node`):

```json
{
  "nodes": [
    {
      "address": "block-node",
      "streamingPort": 40840,
      "priority": 0
    }
  ]
}
```

**`CNtoBNConfig/2to1/CN2/block-nodes.json`** (used by `network-node-2` and `network-node-3` →
`block-node-1`):

```json
{
  "nodes": [
    {
      "address": "block-node-1",
      "streamingPort": 8083,
      "priority": 0
    }
  ]
}
```

`network-node` (CN0) uses the global `block-nodes.json` described in Step 3 of the single-node
section above - apply that same fix there.

### Apply the same application.properties fix

The same `application.properties` fix from Step 2 applies - `streamMode = RECORDS` and
`writerMode = FILE` disable streaming on all four CNs. Edit
`compose-network/network-node/data/config/application.properties` and set:

```properties
blockStream.streamMode = BOTH
blockStream.writerMode = FILE_AND_GRPC
```

### Extending to 1:1 topology (4 CNs, 4 BNs)

To create a 1:1 mapping, add two more Block Node services to
`docker-compose.multinode.blocknode.yml` using the next available IP addresses and ports:

```yaml
block-node-2:
  image: "${BLOCK_NODE_IMAGE_PREFIX}hiero-block-node:${BLOCK_NODE_IMAGE_TAG}"
  container_name: block-node-2
  networks:
    network-node-bridge:
      ipv4_address: 172.27.0.7
    mirror-node:
  environment:
    VERSION: ${BLOCK_NODE_IMAGE_TAG}
    REGISTRY_PREFIX: ${BLOCK_NODE_REGISTRY_PREFIX}
    BLOCKNODE_STORAGE_ROOT_PATH: ${BLOCK_NODE_STORAGE_ROOT_PATH}
    JAVA_OPTS: ${BLOCK_NODE_JAVA_OPTS}
    SERVER_PORT: 8084
  ports:
    - "8084:8084"

block-node-3:
  image: "${BLOCK_NODE_IMAGE_PREFIX}hiero-block-node:${BLOCK_NODE_IMAGE_TAG}"
  container_name: block-node-3
  networks:
    network-node-bridge:
      ipv4_address: 172.27.0.8
    mirror-node:
  environment:
    VERSION: ${BLOCK_NODE_IMAGE_TAG}
    REGISTRY_PREFIX: ${BLOCK_NODE_REGISTRY_PREFIX}
    BLOCKNODE_STORAGE_ROOT_PATH: ${BLOCK_NODE_STORAGE_ROOT_PATH}
    JAVA_OPTS: ${BLOCK_NODE_JAVA_OPTS}
    SERVER_PORT: 8085
  ports:
    - "8085:8085"
```

Then create the corresponding `block-nodes.json` files:

```json
// CNtoBNConfig/2to1/CN3/block-nodes.json  (for network-node-2)
{
  "nodes": [
    {
      "address": "block-node-2",
      "streamingPort": 8084,
      "priority": 0
    }
  ]
}
```

```json
// CNtoBNConfig/2to1/CN4/block-nodes.json  (for network-node-3)
{
  "nodes": [
    {
      "address": "block-node-3",
      "streamingPort": 8085,
      "priority": 0
    }
  ]
}
```

And update `docker-compose.multinode.yml` to mount the new files for `network-node-2` and
`network-node-3`:

```yaml
volumes:
  - "${APPLICATION_CONFIG_PATH}/CNtoBNConfig/2to1/CN3/block-nodes.json:\
/opt/hgcapp/data/config/block-nodes.json"
```

## Step 3 - Verify blocks are flowing

### Check the Block Node status endpoint

After starting the stack with the corrected configuration, query the Block Node's status
endpoint directly. Download the proto bundle from the
[Block Node releases](https://github.com/hiero-ledger/hiero-block-node/releases) page, then
run:

```bash
grpcurl -plaintext \
  -emit-defaults \
  -import-path block-node-protobuf-<VERSION> \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  localhost:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

A successful response shows `firstAvailableBlock` and `lastAvailableBlock` incrementing as the
CN streams new blocks.

### Check the metrics endpoint

```bash
curl -s localhost:16007/metrics | grep blocknode_publisher_block_items_received_total
```

A non-zero and steadily increasing `blocknode_publisher_block_items_received_total` counter
confirms the Block Node is receiving blocks from the CN. A value of `0` means the CN has not
yet established a streaming connection.

### Check CN logs

Look for these messages in the CN container logs (`docker logs network-node`):

|                             Log message                             |                               Meaning                                |
|---------------------------------------------------------------------|----------------------------------------------------------------------|
| `Starting block node connection manager...`                         | `writerMode` is set to a streaming value and the CN is initialising. |
| `Block node configuration loaded (version: N)`                      | `block-nodes.json` was parsed successfully.                          |
| `Block node configuration watcher started`                          | CN is now watching the file for changes.                             |
| `Selected new block node for streaming: HOST:PORT (wantedBlock: N)` | Active streaming connection established.                             |

If you see `Streaming is not enabled; block node connection manager will not be started`, the
`writerMode` fix in Step 2 has not taken effect - restart the stack.

## Troubleshooting

|                                        Symptom                                        |                                     Likely cause                                      |                                                                                                Resolution                                                                                                 |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `blocknode_publisher_block_items_received_total` stays at `0`                         | `blockStream.writerMode = FILE` or `blockStream.streamMode = RECORDS` still active.   | Edit `application.properties`, set `writerMode = FILE_AND_GRPC` and `streamMode = BOTH`, then restart the stack.                                                                                          |
| CN log: `Streaming is not enabled; block node connection manager will not be started` | `blockStream.writerMode` is still `FILE`.                                             | Apply the Step 2 fix and restart.                                                                                                                                                                         |
| CN log: `Block node configuration file does not exist at PATH`                        | `block-nodes.json` is missing from the mounted path.                                  | Verify the file exists at `compose-network/network-node/data/config/block-nodes.json` and that the Docker volume mount in the compose file points to the correct path.                                    |
| CN log: `No block nodes available for streaming`                                      | CN cannot reach the Block Node - wrong port or `address` in `block-nodes.json`.       | Confirm `streamingPort` matches the Block Node's gRPC port (`40840` for single-node, `SERVER_PORT` value for multinode). Run `nc -vz block-node 40840` from inside the CN container to test reachability. |
| `grpcurl` or `nc` to `localhost:40840` fails                                          | Block Node container is not running or the `--enable-block-node` flag was not passed. | Run `docker ps | grep block-node` to confirm the container is running. Restart with `--enable-block-node`.                                                                                                |
| Blocks flow initially then stop                                                       | CN switched Block Nodes or the connection stalled.                                    | Check CN logs for `No block nodes available for streaming`. See [Block Node Troubleshooting](../troubleshooting.md).                                                                                      |
