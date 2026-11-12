# E2E Test Plan - Real Blocks Streaming

## Overview

The Real Blocks suite streams block sets captured from real Consensus Node
deployments into a Block Node with verification on. The blocks come from OCI
load archives that `fetchTestBlocks` downloads before the run.

The purpose is a load test with real blocks: real block sizes, transaction mix
and rate, instead of generated ones. The suite supports several sources of
real blocks. This E2E test plan lists the sources and what the Block Node must
do with the blocks of each: keep verifying, persisting and acknowledging them,
stream them to a subscriber, and serve them to a second Block Node that
backfills from it.

## Key Considerations

- **Sources**: each source is a CN deployment or test that produces the
  blocks: SDPT/SDLT, SDCT / longevity perf1 and BNCE 10K so far. Adding a
  source means publishing a new load archive (or a new set folder in one); the
  test needs no change.
- **Self-describing Set**: each set folder in a load archive holds the blocks
  (`.blk.gz`), a `manifest.json` naming the source, and the roster files the
  Block Node needs to verify them, if the blocks do not provide that data
  themselves: `tss-bootstrap-roster.json` for TSS sets,
  `rsa-bootstrap-roster.json` for WRB/RSA sets. First and last block come from
  the file names, and the test mounts whichever roster files the set has.
- **Shape Of A Set**: where a set starts (block 0 or mid-chain) and its proof
  type (TSS or WRB/RSA) depend on how the source was captured, not on the test.
  The test handles each case from the set's own files:
  - First block: the Block Node runs with `BLOCK_NODE_EARLIEST_MANAGED_BLOCK`
    and `VERIFICATION_FIRST_ORDERED_BLOCK` set to the first block of the set
    (the defaults for a set that starts at block 0). Chain hashes need no local
    history: the Block Node takes the previous block root hash and the
    all-blocks tree root from each block's footer.
  - TSS: a set that starts at block 0 carries the ledger ID in the
    `LedgerIdPublication` of block 0. A mid-chain set must ship
    `tss-bootstrap-roster.json`, otherwise every block fails with
    `MISSING_VERIFICATION_DATA`.
  - WRB/RSA: the set must ship `rsa-bootstrap-roster.json`.
- **One Test Per Set**: every set folder runs as its own test against its own
  fresh Block Node, named by the set's path relative to the archive root.
  `-PrealBlocks.set=<glob>` narrows the selection.
- **Roster Files Are Overwritten**: the Block Node writes its TSS data and
  address book history back to the configured paths, so the test mounts a
  writable copy of the roster files, never the originals.
- **Hash Algorithm**: the Block Node hashes with SHA-256 only. A SHA-384 set
  is refused with `UNSUPPORTED_STREAM_FORMAT` and cannot be re-hashed (the TSS
  signature is over the SHA-384 root hash). Published archives hold SHA-256
  captures only.
- **Crypto Version**: the Block Node's TSS crypto library (hinTS/WRAPS) must
  be compatible with the one the Consensus Node signed the blocks with. The
  versions may differ; an incompatible pair fails every block with
  `BAD_BLOCK_PROOF`.
- **Simulator**: the simulator is the publisher client only. It reads the
  set's `.blk.gz` files in `DIR` mode, streams them over the publish API, and
  handles acknowledgements, resends and behind responses. It is not used to
  provoke other publish response codes; those are covered by the publisher
  suites. A second simulator in `CONSUMER` mode is the subscriber (see
  Subscriber).
- **Large Blocks**: real blocks can be hundreds of MB. The Block Node runs with
  `SERVER_MAX_MESSAGE_SIZE_BYTES=300000000`, and the simulator's
  `blockStream.maxBlockItemsToStream` is raised so it does not stop mid-set.
  The backfilling Block Node runs with `BACKFILL_MAX_INCOMING_BUFFER_SIZE` and
  `BACKFILL_MAX_PROTOBUF_MESSAGE_SIZE_BYTES` at 314572800, the buffer's upper
  bound; the defaults (100 MiB and 125 MiB) are below the largest real blocks.
- **Resend Loop**: a refused block is resent by the simulator forever. The
  test fails early when `verification_blocks_verified` stops advancing for a
  stall window, instead of waiting for the full timeout.
- **Pace**: the simulator sends one block every
  `blockStream.millisecondsPerBlock` ms, a parameter of the run. With 0 it
  sends as fast as the Block Node accepts.
- **Report**: each set writes `build/real-blocks/<set>.txt`, uploaded as a CI
  artifact so runs can be compared. It holds:
  - the run time of each phase, and blocks/s as the blocks in the set divided
    by it:
    - publish: from the first block sent to the last block acknowledged. With
      pace 0 its blocks/s is the highest rate the Block Node sustains for that
      set; with a fixed pace it shows whether the Block Node keeps up.
    - subscribe: from opening the subscription to receiving the last block.
    - backfill: from starting the backfilling Block Node to its last block
      verified.
  - the P50, P90 and P99 end-to-end latency per block: the time from the
    simulator sending a block's first item to receiving the acknowledgement
    that covers it, the delay a Consensus Node sees. The simulator records
    these per block: an acknowledgement for block N closes every block up to
    N still open, and a resent block is timed from its last send.
  - the average verification time and receive latency per block:
    `verification_block_time` and `publisher_receive_latency_ns` divided by
    the block count. Both are running sums in nanoseconds, so averages are all
    they give; percentiles per stage inside the Block Node need histogram
    metrics, which it does not have yet.
- **Subscriber**: while the set is published, a second simulator in
  `CONSUMER` mode subscribes to `<first>` to `<last>`. It connects once the
  first block is persisted: on an empty Block Node a subscription to a
  mid-chain set is refused with `NOT_AVAILABLE`, since the Block Node only
  serves a range whose first block it already holds. It then reads the early
  blocks from storage and the rest live as they arrive.
- **Backfill**: once the first Block Node acknowledges `<last>`, a second,
  empty Block Node starts with the first one as its only backfill source
  (`BACKFILL_BLOCK_NODE_SOURCES_PATH`). It is configured like the first: the
  same first block (plus `BACKFILL_START_BLOCK`), verification on, its own
  writable copies of the set's roster files. Starting it after publishing
  ends keeps the publish numbers clean and makes the backfill a pure
  historical fetch. The stall check applies here too, on the second Block
  Node's `verification_blocks_verified`.
- **Scope**: a load test with real blocks on the publish and subscribe
  endpoints and on backfill, not a functional test of Block Node behavior;
  failure paths are covered by the other suites. Each set has one expected
  outcome: every block verified, persisted, acknowledged, received by the
  subscriber and backfilled by the second Block Node. Throughput
  and latency are reported per set (see Report), with no thresholds until
  nightly runs give a baseline. The suite runs with
  `./gradlew :suites:runRealBlockSuites`, nightly and on demand, not on pull
  requests.

## Test Scenarios

|            Test Case ID | Test Name                                                     | Implemented ( :white_check_mark: / :x: ) |
|------------------------:|:--------------------------------------------------------------|:----------------------------------------:|
|                         | _**<br/>[Real Blocks Tests](#real-blocks-tests)<br/>&nbsp;**_ |                                          |
| [TC-RB-001](#tc-rb-001) | Verify SDPT/SDLT Blocks                                       |                   :x:                    |
| [TC-RB-002](#tc-rb-002) | Verify SDCT / Longevity perf1 Blocks                          |                   :x:                    |
| [TC-RB-003](#tc-rb-003) | Verify BNCE 10K Blocks                                        |                   :x:                    |

### Real Blocks Tests

#### TC-RB-001

##### Test Name

`Verify SDPT/SDLT Blocks`

##### Scenario Description

A SHA-256 block set captured from an SDPT/SDLT run is streamed by the
simulator into an empty Block Node, starting at the first block of the set,
while a subscriber streams the same blocks out of it. Once the whole set is
acknowledged, a second, empty Block Node backfills the set from the first.

##### Requirements

The Block Node MUST accept the first block of the set as its first block.<br/>
The Block Node MUST verify every block using only the data in the blocks and
the roster files of the set.<br/>
The Block Node MUST persist and acknowledge every block.<br/>
The Block Node MUST stream every block of the set to the subscriber, while it
keeps receiving and verifying the rest of the set.<br/>
The Block Node MUST serve every block of the set to a Block Node that
backfills from it, and that Block Node MUST verify and persist every block.

##### Expected Behaviour

- Every block of the set is verified, persisted and acknowledged.
- No block fails verification.
- The subscriber receives every block of the set, then its stream ends.
- The second Block Node backfills, verifies and persists every block of the
  set.

##### Preconditions

- A load archive with an SDPT/SDLT set, fetched with
  `-PtestBlocks=<archive>`.
- A Block Node container configured to start at the first block of the set,
  with writable copies of the set's roster files mounted.
- A second Block Node container, configured the same way with its own roster
  file copies and the first Block Node as its backfill source, started once
  the first Block Node acknowledges `<last>`.

##### Input

- Blocks `<first>` to `<last>` of the set, streamed by the simulator in `DIR`
  mode.
- A subscribe request with `consumer.startBlockNumber=<first>` and
  `consumer.endBlockNumber=<last>`, sent by the subscriber simulator once
  `files_recent_blocks_written` reaches 1.

##### Output

- `verification_blocks_verified` and `files_recent_blocks_written` equal the
  number of blocks in the set.
- `verification_blocks_failed` is 0.
- The simulator's last known publisher status acknowledges `<last>`.
- The subscriber simulator's consumed block count equals the number of blocks
  in the set, and its stream completes after `<last>`.
- `subscriber_errors` is 0.
- On the second Block Node, `backfill_blocks_backfilled`,
  `verification_blocks_verified` and `files_recent_blocks_written` equal the
  number of blocks in the set, and `verification_blocks_failed` and
  `backfill_persistence_failures` are 0.
- The report `build/real-blocks/<set>.txt` gives the run time and blocks/s
  of the publish, subscribe and backfill phases, the P50/P90/P99 end-to-end
  latency per block, and the average verification time and receive latency
  per block.

##### Other

N/A

---

#### TC-RB-002

##### Test Name

`Verify SDCT / Longevity perf1 Blocks`

##### Scenario Description

Same as [TC-RB-001](#tc-rb-001), with a set captured from an SDCT / longevity
perf1 run.

##### Requirements

Same as [TC-RB-001](#tc-rb-001).

##### Expected Behaviour

- Same as [TC-RB-001](#tc-rb-001).

##### Preconditions

- A load archive with an SDCT / longevity perf1 set.

##### Input

- Blocks `<first>` to `<last>` of the set, streamed by the simulator.

##### Output

- Same as [TC-RB-001](#tc-rb-001).

##### Other

N/A

---

#### TC-RB-003

##### Test Name

`Verify BNCE 10K Blocks`

##### Scenario Description

Same as [TC-RB-001](#tc-rb-001), with a set captured from a BNCE run at 10K
TPS: 5K crypto transfer and 5K HCS.

##### Requirements

Same as [TC-RB-001](#tc-rb-001).

##### Expected Behaviour

- Same as [TC-RB-001](#tc-rb-001).

##### Preconditions

- A load archive with a BNCE 10K set.

##### Input

- Blocks `<first>` to `<last>` of the set, streamed by the simulator.

##### Output

- Same as [TC-RB-001](#tc-rb-001).

##### Other

N/A

---
