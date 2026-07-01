# Cloud Storage Expanded Plugin

## Table of Contents

1. [Purpose](#purpose)
2. [Goals](#goals)
3. [Terms](#terms)
4. [Entities](#entities)
5. [Design](#design)
6. [Diagram](#diagram)
7. [Configuration](#configuration)
8. [Metrics](#metrics)
9. [Exceptions](#exceptions)
10. [Acceptance Tests](#acceptance-tests)

## Purpose

The `cloud-storage-expanded` plugin (CSEP) uploads each individually-verified block as a compressed
`.blk.zstd` object directly to any S3-compatible object store (AWS S3, GCS via S3-interop,
etc.). Unlike the previous `s3-archive` plugin, which batched blocks into large tar
archives, this plugin uploads **one block per S3 object** — making individual blocks
immediately queryable and suitable for consumers that need block-level granularity in the
cloud.

## Goals

* The plugin must store every block, as received, after verification.
* The plugin must store each verified block as a single ZStandard-compressed file using ZSTD-compressed
  Protobuf encoding.
* The plugin must adhere to a file pattern as defined below.
* The plugin must store all blocks as files or objects in a cloud storage system.
* The plugin must not report success until data is stored such that it can be
  recovered if the local system fails unexpectedly, including a failure that
  results in complete and unrecoverable loss of all local storage.
* The plugin must support any S3-compatible store (AWS S3, GCS S3-interop, etc)
  backed by the `com.hedera.bucky:bucky-client` library.

## Terms

<dl>
  <dt>Cloud Storage</dt>
  <dd>Any storage API that stores data remotely with very high
      availability and reliability. Multiple such APIs may be supported
      by the plugin and controlled by configuration.<br/>
      An example of a common cloud storage API is S3 storage.</dd>

  <dt>S3-compatible object store</dt>
  <dd>Any storage service that implements the AWS S3 REST API, including AWS S3 and Google Cloud
      Storage (via S3 interoperability).</dd>

  <dt>Object key</dt>
  <dd>The full path of an object within an S3 bucket, e.g.
      <code>blocks/0000/0000/0000/0000/001.blk.zstd</code>.</dd>

  <dt>ZSTD_PROTOBUF</dt>
  <dd>The block encoding that serialises a block as Protobuf then ZSTD-compresses it. This is
      the canonical on-disk and in-cloud format.</dd>

  <dt>bucky-client</dt>
  <dd><code>com.hedera.bucky:bucky-client</code> — the Hedera S3 client library on Maven
      Central. Provides <code>com.hedera.bucky.S3Client</code> (a final concrete class) and
      the exception hierarchy <code>S3ClientException</code> →
      <code>S3ClientInitializationException</code> / <code>S3ResponseException</code>.
      These types are an implementation detail confined to <code>BuckyS3UploadClient</code>;
      no other class in the package imports them.</dd>

  <dt>S3UploadClient</dt>
  <dd>Package-private interface that abstracts the S3 upload operation. It exposes only
      <code>uploadFile(...)</code> and <code>close()</code>, throwing
      <code>UploadException</code> or <code>IOException</code> — no bucky types.
      Unit tests implement it directly to capture calls or simulate failures without requiring
      a real S3 endpoint or a mocking framework.</dd>

  <dt>BuckyS3UploadClient</dt>
  <dd>Package-private final class that is the sole production implementation of
      <code>S3UploadClient</code>. Wraps <code>com.hedera.bucky.S3Client</code> and
      translates all bucky exceptions (<code>S3ClientInitializationException</code>,
      <code>S3ClientException</code>) into <code>UploadException</code> at the boundary
      so that bucky is fully contained within this one class.</dd>

  <dt>UploadException</dt>
  <dd>Package-private checked exception thrown by <code>S3UploadClient.uploadFile</code>
      and by the <code>BuckyS3UploadClient</code> constructor to signal an S3 service
      error (auth failure, 4xx/5xx response, or initialisation failure). Distinguished
      from <code>IOException</code>, which signals a transport-level failure. Callers use
      this distinction to set <code>UploadStatus.S3_ERROR</code> vs
      <code>UploadStatus.IO_ERROR</code>.</dd>
</dl>

## Entities

### `S3UploadClient` (interface)

Package-private interface in `org.hiero.block.node.cloud.storage.expanded`. Defines the
upload contract used by the rest of the package. Exposes:

- `uploadFile(objectKey, storageClass, Iterator<byte[]> content, contentType)` — throws
  `UploadException, IOException`
- `close()` (from `AutoCloseable`)

The sole production implementation is `BuckyS3UploadClient`, instantiated directly in
`ExpandedCloudStoragePlugin.start()`. Tests implement `S3UploadClient` directly, never
importing bucky types.

### `BuckyS3UploadClient` (concrete class)

Package-private final class. The only class in the package that imports
`com.hedera.bucky.*`. Wraps `com.hedera.bucky.S3Client` and provides the translation
boundary:

- Constructor: catches `S3ClientInitializationException` → rethrows as `UploadException`.
- `uploadFile(...)`: delegates to `bucky.uploadFile(...)`; catches `S3ClientException`
  → rethrows as `UploadException`.
- `close()`: delegates to `bucky.close()`.

Having a named concrete class (rather than an anonymous inner class) makes it visible by
name in stack traces and heap dumps.

### `UploadException`

Package-private checked exception. Wraps any bucky S3 error (initialisation failure,
service error, HTTP error response) so that the rest of the package is decoupled from
bucky's exception hierarchy. Always wraps the original cause for diagnostics.

### `SingleBlockStoreTask`

`Callable<UploadResult>` submitted per block to the `CompletionService`. Responsible for:
1. Serialising the block to Protobuf bytes via `BlockUnparsed.PROTOBUF.write(block, streamingData)`
written into a `ByteArrayOutputStream`.
2. Compressing to ZSTD (`CompressionType.ZSTD.compress(...)`).
3. Uploading via `S3UploadClient.uploadFile()` directly, relying on S3 SDK connection/socket timeouts.

Returns `UploadResult(blockNumber, status, bytesUploaded, blockSource, uploadDurationNs)`.
The `uploadDurationNs` field records wall-clock time of the upload call in nanoseconds, used
to populate the latency metric. Failures (`UploadException`, `IOException`) are captured as
`succeeded=false` and `bytesUploaded=0` so the `CompletionService` always receives a result —
exceptions never propagate to the caller.

The `UploadStatus` enum distinguishes failure types:

|       Status        |                     Cause                      |
|---------------------|------------------------------------------------|
| `SUCCESS`           | Upload completed successfully                  |
| `S3_ERROR`          | `UploadException` (S3 service or auth error)   |
| `IO_ERROR`          | `IOException` (transport / socket error)       |
| `COMPRESSION_ERROR` | Compressed bytes were empty (should not occur) |

### `ExpandedCloudStorageConfig`

`@ConfigData("cloud.storage.expanded")` record carrying all plugin settings. The
`storageClass` field is typed as `StorageClass` (an enum), which causes the config
framework to reject unknown values at startup. The `uploadTimeoutSeconds` field carries
`@Min(1)` for framework-level range validation.

### `ExpandedCloudStoragePlugin`

Implements `BlockNodePlugin` and `BlockNotificationHandler`. Listens for
`VerificationNotification`, builds the S3 object key, and submits one `SingleBlockStoreTask`
per verified block to a `CompletionService` backed by a virtual-thread executor.

The notification handler is always registered during `init()`. If `start()` fails to create
the S3 client (blank endpoint URL, bad credentials, unreachable endpoint), `s3Client` remains
`null` and all `handleVerification` calls are no-ops for the duration of the process
(`completionService` is always created regardless).

## Design

### Trigger: `VerificationNotification`

The plugin registers as a `BlockNotificationHandler` and reacts to `VerificationNotification`
events. Block bytes are taken directly from `notification.block()`, eliminating any dependency
on the local historical block provider and allowing cloud upload to run in parallel with local
file storage.

### Upload flow (`handleVerification`)

1. **Guard**: log TRACE and return if `s3Client == null` (plugin inactive — S3 client failed to
   initialise).
2. **Guard**: `notification.success() == false` → skip (log TRACE).
3. **Guard**: `notification.blockNumber() < 0` → skip (log INFO).
4. **Guard**: `notification.block() == null` → skip (log INFO).
5. **Drain**: poll `CompletionService` for any previously completed upload tasks; publish a
   `PersistedNotification` for each result (success or failure).
6. Build object key using `buildBlockObjectKey(blockNumber)`.
7. Submit `SingleBlockStoreTask` to the `CompletionService`.

Inside `SingleBlockStoreTask.call()`:
- Record `uploadStartNs = System.nanoTime()`.
- Serialise and ZSTD-compress the block bytes.
- Upload via `S3UploadClient.uploadFile()` directly.
- Return `UploadResult(blockNumber, status, bytesUploaded, blockSource, uploadDurationNs)`.

### Shutdown drain (`stop`)

`stop()` unregisters from block notifications, then shuts down the virtual-thread executor
and waits up to `uploadTimeoutSeconds` for in-flight uploads to complete:

- `virtualThreadExecutor.shutdown()` — stops accepting new tasks (none expected since
  notification handling was unregistered above).
- `virtualThreadExecutor.awaitTermination(uploadTimeoutSeconds, SECONDS)` — blocks until all
  submitted tasks finish or the timeout elapses.
- A final non-blocking `drainCompletedTasks()` sweep publishes results for any tasks that
  completed during the wait.

After draining, `s3Client.close()` is called and the reference cleared.

### Processing and publishing results

Results flow through two methods:

**`processCompletedFuture(future)`** — called per drained future:
1. If the future was cancelled (expected during shutdown): logs TRACE and skips.
2. Otherwise calls `future.get()` and stages the `UploadResult` in `pendingPublish` keyed by
block number.
3. On `ExecutionException` (unexpected `RuntimeException` escaped the task): increments
`uploadFailuresTotal` and logs WARNING. No `PersistedNotification` is sent for this case.

**`publishResult(result)`** — called per staged result in ascending block-number order:
1. Publishes `PersistedNotification(blockNumber, succeeded, 0, blockSource)`.
2. On failure: increments `uploadFailuresTotal`, logs INFO.
3. On success: increments `uploadsTotal` and `uploadBytesTotal` by `bytesUploaded`.
4. Always increments `uploadLatencyNs` by `uploadDurationNs`.

### Object key format

```
{objectKeyPrefix}/AAAA/BBBB/CCCC/DDDD/EEE.blk.zstd
```

The 19-digit zero-padded block number is split into four 4-digit folder groups plus a 3-digit
leaf (4/4/4/4/3) for lexicographic ordering and S3 prefix partitioning.

| Block number |                Object key                 |
|--------------|-------------------------------------------|
| 1            | `blocks/0000/0000/0000/0000/001.blk.zstd` |
| 1 234 567    | `blocks/0000/0000/0000/1234/567.blk.zstd` |
| 108 273 182  | `blocks/0000/0000/0010/8273/182.blk.zstd` |

If `objectKeyPrefix` is blank, the hierarchy key is used bare (no leading `/`).

Zero-padding is computed via integer division to produce each segment directly (no string
formatting of the full 19-digit number):

```java
long seg1 = blockNumber / 1_000_000_000_000_000L;
long seg2 = blockNumber / 100_000_000_000L % 10_000L;
long seg3 = blockNumber / 10_000_000L        % 10_000L;
long seg4 = blockNumber / 1_000L             % 10_000L;
long seg5 = blockNumber                      % 1_000L;
```

### Misconfiguration handling

If `cloud.storage.expanded.endpointUrl` is blank or the S3 client fails to initialise at
startup (e.g. invalid credentials, unreachable endpoint), `BuckyS3UploadClient`'s
constructor throws `UploadException`. The plugin catches this in `start()`, logs a WARNING,
and `s3Client` remains `null` — all `handleVerification` calls are no-ops for the duration
of the process. `completionService` and `metricsHolder` are still created normally.

**Intent**: once per-plugin health checks are supported, a misconfigured plugin should be
marked **UNHEALTHY** and surfaced appropriately rather than silently degrading.

## Diagram

### Upload sequence

```mermaid
sequenceDiagram
    participant MF as MessagingFacility
    participant ECS as ExpandedCloudStoragePlugin
    participant CS as CompletionService
    participant Task as SingleBlockStoreTask
    participant S3 as S3UploadClient
    participant Bucky as BuckyS3UploadClient
    participant Store as S3-Compatible Store

    MF->>ECS: handleVerification(VerificationNotification)
    ECS->>ECS: check s3Client != null
    ECS->>ECS: check success() && blockNumber >= 0 && block != null
    ECS->>CS: drain completed tasks → publish PersistedNotifications
    ECS->>CS: submit(SingleBlockStoreTask)
    CS->>Task: call()
    Task->>Task: serialize + compress block to ZSTD
    Task->>S3: uploadFile(objectKey, storageClass, bytes, contentType)
    S3->>Bucky: uploadFile(...)
    Bucky->>Store: multipart PUT
    Store-->>Bucky: 200 OK
    Bucky-->>S3: (success)
    S3-->>Task: (success)
    Task-->>CS: UploadResult(blockNumber, SUCCESS, bytesUploaded, blockSource, durationNs)
```

### Class relationships

```mermaid
classDiagram
    class S3UploadClient {
        <<interface>>
        +uploadFile(objectKey, storageClass, content, contentType) throws UploadException IOException
        +close()
    }
    class BuckyS3UploadClient {
        -bucky: com.hedera.bucky.S3Client
        +BuckyS3UploadClient(config) throws UploadException
        +uploadFile(...) throws UploadException IOException
        +close()
    }
    class UploadException {
        <<checked exception>>
        +UploadException(message, cause)
    }
    class ExpandedCloudStoragePlugin {
        -s3Client: S3UploadClient
        -config: ExpandedCloudStorageConfig
        -completionService: CompletionService
        -virtualThreadExecutor: ExecutorService
        -pendingPublish: ConcurrentSkipListMap
        -metricsHolder: MetricsHolder
        +init(context, serviceBuilder)
        +start()
        +stop()
        +handleVerification(notification)
        +buildBlockObjectKey(blockNumber) String
        ~drainCompletedTasks()
        -processCompletedFuture(future)
        -publishResult(result)
    }
    class SingleBlockStoreTask {
        -blockNumber: long
        -block: BlockUnparsed
        -s3Client: S3UploadClient
        -objectKey: String
        -storageClass: String
        +call() UploadResult
    }
    class UploadResult {
        +blockNumber: long
        +status: UploadStatus
        +bytesUploaded: long
        +blockSource: BlockSource
        +uploadDurationNs: long
        +succeeded() boolean
    }
    S3UploadClient <|.. BuckyS3UploadClient
    BuckyS3UploadClient ..> UploadException : throws
    ExpandedCloudStoragePlugin --> S3UploadClient
    ExpandedCloudStoragePlugin --> SingleBlockStoreTask : submits
    SingleBlockStoreTask --> S3UploadClient
    SingleBlockStoreTask --> UploadResult : returns
    ExpandedCloudStoragePlugin --> ExpandedCloudStorageConfig
```

## Configuration

All properties are under the `cloud.storage.expanded` namespace.

|                   Property                    |  Default   |                                              Description                                              |
|-----------------------------------------------|------------|-------------------------------------------------------------------------------------------------------|
| `cloud.storage.expanded.endpointUrl`          | `""`       | S3-compatible endpoint URL. **Required. Blank value causes plugin to log a WARNING and be inactive.** |
| `cloud.storage.expanded.bucketName`           | `""`       | Name of the S3 bucket. Required when plugin is active.                                                |
| `cloud.storage.expanded.objectKeyPrefix`      | `""`       | Prefix prepended to every object key. Set to empty string for no prefix.                              |
| `cloud.storage.expanded.storageClass`         | `STANDARD` | S3 storage class (`STANDARD`). Validated as enum at startup.                                          |
| `cloud.storage.expanded.regionName`           | `""`       | AWS / S3-compatible region. Required when plugin is active.                                           |
| `cloud.storage.expanded.accessKey`            | `""`       | S3 access key (not logged). Leave blank to use env vars or IAM role.                                  |
| `cloud.storage.expanded.secretKey`            | `""`       | S3 secret key (not logged). Leave blank to use env vars or IAM role.                                  |
| `cloud.storage.expanded.uploadTimeoutSeconds` | `60`       | Max seconds to wait for in-flight uploads during `stop()`. Min value: 1.                              |

### Credential options

Three strategies are supported, in priority order:

1. **Config properties** — set `cloud.storage.expanded.accessKey` and `cloud.storage.expanded.secretKey`
   directly. Use `${CLOUD_EXPANDED_ACCESS_KEY}` in the value to avoid embedding credentials
   in config files on disk (Swirlds Config supports environment-variable substitution).
2. **Environment variables** — if `accessKey` and `secretKey` are blank, the underlying
   S3 client falls back to: `CLOUD_EXPANDED_ACCESS_KEY` / `CLOUD_EXPANDED_SECRET_KEY`.
3. **IAM / instance role** — leave both fields blank and attach an IAM role with
   `s3:PutObject` on the bucket. Recommended for cloud-native deployments
   (EC2 / ECS / GKE Workload Identity).

## Metrics

All counters are registered under the `hiero_block_node` Prometheus category via
`MetricsHolder.createMetrics(MetricRegistry)` in `start()`. Each counter uses the
`org.hiero.metrics.LongCounter` / `MetricKey` API.

|              Metric name               |                                   Description                                    |
|----------------------------------------|----------------------------------------------------------------------------------|
| `cloud_expanded_total_uploads`         | Number of blocks successfully uploaded to S3-compatible storage.                 |
| `cloud_expanded_total_upload_failures` | Number of block uploads that failed (S3 error, timeout, compression error).      |
| `cloud_expanded_total_upload_bytes`    | Total compressed bytes successfully uploaded to S3-compatible storage.           |
| `cloud_expanded_upload_latency_ns`     | Total wall-clock time spent in upload calls, in nanoseconds (success + failure). |

Counters are registered in `start()`. If `start()` fails (e.g., S3 client creation error),
`metricsHolder` remains `null` and no counters are registered.

## Exceptions

|              Exception              |              Source               |                                                                         Handling                                                                          |
|-------------------------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `UploadException`                   | `S3UploadClient.uploadFile`       | Logged at WARNING; upload marked `S3_ERROR`; `PersistedNotification` sent with `succeeded=false`; plugin continues.                                       |
| `IOException`                       | `S3UploadClient.uploadFile`       | Logged at WARNING; upload marked `IO_ERROR`; `PersistedNotification` sent with `succeeded=false`; plugin continues.                                       |
| `UploadException` (init)            | `BuckyS3UploadClient` constructor | Caught in `start()`; logged at WARNING; `s3Client` remains `null`; plugin is effectively inactive (all subsequent `handleVerification` calls are no-ops). |
| Block bytes empty after compression | `SingleBlockStoreTask.call`       | Logged at WARNING; upload skipped; `PersistedNotification` sent with `succeeded=false` (`COMPRESSION_ERROR` status).                                      |

`UploadException` is a package-private wrapper that isolates the rest of the package from
bucky's exception hierarchy. `BuckyS3UploadClient` is the only class that imports
`com.hedera.bucky.*`; it translates all bucky exceptions into `UploadException` at the
boundary.

The plugin is designed to be **fault-isolated**: no exception from S3 will propagate up to
crash the node.

## Acceptance Tests

1. **Correct object key format**: block number `1234567` →
   `blocks/0000/0000/0000/1234/567.blk.zstd` (4/4/4/4/3 folder hierarchy).
2. **Correct content type**: `uploadFile` is called with `"application/octet-stream"`.
3. **Correct storage class**: `uploadFile` receives the configured `storageClass` value.
4. **Failed verification skip**: `VerificationNotification` with `success=false` → no upload.
5. **`UploadException` isolation**: `UploadException` thrown by `uploadFile` → plugin does
   not rethrow; sends `PersistedNotification` with `succeeded=false`.
6. **`IOException` isolation**: `IOException` thrown by `uploadFile` → same handling as above.
7. **Uploads skipped on blank s3 credentials**: If `bucketName`, `endPointUrl` or `regionName` are blank →
   `BuckyS3UploadClient` constructor throws `UploadException` → plugin logs WARNING and
   `handleVerification` is a no-op and uploads are not attempted.
8. **Integration (S3Mock)**: after `handleVerification` for blocks 100–104, all five objects
   appear in the S3Mock bucket with the correct folder-hierarchy keys.
9. **PersistedNotification on success**: successful upload publishes
   `PersistedNotification(blockNumber, succeeded=true)`.
10. **PersistedNotification on failure**: failed upload publishes
    `PersistedNotification(blockNumber, succeeded=false)`.
11. **Latency metric recorded**: `uploadLatencyNs` counter is incremented for both successful
    and failed uploads.
12. **stop() drains before close**: in-flight uploads complete and publish
    `PersistedNotification` before `stop()` calls `s3Client.close()`.
13. **ExecutionException isolation**: unchecked exception escaping `SingleBlockStoreTask.call()`
    increments `uploadFailuresTotal`, sends no `PersistedNotification`, and does not propagate.
