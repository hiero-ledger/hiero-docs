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

Returns `UploadResult(blockNumber, status, bytesUploaded, blockSource, uploadDurationNs, stagedForRetry)`.
The `uploadDurationNs` field records wall-clock time of the upload call in nanoseconds, used
to populate the latency metric. Failures (`UploadException`, `IOException`) are captured as
`succeeded=false` and `bytesUploaded=0` so the `CompletionService` always receives a result —
exceptions never propagate to the caller. On `S3_ERROR` / `IO_ERROR`, the task hands the
already-compressed bytes to `RetryBuffer.stage(...)`; `stagedForRetry` reflects whether
buffering succeeded (`false` for `COMPRESSION_ERROR`, since there are no valid bytes to buffer, or if
`stage(...)` itself was rejected).

The `UploadStatus` enum distinguishes failure types:

|       Status        |                     Cause                      |
|---------------------|------------------------------------------------|
| `SUCCESS`           | Upload completed successfully                  |
| `S3_ERROR`          | `UploadException` (S3 service or auth error)   |
| `IO_ERROR`          | `IOException` (transport / socket error)       |
| `COMPRESSION_ERROR` | Compressed bytes were empty (should not occur) |

### `RetryBuffer`

Package-private class holding, purely in memory, the blocks whose upload failed and are
awaiting background retry. Nothing is ever written to local disk — the block node's
cloud-archive plugins must not depend on local storage — so a buffered block is lost if the
process restarts. A `ConcurrentHashMap<Long, BufferedEntry>` maps block number to its buffered
bytes, object key, storage class, source, attempt count, and timing. Bounded by
`retryMaxAgeSeconds` (how long a block may stay buffered) and `retryMaxPendingBlocks` (how many
blocks may be buffered at once), so a prolonged S3 outage cannot grow the buffer without bound.

`stage` and `recordFailure` mutate the map via `ConcurrentHashMap#compute` so that concurrent
calls for the *same* block number (a duplicate `VerificationNotification` is possible upstream)
are serialized; different block numbers may still be manipulated fully concurrently.
`computeIfAbsent` is not used, and `unstage` is a plain `remove`.

Key operations:

- `stage(...)` returns a `StageOutcome(staged, evicted)` record. `staged` is `false` only when
  `retryEnabled` is `false` or the buffer has been `close()`d. At `retryMaxPendingBlocks`
  capacity the new block is still admitted: `evictOldestToMakeRoom` removes the
  longest-buffered entry and hands it back as `evicted` for the caller to report as a terminal
  failure. A duplicate `stage` for an already-buffered block does not grow the buffer, so it
  never evicts.
- `dueForRetry(now)` returns the entries whose backoff has elapsed.
- `unstage(blockNumber)` removes an entry, e.g. once a retry succeeds.
- `recordFailure(blockNumber)` pushes the next eligible retry time out by the fixed
  `retryIntervalSeconds` and returns `EXHAUSTED` once the block has been buffered longer than
  `retryMaxAgeSeconds`, or `NOT_STAGED` if a concurrent `unstage()` already resolved the block.
- `drainAll()` removes and returns every buffered entry, used by `stop()` after `close()`.

### `RetryUploadTask`

Package-private `Callable<UploadResult>` used for retry attempts. Takes the block number,
pre-compressed bytes held in the `RetryBuffer`, and the upload target; calls
`S3UploadClient.uploadFile(...)` directly — no compression step, since the bytes were already
compressed when originally buffered. Reuses `SingleBlockStoreTask.UploadResult`; `stagedForRetry`
is always `false` on its results, since the retry pipeline in `ExpandedCloudStoragePlugin`
handles buffer bookkeeping itself (`unstage` / `recordFailure`) rather than re-buffering an
already-buffered block.

### `ExpandedCloudStorageConfig`

`@ConfigData("cloud.storage.expanded")` record carrying all plugin settings. The
`storageClass` field is typed as `StorageClass` (an enum), which causes the config
framework to reject unknown values at startup. `uploadTimeoutSeconds` and the `retry*` numeric
fields carry `@Min(1)` for framework-level range validation.

### `ExpandedCloudStoragePlugin`

Implements `BlockNodePlugin` and `BlockNotificationHandler`. Listens for
`VerificationNotification`, builds the S3 object key, and submits one `SingleBlockStoreTask`
per verified block to a `CompletionService` backed by a virtual-thread executor.

The notification handler is always registered during `init()`. If `start()` fails to create
the S3 client (blank endpoint URL, bad credentials, unreachable endpoint), `s3Client` remains
`null` and all `handleVerification` calls are no-ops for the duration of the process
(`completionService` is always created regardless).

`start()` creates a dedicated single-thread scheduled executor
(`ThreadPoolManager.createSingleThreadScheduledExecutor`) and schedules the drain tick
(`drainTick()`) on it every `DRAIN_INTERVAL_MS` (100 ms); see [Drain tick](#drain-tick). When
`retryEnabled` is `true`, `start()` also schedules the retry tick (`retryTick()`, which runs
`retryStagedBlocks()`) on the same executor at `retryIntervalSeconds` intervals. Both ticks catch
any `RuntimeException`, since one escaping a periodic task cancels all its later runs. `stop()` shuts this scheduler down, waits for in-flight uploads
to drain, then flushes any block still left in the `RetryBuffer` as a terminal
`PersistedNotification(succeeded=false)` — since nothing persists across a restart, a block left
buffered here would otherwise never receive any notification at all.

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
5. Build object key using `buildBlockObjectKey(blockNumber)`.
6. Submit `SingleBlockStoreTask` to the `CompletionService`.

`handleVerification` does not publish results; the [drain tick](#drain-tick) does.

Inside `SingleBlockStoreTask.call()`:
- Record `uploadStartNs = System.nanoTime()`.
- Serialise and ZSTD-compress the block bytes.
- Upload via `S3UploadClient.uploadFile()` directly.
- Return `UploadResult(blockNumber, status, bytesUploaded, blockSource, uploadDurationNs)`.

### Drain tick

`drainTick()` runs `drainCompletedTasks()` on the scheduler every `DRAIN_INTERVAL_MS` (100 ms).
Each run polls `CompletionService` for all upload tasks completed so far, stages their results in
`pendingPublish`, and publishes a `PersistedNotification` for each staged result (success or
failure) in ascending block-number order. It then processes any completed retry attempts.

- **No wait for the next block:** a block is reported as persisted at most one tick after its
  upload completes, even if no further `VerificationNotification` arrives. A backfill chunk or
  the last block before a pause in the live stream would otherwise never be reported, since
  backfill waits for each chunk to persist before sending the next.
- **Single drainer:** the scheduler thread is the only thread that drains while the plugin runs,
  so two drains never interleave. Order is ascending within each drain; across drains it is not
  enforced. A block that completes in an earlier drain is published before a lower-numbered block
  that completes in a later one.
- **Tick stays alive:** an exception escaping a `scheduleWithFixedDelay` task cancels every later
  run, so `drainTick()` catches any `RuntimeException` and logs WARNING.

### Shutdown drain (`stop`)

`stop()` unregisters from block notifications, shuts down the scheduler and waits up to
`uploadTimeoutSeconds` for a running tick to finish so it doesn't race the final sweep below,
then shuts down the virtual-thread executor and waits up to `uploadTimeoutSeconds` for in-flight
uploads to complete:

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
Metrics and the pending-retry gauge are updated before the notification is sent, so a consumer
reacting to the notification sees them already updated.
1. On success: increments `uploadsTotal` and `uploadBytesTotal` by `bytesUploaded`; then
publishes `PersistedNotification(blockNumber, true, 0, blockSource)`.
2. On failure **with** `stagedForRetry == true`: publishes **no** notification yet — logs INFO and
updates the `cloud_expanded_pending_retry_blocks` gauge. The deferred `succeeded=false` fires
later, only once retries are exhausted (see [Background retry](#background-retry) below).
3. On failure **without** `stagedForRetry` (compression error, or retry disabled/staging
rejected): increments `uploadFailuresTotal`; then publishes
`PersistedNotification(blockNumber, false, 0, blockSource)` immediately; logs INFO.
4. Always increments `uploadLatencyNs` by `uploadDurationNs`, first, before any of the above.

### Background retry

**Why deferred, not immediate, on failure:** two downstream consumers overreact to an immediate
`succeeded=false` for what may be a merely-transient S3 error — `LiveStreamPublisherManager`
tears down all live publisher connections for `BlockSource.PUBLISHER`, and `BackfillPlugin`
re-fetches the block from a peer. Since the block already passed verification and just needs an
S3 retry, `succeeded=false` is deferred until the retry buffer gives up on it (or buffering itself
isn't possible) rather than sent on the first failure.

**Why in memory, not on disk:** the block node's cloud-archive plugins have a standing design
goal of not depending on local disk. A failed upload's compressed bytes are held in the
`RetryBuffer` instead — bounded by a short `retryMaxAgeSeconds` window and a
`retryMaxPendingBlocks` cap rather than surviving a restart.

When enough time remains under `retryMaxAgeSeconds`, a failed upload's compressed bytes are
buffered via `RetryBuffer.stage(...)` instead of being discarded. The scheduled tick
`retryStagedBlocks()`:
1. Returns immediately if `s3Client == null` or `retryCompletionService == null` (the latter is
only created when `retryEnabled` is `true`).
2. Drains any retry attempts completed since the last tick.
3. For each `RetryBuffer.dueForRetry(now)` entry not already retrying
(`retryFutureBlockNumbers` guards against a second concurrent attempt for the same block), submits a
`RetryUploadTask` to `retryCompletionService`, a second `CompletionService` over the same
`virtualThreadExecutor`, independent of `completionService` / `pendingPublish`, since that
machinery exists to keep the *live* stream monotonically increasing, and retries are out-of-band
corrections for already-verified blocks. Only entries whose backoff has elapsed are re-attempted,
not every buffered block.

`processRetryResult(entry, result)` applies the outcome:
- **Success**: `RetryBuffer.unstage(...)`, publish `PersistedNotification(true)`,
increment `retrySuccessTotal` + `uploadsTotal` + `uploadBytesTotal`.
- **Failure, `RETRYING`**: `RetryBuffer.recordFailure(...)` pushes the next eligible retry time
out by the fixed `retryIntervalSeconds`; log DEBUG; no notification yet.
- **Failure, `EXHAUSTED`**: once buffered longer than `retryMaxAgeSeconds`, publish
`PersistedNotification(false)`, increment `retryExhaustedTotal` + `uploadFailuresTotal`, log
WARNING — this is the "silently missing" failure mode the feature exists to surface.

A fourth path bypasses the retry loop entirely: when `stage(...)` had to evict an older block to
admit this one, `publishResult` sees a non-null `UploadResult.evictedEntry()` and calls
`reportEvictedBlock`, which publishes `PersistedNotification(false)` for the evicted block and
increments both `retryExhaustedTotal` and `uploadFailuresTotal`. That block is never retried.

The `cloud_expanded_pending_retry_blocks` gauge is refreshed after every outcome that changes the
buffered set. `stop()` additionally flushes any block still in the buffer as a terminal
`PersistedNotification(false)`, since nothing persists across a restart to pick it up later.

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

Five properties are required and must not be blank: `endpointUrl`, `bucketName`, `regionName`,
`accessKey`, and `secretKey`. `init()` logs one WARNING naming each blank property (values are
never logged), so a misconfigured node can be diagnosed by grepping the startup log for
`cloud.storage.expanded`.

Whether or not `init()` warned, a blank required field also fails at client construction:
`BuckyS3UploadClient`'s constructor throws `UploadException`, as it does for a genuinely bad
credential or an unreachable endpoint. The plugin catches this in `start()`, logs a second
WARNING, and `s3Client` remains `null`; all `handleVerification` calls are no-ops for the
duration of the process. `completionService` and `metricsHolder` are still created normally.

**Intent**: once per-plugin health checks are supported, a misconfigured plugin should be
marked **UNHEALTHY** and surfaced appropriately rather than silently degrading.

## Diagram

### Upload sequence

```mermaid
sequenceDiagram
    participant MF as MessagingFacility
    participant ECS as ExpandedCloudStoragePlugin
    participant Sched as Scheduler
    participant CS as CompletionService
    participant Task as SingleBlockStoreTask
    participant S3 as S3UploadClient
    participant Bucky as BuckyS3UploadClient
    participant Store as S3-Compatible Store

    MF->>ECS: handleVerification(VerificationNotification)
    ECS->>ECS: check s3Client != null
    ECS->>ECS: check success() && blockNumber >= 0 && block != null
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
    Sched->>ECS: drainTick() (every 100 ms)
    ECS->>CS: poll completed tasks
    ECS->>MF: sendBlockPersisted(PersistedNotification), ascending within the drain
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
        -threadPoolManager: ThreadPoolManager
        -retryBuffer: RetryBuffer
        -scheduler: ScheduledExecutorService
        -retryFutureBlockNumbers: Map~Future, Long~
        +init(context, serviceBuilder)
        +start()
        +stop()
        +handleVerification(notification)
        +buildBlockObjectKey(blockNumber) String
        -drainCompletedTasks()
        -drainTick()
        ~retryTick()
        -retryStagedBlocks()
        -processCompletedFuture(future)
        -publishResult(result)
        -executeRetry(entry)
        -processRetryResult(entry, result)
    }
    class SingleBlockStoreTask {
        -blockNumber: long
        -block: BlockUnparsed
        -s3Client: S3UploadClient
        -objectKey: String
        -storageClass: String
        -retryBuffer: RetryBuffer
        +call() UploadResult
    }
    class UploadResult {
        +blockNumber: long
        +status: UploadStatus
        +bytesUploaded: long
        +blockSource: BlockSource
        +uploadDurationNs: long
        +stagedForRetry: boolean
        +succeeded() boolean
    }
    class RetryBuffer {
        -config: ExpandedCloudStorageConfig
        -buffered: ConcurrentHashMap
        +stage(blockNumber, bytes, objectKey, storageClass, blockSource) boolean
        +dueForRetry(now) List~BufferedEntry~
        +unstage(blockNumber)
        +recordFailure(blockNumber) RetryOutcome
        +pendingCount() int
        +drainAll() List~BufferedEntry~
    }
    class RetryUploadTask {
        -blockNumber: long
        -compressedBytes: byte[]
        -s3Client: S3UploadClient
        -objectKey: String
        -storageClass: String
        +call() UploadResult
    }
    S3UploadClient <|.. BuckyS3UploadClient
    BuckyS3UploadClient ..> UploadException : throws
    ExpandedCloudStoragePlugin --> S3UploadClient
    ExpandedCloudStoragePlugin --> SingleBlockStoreTask : submits
    ExpandedCloudStoragePlugin --> RetryUploadTask : submits
    ExpandedCloudStoragePlugin --> RetryBuffer
    SingleBlockStoreTask --> S3UploadClient
    SingleBlockStoreTask --> UploadResult : returns
    SingleBlockStoreTask --> RetryBuffer : buffers on failure
    RetryUploadTask --> S3UploadClient
    RetryUploadTask --> UploadResult : returns
    ExpandedCloudStoragePlugin --> ExpandedCloudStorageConfig
```

## Configuration

All properties are under the `cloud.storage.expanded` namespace.

|                    Property                    |  Default   |                                                                                Description                                                                                |
|------------------------------------------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `cloud.storage.expanded.endpointUrl`           | `""`       | S3-compatible endpoint URL. Required; must not be blank.                                                                                                                  |
| `cloud.storage.expanded.bucketName`            | `""`       | Name of the S3 bucket. Required; must not be blank.                                                                                                                       |
| `cloud.storage.expanded.objectKeyPrefix`       | `""`       | Prefix prepended to every object key. Set to empty string for no prefix.                                                                                                  |
| `cloud.storage.expanded.storageClass`          | `STANDARD` | S3 storage class (`STANDARD`). Validated as enum at startup.                                                                                                              |
| `cloud.storage.expanded.regionName`            | `""`       | AWS / S3-compatible region. Required; must not be blank.                                                                                                                  |
| `cloud.storage.expanded.accessKey`             | `""`       | S3 access key (not logged). Required; must not be blank.                                                                                                                  |
| `cloud.storage.expanded.secretKey`             | `""`       | S3 secret key (not logged). Required; must not be blank.                                                                                                                  |
| `cloud.storage.expanded.uploadTimeoutSeconds`  | `60`       | Max seconds to wait for in-flight uploads during `stop()`. Min value: 1.                                                                                                  |
| `cloud.storage.expanded.retryEnabled`          | `true`     | Whether failed uploads are held in memory and retried in the background instead of failing immediately. Never disk-backed.                                                |
| `cloud.storage.expanded.retryIntervalSeconds`  | `10`       | Period of the background retry tick, and the per-block backoff applied by `recordFailure`. Each tick re-attempts only the blocks whose backoff has elapsed. Min value: 1. |
| `cloud.storage.expanded.retryMaxAgeSeconds`    | `60`       | Maximum time a block may remain buffered for retry before it is dropped and reported as a terminal failure. Min value: 1.                                                 |
| `cloud.storage.expanded.retryMaxPendingBlocks` | `30`       | Maximum number of blocks held in the in-memory retry buffer at once. Min value: 1.                                                                                        |

**Why the retry window is short:** the buffer is purely in memory, so it must stay small
enough to bound memory usage — there is no disk backstop. `retryMaxAgeSeconds` and
`retryMaxPendingBlocks` are the two independent bounds, but they drop different blocks.
Exceeding `retryMaxAgeSeconds` drops the block that aged out. Reaching `retryMaxPendingBlocks`
admits the newly-failed block and evicts the *longest-buffered* one instead. Either way the
dropped block is reported as a terminal failure.

### Credential options

`accessKey` and `secretKey` are both required and must not be blank. `com.hedera.bucky.S3Client`
calls `Preconditions.requireNotBlank` on each of them; it has no credential chain, no
instance-metadata lookup, and reads no environment variable of its own. These two values are the
only way to authenticate.

They can be supplied in one of two equivalent ways:

1. **Config properties**: set `cloud.storage.expanded.accessKey` and
   `cloud.storage.expanded.secretKey` directly in a config file. There is no `${...}` value
   substitution.
2. **Environment**: the block node maps every config property to an environment variable name
   automatically (`AutomaticEnvironmentVariableConfigSource`, MicroProfile style), so the same two
   properties are settable as `CLOUD_STORAGE_EXPANDED_ACCESS_KEY` and
   `CLOUD_STORAGE_EXPANDED_SECRET_KEY`. This keeps credentials out of config files on disk.

If either key is blank, `BuckyS3UploadClient`'s constructor throws `UploadException`, `start()`
catches it and logs a WARNING, and `s3Client` stays `null`; the plugin uploads nothing for the
life of the process.

## Metrics

All counters are registered under the `blocknode` Prometheus category
(`BlockNodePlugin.METRICS_CATEGORY`) via
`MetricsHolder.createMetrics(MetricRegistry)` in `start()`. Each counter uses the
`org.hiero.metrics.LongCounter` / `MetricKey` API.

|              Metric name               |                                                                                                                     Description                                                                                                                     |
|----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `cloud_expanded_total_uploads`         | Number of blocks successfully uploaded to S3-compatible storage (first attempt or retry).                                                                                                                                                           |
| `cloud_expanded_total_upload_failures` | Number of block uploads that ended in terminal failure (compression error, retry disabled/rejected, or retries exhausted).                                                                                                                          |
| `cloud_expanded_total_upload_bytes`    | Total compressed bytes successfully uploaded to S3-compatible storage.                                                                                                                                                                              |
| `cloud_expanded_upload_latency_ns`     | Total wall-clock time spent in upload calls, in nanoseconds (success + failure).                                                                                                                                                                    |
| `cloud_expanded_pending_retry_blocks`  | Gauge: current number of blocks buffered in memory and awaiting a background retry upload.                                                                                                                                                          |
| `cloud_expanded_retry_success`         | Number of blocks recovered by a later background retry after an initial upload failure.                                                                                                                                                             |
| `cloud_expanded_retry_exhausted`       | Number of blocks dropped after exhausting all background retry attempts, evicted from the retry buffer to make room for a newer failure, **or** still buffered when the plugin shut down (the latter two are not themselves a sign of S3 failures). |

`cloud_expanded_total_upload_failures` changed meaning with the retry feature: it now counts
*terminal* failures only, not every single failed attempt — a block that fails once and later
recovers via retry does **not** increment it.

Counters are registered in `start()`, unconditionally. An S3 client creation error does not skip
registration: `start()` catches the `UploadException`, logs a WARNING, and goes on to create
`metricsHolder`, so the counters exist (and stay at zero) even when the plugin is inactive.

The names above are the registered metric keys. The OpenMetrics exporter appends the `blocknode_`
category prefix and, for every counter, a `_total` suffix, so `cloud_expanded_retry_success` is
scraped as `blocknode_cloud_expanded_retry_success_total`.

## Exceptions

|                 Exception                 |                      Source                       |                                                                                                                    Handling                                                                                                                     |
|-------------------------------------------|---------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `UploadException`                         | `S3UploadClient.uploadFile`                       | Logged at WARNING; upload marked `S3_ERROR`; compressed bytes buffered for retry (`stagedForRetry=true`) if `retryEnabled` and buffer capacity remains, else `PersistedNotification` sent immediately with `succeeded=false`; plugin continues. |
| `IOException`                             | `S3UploadClient.uploadFile`                       | Same handling as `UploadException`, marked `IO_ERROR`.                                                                                                                                                                                          |
| `UploadException` (init)                  | `BuckyS3UploadClient` constructor                 | Caught in `start()`; logged at WARNING; `s3Client` remains `null`; plugin is effectively inactive (all subsequent `handleVerification` calls are no-ops).                                                                                       |
| Block bytes empty after compression       | `SingleBlockStoreTask.call`                       | Logged at WARNING; upload skipped; `PersistedNotification` sent with `succeeded=false` immediately (`COMPRESSION_ERROR` status; nothing valid to buffer).                                                                                       |
| `UploadException` / `IOException` (retry) | `S3UploadClient.uploadFile` via `RetryUploadTask` | Logged at DEBUG (expected, not the first-failure signal); `RetryBuffer.recordFailure` pushes out the next retry time or exhausts once `retryMaxAgeSeconds` is exceeded.                                                                         |

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
   not rethrow; with `retryEnabled=false`, sends `PersistedNotification` with `succeeded=false`
   immediately.
6. **`IOException` isolation**: `IOException` thrown by `uploadFile` → same handling as above.
7. **Uploads skipped on blank s3 settings**: if any of `bucketName`, `endpointUrl`, `regionName`,
   `accessKey` or `secretKey` is blank → `init()` logs a WARNING naming that property (never its
   value), the `BuckyS3UploadClient` constructor throws `UploadException` → plugin logs a second
   WARNING and `handleVerification` is a no-op and uploads are not attempted.
8. **Integration (S3Mock)**: after `handleVerification` for blocks 100–104, all five objects
   appear in the S3Mock bucket with the correct folder-hierarchy keys.
9. **PersistedNotification on success**: successful upload publishes
   `PersistedNotification(blockNumber, succeeded=true)`.
10. **PersistedNotification on failure**: with `retryEnabled=false`, failed upload publishes
    `PersistedNotification(blockNumber, succeeded=false)` immediately.
11. **Latency metric recorded**: `uploadLatencyNs` counter is incremented for both successful
    and failed uploads.
12. **stop() drains before close**: in-flight uploads complete and publish
    `PersistedNotification` before `stop()` calls `s3Client.close()`.
13. **ExecutionException isolation**: unchecked exception escaping `SingleBlockStoreTask.call()`
    increments `uploadFailuresTotal`, sends no `PersistedNotification`, and does not propagate.
14. **Deferred notification on buffered failure**: with retry enabled (default), a failed upload
    buffers the compressed bytes in memory and sends **no** `PersistedNotification` yet; the
    `cloud_expanded_pending_retry_blocks` gauge reflects the buffered block.
15. **Retry recovers a transient failure**: driving `retryTick()` after a block that
    failed once now succeeds → publishes `PersistedNotification(succeeded=true)`, clears
    the buffer, increments `cloud_expanded_retry_success`.
16. **Retry exhaustion**: with `retryMaxAgeSeconds=1`, a retry tick after the block has been
    buffered longer than that exhausts it → publishes `PersistedNotification(succeeded=false)`,
    increments `cloud_expanded_retry_exhausted`.
17. **`stage()` respects `retryEnabled=false`**: with retry disabled, `stage(...)` is a no-op
    that returns `StageOutcome(false, null)` without buffering anything.
18. **`stage()` evicts at `retryMaxPendingBlocks`**: once the buffer holds
    `retryMaxPendingBlocks` blocks, a new failure is still admitted and `stage(...)` returns
    `StageOutcome(true, oldest)`, where `oldest` is the evicted longest-buffered entry; the
    caller reports it as a terminal failure.
19. **stop() flushes pending retries**: any block still in the `RetryBuffer` when `stop()` is
    called is reported as `PersistedNotification(succeeded=false)` before the buffer is
    discarded — since nothing persists across a restart, this is the only chance to report it.
20. **Evicted block reported as failure**: with `retryMaxPendingBlocks=1`, a second buffered
    failure evicts the first, which is reported as `PersistedNotification(succeeded=false)` and
    counted in `cloud_expanded_retry_exhausted_total`.
21. **Drain tick survives an exception**: a `RuntimeException` from one send does not stop later
    blocks from being published.
22. **stop() bounds a hung tick**: with `uploadTimeoutSeconds=1`, `stop()` returns even while a
    tick is stuck in a send.
