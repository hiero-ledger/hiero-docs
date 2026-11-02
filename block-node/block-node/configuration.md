# Hiero Block Node Configuration Document

## Overview

This document outlines configuration options for the Hiero Block Node. Most settings are controlled via environment variables for flexible deployment.

Each plugin has its own properties, but this focuses on core options and core plugins.

## Logging configuration

Application logging is **not** controlled by environment variables. It is configured through a
`java.util.logging` `logging.properties` file (global, per-package, and per-class levels),
selected at startup via `-Djava.util.logging.config.file`. In Kubernetes the level and any
per-package/class overrides are set through `blockNode.logs.level` and
`blockNode.logs.loggingProperties` in the chart values.

See the [Logging Reference](./logging.md) for the format and step-by-step examples of changing
the level, and the [Logging Architecture](../design/architecture/Logging-Architecture.md) for
the design.

## Core Configuration Options

| ENV Variable                      | Description                                                                                 | Default |
|:----------------------------------|:--------------------------------------------------------------------------------------------|--------:|
| BLOCK_NODE_EARLIEST_MANAGED_BLOCK | Earliest block managed by this node. Older blocks may exist but won’t be fetched or stored. |       0 |

### Server Configuration

| ENV Variable                            | Description                                                                                                                                                         | Default     |
|:----------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------|
| SERVER_MAX_MESSAGE_SIZE_BYTES           | Max message size (bytes) for HTTP/2. Also the cumulative byte ceiling for a single block on the publish stream.                                                     | 131,072,000 |
| SERVER_SOCKET_SEND_BUFFER_SIZE_BYTES    | Send buffer size (bytes). `0` lets the kernel autotune it (recommended). Explicit sizes must be at least 32768. Nano sets 131072 (`values-overrides/nano.yaml`).    | 0           |
| SERVER_SOCKET_RECEIVE_BUFFER_SIZE_BYTES | Receive buffer size (bytes). `0` lets the kernel autotune it (recommended). Explicit sizes must be at least 32768. Nano sets 131072 (`values-overrides/nano.yaml`). | 0           |
| SERVER_PORT                             | Default port for all services. Individual plugins may bind to a different port via their own config.                                                                | 40840       |
| SERVER_SHUTDOWN_DELAY_MILLIS            | Delay before shutdown (ms).                                                                                                                                         | 500         |
| SERVER_MAX_TCP_CONNECTIONS              | Max TCP connections allowed.                                                                                                                                        | 1000        |
| SERVER_IDLE_CONNECTION_PERIOD_MINUTES   | Period for idle connections check (minutes).                                                                                                                        | 5           |
| SERVER_IDLE_CONNECTION_TIMEOUT_MINUTES  | Timeout for idle connections (minutes).                                                                                                                             | 30          |
| SERVER_TCP_NO_DELAY                     | Disable Nagle's algorithm (TCP_NODELAY). Reduces latency for small, frequent writes.                                                                                | true        |
| SERVER_BACKLOG_SIZE                     | Maximum length of the queue of incoming connections on the server socket.                                                                                           | 8,192       |
| SERVER_WRITE_QUEUE_LENGTH               | Number of write buffers queued for write operations. HTTP/1.1 only; HTTP/2 (gRPC) frames bypass this queue.                                                         | 8,192       |

With the default `0`, the Block Node does not set the socket buffers, and the kernel autotunes each connection's
buffers up to the maximum in `net.ipv4.tcp_rmem` / `net.ipv4.tcp_wmem` (typically 6 MB / 4 MB on Linux). A positive size
turns off autotuning for that buffer, and the kernel silently caps it at `net.core.rmem_max` / `net.core.wmem_max`
(Linux default 212,992 bytes), which limits every stream to about that much data per round trip. If you set explicit
sizes, raise both sysctls on the host to at least those sizes. The Block Node logs a WARNING at startup when the kernel
grants less than configured. On high-latency links, raise the `tcp_rmem` / `tcp_wmem` maxima too, see
[Host kernel network settings](operations/block-node-hardware-specifications.md#host-kernel-network-settings).

### WebServerHttp2 Configuration

| ENV Variable                          | Description                                                                  |   Default |
|:--------------------------------------|:-----------------------------------------------------------------------------|----------:|
| SERVER_HTTP2_FLOW_CONTROL_TIMEOUT     | Outbound flow control blocking timeout (ms).                                 |    10,000 |
| SERVER_HTTP2_INITIAL_WINDOW_SIZE      | Sender's maximum window size (bytes) for stream-level flow control.          | 8,388,608 |
| SERVER_HTTP2_MAX_CONCURRENT_STREAMS   | Max concurrent streams the server will allow.                                |       100 |
| SERVER_HTTP2_MAX_EMPTY_FRAMES         | Max consecutive empty frames allowed on connection.                          |        10 |
| SERVER_HTTP2_MAX_FRAME_SIZE           | Largest frame payload size (bytes) the sender is willing to receive.         | 8,388,608 |
| SERVER_HTTP2_MAX_HEADER_LIST_SIZE     | Max field section size (bytes) the sender is prepared to accept.             |     8,192 |
| SERVER_HTTP2_MAX_RAPID_RESETS         | Max rapid resets (stream RST sent by client before any data sent by server). |        50 |
| SERVER_HTTP2_RAPID_RESET_CHECK_PERIOD | Period for counting rapid resets (ms).                                       |    10,000 |

### Application State Configuration

| ENV Variable                                 | Description                                                                                                                                                                                                                                                                                                                            |                                Default                                |
|:---------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| APP_STATE_TSS_BOOTSTRAP_FILE_PATH            | Path where [TSS](./glossary.md#tss-hints) data (ledger ID and the informational current Roster) is persisted across restarts.                                                                                                                                                                                                          | /opt/hiero/block-node/application-state/tss-bootstrap-roster.json     |
| APP_STATE_RSA_BOOTSTRAP_FILE_PATH            | Path where the single current RSA node address book is persisted across restarts. Used by single-book deployments that have not yet migrated to the address book history file.                                                                                                                                                         | /opt/hiero/block-node/application-state/rsa-bootstrap-roster.json     |
| APP_STATE_RSA_ADDRESS_BOOK_HISTORY_FILE_PATH | Path to the block-number-keyed RSA address book history file (JSON-encoded `RangedAddressBookHistory`). When present, this file takes precedence over `APP_STATE_RSA_BOOTSTRAP_FILE_PATH` and enables verification of historical [WRBs](./glossary.md#wrb-wrapped-record-block) against the address book that was in effect per block. | /opt/hiero/block-node/application-state/rsa-address-book-history.json |
| APP_STATE_BLOCK_RANGES_FILE_PATH             | Path where the set of available and stored block ranges is persisted. Written every 1,000 blocks received.                                                                                                                                                                                                                             | /opt/hiero/block-node/application-state/block-ranges.json             |
| KNOWN_PUBLISHERS_FILE_PATH                   | Path where connections for known publisher are persisted. Read only on start.                                                                                                                                                                                                                                                          | /opt/hiero/block-node/application-state/known-publishers.json         |
| INBOUND_PARTNERS_FILE_PATH                   | Path where connections for designated inbound partners are persisted. Read only on start.                                                                                                                                                                                                                                              | /opt/hiero/block-node/application-state/inbound-partners.json         |
| OUTBOUND_PARTNERS_FILE_PATH                  | Path where connections for designated outbound partners are persisted. Read only on start.                                                                                                                                                                                                                                             | /opt/hiero/block-node/application-state/outbound-partners.json        |
| APP_STATE_UPDATE_SCAN_INTERVAL               | How often (ms) the application state facility checks whether stored block ranges need persisting. Minimum 100.                                                                                                                                                                                                                         | 500                                                                   |
| APP_STATE_UPDATE_INITIAL_DELAY               | Delay (ms) before the application state facility begins its first scan.                                                                                                                                                                                                                                                                | 0                                                                     |

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

A base Block Node deployment ships with no plugins; the Helm chart downloads them into the container at pod start. The chart's default plugin set is focused on a [Tier 1](./glossary.md#tier-1-block-node) [Local Full History (LFH)](./glossary.md#local-full-history-lfh) profile, enabled via `plugins.names`. The value is a comma-separated string of plugin identifiers:

```yaml
plugins:
  names: "facility-app-state,facility-messaging,block-access-service,health,server-status,stream-publisher,stream-subscriber,block-verification,blocks-file-historic,blocks-file-recent,backfill"
```

> **Note:** All [Tier 2](./glossary.md#tier-2-block-node) Block Nodes should drop `stream-publisher` from `plugins.names`. The presence of `stream-publisher` is the main difference between a Tier 1 and a Tier 2 deployment.
>
> **Upgrading to 0.46.0:** `facility-app-state` is now required. If you set your own `plugins.names`, add `facility-app-state` before upgrading, or the node fails at startup with `No ApplicationStateFacility provided`. If you roll back to an earlier version, remove it again, since earlier versions do not publish that plugin.

To change which plugins load, edit the list. Adding a name causes that plugin to be downloaded and loaded on the next pod start. Removing a name skips it on the next start, but the previously-installed JAR remains in the plugins folder unless the folder is cleared between runs (for example, by recreating the PVC or the volume backing the plugins directory). Operators do not need to rebuild the Block Node image — the chart resolves plugins from configured sources at pod start.

#### Plugin sources

The chart supports two sources for downloading plugins; both are configurable:

- **`plugins.repositories`** (recommended for production): Maven repositories the init container queries to resolve plugin artifacts. Defaults to Maven Central and Sonatype Snapshots. Operators running at scale should add a local mirror to `plugins.repositories` to reduce dependency on public infrastructure.
- **`plugins.mavenImage`** (intended for local and Solo testing): a container image with plugin JARs pre-built into it. Used as a fallback when network-based resolution from `plugins.repositories` isn't appropriate.

|      Chart value       |                       Purpose                       |                                                                                                              Default                                                                                                               |
|------------------------|-----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `plugins.names`        | Comma-separated list of plugin identifiers to load  | `backfill,block-access-service,block-verification,blocks-file-historic,blocks-file-recent,facility-app-state,facility-messaging,health,roster-bootstrap-rsa,roster-bootstrap-tss,server-status,stream-publisher,stream-subscriber` |
| `plugins.repositories` | Maven repositories used to resolve plugin artifacts | `central` (`https://repo1.maven.org/maven2`), `sonatype-snapshots` (`https://central.sonatype.com/repository/maven-snapshots`)                                                                                                     |
| `plugins.mavenImage`   | Container image used as a fallback plugin source    | `maven:3-eclipse-temurin-25-alpine`                                                                                                                                                                                                |

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

`CLOUD_STORAGE_ARCHIVE_ENDPOINT_URL`, `_REGION_NAME`, `_ACCESS_KEY`, `_SECRET_KEY`, and
`_BUCKET_NAME` are all required. If any one of them is blank, the plugin logs a WARNING at
startup naming the empty settings and stays inactive: it registers no notification handler and
archives nothing.

| ENV Variable                                       | Description                                                                                                                                                                                                                                                                                                                           |    Default |
|:---------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------:|
| CLOUD_STORAGE_ARCHIVE_GROUPING_LEVEL               | Files per archive in powers of ten (1=10, 2=100, …, 6=1,000,000).                                                                                                                                                                                                                                                                     |          5 |
| CLOUD_STORAGE_ARCHIVE_PART_SIZE_MB                 | The size of each multi-part upload part in megabytes. Minimum value is 5, maximum value is 2047                                                                                                                                                                                                                                       |         10 |
| CLOUD_STORAGE_ARCHIVE_ENDPOINT_URL                 | Endpoint URL for the cloud archive service (e.g., `https://s3.amazonaws.com/`).                                                                                                                                                                                                                                                       |         "" |
| CLOUD_STORAGE_ARCHIVE_BUCKET_NAME                  | Bucket name where cloud archive files are stored.                                                                                                                                                                                                                                                                                     |         "" |
| CLOUD_STORAGE_ARCHIVE_OBJECT_KEY_PREFIX            | Optional prefix prepended to every S3 object key (e.g. `blocks`). Leave empty for no prefix. See the key format note below.                                                                                                                                                                                                           |         "" |
| CLOUD_STORAGE_ARCHIVE_STORAGE_CLASS                | Storage class (e.g., STANDARD, INTELLIGENT_TIERING, GLACIER, DEEP_ARCHIVE, ARCHIVE). Values available at [AWS S3 storage classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html). `ARCHIVE` is not an AWS class: it is the native Google Cloud Storage class, reached through the S3-compatible API. | "STANDARD" |
| CLOUD_STORAGE_ARCHIVE_REGION_NAME                  | Region for the cloud archive service (e.g., `us-east-1`).                                                                                                                                                                                                                                                                             |         "" |
| CLOUD_STORAGE_ARCHIVE_ACCESS_KEY                   | Access key for the archive service.                                                                                                                                                                                                                                                                                                   |         "" |
| CLOUD_STORAGE_ARCHIVE_SECRET_KEY                   | Secret key for the archive service.                                                                                                                                                                                                                                                                                                   |         "" |
| CLOUD_STORAGE_ARCHIVE_MAX_CONCURRENT_TEMP_ARCHIVES | Maximum number of temporary archive uploads that may run in parallel. Must be between 1 and 16.                                                                                                                                                                                                                                       |          4 |
| CLOUD_STORAGE_ARCHIVE_GAP_BUFFER_SIZE              | Number of blocks to buffer when a gap is detected before triggering recovery. Must be between 1 and 10.                                                                                                                                                                                                                               |          5 |

**Object key format.** An archive key is derived from the first block number of the group, not
from a single block, so it does *not* use the expanded plugin's fixed
`{prefix}/AAAA/BBBB/CCCC/DDDD/EEE` shape. The 19-digit zero-padded group start has its
`CLOUD_STORAGE_ARCHIVE_GROUPING_LEVEL` trailing digits dropped (they are always zero) and the
remainder is split into 4-character segments, so the number of segments varies with the grouping
level:

| Grouping level | Group start | Object key                           |
|:---------------|:------------|:-------------------------------------|
| 2              | 1 200       | `{prefix}/0000/0000/0000/0001/2.tar` |
| 5 (default)    | 1 200 000   | `{prefix}/0000/0000/0000/12.tar`     |

**Temporary objects.** The plugin writes in-progress archives under `{prefix}/tmp/` as
`{firstBlock:019d}.tmp` plus a `.meta` companion, and reads them back at startup to resume an
interrupted group. Bucket lifecycle rules over the prefix must not delete objects under `tmp/`.

### Backfill Plugin Configuration

| ENV Variable                             | Description                                                                                                            |   Default |
|:-----------------------------------------|:-----------------------------------------------------------------------------------------------------------------------|----------:|
| BACKFILL_START_BLOCK                     | First block this BN deploy wants.                                                                                      |         0 |
| BACKFILL_END_BLOCK                       | Max block number, -1 means no limit.                                                                                   |        -1 |
| BACKFILL_BLOCK_NODE_SOURCES_PATH         | File path for BN sources (PBJ JSON `block-nodes.json`).                                                                |        "" |
| BACKFILL_SCAN_INTERVAL                   | Scan interval for gap detection (ms).                                                                                  |     60000 |
| BACKFILL_MAX_RETRIES                     | Max attempts to fetch a block, minimum 1.                                                                              |         3 |
| BACKFILL_INITIAL_RETRY_DELAY             | Initial retry delay (ms), grows linearly.                                                                              |      5000 |
| BACKFILL_FETCH_BATCH_SIZE                | Number of blocks per gRPC call.                                                                                        |        10 |
| BACKFILL_DELAY_BETWEEN_BATCHES           | Delay (ms) between block batches.                                                                                      |      1000 |
| BACKFILL_INITIAL_DELAY                   | Initial delay (ms) before starting backfill.                                                                           |     15000 |
| BACKFILL_PER_BLOCK_PROCESSING_TIMEOUT    | Timeout (ms) to wait for a block batch.                                                                                |      1000 |
| BACKFILL_GRPC_OVERALL_TIMEOUT            | Overall gRPC timeout (connect, read, poll) in ms.                                                                      |     60000 |
| BACKFILL_MAX_INCOMING_BUFFER_SIZE        | Max gRPC incoming buffer size in bytes (min 10 MB, max 300 MB).                                                        | 104857600 |
| BACKFILL_ENABLE_TLS                      | Enable TLS if supported by block-node client.                                                                          |     false |
| BACKFILL_MAX_PROTOBUF_MESSAGE_SIZE_BYTES | Max protobuf message size (bytes) accepted when parsing a block fetched over the wire during backfill.                 | 131072000 |
| BACKFILL_GREEDY                          | When true, searches and retrieves blocks beyond latestAcknowledged to prevent the BN from falling too far behind live. |      true |
| BACKFILL_HISTORICAL_QUEUE_CAPACITY       | Bounded queue capacity for historical block fetch tasks (min 1, max 1000).                                             |        20 |
| BACKFILL_LIVE_TAIL_QUEUE_CAPACITY        | Bounded queue capacity for live-tail block fetch tasks (min 1, max 100).                                               |        10 |
| BACKFILL_HEALTH_PENALTY_PER_FAILURE      | Health score penalty applied per failed fetch attempt to a peer node.                                                  |    1000.0 |
| BACKFILL_MAX_BACKOFF_MS                  | Maximum backoff (ms) before retrying a failed peer node (minimum: 30,000).                                             |   300,000 |

**Note:** The following can be configured in the JSON file at `BACKFILL_BLOCK_NODE_SOURCES_PATH`:
- Per-node gRPC timeout overrides: `grpc_connect_timeout`, `grpc_read_timeout`, `grpc_poll_wait_time`
- Advanced HTTP/2 tuning: `grpc_webclient_tuning`

See [Backfill Plugin Design](../design/backfill-plugin.md#peer-nodes-configuration-json)
for the JSON schema.

### Block Access Plugin Configuration

| ENV Variable      | Description                                                                                     | Default |
|:------------------|:------------------------------------------------------------------------------------------------|--------:|
| BLOCK_ACCESS_PORT | Dedicated port for the block-access gRPC service. When unset, the service shares `SERVER_PORT`. | (unset) |

### Files Historic Plugin Configuration

| ENV Variable                                       | Description                                                                                                     |                             Default |
|:---------------------------------------------------|:----------------------------------------------------------------------------------------------------------------|------------------------------------:|
| FILES_HISTORIC_ROOT_PATH                           | Root path for saving historic blocks.                                                                           | /opt/hiero/block-node/data/historic |
| FILES_HISTORIC_COMPRESSION                         | Compression type (e.g., ZSTD).                                                                                  |                                ZSTD |
| FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS | Files per zip in powers of ten (1=10, 2=100, …, 6=1,000,000).                                                   |                                   4 |
| FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD           | Number of zips to retain. 0 means keep indefinitely.                                                            |                                   0 |
| FILES_HISTORIC_MAX_FILES_PER_DIR                   | Max files per directory, as a number of digits, to avoid filesystem issues.                                     |                                   3 |
| FILES_HISTORIC_STAGED_BLOCK_NOTIFICATIONS_ENABLED  | Send a Persisted Notification for every block as soon as it is staged, instead of once per completed zip batch. |                               false |

> **Retention arithmetic:** Effective blocks retained = `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD` × 10^`FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS`.
> Example: `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD=5` with `FILES_HISTORIC_POWERS_OF_TEN_PER_ZIP_FILE_CONTENTS=4` retains 50,000 blocks.
> A value of `0` for `FILES_HISTORIC_BLOCK_RETENTION_THRESHOLD` keeps blocks indefinitely.
>
> **Staged notifications caveat:** with `FILES_HISTORIC_STAGED_BLOCK_NOTIFICATIONS_ENABLED=true`, a block is not
> retrievable from this plugin until its batch is zipped, so the Persisted Notification can arrive before the
> block is actually accessible.

### Files Recent Plugin Configuration

| ENV Variable                           | Description                                         |                         Default |
|:---------------------------------------|:----------------------------------------------------|--------------------------------:|
| FILES_RECENT_LIVE_ROOT_PATH            | Root path for saving live blocks.                   | /opt/hiero/block-node/data/live |
| FILES_RECENT_COMPRESSION               | Compression type (e.g., ZSTD).                      |                            ZSTD |
| FILES_RECENT_MAX_FILES_PER_DIR         | Max files per directory to avoid filesystem issues. |                               3 |
| FILES_RECENT_BLOCK_RETENTION_THRESHOLD | Block retention count. `0` means keep indefinitely. |                          96,000 |

### Health Plugin Configuration

| ENV Variable | Description                                                                                                                              | Default |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------|--------:|
| HEALTH_PORT  | Dedicated port for the health HTTP endpoints. The Health plugin always runs on its own dedicated web server; this port is always active. |   40983 |

### Messaging Plugin Configuration

| ENV Variable                            | Description                                                                               | Default |
|:----------------------------------------|:------------------------------------------------------------------------------------------|--------:|
| MESSAGING_BLOCK_ITEM_QUEUE_SIZE         | Max messages in block item queue. Each batch ~100 items. Must be power of 2.              |     512 |
| MESSAGING_BLOCK_NOTIFICATION_QUEUE_SIZE | Max block notifications queued. Each may hold a full block in memory. Must be power of 2. |      32 |

### Server Status Plugin Configuration

| ENV Variable       | Description                                                                                      | Default |
|:-------------------|:-------------------------------------------------------------------------------------------------|--------:|
| SERVER_STATUS_PORT | Dedicated port for the server-status gRPC service. When unset, the service shares `SERVER_PORT`. | (unset) |

### Throttle Configuration

The `serverStatus` and `serverStatusDetail` gRPC methods are throttled per client and node-wide. All keys have defaults.

#### Global limits (prefix `throttle.global.`)

| Config Key                      | Description                                                                                                           | Default |
|:--------------------------------|:----------------------------------------------------------------------------------------------------------------------|--------:|
| serverStatusMaxConcurrent       | Node-wide ceiling on concurrent in-flight `serverStatus` calls across all clients.                                    |   1,000 |
| serverStatusDetailMaxConcurrent | Node-wide ceiling on concurrent in-flight `serverStatusDetail` calls across all clients.                              |   1,000 |
| clientStateTtlMinutes           | How long a throttled method keeps a client's rate and concurrency state after its last call before it can be evicted. |      30 |
| clientStateSweepIntervalMinutes | How often the sweep that evicts stale client state runs.                                                              |       5 |

#### Per-method limits (prefixes `throttle.serverStatus.` and `throttle.serverStatusDetail.`)

The same four keys apply under each prefix.

| Config Key             | Description                                                                                                                                                                                                                                                                                                          |                                        Default |
|:-----------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------:|
| ratePerSecond          | Sustained requests per second allowed per client.                                                                                                                                                                                                                                                                    |                                             50 |
| burstTolerance         | How many pacing intervals early a request may arrive and still be admitted.                                                                                                                                                                                                                                          |                                             20 |
| maxConcurrentPerClient | Concurrent in-flight calls allowed per client.                                                                                                                                                                                                                                                                       |                                              5 |
| maxPermitHoldSeconds   | Age after which a concurrency permit that is still held is force-released by a background sweep. This only corrects the throttle's accounting for calls whose connection dropped before completing. It never terminates a call. The sweep runs about every half of the smallest configured value (minimum 1 second). | 10 (`serverStatus`), 60 (`serverStatusDetail`) |

#### When a client is rejected

The client receives gRPC status `RESOURCE_EXHAUSTED` with a reason string: rate limit exceeded, per-client concurrency limit reached, or node-wide concurrency limit reached. The matching `outcome` label on the `throttle_calls` metric increments. See [metrics](./metrics.md).

#### Operator note: client identity

Client identity is the remote IP address. Behind a Kubernetes LoadBalancer Service with the default `externalTrafficPolicy: Cluster`, source IPs are SNATed, so all external clients share one key and therefore one per-client budget. Set `externalTrafficPolicy: Local` or raise the per-client limits. The helm chart has no setting for this today.

The opposite holds for IPv6: one client usually controls a whole /64, so it can rotate addresses to get fresh per-client budgets, and the client-state table has no size cap. Subnet-level limits are tracked in [#3573](https://github.com/hiero-ledger/hiero-block-node/issues/3573).

### Publisher Plugin Configuration

| ENV Variable                                 | Description                                                                                                                                                                                                                                                                                                                                                                     |                   Default |
|:---------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------:|
| PRODUCER_BATCH_FORWARD_LIMIT                 | Max number of blocks to forward in a batch. Must be ≥ 100,000.                                                                                                                                                                                                                                                                                                                  | 9,223,372,036,854,775,807 |
| PRODUCER_PUBLISHER_UNAVAILABILITY_TIMEOUT    | The time in seconds to wait when we have no active publishers before sending a publisher unavailability timeout status update. Must be ≥ 0.                                                                                                                                                                                                                                     |                       300 |
| PRODUCER_STALE_RESEND_PRUNE_BUFFER           | Number of blocks behind `lastPersistedBlockNumber` that a `blocksToResend` entry may sit before `handlePersisted` prunes it. Entries within the buffer are still considered fillable by a publisher; entries older than the buffer are dropped (the gap is owned by [backfill](./glossary.md#backfill) at that point). Set to ~CN block-history depth. Must be 0 ≤ value ≤ 200. |                       100 |
| PRODUCER_FLOW_CONTROL_REFRESH_INTERVAL_NANOS | Duration (ns) between flow-control refresh cycles. The manager resets per-handler message budgets and checks aggregate consumption at this cadence. Must be 100,000 ≤ value ≤ 100,000,000.                                                                                                                                                                                      |                10,000,000 |
| PRODUCER_DUPLICATE_BLOCK_SKIP_WINDOW         | Number of blocks behind `lastPersistedBlockNumber` for which a duplicate block header is answered with `SkipBlock` instead of `EndOfStream(DUPLICATE_BLOCK)`. A publisher only slightly behind can fast-forward without reconnecting; duplicates further behind than the window still close the stream so the publisher reconnects. Must be 1 ≤ value ≤ 10.                     |                         5 |
| PRODUCER_PORT                                | Dedicated port for the publisher gRPC service. When unset, the service shares `SERVER_PORT`.                                                                                                                                                                                                                                                                                    |                   (unset) |

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

| ENV Variable                                       | Description                                                                                            |   Default |
|:---------------------------------------------------|:-------------------------------------------------------------------------------------------------------|----------:|
| SUBSCRIBER_LIVE_QUEUE_SIZE                         | Queue size (in batches) for transferring live data between messaging and client threads. Must be ≥100. |      4000 |
| SUBSCRIBER_MAXIMUM_FUTURE_REQUEST                  | Max blocks ahead of latest "live" block a request can start from. Must be ≥10.                         |      4000 |
| SUBSCRIBER_MINIMUM_LIVE_QUEUE_CAPACITY             | Minimum free capacity in the live queue before dropping oldest blocks. Typically ~10% of queue size.   |       400 |
| SUBSCRIBER_MAX_PROTOBUF_MESSAGE_SIZE_BYTES         | Max protobuf message size (bytes) accepted when parsing a block to stream to a subscriber.             | 131072000 |
| SUBSCRIBER_PORT                                    | Dedicated port for the subscriber gRPC service. When unset, the service shares `SERVER_PORT`.          |   (unset) |
| SUBSCRIBER_COMPLETED_SESSION_REAP_INTERVAL_SECONDS | How often the background reaper drains completed subscriber sessions, independent of new traffic.      |         5 |

### TSS Bootstrap Plugin Configuration

| ENV Variable                                  | Description                                                                               |   Default |
|:----------------------------------------------|:------------------------------------------------------------------------------------------|----------:|
| ROSTER_BOOTSTRAP_TSS_BLOCK_NODE_SOURCES_PATH  | File path to the JSON file containing a list of block node servers to query for TSS data. |        "" |
| ROSTER_BOOTSTRAP_TSS_QUERY_PEER_INTERVAL      | The amount of time in milliseconds between queries to the Peer Block Nodes for TSS data.  |     60000 |
| ROSTER_GRPC_OVERALL_TIMEOUT                   | Overall gRPC timeout (connect, read, poll) in ms.                                         |     60000 |
| ROSTER_BOOTSTRAP_TSS_MAX_INCOMING_BUFFER_SIZE | Maximum block size used for the BlockNode Client                                          | 104857600 |
| ROSTER_BOOTSTRAP_TSS_ENABLE_TLS               | Flag indicating whether TLS should be enabled for the BlockNode client.                   |     false |

### Verification Plugin Configuration

| ENV Variable                                      | Description                                                                                                                     |                                  Default |
|:--------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------:|
| VERIFICATION_RECENTLY_VERIFIED_BLOCKS_BUFFER_SIZE | Size of recently verified blocks buffer. Affects informational failures.                                                        |                                      100 |
| VERIFICATION_ACTIVE_SESSIONS_BUFFER_SIZE          | Size of active active sessions allowed simultaneously.                                                                          |                                      100 |
| VERIFICATION_FIRST_ORDERED_BLOCK                  | The first block number that requires the strict ordering guarantee. All blocks below this number will report immediate success. |                                        0 |
| VERIFICATION_ALL_SOURCES_REQUIRE_ORDERING         | Flag, indicating if all sources require the strict ordering guarantee on the last verified block high watermark.                |                                     true |
| VERIFICATION_DUMP_ENABLED                         | Flag, indicating if the bad block dumper is enabled. (Write blocks that failed verification to disk)                            |                                    false |
| VERIFICATION_DUMP_DIRECTORY_PATH                  | Root path to be used by the bad block dumper.                                                                                   | /opt/hiero/block-node/verification/dumps |
| VERIFICATION_DUMP_RETENTION_DAYS                  | How many days dumped blocks will be retained for.                                                                               |                                        7 |

> _NOTE_ the `VERIFICATION_ALL_SOURCES_REQUIRE_ORDERING` value should remain `true` in all "Tier 1" Block Nodes.
>
> _NOTE_ even if the `VERIFICATION_ALL_SOURCES_REQUIRE_ORDERING` value is set to false, strict ordering will always
> be imposed for the `PUBLISHER` source. In that scenario, it is possible that gaps can happen, because sources,
> other than `PUBLISHER`, can supply a valid block, much higher than last verified. This concern is understood and
> accepted.
>
> _NOTE_ the TSS library that verifies TSS block proofs has no configuration. At start it extracts its native libraries
> (a few MB) into the directory named by the `java.io.tmpdir` system property (`/tmp` by default), which must be
> writable and allow executing mapped files; the Block Node shuts down when the library cannot load. See
> [TSS Block Proof Verification](../design/tss-block-proof-verification.md#start-up-warm-up).

### Verification Plugin Configuration (DEPRECATED)

| ENV Variable                                        | Description                                                                    |                                                                 Default |
|:----------------------------------------------------|:-------------------------------------------------------------------------------|------------------------------------------------------------------------:|
| VERIFICATION_ALL_BLOCKS_HASHER_ENABLED              | Enable the all-blocks hasher to compute and verify a rolling root hash.        |                                                                   false |
| VERIFICATION_ALL_BLOCKS_HASHER_FILE_PATH            | Path to the persisted root hash file for all previous blocks.                  | /opt/hiero/block-node/application-state/rootHashOfAllPreviousBlocks.bin |
| VERIFICATION_ALL_BLOCKS_HASHER_PERSISTENCE_INTERVAL | How often (in blocks) the hasher persists its state to disk.                   |                                                                      10 |
| VERIFICATION_TSS_PARAMETERS_FILE_PATH               | Not read: the TSS data is kept at `APP_STATE_TSS_BOOTSTRAP_FILE_PATH` instead. |              /opt/hiero/block-node/application-state/tss-parameters.bin |
| VERIFICATION_DUMP_ENABLED                           | Write failing block bytes and metadata to disk for post-incident diagnostics.  |                                                                   false |
| VERIFICATION_DUMP_DIRECTORY_PATH                    | Directory where bad-block dump files are written.                              |                                /opt/hiero/block-node/verification/dumps |
| VERIFICATION_DUMP_RETENTION_DAYS                    | Number of days to retain dump files before the daily purge removes them.       |                                                                       7 |

> **Note:** `VERIFICATION_ALL_BLOCKS_HASHER_ENABLED` must remain `false` (the default).
> The all-blocks hasher requires a strictly sequential block stream; out-of-order or
> forward-arriving blocks will cause it to fall out of sync and produce incorrect root hashes.

### Cloud Storage Expanded Plugin Configuration

Uploads each [verified block](./glossary.md#verified-block) as a single ZSTD-compressed `.blk.zstd` object to any
S3-compatible store (AWS S3, GCS S3-interop, MinIO, etc.). The plugin is loaded only when
`cloud-storage-expanded` appears in `plugins.names`; an endpoint URL alone does not activate it.
Once loaded, it needs all five required settings below; a blank one leaves it loaded but
inactive, logging a WARNING at startup and uploading nothing.

| ENV Variable                                    | Description                                                                                                                                                                |  Default |
|:------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|
| CLOUD_STORAGE_EXPANDED_ENDPOINT_URL             | S3-compatible endpoint URL. Required; must not be blank.                                                                                                                   |       "" |
| CLOUD_STORAGE_EXPANDED_BUCKET_NAME              | Name of the S3 bucket where blocks are stored. Required; must not be blank.                                                                                                |       "" |
| CLOUD_STORAGE_EXPANDED_OBJECT_KEY_PREFIX        | Prefix prepended to every object key (e.g. `blocks`). Set to `""` for no prefix.                                                                                           |       "" |
| CLOUD_STORAGE_EXPANDED_STORAGE_CLASS            | S3 storage class for uploaded objects. Must be `STANDARD` for the current bucky-client version.                                                                            | STANDARD |
| CLOUD_STORAGE_EXPANDED_REGION_NAME              | AWS / S3-compatible region name. Required; must not be blank.                                                                                                              |       "" |
| CLOUD_STORAGE_EXPANDED_ACCESS_KEY               | S3 access key (not logged). Required; must not be blank.                                                                                                                   |       "" |
| CLOUD_STORAGE_EXPANDED_SECRET_KEY               | S3 secret key (not logged). Required; must not be blank.                                                                                                                   |       "" |
| CLOUD_STORAGE_EXPANDED_UPLOAD_TIMEOUT_SECONDS   | Max seconds `stop()` waits for in-flight uploads to drain. There is no per-upload timeout.                                                                                 |       60 |
| CLOUD_STORAGE_EXPANDED_RETRY_ENABLED            | Hold failed uploads in memory and retry them in the background instead of failing immediately. Never disk-backed.                                                          |     true |
| CLOUD_STORAGE_EXPANDED_RETRY_INTERVAL_SECONDS   | Period of the background retry tick, and the per-block backoff after a failed attempt. Each tick re-attempts only the blocks whose backoff has elapsed.                    |       10 |
| CLOUD_STORAGE_EXPANDED_RETRY_MAX_AGE_SECONDS    | Maximum time a block may remain buffered for retry before it is dropped as a terminal failure.                                                                             |       60 |
| CLOUD_STORAGE_EXPANDED_RETRY_MAX_PENDING_BLOCKS | Maximum number of blocks held in the in-memory retry buffer at once. At capacity a new failure evicts the longest-buffered block, which is reported as a terminal failure. |       30 |

Object keys follow the format `{prefix}/AAAA/BBBB/CCCC/DDDD/EEE.blk.zstd`, where the
19-digit zero-padded block number is split into a 4/4/4/4/3 folder hierarchy:

| Block number | Object key                                |
|:-------------|:------------------------------------------|
| 1            | `blocks/0000/0000/0000/0000/001.blk.zstd` |
| 1 234 567    | `blocks/0000/0000/0000/1234/567.blk.zstd` |
| 108 273 182  | `blocks/0000/0000/0010/8273/182.blk.zstd` |
