# Backfill Partial Batch Handling Design

## Purpose

Backfill fetches missing blocks from peer Block Nodes in batches, one gRPC server
stream per batch. When a batch times out or fails, every block that already
arrived is thrown away and the same full range is requested again.

This was seen on a Block Node backfilling a range of unusually large blocks
(about 6 to 18 MB each) with `BACKFILL_FETCH_BATCH_SIZE=20`. One batch was 120 to
360 MB and took longer than the 60 second `BACKFILL_GRPC_OVERALL_TIMEOUT`, even
on a healthy connection. In one reproduction 13 of 20 blocks (about 100 MB) had
arrived when the deadline fired. The fetcher discarded them, asked for the same
20 blocks again, hit the same deadline on every retry and every cycle, and the
node never made progress.

This document describes how backfill keeps the blocks it received, how the
timeout follows progress instead of total batch time, and how a stream that
backfill has given up on is stopped. It covers the three sub-issues of epic
[#3936](https://github.com/hiero-ledger/hiero-block-node/issues/3936) together
because they change the same classes:

- [#3937](https://github.com/hiero-ledger/hiero-block-node/issues/3937) keep
  the complete blocks received before a timeout or error, and resume after them.
- [#3939](https://github.com/hiero-ledger/hiero-block-node/issues/3939) stop gRPC
  streams that backfill has abandoned.
- [#3938](https://github.com/hiero-ledger/hiero-block-node/issues/3938) use a
  progress-based timeout instead of a whole-batch deadline.

The guiding principle is: **never request again data we already hold.**

> **Companion document.** The overall backfill architecture (gap detection,
> schedulers, node selection, health score, persistence back-pressure) is
> described in [Backfill Plugin Design](./backfill-plugin.md). This document only
> changes the fetch step of that design.

The operator workaround that works without any code change, lowering
`BACKFILL_FETCH_BATCH_SIZE` (for example to 5) and raising
`BACKFILL_GRPC_OVERALL_TIMEOUT` (for example to 180000 to 300000 ms), stays valid
but is not needed with this design.

## Goals

1. A batch that partially succeeds **must** persist the blocks it received, and
   the next request **must** start right after them.
2. A block that was already received **must not** be requested again, neither on
   a retry inside the fetcher nor on the next chunk planned by the runner.
3. A block that was only partly received (items without an end of block) **must
   never** be part of a batch result, so it is never persisted or sent to other
   plugins. It is requested again in full, as the first block of the next
   request.
4. The result of a batch request **must** be a contiguous prefix of the requested
   range, starting at the requested start block, plus an optional error.
5. A batch that ends before the whole range arrived, by an error, a timeout or
   an early success status, but after at least one complete block arrived,
   **must not** count as a node failure: it does not put the node in backoff and
   does not remove it from the current availability. A batch that ends before
   any complete block arrived **must** count as a node failure: the peer's
   failure count goes up, it is put in exponential backoff, its client is
   closed, the fetch returns no blocks, and the runner removes the peer from the
   availability of the gap and replans.
6. A healthy but slow stream of large blocks **must** complete without tuning
   batch size and timeout together. The deadline reflects progress, and an
   absolute ceiling still bounds a peer that sends data very slowly.
7. The meaning of an existing setting **must not** change silently.
8. When a batch request returns early because of a timeout or an error, its gRPC
   stream **must** be closed.

Out of scope:

- A peer that ends the stream early with a success status. The source peer in
  the reproduction did this after 13 blocks. That is a peer-side question; this
  design makes backfill robust to it but does not fix it.
- The check-then-create race in `ZipBlockAccessor.createTempLink`, which is
  tracked separately.
- A per-stream `cancel()` on pbj's `GrpcCall` (an HTTP/2 `RST_STREAM`). It would
  let a client drop one stream without closing the connection, but backfill
  runs one stream per connection at a time, so closing the connection is enough.

## Terms

* **Batch Request**: One call to
  `BlockStreamSubscribeUnparsedClient.getBatchOfBlocks(start, end)`. It opens
  one gRPC server stream for a closed block range and waits for it to finish.
* **Attempt**: One batch request made by `BackfillFetcher.fetchBlocksFromNode`.
  The fetcher makes up to `maxRetries` attempts per chunk. Every attempt counts,
  whether or not it received blocks. When the attempts run out, the fetcher
  returns the blocks it has, and the runner's next chunk starts after them with
  a new budget of `maxRetries` attempts.
* **Chunk**: The block range the runner asks the fetcher for, bounded by the
  peer's available range, `fetchBatchSize` and the gap end. See
  [Backfill Plugin Design](./backfill-plugin.md).
* **Prefix**: The complete blocks received for a batch request, in order,
  starting at the requested start block with no holes. A prefix may be empty,
  cover part of the range, or cover all of it.
* **Partial Block**: A block whose header and possibly some items arrived, but
  whose end of block marker did not. It is never part of a prefix.
* **Terminal Event**: The first of: stream completed, stream error, block number
  mismatch, unexpected response, internal error while processing a response
  message (for example a parse error), timeout, interruption of the waiting
  thread. After the terminal event a request accepts no more data.
* **Abandoned Stream**: A gRPC stream whose batch request has already returned
  to the caller (because of a timeout or an error) while the peer may still be
  sending data.
* **Response Message**: One gRPC message of the block stream: a set of block
  items, an end of block marker, or a status. The client sees each message only
  once it has fully arrived.
* **Inactivity Timeout**: The longest time a batch request waits without
  receiving any response message from the peer.
* **Overall Timeout (Ceiling)**: The longest time one batch request may take in
  total, regardless of progress. Configured by `BACKFILL_GRPC_OVERALL_TIMEOUT`.

## Entities

### BlockStreamSubscribeUnparsedClient

The client that requests a closed range of blocks from a peer over one gRPC
stream and waits for it to finish. It returns a `BatchResult`, also when the
stream ends early, and enforces the inactivity and overall timeouts.

This class and `BlockNodeClient` live in the `base` module, so the change
reaches callers outside backfill:

- `TssDataFetcher` (roster-bootstrap-tss) and `AddressBookFetcher`
  (roster-bootstrap-rsa) build a `BlockNodeClient` but only query server status;
  they never open a block stream. They keep the existing constructor, which
  passes the overall timeout as the inactivity timeout, so they get a single
  deadline and need no change.
- The E2E test `BackfillMultiportSourceTests` calls `getBatchOfBlocks` directly.
  It reads the blocks from the `BatchResult` and asserts that it has no error.

### BatchResult

A new class representing the outcome of one batch request: the blocks received,
in order from the requested start block, and the error that ended the request
early, if any.

### RequestContext and SubscribePipeline

The per-request state and the pbj pipeline that turns response messages into
blocks. The request ends at the first terminal event, and the pipeline ignores
everything that arrives after it. The completed blocks live in the same atomic
snapshot as the outcome, so the prefix handed to the waiting thread is fixed at
the terminal event.

### BlockNodeClient

Holds the gRPC connections to one peer. Closing it is the only way to stop a
stream the client no longer waits for. Backfill uses a constructor that takes
the inactivity timeout; the roster fetchers use the one without it.

### BackfillFetcher

Owns the cached client and the health record of each peer, and retries a chunk
up to `maxRetries` times. It keeps the blocks from every attempt, resumes
after them, and closes the client after a failed attempt. Each scheduler has
its own fetcher on its own thread, so a fetcher runs one fetch at a time.

### BackfillRunner

Drives one gap: selects a peer and chunk, fetches, and waits for the blocks to
persist. It persists a partial chunk and continues after it. Sending blocks for
persistence and waiting for them to persist are unchanged by this design; see
[Backfill Plugin Design](./backfill-plugin.md).

## Design

### Keeping the Received Blocks

A batch request does not fail as a whole. It returns a `BatchResult`: the
blocks that arrived complete, in order from the requested start block, and the
error that ended the request early, if any. A block whose end of block marker
never arrived is not part of the result; the next request asks for it again in
full.

The request ends at its first terminal event: the stream completes, the stream
fails, the peer sends an error status or a block with an unexpected number,
processing a response message fails on our side, a timeout trips, or the
waiting thread is interrupted. The client keeps the outcome and the completed
blocks together in one `AtomicReference` updated by `compareAndSet`, so only the
first event counts and no block is added after it, even while the pbj reader
thread is still running. The waiting thread then returns those blocks, together
with the error, if any; it does not throw.

### Resuming After the Received Blocks

The fetcher keeps the blocks from every attempt. Each retry starts at the first
block not yet received, so nothing the fetcher already holds is requested again.

A peer can also end the stream with a success status after sending fewer blocks
than requested, as the peer in the reproduction did after 13 of 20 blocks. There
is no error to retry in that case, so the fetcher returns the blocks it has, and
the runner asks for the rest in its next round, where node selection may pick a
different peer.

The runner persists whatever the fetcher returns and continues from the block
after it. The peer stays available for the next selection. If it cannot serve
the remaining blocks, the next fetch returns nothing and the peer is removed
from the availability of the gap. Every round either persists at least one block or removes a peer, so a
gap always moves forward or ends.

A fetch counts toward the peer's health as follows:

|            Outcome            |                        Health effect                        |
|-------------------------------|-------------------------------------------------------------|
| All blocks received           | Success: failures reset, latency recorded                   |
| Some blocks received, not all | No new failure, no backoff, failures kept, latency recorded |
| No block received             | Failure: failure count raised, backoff                      |

The middle row covers every way a fetch can end before the whole chunk arrived:
an error, a timeout, or a success status sent early. A peer that ends the stream
early with a success status is not rewarded with a reset of its failures, and a
peer that delivered some blocks is not put in backoff, whichever way the stream
ended.

The latency in the health score is a time per block, not per batch. Every fetch that received blocks adds the total time of its attempts,
without the retry delays between them, and the number of blocks received. The
average latency of a peer is the total time divided by the total blocks.

The duration of each attempt is logged at `DEBUG`, so the raw batch time stays
available for diagnostics.

### Stopping Abandoned Streams

pbj 0.16.2 has no way to cancel a single gRPC stream. A stream ends only when
the peer finishes sending or the connection closes, so after a batch request
returns early, the peer keeps sending the rest of the range.

Each `BlockNodeClient` has its own connections. After every attempt that ended
with an error, the fetcher closes the client for that peer and creates a new one
for the next attempt. Closing the connection ends the abandoned stream, and the
retry never shares a connection with it. A failed attempt is already followed by
the retry delay, so the cost of new connections is small.

A `BlockNodeClient` holds two connections to its peer:

- **Stream connection.** Carries the block stream of each batch request. This is
  the connection that has to close to stop an abandoned stream.
- **Status connection.** Carries the server status queries the runner uses to
  find out which blocks each peer has, when it plans or replans a gap.

Closing the client closes both. This is safe because each fetcher runs on a
single thread, so no status query is in flight when it closes a client.

Closing the client is not a failure in itself: a peer that delivered some blocks
is not put in backoff. After a fully successful fetch the client is kept for the
next chunk.

### Progress-Based Timeout

A batch request is bounded by two limits:

- **Inactivity timeout** (`grpcInactivityTimeout`). The request ends when
  no response message arrived from the peer for this long. Every response
  message counts, not only complete blocks. A serving Block Node splits each
  block into messages of about 1 MB (`maxChunkSizeBytes` on that node), so a
  very large block keeps the stream alive while it is being sent. A peer
  configured with much larger messages gets fewer progress signals, and needs a
  correspondingly larger inactivity timeout.

  Progress counts only complete messages: pbj hands a message to the client
  once all of it has arrived. A block item larger than the chunk size is sent
  as one message, so the inactivity timeout must cover the transfer of the
  largest message on the slowest expected link, or every attempt times out at
  that block. The default of 60 seconds equals the old whole-batch deadline, so
  no message that arrived in time before now times out. Slow links raise
  `BACKFILL_GRPC_INACTIVITY_TIMEOUT`. The floor is not tied to
  `maxIncomingBufferSize`: that is a buffer limit, not a message size, and would
  slow stall detection everywhere.

- **Overall timeout** (`grpcOverallTimeout`). The request ends when this much
  time has passed since it started, whatever the progress. It stops a peer that
  sends just enough data to stay within the inactivity timeout.

The overall timeout limits one attempt, not one chunk. A chunk gets up to
`maxRetries` attempts, so with the defaults a peer that sends just enough data
to stay within the inactivity timeout can hold a chunk for about 45 minutes:
three attempts of 15 minutes, plus the retry delays. During that time the
scheduler running the fetch starts no other chunk; the other scheduler is not
affected, and every block the peer completes is kept.

Each limit logs its own `WARNING`, so operators can tell a stalled peer apart
from a batch that did not finish in time.

pbj's per-peer read timeout (10 seconds by default) does not end a stream: it
only sends a ping and keeps reading. A stalled peer is therefore caught by the
inactivity timeout, while a peer whose connection is gone ends with a stream
error.

`BACKFILL_GRPC_OVERALL_TIMEOUT` keeps its meaning: the longest time one batch
request may take. An operator who set it keeps the same cap.

Its documentation also says it is the fallback for the connect, read and poll
timeouts. That is not the case: those come from the per-peer gRPC tuning, and a
tuning value that is 0 or not set falls back to a fixed 10 seconds in
`BlockNodeClient`. The claim is corrected everywhere it appears:

- `protobuf-sources/src/main/proto/internal/block_node_source.proto`: the
  `GrpcWebClientTuning` message comment and its `connect_timeout`,
  `read_timeout` and `poll_wait_time` fields.
- [Backfill Plugin Design](./backfill-plugin.md): the configuration table, which
  also gets the new `grpcInactivityTimeout` row and the new default, and the
  per-peer tuning section.
- `docs/block-node/configuration.md`: the `BACKFILL_GRPC_OVERALL_TIMEOUT` row,
  plus a new row for `BACKFILL_GRPC_INACTIVITY_TIMEOUT`.
- `BackfillConfiguration`: the `grpcOverallTimeout` parameter documentation.
- `BackfillFetcher`: the global timeout field comment and the `getNodeClient`
  documentation.
- `BlockNodeClient`: the `globalTimeoutMs` constructor parameter documentation.

The roster bootstrap plugins have the same wrong description for their own
`ROSTER_BOOTSTRAP_RSA_GRPC_OVERALL_TIMEOUT` and `ROSTER_GRPC_OVERALL_TIMEOUT`
rows in `docs/block-node/configuration.md` and in `RsaRosterBootstrapConfig`.
They get the same text correction; their behavior does not change.

The default of `BACKFILL_GRPC_OVERALL_TIMEOUT` rises from 60 seconds to 15
minutes. The inactivity timeout catches a stalled peer, and with the old
default a default installation would still fail on the large blocks described
in the [Purpose](#purpose). The change is announced in the release notes.

### Delivery Order

The three parts ship separately, in this order:

1. Keeping and resuming after the received blocks (#3937). This is the data-loss
   fix.
2. Stopping abandoned streams (#3939). It must be in place before streams are
   allowed to run longer.
3. The progress-based timeout (#3938). It changes configuration and
   documentation.

Each step is safe to release on its own. The first one alone already stops the
data loss, but a fetch that received some blocks no longer closes its
connection, so abandoned streams keep using bandwidth until the peer has sent
the rest of their range. The second one should therefore follow soon after.

Releasing the first two without the third is safe. Large blocks then make
progress under the old 60 second timeout, only more slowly, and the operator
workaround described in the [Purpose](#purpose) still applies. The third one
can follow whenever it is ready.

## Diagram

### Partial batch with resume

The reproduction from the [Purpose](#purpose) section, with this design in
place:

```mermaid
sequenceDiagram
    participant R as BackfillRunner
    participant F as BackfillFetcher
    participant C as SubscribeUnparsedClient
    participant P as Peer Block Node

    R->>F: fetchBlocksFromNode(node, 100..119)
    F->>C: getBatchOfBlocks(100, 119)
    C->>P: subscribe 100..119
    P-->>C: blocks 100..112
    P-->>C: block 113 (partial)
    Note over C: timeout: no response message within inactivity window
    C-->>F: BatchResult(100..112, timeout)
    F->>F: close and evict client (stops the stream)
    F->>F: retry delay
    F->>C: getBatchOfBlocks(113, 119) on a new connection
    C->>P: subscribe 113..119
    P-->>C: blocks 113..119, success
    C-->>F: BatchResult(113..119, no error)
    F->>F: markSuccess
    F-->>R: blocks 100..119
    R->>R: persist, continue from 120
```

If every attempt had failed after block 112, the fetcher would return blocks
100..112 without marking a failure, the runner would persist them and continue
from 113.

### Request context lifecycle

```mermaid
stateDiagram-v2
    [*] --> Receiving: request sent
    Receiving --> Receiving: response message (items, end of block)
    Receiving --> Completed: onComplete
    [*] --> Failed: stream could not be opened
    Receiving --> Failed: onError, error status, mismatch, unexpected message
    Receiving --> Failed: internal error while processing a message
    Receiving --> Failed: inactivity timeout or ceiling
    Receiving --> Failed: caller interrupted
    Completed --> [*]: return prefix, no error
    Failed --> [*]: return prefix, first error
    note right of Failed
        Any later callback is ignored.
        The partial block is dropped.
    end note
```

### Fetch outcome handling in the runner

```mermaid
flowchart TD
    A[fetchBlocksFromNode] --> B{Blocks returned?}
    B -->|None| C[Remove node, replan: RETRY or EXHAUSTED]
    B -->|Whole chunk| D[Persist chunk, continue after chunk end]
    B -->|Prefix of chunk| E[Shrink chunk to prefix]
    E --> F[Persist prefix, continue after prefix end]
    F --> G[Node stays available for the next selection]
```

## Configuration

Properties use the `backfill.` prefix, set through environment variables as
shown.

|           Property            |        Environment variable        | Type |      Default       |                                                                  Description                                                                   |
|-------------------------------|------------------------------------|------|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `grpcInactivityTimeout` (new) | `BACKFILL_GRPC_INACTIVITY_TIMEOUT` | int  | 60000              | Longest time in ms a batch request waits for the next complete response message. Must cover the transfer of the largest message. Minimum 1000. |
| `grpcOverallTimeout`          | `BACKFILL_GRPC_OVERALL_TIMEOUT`    | int  | 900000 (was 60000) | Longest time in ms one batch request may take in total. Minimum 10000.                                                                         |
| `fetchBatchSize`              | `BACKFILL_FETCH_BATCH_SIZE`        | int  | 10                 | Unchanged. Maximum blocks per batch request.                                                                                                   |
| `maxRetries`                  | `BACKFILL_MAX_RETRIES`             | int  | 3                  | Unchanged. Attempts per chunk, counted with or without progress; each resumes after the prefix.                                                |

The two timeouts are not validated against each other. If the inactivity
timeout is set at or above the overall timeout, the overall timeout always
trips first, so the request has a single deadline.

## Metrics

No new metrics. Existing backfill counters keep their meaning, with these
details:

- `backfill_blocks_fetched` counts every block of a returned prefix, so it now
  also grows for partial batches.
- `backfill_retries` counts each retry attempt inside the fetcher. Each of those
  attempts requests only the blocks after the prefix.

## Exceptions

- **Stream errors.** A gRPC error, a non-success status, a block number
  mismatch, a block outside the requested range, block items or an end of block
  without a block header, an unexpected response, a parse error and any other
  error while processing a response message all end the request. They are
  returned as the `error` of the `BatchResult` together with the prefix, never
  thrown.
- **Stream not opened.** When creating the call or sending the request fails,
  the batch request returns a `BatchResult` with no blocks and that error. The
  fetcher handles it like any other attempt that received no blocks.
- **Invalid range.** A start after the end, or a negative block number,
  throws `IllegalArgumentException`, because it is a bug in the caller, not a
  failure of the peer.
- **Timeouts.** The inactivity timeout and the ceiling end the request the same
  way, each with its own `WARNING` message naming the setting and the first
  block not received.
- **Interruption.** The waiting thread is interrupted only when the backfill
  plugin stops: `stop()` shuts down the scheduler executors with
  `shutdownNow()`. The interrupt flag is restored, the prefix is returned with
  an `InterruptedException`, the fetcher stops retrying, and the peer is not
  marked as failed.
- **Late callbacks.** Any callback after the terminal event is ignored. The log
  level of `onError` depends on when it arrives: an `onError` that ends the
  request is logged at `WARNING`, while one that arrives after
  the terminal event, typically because the connection of an abandoned stream
  was closed, is logged at `DEBUG`.
- **Exhausted attempts with a prefix.** When every attempt failed but some
  blocks arrived, the fetcher returns them, logs the last error at `WARNING`,
  and does not mark the node as failed.

## Acceptance Tests

1. **Prefix on error or timeout.** A batch request that ends early returns the
   blocks received so far and the error.
2. **Partial block dropped.** A block without an end of block is never part of
   the result.
3. **Block number mismatch.** The result holds the blocks before the mismatch,
   and nothing the stream sends afterwards is added.
4. **Resume after prefix.** A retry requests only the blocks after the prefix,
   and the whole range is returned once they arrive. The peer is marked
   successful, with the time of both attempts divided by the blocks returned as
   its latency, not counting the retry delay between them.
5. **Prefix kept when all attempts fail.** The prefix is returned, persisted,
   and the next fetch starts after it; the peer is not put in backoff, its
   failures are not reset, and a latency per block is recorded for the prefix.
6. **Short stream with success status.** The prefix is returned without
   retrying, and the rest is requested in the next round. The peer is not put in
   backoff, its failures are not reset, and a latency per block is recorded for
   the prefix.
7. **No block received.** The peer's failure count goes up, it is put in
   backoff, and it is removed from the availability of the gap.
8. **Abandoned stream closed.** After a failed attempt the client is closed and
   a new one is used for the next attempt; after a full success it is kept.
9. **Slow but steady stream.** A stream that keeps sending data completes even
   when it takes longer than the inactivity timeout.
10. **Stalled peer.** A stream that stops sending data ends after the inactivity
    timeout.
11. **Overall timeout.** A stream that keeps sending data ends at the overall
    timeout.
