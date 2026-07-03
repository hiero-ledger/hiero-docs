# Hiero Block Node Configuration Document

## Overview

This document outlines configuration options for the Hiero Block Node. Most settings are controlled via environment variables for flexible deployment.

Each plugin has its own properties, but this focuses on core options and core plugins.

## Core Configuration Options

| ENV Variable                      | Description                                                                                 | Default |
|:----------------------------------|:--------------------------------------------------------------------------------------------|--------:|
| BLOCK_NODE_EARLIEST_MANAGED_BLOCK | Earliest block managed by this node. Older blocks may exist but won’t be fetched or stored. |       0 |

### Server Configuration

| ENV Variable                            | Description                                                                                                            | Default     |
|:----------------------------------------|:-----------------------------------------------------------------------------------------------------------------------|:------------|
| SERVER_MAX_MESSAGE_SIZE_BYTES           | Max message size (bytes) for HTTP/2.                                                                                   | 131,072,000 |
| SERVER_SOCKET_SEND_BUFFER_SIZE_BYTES    | Send buffer size (bytes).                                                                                              | 131,072     |
| SERVER_SOCKET_RECEIVE_BUFFER_SIZE_BYTES | Receive buffer size (bytes). Override to 131072 for memory-constrained deployments (see `values-overrides/nano.yaml`). | 8,388,608   |
| SERVER_PORT                             | Default port for all services. Individual plugins may bind to a different port via their own config.                   | 40840       |
| SERVER_SHUTDOWN_DELAY_MILLIS            | Delay before shutdown (ms).                                                                                            | 500         |
| SERVER_MAX_TCP_CONNECTIONS              | Max TCP connections allowed.                                                                                           | 1000        |
| SERVER_IDLE_CONNECTION_PERIOD_MINUTES   | Period for idle connections check (minutes).                                                                           | 5           |
| SERVER_IDLE_CONNECTION_TIMEOUT_MINUTES  | Timeout for idle connections (minutes).                                                                                | 30          |
| SERVER_TCP_NO_DELAY                     | Disable Nagle's algorithm (TCP_NODELAY). Reduces latency for small, frequent writes.                                   | true        |
| SERVER_BACKLOG_SIZE                     | Maximum length of the queue of incoming connections on the server socket.                                              | 8,192       |
| SERVER_WRITE_QUEUE_LENGTH               | Number of write buffers queued for write operations.                                                                   | 8,192       |

### WebServerHttp2 Configuration

| ENV Variable                          | Description                                                                  |   Default |
|:--------------------------------------|:-----------------------------------------------------------------------------|----------:|
| SERVER_HTTP2_FLOW_CONTROL_TIMEOUT     | Outbound flow control blocking timeout (ms).                                 |       500 |
| SERVER_HTTP2_INITIAL_WINDOW_SIZE      | Sender's maximum window size (bytes) for stream-level flow control.          | 8,388,608 |
| SERVER_HTTP2_MAX_CONCURRENT_STREAMS   | Max concurrent streams the server will allow.                                |         8 |
| SERVER_HTTP2_MAX_EMPTY_FRAMES         | Max consecutive empty frames allowed on connection.                          |        10 |
| SERVER_HTTP2_MAX_FRAME_SIZE           | Largest frame payload size (bytes) the sender is willing to receive.         | 8,388,608 |
| SERVER_HTTP2_MAX_HEADER_LIST_SIZE     | Max field section size (bytes) the sender is prepared to accept.             |     8,192 |
| SERVER_HTTP2_MAX_RAPID_RESETS         | Max rapid resets (stream RST sent by client before any data sent by server). |        50 |
| SERVER_HTTP2_RAPID_RESET_CHECK_PERIOD | Period for counting rapid resets (ms).                                       |    10,000 |

### Application State Configuration

| ENV Variable                                 | Description                                                                                                                                                                                                                                                                                  |                                Default                                |
|:---------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| APP_STATE_TSS_BOOTSTRAP_FILE_PATH            | Path where TSS data (ledger ID, current roster, WRAPS VK) is persisted across restarts.                                                                                                                                                                                                      | /opt/hiero/block-node/application-state/tss-bootstrap-roster.json     |
| APP_STATE_RSA_BOOTSTRAP_FILE_PATH            | Path where the single current RSA node address book is persisted across restarts. Used by single-book deployments that have not yet migrated to the address book history file.                                                                                                               | /opt/hiero/block-node/application-state/rsa-bootstrap-roster.json     |
| APP_STATE_RSA_ADDRESS_BOOK_HISTORY_FILE_PATH | Path to the block-number-keyed RSA address book history file (JSON-encoded `RangedAddressBookHistory`). When present, this file takes precedence over `APP_STATE_RSA_BOOTSTRAP_FILE_PATH` and enables verification of historical WRBs against the address book that was in effect per block. | /opt/hiero/block-node/application-state/rsa-address-book-history.json |
| APP_STATE_BLOCK_RANGES_FILE_PATH             | Path where the set of available and stored block ranges is persisted. Written every 1,000 blocks received.                                                                                                                                                                                   | /opt/hiero/block-node/application-state/block-ranges.json             |
| KNOWN_PUBLISHERS_FILE_PATH                   | Path where connections for known publisher are persisted. Read only on start.                                                                                                                                                                                                                | /opt/hiero/block-node/application-state/known-publishers.json         |
| INBOUND_PARTNERS_FILE_PATH                   | Path where connections for designated inbound partners are persisted. Read only on start.                                                                                                                                                                                                    | /opt/hiero/block-node/application-state/inbound-partners.json         |
| OUTBOUND_PARTNERS_FILE_PATH                  | Path where connections for designated outbound partners are persisted. Read only on start.                                                                                                                                                                                                   | /opt/hiero/block-node/application-state/outbound-partners.json        |
| APP_STATE_UPDATE_SCAN_INTERVAL               | How often (ms) the application state facility checks for pending TSS data updates. Minimum 100.                                                                                                                                                                                              | 500                                                                   |
| APP_STATE_UPDATE_INITIAL_DELAY               | Delay (ms) before the application state facility begins its first scan.                                                                                                                                                                                                                      | 0                                                                     |

Stored blocks are all blocks reported as persisted by any plugin. Block availability is derived
from `HistoricalBlockFacility` at query time and is not persisted separately. The stored block
range set is loaded at startup and persisted to disk automatically.

#### RSA address book history vs. single-book file

The BN supports two modes for RSA key material:

- **Single-book mode** (`APP_STATE_RSA_BOOTSTRAP_FILE_PATH`): the original mode — one `NodeAddressBook` covering the current network state. Sufficient for deployments that only handle live blocks.
- **History mode** (`APP_STATE_RSA_ADDRESS_BOOK_HISTORY_FILE_PATH`): a `RangedAddressBookHistory` containing one entry per address book era, each scoped to a `[startBlock, endBlock]` range. Required for verifying historical Wrapped Record Blocks (WRBs) against the keys that were in effect when those blocks were produced.

When the history file is present at startup it takes precedence. When only the single-book file is present, the BN wraps it into a single open-ended era (covering all block numbers) so verification behaviour is unchanged.

### Metrics Endpoint Configuration

The metrics HTTP server is provided by the `hiero-metrics` library and exposes
Prometheus-format metrics. Its properties are prefixed with
`metrics.exporter.openmetrics.http.` and can be set via:

- `app.properties` (classpath, lowest priority)
- JVM system properties (`-D` flags via `JAVA_TOOL_OPTIONS`, highest priority)
- Helm chart `blockNode.metrics.*` values (which inject `-D` flags automatically)

| Chart Value                  | JVM Property                                 | Description                         |  Default |
|:-----------------------------|:---------------------------------------------|:------------------------------------|---------:|
| `blockNode.metrics.hostname` | `metrics.exporter.openmetrics.http.hostname` | Bind address for the metrics server |  0.0.0.0 |
| `blockNode.metrics.port`     | `metrics.exporter.openmetrics.http.port`     | Prometheus endpoint port            |    16007 |
| `blockNode.metrics.path`     | `metrics.exporter.openmetrics.http.path`     | HTTP path for metrics endpoint      | /metrics |

> **Note:** These properties come from the `hiero-metrics` library, not from a Block
> Node `@ConfigData` record. They cannot be set via environment variable
> mapping (`AutomaticEnvironmentVariableConfigSource`). The chart injects them as
> JVM system properties through `JAVA_TOOL_OPTIONS`.

## Plugin Management

The Block Node is composed of plugins. The Helm chart determines which plugins are loaded into the running pod from the `plugins.*` values block. The next section ([Configurations By Plugin](#configurations-by-plugin)) covers the per-plugin environment-variable settings; this section covers how plugins themselves are selected and added. For the design rationale, see [Deployment with Selected Plugins](../design/deployment-with-selected-plugins.md).

### Default Hiero plugins

A base Block Node deployment ships with no plugins; the Helm chart downloads them into the container at pod start. The chart's default plugin set is focused on a Tier 1 Local Full History (LFH) profile, enabled via `plugins.names`. The value is a comma-separated string of plugin identifiers:

```yaml
plugins:
  names: "facility-messaging,block-access-service,health,server-status,stream-publisher,stream-subscriber,verification,blocks-file-historic,blocks-file-recent,backfill"
```

> **Note:** All Tier 2 Block Nodes should drop `stream-publisher` from `plugins.names`. The presence of `stream-publisher` is the main difference between a Tier 1 and a Tier 2 deployment.

To change which plugins load, edit the list. Adding a name causes that plugin to be downloaded and loaded on the next pod start. Removing a name skips it on the next start, but the previously-installed JAR remains in the plugins folder unless the folder is cleared between runs (for example, by recreating the PVC or the volume backing the plugins directory). Operators do not need to rebuild the Block Node image — the chart resolves plugins from configured sources at pod start.

#### Plugin sources

The chart supports two sources for downloading plugins; both are configurable:

- **`plugins.repositories`** (recommended for production): Maven repositories the init container queries to resolve plugin artifacts. Defaults to Maven Central and Sonatype Snapshots. Operators running at scale should add a local mirror to `plugins.repositories` to reduce dependency on public infrastructure.
- **`plugins.mavenImage`** (intended for local and Solo testing): a container image with plugin JARs pre-built into it. Used as a fallback when network-based resolution from `plugins.repositories` isn't appropriate.

|      Chart value       |                       Purpose                       |                                                                             Default                                                                             |
|------------------------|-----------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `plugins.names`        | Comma-separated list of plugin identifiers to load  | `facility-messaging,block-access-service,health,server-status,stream-publisher,stream-subscriber,verification,blocks-file-historic,blocks-file-recent,backfill` |
| `plugins.repositories` | Maven repositories used to resolve plugin artifacts | `central` (`https://repo1.maven.org/maven2`), `sonatype-snapshots` (`https://central.sonatype.com/repository/maven-snapshots`)                                  |
| `plugins.mavenImage`   | Container image used as a fallback plugin source    | `maven:3-eclipse-temurin-25-alpine`                                                                                                                             |

### Third-party plugins (Maven-resolvable)

To add a plugin published to a Maven repository, list its full coordinates under `plugins.thirdParty`:

```yaml
plugins:
  thirdParty:
    - "com.example:my-custom-plugin:1.0.0"
```

Third-party plugins are resolved alongside the default Hiero set using the repositories listed in `plugins.repositories`. To pull from a private repository, append it to `plugins.repositories` with the appropriate `id` and `url`.

### Non-Maven plugins (manual JAR placement)

For plugins not published to a Maven repository, add an init container under `blockNode.initContainers` that places the JAR (and its transitive dependencies) into the plugin directory at pod start. The chart's `values.yaml` carries a commented example using `wget` to download a JAR. Dependencies are **not** auto-resolved on this path — the operator must supply every JAR the plugin needs.

A more robust pattern for fully operator-managed plugins is to mount a pre-populated PVC containing the JAR set; the chart supports this through standard Kubernetes volume mounts. See [Deployment with Selected Plugins](../design/deployment-with-selected-plugins.md) for the design boundary on what the chart resolves versus what the operator supplies.

## Configurations By Plugin

### Cloud Storage Archive Plugin Configuration

| ENV Variable                                       | Description                                                                                                                                                                                              |    Default |
|:---------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------:|
| CLOUD_STORAGE_ARCHIVE_GROUPING_LEVEL               | Files per archive in powers of ten (1=10, 2=100, …, 6=1,000,000).                                                                                                                                        |          5 |
| CLOUD_STORAGE_ARCHIVE_PART_SIZE_MB                 | The size of each multi-part upload part in megabytes. Minimum value is 5, maximum value is 2047                                                                                                          |         10 |
| CLOUD_STORAGE_ARCHIVE_ENDPOINT_URL                 | Endpoint URL for the cloud archive service (e.g., `https://s3.amazonaws.com/`).                                                                                                                          |         "" |
| CLOUD_STORAGE_ARCHIVE_BUCKET_NAME                  | Bucket name where cloud archive files are stored.                                                                                                                                                        |         "" |
| CLOUD_STORAGE_ARCHIVE_OBJECT_KEY_PREFIX            | Optional prefix prepended to every S3 object key (e.g. `blocks`). When set, the full key format is `{prefix}/AAAA/BBBB/CCCC/DDDD/EEE.tar`. Leave empty for no prefix.                                    |         "" |
| CLOUD_STORAGE_ARCHIVE_STORAGE_CLASS                | Storage class (e.g., STANDARD, INTELLIGENT_TIERING, GLACIER, DEEP_ARCHIVE). Values available at [AWS S3 storage classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) | "STANDARD" |
| CLOUD_STORAGE_ARCHIVE_REGION_NAME                  | Region for the cloud archive service (e.g., `us-east-1`).                                                                                                                                                |         "" |
| CLOUD_STORAGE_ARCHIVE_ACCESS_KEY                   | Access key for the archive service.                                                                                                                                                                      |         "" |
| CLOUD_STORAGE_ARCHIVE_SECRET_KEY                   | Secret key for the archive service.                                                                                                                                                                      |         "" |
| CLOUD_STORAGE_ARCHIVE_MAX_CONCURRENT_TEMP_ARCHIVES | Maximum number of temporary archive uploads that may run in parallel. Must be between 1 and 16.                                                                                                          |          4 |
| CLOUD_STORAGE_ARCHIVE_GAP_BUFFER_SIZE              | Number of blocks to buffer when a gap is detected before triggering recovery. Must be between 1 and 10.                                                                                                  |          5 |

### Backfill Plugin Configuration

| ENV Variable                             | Description                                                                                                            |   Default |
|:-----------------------------------------|:-----------------------------------------------------------------------------------------------------------------------|----------:|
| BACKFILL_START_BLOCK                     | First block this BN deploy wants.                                                                                      |         0 |
| BACKFILL_END_BLOCK                       | Max block number, -1 means no limit.                                                                                   |        -1 |
| BACKFILL_BLOCK_NODE_SOURCES_PATH         | File path for BN sources (PBJ JSON `block-nodes.json`).                                                                |        "" |
| BACKFILL_SCAN_INTERVAL                   | Scan interval for gap detection (ms).                                                                                  |     60000 |
| BACKFILL_MAX_RETRIES                     | Max retries to fetch a block.                                                                                          |         3 |
| BACKFILL_INITIAL_RETRY_DELAY             | Initial retry delay (ms), grows linearly.                                                                              |      5000 |
| BACKFILL_FETCH_BATCH_SIZE                | Number of blocks per gRPC call.                                                                                        |        10 |
| BACKFILL_DELAY_BETWEEN_BATCHES           | Delay (ms) between block batches.                                                                                      |      1000 |
| BACKFILL_INITIAL_DELAY                   | Initial delay (ms) before starting backfill.                                                                           |     15000 |
| BACKFILL_PER_BLOCK_PROCESSING_TIMEOUT    | Timeout (ms) to wait for a block batch.                                                                                |      1000 |
| BACKFILL_GRPC_OVERALL_TIMEOUT            | Overall gRPC timeout (connect, read, poll) in ms.                                                                      |     60000 |
| BACKFILL_MAX_INCOMING_BUFFER_SIZE        | Max gRPC incoming buffer size in bytes (min 10 MB, max 300 MB).                                                        | 104857600 |
| BACKFILL_ENABLE_TLS                      | Enable TLS if supported by block-node client.                                                                          |     false |
| BACKFILL_MAX_PROTOBUF_MESSAGE_SIZE_BYTES | Max protobuf message size (bytes) accepted when parsing a block fetched over the wire during backfill.                 | 131072000 |
| BACKFILL_GREEDY                          | When true, searches and retrieves blocks beyond latestAcknowledged to prevent the BN from falling too far behind live. |     false |
| BACKFILL_HISTORICAL_QUEUE_CAPACITY       | Bounded queue capacity for historical block fetch tasks (min 1, max 1000).                                             |        20 |
| BACKFILL_LIVE_TAIL_QUEUE_CAPACITY        | Bounded queue capacity for live-tail block fetch tasks (min 1, max 100).                                               |        10 |
| BACKFILL_HEALTH_PENALTY_PER_FAILURE      | Health score penalty applied per failed fetch attempt to a peer node.                                                  |    1000.0 |
| BACKFILL_MAX_BACKOFF_MS                  | Maximum backoff (ms) before retrying a failed peer node (minimum: 30,000).                                             |   300,000 |

**Note:** The following can be configured in the JSON file at `BACKFILL_BLOCK_NODE_SOURCES_PATH`:
- Per-node gRPC timeout overrides: `grpc_connect_timeout`, `grpc_read_timeout`, `grpc_poll_wait_time`
- Advanced HTTP/2 tuning: `grpc_webclient_tuning`

See [Backfill Plugin Design](../design/backfill-plugin.md#blocknode-sources-configuration-file-structure)
for the JSON schema.

### Block Access Plugin Configuration

| ENV Variable      | Description                                                                                     | Default |
|:------------------|:------------------------------------------------------------------------------------------------|--------:|
| BLOCK_ACCESS_PORT | Dedicated port for the block-access gRPC service. When unset, the service shares `SERVER_PORT`. | (unset) |

### Files Historic Plugin Configuration

| ENV Variable                                       | Description                                                                 |                             Default |
|:---------------------------------------------------|:----------------------------------------------------------------------------|------------------------------------:|
| FILES_HISTORIC_ROOT_PATH                           | Root path for saving historic blocks.                                       | /opt/hiero/block-node/data/historic |
| FILES_HISTORIC_COMPRESSION                         | Compression type (e.g., ZSTD).                                              |                                ZSTD |
| FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS | Files per zip in powers of ten (1=10, 2=100, …, 6=1,000,000).               |                                   4 |
| FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD           | Number of zips to retain. 0 means keep indefinitely.                        |                                   0 |
| FILES_HISTORIC_MAX_FILES_PER_DIR                   | Max files per directory, as a number of digits, to avoid filesystem issues. |                                   3 |

> **Retention arithmetic:** Effective blocks retained = `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD` × 10^`FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS`.
> Example: `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD=5` with `FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS=4` retains 50,000 blocks.
> A value of `0` for `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD` keeps blocks indefinitely.

### Files Recent Plugin Configuration

| ENV Variable                           | Description                                         |                         Default |
|:---------------------------------------|:----------------------------------------------------|--------------------------------:|
| FILES_RECENT_LIVE_ROOT_PATH            | Root path for saving live blocks.                   | /opt/hiero/block-node/data/live |
| FILES_RECENT_COMPRESSION               | Compression type (e.g., ZSTD).                      |                            ZSTD |
| FILES_RECENT_MAX_FILES_PER_DIR         | Max files per directory to avoid filesystem issues. |                               3 |
| FILES_RECENT_BLOCK_RETENTION_THRESHOLD | Block retention count. `0` means keep indefinitely. |                          96,000 |

### Health Plugin Configuration

| ENV Variable | Description                                                                                 | Default |
|:-------------|:--------------------------------------------------------------------------------------------|--------:|
| HEALTH_PORT  | Dedicated port for the health HTTP endpoints. When unset, the service shares `SERVER_PORT`. | (unset) |

### Messaging Plugin Configuration

| ENV Variable                            | Description                                                                               | Default |
|:----------------------------------------|:------------------------------------------------------------------------------------------|--------:|
| MESSAGING_BLOCK_ITEM_QUEUE_SIZE         | Max messages in block item queue. Each batch ~100 items. Must be power of 2.              |     512 |
| MESSAGING_BLOCK_NOTIFICATION_QUEUE_SIZE | Max block notifications queued. Each may hold a full block in memory. Must be power of 2. |      32 |

### Archive Plugin Configuration (S3 Archive)

| ENV Variable            | Description                                                                 |            Default |
|:------------------------|:----------------------------------------------------------------------------|-------------------:|
| ARCHIVE_BLOCKS_PER_FILE | Number of blocks per archive file. Must be a positive power of 10.          |             100000 |
| ARCHIVE_ENDPOINT_URL    | Endpoint URL for the archive service (e.g., `https://s3.amazonaws.com/`).   |                 "" |
| ARCHIVE_BUCKET_NAME     | Bucket name where archive files are stored.                                 | block-node-archive |
| ARCHIVE_BASE_PATH       | Base path inside the bucket for archive files.                              |             blocks |
| ARCHIVE_STORAGE_CLASS   | Storage class (e.g., STANDARD, INTELLIGENT_TIERING, GLACIER, DEEP_ARCHIVE). |           STANDARD |
| ARCHIVE_REGION_NAME     | Region for the archive service (e.g., `us-east-1`).                         |          us-east-1 |
| ARCHIVE_ACCESS_KEY      | Access key for the archive service.                                         |                 "" |
| ARCHIVE_SECRET_KEY      | Secret key for the archive service.                                         |                 "" |

### Server Status Plugin Configuration

| ENV Variable       | Description                                                                                      | Default |
|:-------------------|:-------------------------------------------------------------------------------------------------|--------:|
| SERVER_STATUS_PORT | Dedicated port for the server-status gRPC service. When unset, the service shares `SERVER_PORT`. | (unset) |

### Publisher Plugin Configuration

| ENV Variable                                 | Description                                                                                                                                                                                                                                                                                                                                                 |                   Default |
|:---------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------:|
| PRODUCER_BATCH_FORWARD_LIMIT                 | Max number of blocks to forward in a batch. Must be ≥ 100,000.                                                                                                                                                                                                                                                                                              | 9,223,372,036,854,775,807 |
| PRODUCER_PUBLISHER_UNAVAILABILITY_TIMEOUT    | The time in seconds to wait when we have no active publishers before sending a publisher unavailability timeout status update. Must be ≥ 0.                                                                                                                                                                                                                 |                       300 |
| PRODUCER_STALE_RESEND_PRUNE_BUFFER           | Number of blocks behind `lastPersistedBlockNumber` that a `blocksToResend` entry may sit before `handlePersisted` prunes it. Entries within the buffer are still considered fillable by a publisher; entries older than the buffer are dropped (the gap is owned by backfill at that point). Set to ~CN block-history depth. Must be 0 ≤ value ≤ 200.       |                       100 |
| PRODUCER_FLOW_CONTROL_REFRESH_INTERVAL_NANOS | Duration (ns) between flow-control refresh cycles. The manager resets per-handler message budgets and checks aggregate consumption at this cadence. Must be 100,000 ≤ value ≤ 100,000,000.                                                                                                                                                                  |                10,000,000 |
| PRODUCER_DUPLICATE_BLOCK_SKIP_WINDOW         | Number of blocks behind `lastPersistedBlockNumber` for which a duplicate block header is answered with `SkipBlock` instead of `EndOfStream(DUPLICATE_BLOCK)`. A publisher only slightly behind can fast-forward without reconnecting; duplicates further behind than the window still close the stream so the publisher reconnects. Must be 1 ≤ value ≤ 10. |                         5 |
| PRODUCER_PORT                                | Dedicated port for the publisher gRPC service. When unset, the service shares `SERVER_PORT`.                                                                                                                                                                                                                                                                |                   (unset) |

### RSA Bootstrap Plugin Configuration

| ENV Variable                                             | Description                                                                                  |   Default |
|:---------------------------------------------------------|:---------------------------------------------------------------------------------------------|----------:|
| ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_BASE_URL                | The URL of the mirror node from which to request the node address book.                      |        "" |
| ROSTER_BOOTSTRAP_RSA_MN_INITIAL_QUERY_INTERVAL_MILLIS    | The initial period between queries to the mirror node until a node address book is found.    |      5000 |
| ROSTER_BOOTSTRAP_RSA_MN_SUBSEQUENT_QUERY_INTERVAL_MILLIS | The subsequent period between queries to the mirror node after a node address book is found. |     60000 |
| ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_CONNECT_TIMEOUT_SECONDS | TCP connect timeout when calling the Mirror Node.                                            |         5 |
| ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_READ_TIMEOUT_SECONDS    | Per-request read timeout when calling the Mirror Node.                                       |        10 |
| ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_PAGE_SIZE               | Number of nodes requested per paginated Mirror Node call.                                    |       100 |
| ROSTER_BOOTSTRAP_RSA_BLOCK_NODE_SOURCES_PATH             | File path to the JSON file containing a list of block node servers to query for RSA data.    |        "" |
| ROSTER_BOOTSTRAP_RSA_BN_INITIAL_QUERY_INTERVAL_MILLIS    | The initial period between queries to the block node until a node address book is found.     |      5000 |
| ROSTER_BOOTSTRAP_RSA_BN_SUBSEQUENT_QUERY_INTERVAL_MILLIS | The subsequent period between queries to the block node after a node address book is found.  |     60000 |
| ROSTER_BOOTSTRAP_RSA_GRPC_OVERALL_TIMEOUT                | Overall gRPC timeout (connect, read, poll) in ms.                                            |     60000 |
| ROSTER_BOOTSTRAP_RSA_MAX_INCOMING_BUFFER_SIZE            | Maximum block size used for the BlockNode Client                                             | 104857600 |
| ROSTER_BOOTSTRAP_RSA_ENABLE_TLS                          | Flag indicating whether TLS should be enabled for the BlockNode client.                      |     false |

### Subscriber Plugin Configuration

| ENV Variable                               | Description                                                                                            |   Default |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------|----------:|
| SUBSCRIBER_LIVE_QUEUE_SIZE                 | Queue size (in batches) for transferring live data between messaging and client threads. Must be ≥100. |      4000 |
| SUBSCRIBER_MAXIMUM_FUTURE_REQUEST          | Max blocks ahead of latest "live" block a request can start from. Must be ≥10.                         |      4000 |
| SUBSCRIBER_MINIMUM_LIVE_QUEUE_CAPACITY     | Minimum free capacity in the live queue before dropping oldest blocks. Typically ~10% of queue size.   |       400 |
| SUBSCRIBER_MAX_PROTOBUF_MESSAGE_SIZE_BYTES | Max protobuf message size (bytes) accepted when parsing a block to stream to a subscriber.             | 131072000 |
| SUBSCRIBER_PORT                            | Dedicated port for the subscriber gRPC service. When unset, the service shares `SERVER_PORT`.          |   (unset) |

### TSS Bootstrap Plugin Configuration

| ENV Variable                                  | Description                                                                               |   Default |
|:----------------------------------------------|:------------------------------------------------------------------------------------------|----------:|
| ROSTER_BOOTSTRAP_TSS_BLOCK_NODE_SOURCES_PATH  | File path to the JSON file containing a list of block node servers to query for TSS data. |        "" |
| ROSTER_BOOTSTRAP_TSS_QUERY_PEER_INTERVAL      | The amount of time in milliseconds between queries to the Peer Block Nodes for TSS data.  |     60000 |
| ROSTER_GRPC_OVERALL_TIMEOUT                   | Overall gRPC timeout (connect, read, poll) in ms.                                         |     60000 |
| ROSTER_BOOTSTRAP_TSS_MAX_INCOMING_BUFFER_SIZE | Maximum block size used for the BlockNode Client                                          | 104857600 |
| ROSTER_BOOTSTRAP_TSS_ENABLE_TLS               | Flag indicating whether TLS should be enabled for the BlockNode client.                   |     false |

### Verification Plugin Configuration

| ENV Variable                                        | Description                                                                    |                                                                 Default |
|:----------------------------------------------------|:-------------------------------------------------------------------------------|------------------------------------------------------------------------:|
| VERIFICATION_ALL_BLOCKS_HASHER_ENABLED              | Enable the all-blocks hasher to compute and verify a rolling root hash.        |                                                                   false |
| VERIFICATION_ALL_BLOCKS_HASHER_FILE_PATH            | Path to the persisted root hash file for all previous blocks.                  | /opt/hiero/block-node/application-state/rootHashOfAllPreviousBlocks.bin |
| VERIFICATION_ALL_BLOCKS_HASHER_PERSISTENCE_INTERVAL | How often (in blocks) the hasher persists its state to disk.                   |                                                                      10 |
| VERIFICATION_TSS_PARAMETERS_FILE_PATH               | Path to the persisted TSS parameters file (ledger ID, address book, WRAPS VK). |              /opt/hiero/block-node/application-state/tss-parameters.bin |
| VERIFICATION_DUMP_ENABLED                           | Write failing block bytes and metadata to disk for post-incident diagnostics.  |                                                                   false |
| VERIFICATION_DUMP_DIRECTORY_PATH                    | Directory where bad-block dump files are written.                              |                                /opt/hiero/block-node/verification/dumps |
| VERIFICATION_DUMP_RETENTION_DAYS                    | Number of days to retain dump files before the daily purge removes them.       |                                                                       7 |

> **Note:** `VERIFICATION_ALL_BLOCKS_HASHER_ENABLED` must remain `false` (the default).
> The all-blocks hasher requires a strictly sequential block stream; out-of-order or
> forward-arriving blocks will cause it to fall out of sync and produce incorrect root hashes.

### Cloud Storage Expanded Plugin Configuration

Uploads each verified block as a single ZSTD-compressed `.blk.zstd` object to any
S3-compatible store (AWS S3, GCS S3-interop, MinIO, etc.). The plugin is **disabled by
default** — setting `CLOUD_EXPANDED_ENDPOINT_URL` to a non-empty value activates it.

| ENV Variable                                  | Description                                                                                     |  Default |
|:----------------------------------------------|:------------------------------------------------------------------------------------------------|---------:|
| CLOUD_STORAGE_EXPANDED_ENDPOINT_URL           | S3-compatible endpoint URL. **Blank disables the plugin.**                                      |       "" |
| CLOUD_STORAGE_EXPANDED_BUCKET_NAME            | Name of the S3 bucket where blocks are stored. Required; must not be blank.                     |       "" |
| CLOUD_STORAGE_EXPANDED_OBJECT_KEY_PREFIX      | Prefix prepended to every object key (e.g. `blocks`). Set to `""` for no prefix.                |       "" |
| CLOUD_STORAGE_EXPANDED_STORAGE_CLASS          | S3 storage class for uploaded objects. Must be `STANDARD` for the current bucky-client version. | STANDARD |
| CLOUD_STORAGE_EXPANDED_REGION_NAME            | AWS / S3-compatible region name. Required; must not be blank.                                   |       "" |
| CLOUD_STORAGE_EXPANDED_ACCESS_KEY             | S3 access key (not logged).                                                                     |       "" |
| CLOUD_STORAGE_EXPANDED_SECRET_KEY             | S3 secret key (not logged).                                                                     |       "" |
| CLOUD_STORAGE_EXPANDED_UPLOAD_TIMEOUT_SECONDS | Max seconds per block upload before treating the upload as failed.                              |       60 |

Object keys follow the format `{prefix}/AAAA/BBBB/CCCC/DDDD/EEE.blk.zstd`, where the
19-digit zero-padded block number is split into a 4/4/4/4/3 folder hierarchy:

| Block number | Object key                                |
|:-------------|:------------------------------------------|
| 1            | `blocks/0000/0000/0000/0000/001.blk.zstd` |
| 1 234 567    | `blocks/0000/0000/0000/1234/567.blk.zstd` |
| 108 273 182  | `blocks/0000/0000/0010/8273/182.blk.zstd` |
