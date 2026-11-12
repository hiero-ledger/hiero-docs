# Block Verification Design

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

The Block Node receives blocks from different types of sources. Once received,
blocks must pass **verification** before they can be safely propagated
downstream. Verification is the process through which we confirm that a block,
received from any source, is valid and not tampered with. It is the central
point of the Block Node, and it is implemented by the **Verification Service
Plugin**.

- Upon *successful* verification, the plugin propagates the block downstream via
  the internal messaging system.
- Upon *failed* verification, be that a bad proof, a parse failure, missing
  mandatory items or fields, or any other reason, the plugin conveys the failure
  throughout the Block Node via the internal messaging system.
- In both cases, any plugin can take an appropriate action.

Due to the asynchronous nature of the Block Node and the way blocks are received
from different sources, strict ordering of block *reception* cannot be
guaranteed. It is therefore a requirement of the plugin to ensure a **strong
ordering** of successful verification messages, so that downstream plugins see a
contiguous, in order stream of verified blocks they can process safely.

> **Companion document.** The hashing stage of verification, including how block
> items are placed into subtree categories and how the design stays forward
> compatible with future item types, is described in
> [Block Stream Forward Compatibility](./block-stream-forward-compatibility.md).

## Goals

1. The Block Node **must** be able to receive blocks as raw data from any given
   source.
2. The Block Node **must** be able to start a verification session for every
   received block, and every session must always produce a result.
3. The Block Node **must** accurately convey verification results downstream via
   the internal messaging system.
   - Successful verifications **must** be propagated strictly in order, subject
     to the ordering configuration described below.
   - Failures **must** be propagated immediately, without ordering.
4. The plugin **must** bound the number of simultaneously active sessions: an
   admission that finds the buffer full first gives up one of the sessions
   already held, so the count never exceeds the limit, whatever order and rate
   the sources deliver blocks in, and a session is never given up by its own
   admission. A session stuck behind a block that no session holds goes first, the
   highest one first; only when no session is stuck does a session that would
   complete on its own make room. The block the publisher is still streaming is
   never given up.
5. A failure for a block that was *recently verified successfully* **must** be
   distinguishable from a first-time failure, so downstream plugins can treat it
   as informational.

## Terms

<dl>
<dt><code>Publisher</code></dt><dd>A type of data source that publishes
(streams) blocks as raw data to the Block Node, one batch of items at a
time.</dd>
<dt><code>Backfill</code></dt><dd>A type of data source that supplies whole,
complete blocks, typically to fill historical gaps.</dd>
<dt><code>Block Items</code></dt><dd>The data pieces (header, events,
transactions, results, state changes, proofs) that make up a block.</dd>
<dt><code>Block Root Hash</code></dt><dd>The single hash that identifies a
block, computed from the block's items during the hashing stage.</dd>
<dt><code>Verification Session</code></dt><dd>An asynchronous unit of work that
verifies a single block from start to finish and always produces a result.</dd>
<dt><code>Session Stage</code></dt><dd>One of the distinct, chained steps a
session passes through: hashing, proof verification, result ordering, and
result handling.</dd>
<dt><code>Session Handler</code></dt><dd>The component that manages the
sessions and their lifecycle: it starts, supplies and cancels them, and routes
incoming data to the correct session.</dd>
<dt><code>Active Sessions Buffer</code></dt><dd>A passive helper of the session
handler that holds the running sessions in two lanes under one limit and
applies the eviction policy when an admission exceeds it.</dd>
<dt><code>Verification Notification</code></dt><dd>A message sent to the
internal messaging system that conveys the result of a verification session,
including failure details when verification did not succeed.</dd>
<dt><code>Informational Failure</code></dt><dd>A failure reported for a block
that was recently verified successfully. It signals that the failure is not a
new integrity problem for the chain, since a verified copy of the block already
passed through.</dd>
<dt><code>Last Verified Block</code></dt><dd>The highest block number whose
successful verification has been propagated. Ordering of successes is enforced
against this value.</dd>
<dt><code>First Gap</code></dt><dd>The lowest block after the last verified
block that no running session holds.</dd>
<dt><code>Stuck Session</code></dt><dd>A session waiting for order above the
first gap: it waits for a block nobody is working on. The best candidate for
eviction.</dd>
<dt><code>Backup Candidate</code></dt><dd>The session the eviction policy gives
up when no session is stuck: a low priority session, preferably one that no
high priority session waits on, or, once the low priority lane is empty, the
high priority session at the backup position, a fixed fraction of the limit
below the highest block.</dd>
</dl>

## Entities

### VerificationServicePlugin

The main plugin class. It listens for live block items from the publisher,
backfilled block notifications, persistence notifications, and application
state updates. On startup it establishes the initial *last verified block* from
the highest persisted block known to application state. For each incoming block
it validates the start of the block (a header must be present and its number
must match), then hands the items to the session handler. It owns the shared
state the sessions work against: the last verified block and the recently
verified blocks buffer.

### BlockSessionHandler

Manages the verification sessions and their lifecycle. It starts a session for
every block that starts, admits it to the active sessions buffer, and supplies
every session with the block items of its block: publisher
item batches go to the active publisher session, and every backfilled block gets
a session of its own. When the publisher starts a new block before the active one
ended, it cancels the active one.

### ActiveSessionsBuffer

A passive helper of the session handler that holds the sessions the handler
started and has not cleared yet, in two lanes: a **high priority lane** for
sessions of blocks from the publisher and a **low priority lane** for sessions
of blocks from any other source, under one combined limit. It gives the
handler the means to manage its sessions: admitting a session to the lane its
block's source calls for, which first clears the sessions that are complete and
applies the **eviction policy** when the buffer is still full, and finding the
active publisher session. Sessions never change the buffer themselves.

### VerificationOutbox

The one place the plugin sends its verification notifications from. The plugin
and its sessions build their notifications and give them to the outbox, which
sends them on the calling thread and logs and swallows a failure to send.

### CompletableVerificationSession

The session implementation. One session verifies one block. The session is a
chain of four distinct stages, each handing its output to the next. Sessions
run asynchronously on a dedicated executor, can be cancelled at any point, and
always end in the result handling stage.

#### The Four Stages

| Stage |        Component        |          Input           |                            Output                             |
|-------|-------------------------|--------------------------|---------------------------------------------------------------|
| 1     | `BlockHasher`           | block items, in batches  | a hashing result (block root hash plus block data and proofs) |
| 2     | `BlockVerifier`         | the hashing result       | a verification result (verified block and its root hash)      |
| 3     | `ResultOrderingManager` | the verification result  | the same result, released in order                            |
| 4     | `SessionResultHandler`  | the result, or a failure | a verification notification to messaging                      |

### VerificationDataProvider

Supplies the cryptographic material the verifiers need: TSS data received
through application state updates, and RSA public keys resolved from the
address book for the block being verified.

### BadBlockDumper

An optional diagnostics helper. When enabled, it writes the raw bytes and a
metadata sidecar of a failing block to disk, at most once per block and failure
type, and purges old dumps daily.

## Design

### Receiving Blocks

The plugin is *source agnostic*: every block is verified by the same rules, and
the source is only carried along in the result notification. There are two
sources today:

- **Publisher**: blocks arrive as a stream of item batches. The publisher
  guarantees that once a block starts, its items arrive in order. It cannot
  guarantee that a block will finish. If a new block starts before the previous
  one ended, the previous session is cancelled, since the stream has clearly
  moved on; because the superseded session never received its full block, it
  reports a `CANCELLED_INCOMPLETE` failure, and it is counted in
  `verification_sessions_superseded`.
- **Backfill**: blocks arrive whole, one complete block per notification. Each
  is wrapped as a single batch that is both the start and the end of the block,
  and a session is started for it.

Before any session is started, the plugin validates the start of the block: the
first item must be a block header and the header's number must match the block
number announced with the items. If this validation fails, a failure
notification is sent immediately and no session is created. The plugin then
checks the hash algorithm the header declares: only `SHA2_256`, the one algorithm
the node hashes with, is admitted. A block declaring any other value, including
one the node does not recognize, is refused with an `UNSUPPORTED_STREAM_FORMAT`
failure notification and no session is created. The `BlockHasher` always hashes
with SHA-256 and the algorithm is never resolved from the header.

The header check alone never sees a block from before the move to SHA-256:
`SHA2_384` was the default value of the enumeration then, so the header of such a
block carries no hash algorithm and decodes as `SHA2_256`. The hashing stage
therefore also checks the block footer. The previous block root hash, the root
of all previous block hashes and the start of block state root are leaves of the
block root tree, so each must be one SHA-256 digest (32 bytes). A footer hash of
any other size refuses the block with the same `UNSUPPORTED_STREAM_FORMAT`
failure, before a root hash is computed. Together, the header check and the
footer size check decide which blocks the node hashes.

### The Session Stages

Every session passes through four distinct stages, chained so that each stage
begins only when the previous one has produced its output. A failure at any
stage skips the remaining work and goes straight to result handling.

1. **Hashing.** The `BlockHasher` consumes the block's item batches as they
   arrive and incrementally hashes them into the block's subtrees, producing the
   block root hash together with the collected block data and proofs. The
   `BlockHasher` always hashes with SHA-256, the one algorithm the plugin admits:
   every hash of the block root tree is a SHA-256 digest, a block whose footer
   carries a hash of another size is refused with `UNSUPPORTED_STREAM_FORMAT`
   (see [Receiving Blocks](#receiving-blocks)), and the state proof verifier
   reconstructs its paths with the same algorithm. The record file
   signatures of a wrapped record block are verified over SHA-384 payloads by
   the RSA proof verifier, independently of the block hashing algorithm. The
   block root tree has a fixed 16-leaf shape: every position always contributes,
   using the empty tree hash when a subtree, state root, or extension slot is
   absent, so Merkle proof paths are stable across presence patterns. How
   items map to subtrees, and how this stage remains forward compatible with
   future item types, is described in
   [Block Stream Forward Compatibility](./block-stream-forward-compatibility.md).
2. **Proof verification.** The `BlockVerifier` checks every proof present in the
   block against the computed root hash. Each recognized proof type (TSS, RSA
   record file, state proof) has its own verifier. *All* proofs present must
   pass; a single failed proof fails the block. A block with no usable proof
   fails as missing a mandatory item.
3. **Result ordering.** The `ResultOrderingManager` holds a *successful* result
   until it is its turn to be released, following the last verified block. This
   is what turns out-of-order verification into an in-order stream of successes.
   Which results wait is decided by `OrderingRules`, the rules the eviction
   policy applies as well, so the two always agree. Failures never pass through
   this stage; they are reported immediately.
4. **Result handling.** The `SessionResultHandler` turns the outcome into a
   verification notification and sends it to messaging through the outbox. On
   success it then advances the last verified block and records the block in the
   recently verified buffer. On failure it decides whether the failure is
   informational, optionally dumps the bad block for diagnostics, and updates
   metrics. Once this stage has returned, the session is complete, and the next
   admission to the active sessions buffer clears it.

### The Active Sessions Buffer

The active sessions buffer keeps all running sessions. The buffer has two
lanes: the **high priority lane** holds the sessions of blocks from the
publisher, the **low priority lane** holds the sessions of blocks from any
other source (backfill today). `activeSessionsBufferSize` applies to the
combined count of both lanes, so either lane may use the whole budget while
the other is idle.

Every new session is added to the lane its block's source calls for. A
publisher session also becomes the *active publisher session*, whether its block
comes in many batches or in one, until it has received the end of its block:
the publisher's later batches go to it and the eviction policy never selects
it. When the next publisher block starts before it ended, the session handler
cancels it, because the publisher has moved on. Every admission first clears
the sessions that are complete, which stay in their lanes until then. If the
buffer is still full, the buffer consults an **eviction policy**: a separate
unit that receives an immutable view of both lanes and the last verified block,
and selects one of the sessions already held to give up. Only then is the new
session added. The count therefore never exceeds the limit, a session is never
evicted by its own admission, and admissions run one at a time, their clearing
and eviction included. The policy in use sorts every session into one of three
states, by the same `OrderingRules` the ordering stage applies.

- *Finishing*: the session does not wait for order. No block has been verified
  yet, its block is at most one ahead of the last verified block (the next
  expected block, or a block at or below the last verified block, such as
  historical backfill), below `firstOrderedBlock`, or in the low priority lane
  while `allSourcesRequireOrdering` is `false`. It reports its result on its
  own. Before the first success the ordering stage adopts the first block that
  verifies as the last verified one, so nothing waits, no session is stuck, and
  the backup candidates apply.
- *Progressing*: the session waits for order, and every block from the next
  expected block up to its own is held by a session of either lane. It is
  released as the run below it verifies.
- *Stuck*: the session waits for order above the *first gap*, the lowest block
  after the last verified block that no session holds. It waits for a block
  nobody is working on, and can hold its place indefinitely.

The **best candidate** is the stuck session with the highest block. Successes
are released in order, so evicting a stuck session never holds up a session
that could otherwise be released: everything below the first gap is
unaffected, and everything above it is stuck already. The highest stuck session
leaves no other session behind it but, possibly, the active publisher session,
and for the publisher it is the newest block, the smallest rewind for a resend.
When sessions of both lanes hold the highest stuck block, the low priority one
is selected.

When no session is stuck, every session would complete on its own, yet the
buffer has to make room, so the policy selects the first **backup candidate**
of:

1. The low priority session with the highest block whose eviction opens no gap
   below a high priority session: its block is at or below the last verified
   block, another session holds the same block, or no high priority session
   holds a higher block.
2. The low priority session with the highest block. A low priority session
   always goes before a high priority one: backfill fetches the block again on
   a later gap scan, while a publisher block given up has to be resent on the
   live stream. At this point high priority sessions wait on every low priority
   session, so the ones above the evicted block stay stuck until backfill
   supplies it again. A backfilled block that closes the gap below a full run of
   publisher blocks is such a session, and while the buffer stays full it can
   be evicted again on its next arrival.
3. The high priority session at the **backup position**, once the low priority
   lane is empty, counted from the highest block down:
   `activeSessionsBufferSize` divided by 11, truncated, and at least the third
   session (the ninth with the default limit of 101), or the lowest session of a
   view holding fewer, which a full buffer never hands over. The position keeps
   clear of both ends of the lane. The lowest session
   is the next to complete, and evicting it holds up every session above it,
   one resend at a time. The highest sessions are the blocks the publisher has
   just sent, and evicting them asks for the same blocks again on every
   admission. A session about one eleventh of the way down leaves a short run
   above the gap it opens, and a long run below it that keeps draining while
   the publisher resends the evicted block. When the session at the position is
   the active publisher session, the next one up is taken, or the next one down
   when none is above it. That can happen while the publisher resends a block
   below its newest ones: the next one up opens the gap above the block being
   received, so that block and every block below it still complete in order,
   and it is the block the publisher sends next.

The policy therefore selects a session whenever the buffer holds one other than
the active publisher session, and that session is never selected: the publisher
does not resend a block that ends incomplete, so evicting it would lose the
block. The policy chooses among the sessions already in the buffer, so the
session being admitted always gets its place, and a block that arrives stuck
far ahead of the rest goes on a later admission, as the highest stuck session.
The policy is consulted once per admission into a full buffer; eviction happens
only when a session is admitted, never on a timer; an admission never waits for
room; and each admission runs under one lock, its eviction included, so the two
ingress threads never admit or evict at the same time. The
`verification_active_sessions` gauge shows the count, and
`verification_sessions_evicted` counts the evictions. How often sessions are
evicted depends on how far ahead the sources deliver: backfill sends its blocks
in chunks and waits for each chunk to be persisted, and the publisher streams
ahead of the blocks the node has acknowledged, which the node does only once
they are verified and persisted.

An evicted session reports a failure through the normal result handling path,
always as `CANCELLED`: the session the publisher is still streaming is never
evicted, so an evicted session has always received its complete block.
`CANCELLED_INCOMPLETE` is reserved for a block its supplier never finished: the
publisher started the next block before ending this one, and the session
handler cancelled it. A plugin shutdown is the one exception: the session still
receiving its block then reports `CANCELLED_INCOMPLETE` as well. The publisher
plugin asks for a resend of a `CANCELLED` publisher block; the backfill plugin
fetches a cancelled backfilled block again on a later gap scan.

### The Recently Verified Blocks Buffer and Informational Failures

The plugin keeps a bounded queue of the block numbers that were most recently
verified successfully, sized by `recentlyVerifiedBlocksBufferSize`. When the
queue is full, the oldest entry is dropped.

When a session fails for some block, the result handler checks this buffer. If
the block is present, the failure is reported as **informational** rather than
standard: a verified copy of that block has already passed through, so the new
failure does not put the chain's integrity in question. Downstream plugins can
react accordingly based on informational status of a failure.

There is one important interaction with persistence. If a recently verified
block later *fails to persist*, the plugin removes it from the buffer. The
block will inevitably be received and verified again, and a failure on that
retry must be treated as a standard failure, not an informational one.

### Ordering of Successful Results

Successful results are released strictly in order of block number, one after
the last verified block. A session whose block is more than one ahead of the
last verified block waits until the gap is filled. Two settings shape this
behavior:

- **`firstOrderedBlock`** sets the first block number that requires strict
  ordering. Blocks *below* this number never wait: their successes are reported
  immediately. This establishes the starting point of the ordered stream, which
  matters when a node begins its history mid-chain. The default of `0` means
  every block is ordered. At startup, if no last verified block is known yet,
  the first successful verification establishes it.
- **`allSourcesRequireOrdering`** controls which sources are subject to
  ordering. Blocks from the publisher are *always* strictly ordered. When this
  setting is `true` (the default), blocks from every other source are ordered
  too. When `false`, other sources (such as backfill) report success
  immediately, which can create gaps in the last verified sequence. This is an
  understood and accepted trade off for nodes that prioritize backfill
  throughput.

> **Note.** `allSourcesRequireOrdering` should remain `true` on all *Tier 1*
> Block Nodes.

Failures are never ordered. A failure is reported the moment it happens, from
whichever stage produced it.

### Sending Results

Every notification goes through the `VerificationOutbox` and is sent on the
calling thread. A success is only ever sent from a session thread, before the
session advances the last verified block, so when messaging has no room for a
verified block the send waits, and every later block waits in the ordering
stage of its session, held in the active sessions buffer. Failures,
cancellations included, can also be sent from the threads that deliver blocks
to the plugin (an evicted or superseded session reports on the thread that
cancelled it); they travel on the notifications channel, whose sender never
waits, so those threads are never held up by messaging.

### Failure Types

Every failed session reports a failure type in its notification. The
**informational flag is orthogonal to the type**: any failure type is reported
as informational when the block is in the recently verified buffer, and as
standard otherwise.

|        Failure type         |                                                    Meaning                                                     | Can be informational |
|-----------------------------|----------------------------------------------------------------------------------------------------------------|----------------------|
| `BAD_BLOCK_PROOF`           | A proof did not verify against the computed root hash                                                          | yes                  |
| `UNABLE_TO_PARSE`           | The block or one of its items could not be parsed                                                              | yes                  |
| `MISSING_MANDATORY_ITEM`    | A mandatory item (header, footer, at least one usable proof) is missing                                        | yes                  |
| `MISSING_MANDATORY_FIELD`   | A mandatory item is present but a required field has no value                                                  | yes                  |
| `MISSING_VERIFICATION_DATA` | Required verification data is unavailable (TSS data, RSA keys, or a required algorithm)                        | yes                  |
| `UNRECOGNIZED_PROOF_TYPE`   | The proof(s) provided for the block are not of a recognized type                                               | yes                  |
| `UNSUPPORTED_HAPI_VERSION`  | The block declares a HAPI version the node does not support                                                    | yes                  |
| `UNSUPPORTED_ITEM_TYPE`     | The block contains an item type this version cannot process; an upgrade is required                            | yes                  |
| `UNSUPPORTED_STREAM_FORMAT` | The block parses but is not a processable stream, including a block not hashed with SHA-256                    | yes                  |
| `CANCELLED`                 | The plugin cancelled the session after its complete block was received (evicted from the buffer, or shut down) | yes                  |
| `CANCELLED_INCOMPLETE`      | The supplier never finished the block: superseded by the next publisher block, or shut down mid-block          | yes                  |
| `UNKNOWN_ERROR`             | An unexpected error occurred during verification                                                               | yes                  |

## Diagram

The overall workflow, from reception to notification:

```mermaid
flowchart TD
    P[Publisher: item batches] --> V{Valid start of block?}
    B[Backfill: whole block] --> V
    V -- no --> N1[Failure notification, no session started]
    V -- yes --> SH[Session handler]
    SH --> S["Verification session (one per block)"]
    S --> H[1. Hashing]
    H --> PV[2. Proof verification]
    PV --> O[3. Result ordering]
    O --> RH[4. Result handling]
    H -- failure --> RH
    PV -- failure --> RH
    O -- cancelled --> RH
    RH --> N2[Verification notification to messaging]
```

The session stages and what flows between them:

```mermaid
flowchart LR
    A[Block items] --> H[BlockHasher]
    H -- "hashing result (root hash, block, proofs)" --> VF[BlockVerifier]
    VF -- "verification result" --> OM[ResultOrderingManager]
    OM -- "result, in order" --> RH[SessionResultHandler]
    RH --> M[Messaging]
```

How the ordering of successes works:

```mermaid
flowchart TD
    R[Successful result for block N] --> C1{"N below firstOrderedBlock?"}
    C1 -- yes --> GO[Release immediately]
    C1 -- no --> C2{"Source is publisher, or allSourcesRequireOrdering is true?"}
    C2 -- no --> GO
    C2 -- yes --> C3{"N is the next expected block?"}
    C3 -- yes --> GO
    C3 -- no --> W[Wait until the gap is filled] --> C3
```

How a failure becomes standard or informational:

```mermaid
flowchart TD
    F[Session fails for block N] --> C{"Is N in the recently verified buffer?"}
    C -- yes --> I[Informational failure notification]
    C -- no --> S[Standard failure notification]
    PN["Persistence failed for block N"] -.->|removes N from buffer| C
```

How the active sessions buffer makes room:

```mermaid
flowchart TD
    NS[New session starts] --> FULL{"Combined count at the limit?"}
    FULL -- no --> ADD[Add it to its lane]
    FULL -- yes --> GAP["Find the first gap among the sessions already held: the lowest block after the last verified block that no session holds"]
    GAP --> STUCK{"A stuck session above the gap, other than the active publisher session?"}
    STUCK -- yes --> C1["Best candidate: the highest stuck session, the low priority one on a tie"]
    STUCK -- no --> LOW{"A low priority session whose eviction opens no gap below a high priority session?"}
    LOW -- yes --> C2["The highest such low priority session"]
    LOW -- no --> ANYLOW{"Any low priority session?"}
    ANYLOW -- yes --> C3["The highest low priority session"]
    ANYLOW -- no --> C4["The high priority session at the backup position, limit / 11 and at least the third from the top, or the next one up when that is the active publisher session"]
    C1 --> CANCEL["Cancel it (reports CANCELLED)"]
    C2 --> CANCEL
    C3 --> CANCEL
    C4 --> CANCEL
    CANCEL --> ADD
    ADD --> RUN[Admission done]
```

## Configuration

Configuration prefix: `verification`

|              Property              |                  Default                   |                                                                                                                    Description                                                                                                                     |
|------------------------------------|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `recentlyVerifiedBlocksBufferSize` | `100`                                      | Maximum number of recently verified block numbers kept for the informational failure check. When full, the oldest entry is dropped.                                                                                                                |
| `activeSessionsBufferSize`         | `101`                                      | Limit of simultaneously active sessions across both lanes. An admission at the limit first evicts one, stuck sessions first, never the block being streamed. Must be at least 13; a prime is preferred, a Mersenne number (2^n - 1) is acceptable. |
| `firstOrderedBlock`                | `0`                                        | The first block number that requires strict ordering. Blocks below this value report success immediately, without waiting for order.                                                                                                               |
| `allSourcesRequireOrdering`        | `true`                                     | If `true`, successes from every source are strictly ordered. If `false`, only publisher blocks are ordered. Should remain `true` on Tier 1 Block Nodes.                                                                                            |
| `dumpEnabled`                      | `false`                                    | Whether to write failing block bytes and metadata to disk for diagnostics.                                                                                                                                                                         |
| `dumpDirectoryPath`                | `/opt/hiero/block-node/verification/dumps` | Directory where bad block dump files are written.                                                                                                                                                                                                  |
| `dumpRetentionDays`                | `7`                                        | How many days dump files are retained before the daily purge removes them.                                                                                                                                                                         |

## Metrics

All metrics are in the `blocknode` category. Metrics are grouped per **metric
holder**, the component that owns and records them, so the four groups below
mirror the session pipeline: the session handler and its active sessions
buffer, the hashing stage, the proof verification stage, and the result
handling stage.

|                                     Metric                                      |     Type     |                     Labels                     |                           Description                            |
|---------------------------------------------------------------------------------|--------------|------------------------------------------------|------------------------------------------------------------------|
| _**<br/>[Session Handler Metrics](#session-handler-metrics)<br/>&nbsp;**_       |              |                                                |                                                                  |
| [`verification_blocks_received`](#verification_blocks_received)                 | Counter      | none                                           | Blocks received for verification, one per session started        |
| [`verification_active_sessions`](#verification_active_sessions)                 | Gauge        | none                                           | Combined number of sessions in both lanes of the buffer          |
| [`verification_sessions_evicted`](#verification_sessions_evicted)               | Counter      | `lane="high_priority"`                         | Sessions evicted from the high priority lane                     |
| [`verification_sessions_evicted`](#verification_sessions_evicted)               | Counter      | `lane="low_priority"`                          | Sessions evicted from the low priority lane                      |
| [`verification_sessions_superseded`](#verification_sessions_superseded)         | Counter      | none                                           | Publisher sessions superseded by the publisher's next block      |
| _**<br/>[Hashing Metrics](#hashing-metrics)<br/>&nbsp;**_                       |              |                                                |                                                                  |
| [`hashing_block_time`](#hashing_block_time)                                     | Counter (ns) | none                                           | Cumulative time spent hashing blocks                             |
| _**<br/>[Proof Verification Metrics](#proof-verification-metrics)<br/>&nbsp;**_ |              |                                                |                                                                  |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="tss"`, `result="success"`         | TSS proofs that verified against the block root hash             |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="tss"`, `result="failure"`         | TSS proofs that failed to verify                                 |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="rsa"`, `result="success"`         | Record file (WRB RSA) proofs that verified                       |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="rsa"`, `result="failure"`         | Record file (WRB RSA) proofs that failed to verify               |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="state_proof"`, `result="success"` | State proofs that verified                                       |
| [`verification_proof_total`](#verification_proof_total)                         | Counter      | `proof_type="state_proof"`, `result="failure"` | State proofs that failed to verify                               |
| [`rsa_roster_mismatch_total`](#rsa_roster_mismatch_total)                       | Counter      | none                                           | RSA signatures from node ids absent from the loaded address book |
| [`verification_block_time`](#verification_block_time)                           | Counter (ns) | none                                           | Cumulative time from session start through proof verification    |
| _**<br/>[Result Handling Metrics](#result-handling-metrics)<br/>&nbsp;**_       |              |                                                |                                                                  |
| [`verification_blocks_verified`](#verification_blocks_verified)                 | Counter      | none                                           | Blocks that passed verification                                  |
| [`verification_blocks_failed`](#verification_blocks_failed)                     | Counter      | none                                           | Blocks that failed verification                                  |
| [`verification_blocks_error`](#verification_blocks_error)                       | Counter      | none                                           | Internal errors during verification                              |

### Session Handler Metrics

Owned by the session handler, which creates sessions, and by the active
sessions buffer, which holds them.

#### `verification_blocks_received`

Increments each time the session handler starts a new session, one per block,
regardless of source. Every block the plugin attempts to verify is counted
here, whether it later succeeds, fails, or is cancelled. Comparing this
counter with the verified and failed counters shows how many blocks are still
in flight.

#### `verification_active_sessions`

The combined number of sessions in both lanes of the active sessions buffer,
set on every admission, once the complete sessions are cleared and the new
session is added, so it changes only when a session is admitted. It never
exceeds `activeSessionsBufferSize`: an admission that finds the buffer full
evicts one session before it adds the new one. A value that sits at the limit
means the sources deliver faster than the node verifies, and sessions are
evicted to make room.

#### `verification_sessions_evicted`

A single counter with the `lane` dynamic label (`high_priority` or
`low_priority`). It increments once per session the eviction policy removed
while it was still running; a session that finished on its own at the same
moment is not counted. A rising series means the buffer is full: either blocks
are arriving above a block that no session holds, and the stuck sessions are
evicted first, or the sources deliver faster than the node verifies, and
sessions that would complete on their own make room, backfilled ones first. On
the `low_priority` series, the backfill plugin fetches the evicted blocks again
on a later gap scan; on the `high_priority` series, the publisher resends them.

#### `verification_sessions_superseded`

Increments once per publisher session cancelled because the publisher started
its next block before it finished the session's block; such a session reports
`CANCELLED_INCOMPLETE`. A session that had already produced its result on its
own, for example one that failed on a corrupted item, is not counted. This is
not an eviction and says nothing about the buffer: a rising series means the
publisher leaves blocks unfinished, which points at the publishing side rather
than at verification.

### Hashing Metrics

Owned by the hashing stage, the first stage of every session.

#### `hashing_block_time`

Accumulates, in nanoseconds, the time the hashing stage spent on a block,
measured from the moment the hasher starts consuming items to the moment the
block root hash is produced. It only grows for blocks that complete hashing; a
block that fails mid-hash contributes nothing. Dividing its growth by the
received counter approximates the average hashing time per block.

### Proof Verification Metrics

Owned by the proof verification stage, the second stage of every session.

#### `verification_proof_total`

A single counter with the `proof_type` and `result` dynamic labels, resolved
into six pre-created series, one per proof type and result combination (the
six rows in the table above). It increments once **per proof checked**, not
per block: a block carrying several proofs contributes several increments. The
`proof_type` label identifies which verifier ran (`tss` for TSS proofs, `rsa`
for record file proofs, `state_proof` for state proofs) and the `result` label
records whether that single proof passed or failed. A rise in any `failure`
series is the earliest, most specific signal of which proof path is rejecting
blocks.

#### `rsa_roster_mismatch_total`

Increments once per RSA signature whose node id is not present in the loaded
address book. Such signatures are *skipped*, not failed, so this counter
rising while the RSA failure series stays flat indicates an address book that
does not fully cover the blocks being verified, rather than tampered blocks.

#### `verification_block_time`

Accumulates, in nanoseconds, the time from session start through the end of
proof verification, so it includes hashing. It records nothing for failed
blocks and excludes any time spent waiting in the ordering stage. The
difference between its growth and that of `hashing_block_time` approximates
the time spent purely on proof cryptography.

### Result Handling Metrics

Owned by the result handling stage, the final stage of every session.

#### `verification_blocks_verified`

Increments when a success notification is sent, after ordering. This is the
count of blocks the rest of the Block Node may safely consume.

#### `verification_blocks_failed`

Increments when a failure notification is sent, for every failure type,
informational or not, including cancellations. A failed block never also
counts as verified.

#### `verification_blocks_error`

Increments *in addition to* the failed counter when the failure was an
unexpected internal error, or when result handling itself encountered an
error. It is the signal that something went wrong in the plugin rather than in
the block: a healthy node can see failures, but this counter should stay at
zero.

## Exceptions

The plugin never lets an error escape to its callers. Every error condition
ends in a verification notification, and the plugin keeps running.

- **Invalid start of block.** The first item of a new block is not a header, or
  the header's number does not match. A `MISSING_MANDATORY_ITEM` failure
  notification is sent and no session is started.
- **Unsupported hash algorithm.** The header of a new block declares a hash
  algorithm other than `SHA2_256`, the one the node hashes with. An
  `UNSUPPORTED_STREAM_FORMAT` failure notification is sent and no session is
  started. A block whose footer carries a hash that is not one SHA-256 digest,
  such as a block from before the move to SHA-256 whose header carries no hash
  algorithm, fails the hashing stage with the same `UNSUPPORTED_STREAM_FORMAT`
  failure.
- **Stage failure.** A stage that cannot continue (parse failure, missing item
  or field, bad proof, missing verification data) raises a session failure
  carrying the block number, source, and failure type. The result handling
  stage turns it into a failure notification.
- **Cancellation.** A session the plugin cancels on its own, evicted from the
  active sessions buffer or stopped by a plugin shutdown, reports a `CANCELLED`
  failure; an evicted session has always received its complete block, because
  the session the publisher is still streaming is never evicted. A publisher
  session superseded by the next block before its own ended reports a
  `CANCELLED_INCOMPLETE` failure: the publisher never finished it. At shutdown,
  the session still receiving its block reports `CANCELLED_INCOMPLETE` as well.
- **Unexpected errors.** Any unexpected error, in a stage or in the plugin's
  own handling, is reported as `UNKNOWN_ERROR` and counted in the error metric.
  The plugin logs the details and continues.
- **Notification send failure.** If sending a notification itself fails, the
  failure is logged and the plugin carries on. It must never throw.
- **Diagnostics.** When enabled, failing blocks are dumped to disk with a
  metadata sidecar, at most once per block and failure type, so a retried bad
  block cannot flood the disk. Dump failures are logged and never affect
  verification.

## Acceptance Tests

1. **Every block gets a result.** For any received block, from any source, a
   verification notification is eventually sent: success or failure, never
   silence.
2. **Stage handoff.** A block that fails hashing never reaches proof
   verification; a block that fails proof verification never reaches ordering;
   in both cases the failure notification carries the correct failure type.
3. **Ordered successes.** With ordering enabled, successful notifications are
   emitted strictly in order of block number, even when sessions complete out
   of order.
4. **First ordered block.** Blocks below `firstOrderedBlock` report success
   immediately, and blocks at or above it wait their turn.
5. **Source ordering setting.** With `allSourcesRequireOrdering` set to
   `false`, a backfilled block ahead of the last verified block reports
   success immediately; with `true`, it waits.
6. **Active sessions buffer.** Every admission into a full buffer first cancels
   exactly one of the sessions already held, so the count never exceeds the
   limit and a session is never evicted by its own admission: the stuck session
   with the highest block when there is one, the
   low priority one on a tie; otherwise the highest low priority session that
   no high priority session waits on, then the highest remaining low priority
   session, and only once the low priority lane is empty the high priority
   session at the backup position, or the next one up when that is the active
   publisher session. The session the publisher is still streaming is never
   evicted, and every evicted session reports `CANCELLED`. Blocks delivered in
   descending order keep the lowest blocks and evict the rest, highest first.
7. **Informational failures.** A failure for a block present in the recently
   verified buffer is reported as informational; the same failure for a block
   not in the buffer is standard.
8. **Persistence interaction.** After a failed persistence notification for a
   recently verified block, a subsequent verification failure for that block is
   reported as standard, not informational.
9. **Publisher supersession.** When a new block starts before the previous one
   ended, the previous session is cancelled and reports `CANCELLED_INCOMPLETE`, and the
   new block verifies normally.
10. **Failure types.** Each failure type in the table above is produced by the
    condition it describes, and the informational flag is controlled solely by
    the recently verified buffer.
11. **Metrics.** Each metric in the table above changes exactly when its
    description says it should, including the labeled proof counter for each
    proof type and result.
12. **Blocks not hashed with SHA-256.** A block whose header declares a hash
    algorithm other than `SHA2_256` is refused before a session starts, and a
    block whose footer carries a hash that is not one SHA-256 digest fails the
    hashing stage. Both report `UNSUPPORTED_STREAM_FORMAT`, never
    `UNKNOWN_ERROR`, for publisher and backfill blocks alike.
