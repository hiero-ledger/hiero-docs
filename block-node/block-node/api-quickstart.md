# Block Node gRPC API Quickstart

Block Nodes expose gRPC APIs for querying block data, checking node status, and subscribing to the
live block stream. This guide walks through making your first API call from the command line using
`grpcurl`. No SDK is required.

## Available APIs

|                                                                                     Service                                                                                     |          RPC           |                                        Description                                        |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------|-------------------------------------------------------------------------------------------|
| [`BlockNodeService`](https://github.com/hiero-ledger/hiero-block-node/blob/main/protobuf-sources/src/main/proto/block-node/api/node_service.proto)                              | `serverStatus`         | Returns the block number range available on this node and basic version info. Start here. |
| [`BlockNodeService`](https://github.com/hiero-ledger/hiero-block-node/blob/main/protobuf-sources/src/main/proto/block-node/api/node_service.proto)                              | `serverStatusDetail`   | Returns software version, stream proto version, and the list of installed plugins.        |
| [`BlockAccessService`](https://github.com/hiero-ledger/hiero-block-node/blob/main/protobuf-sources/src/main/proto/block-node/api/block_access_service.proto)                    | `getBlock`             | Retrieves a single block by block number, or the latest block.                            |
| [`BlockStreamSubscribeService`](https://github.com/hiero-ledger/hiero-block-node/blob/main/protobuf-sources/src/main/proto/block-node/api/block_stream_subscribe_service.proto) | `subscribeBlockStream` | Streams a range of blocks, or streams new blocks live as they arrive.                     |

All APIs use gRPC over HTTP/2 (h2c). TLS is terminated upstream at the load balancer - connect
with `-plaintext` for every public endpoint listed below.

## Prerequisites

### Install grpcurl

```bash
# macOS
brew install grpcurl

# Linux
curl -sSL https://github.com/fullstorydev/grpcurl/releases/latest/download/grpcurl_linux_x86_64.tar.gz \
  | tar -xz && sudo mv grpcurl /usr/local/bin/
```

### Download the proto bundle

Every Block Node [GitHub release](https://github.com/hiero-ledger/hiero-block-node/releases)
ships a `block-node-protobuf-<VERSION>.tgz` archive with all API proto files. Download and
extract it:

```bash
BUNDLE_URL=$(curl -s https://api.github.com/repos/hiero-ledger/hiero-block-node/releases/latest \
  | grep "browser_download_url.*block-node-protobuf.*tgz" \
  | head -1 | cut -d '"' -f 4)

mkdir -p ~/bn-proto && cd ~/bn-proto
curl -sL -O "$BUNDLE_URL"
tar -xzf block-node-protobuf-*.tgz
```

All `grpcurl` commands below assume you are running from `~/bn-proto`.

## Available Endpoints

> Start with previewnet or testnet before querying mainnet.

Each Block Node service listens on its own dedicated port. Previewnet is already deployed with
per-service ports; testnet and mainnet will be updated before the v0.76 release.

|                          Service                          | Port  |  Protocol  |
|-----------------------------------------------------------|-------|------------|
| `BlockStreamSubscribeService` (`subscribeBlockStream`)    | 40980 | gRPC (h2c) |
| `BlockAccessService` (`getBlock`)                         | 40981 | gRPC (h2c) |
| `BlockNodeService` (`serverStatus`, `serverStatusDetail`) | 40982 | gRPC (h2c) |
| Health (`/healthz`, `/readyz`)                            | 40983 | HTTP       |

Connect with `-plaintext` for all public endpoints - TLS is terminated upstream at the load balancer.

### Previewnet

|                     Endpoint                      | Tier |
|---------------------------------------------------|------|
| `lfh01.previewnet.blocknode.hashgraph-devops.com` | 1    |
| `lfh02.previewnet.blocknode.hashgraph-devops.com` | 1    |

### Testnet

|                    Endpoint                    | Tier |
|------------------------------------------------|------|
| `s01.test.blk.ams.lat.ope.eng.hashgraph.io`    | 1    |
| `s01.test.blk.sgp.lat.ope.eng.hashgraph.io`    | 1    |
| `s01.test.blk.chi.lat.ope.eng.hashgraph.io`    | 1    |
| `lfh01.testnet.blocknode.hashgraph-devops.com` | 2    |

### Mainnet

|     Endpoint      | Tier |
|-------------------|------|
| `91.242.214.237`  | 1    |
| `46.21.97.212`    | 1    |
| `162.43.189.97`   | 1    |
| `163.114.159.114` | 1    |
| `82.223.201.227`  | 1    |

Additional mainnet operators are being onboarded; the list will grow as more come online. Once
[HIP-1137](https://hips.hedera.com/hip/hip-1137) is live, the full roster will be queryable
on-chain via the Mirror Node REST API at `/api/v1/network/registered-nodes`.

No API key or authentication token is required for the public endpoints listed above.

## API Examples

The examples below use `s01.test.blk.sgp.lat.ope.eng.hashgraph.io` (testnet) with the
per-service ports listed above. Substitute any endpoint from the tables above. Replace example
block numbers with values from the `firstAvailableBlock`–`lastAvailableBlock` range returned by
`serverStatus` - the available range differs across previewnet, testnet, and mainnet.

### 1. Check node status

`serverStatus` returns the range of blocks available on this node. Call it first to determine valid
block numbers for subsequent requests.

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40982 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

The response includes `firstAvailableBlock` and `lastAvailableBlock`.

### 2. Get extended node status

`serverStatusDetail` returns the Block Node software version, stream proto version, and the
list of installed plugins with their versions.

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40982 \
  org.hiero.block.api.BlockNodeService/serverStatusDetail
```

### 3. Retrieve a single block

`getBlock` returns a single block. The easiest starting point is to request the latest available
block:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/block_access_service.proto \
  -d '{"retrieve_latest": true}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40981 \
  org.hiero.block.api.BlockAccessService/getBlock
```

To retrieve a specific block by number, use a block number within the range returned by
`serverStatus`:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/block_access_service.proto \
  -d '{"block_number": 38000000}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40981 \
  org.hiero.block.api.BlockAccessService/getBlock
```

> **Note:** Blocks can be several megabytes. Pipe to `jq` or redirect to a file if you need to
> inspect the full content.

### 4. Subscribe to a block stream

`subscribeBlockStream` streams a range of blocks. Set `end_block_number` to a specific block
number for a finite range, or to `"18446744073709551615"` (uint64 max) to stream new blocks live
as they arrive.

**Finite range - 10 blocks starting at a known block number:**

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/block_stream_subscribe_service.proto \
  -d '{"start_block_number": 38000000, "end_block_number": 38000009}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40980 \
  org.hiero.block.api.BlockStreamSubscribeService/subscribeBlockStream
```

**Live stream - all new blocks from a known block number onward:**

```bash
grpcurl -plaintext -emit-defaults \
  -import-path . \
  -proto block-node/api/block_stream_subscribe_service.proto \
  -d '{"start_block_number": 38000000, "end_block_number": "18446744073709551615"}' \
  s01.test.blk.sgp.lat.ope.eng.hashgraph.io:40980 \
  org.hiero.block.api.BlockStreamSubscribeService/subscribeBlockStream
```

> **Tip:** `end_block_number` is a uint64 field. Pass the uint64 max value as a JSON string
> (`"18446744073709551615"`) rather than a number literal to avoid precision loss in JSON parsers.

## About the block data

Blocks currently served by Block Nodes wrap Hiero Record Stream Files - the same data Mirror Nodes have
always processed, packaged in the Block format. The block content and structure will change at the
block stream cutover, after which blocks will carry the full Hiero block stream: event data, state
changes, and more granular transaction detail. Both forms are wire-compatible using the same proto
schema, but the post-cutover form contains significantly more information. See
[Block Stream Cutover](./Cutover-Process.md) for the cutover schedule and migration details.
