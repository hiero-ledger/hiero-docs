# E2E Test Plan - `Files Recent Persistence` Retention Policy

## Overview

Retention Policy is a concept that concerns Persistence Plugins.
The `Files Recent Persistence` is a Persistence Plugin that is concerned with
the Retention Policy. The Policy is a configurable threshold, measured in
amount of blocks (block count) that defines the maximum amount of blocks that
can be stored (rolling history). The Policy should not block new data incoming.
Also, it should be utilized only if new data is received.

This E2E test plan is designed to highlight the key scenarios that need to be
tested for end results in order to ensure the correctness and proper working of
the `Files Recent Persistence` Retention Policy. Essentially, this test plan
describes the intended behavior of the `Files Recent Persistence` Retention
Policy and the expected results it produces.

## Key Considerations for `Files Recent Persistence`

- **Path Resolution Logic**: `Files Recent Persistence` relies on path
  resolution logic which determines the location of a given block.
- **Trie Data Structure for Path Resolution**: `Files Recent Persistence`
  utilizes a trie data structure to resolve paths for blocks. This is done to
  ensure that the blocks are stored in a structured manner, allowing for
  efficient storage and retrieval. The trie structure is based on the digits of
  the blockNumber, with each directory (tree node) representing the next 3
  digits of the whole blockNumber, going left to right.
- **Files Recent Root Path**: This is the root path (configurable) where all
  blocks that have passed verification are stored. Generally, the stored blocks
  are relatively short-lived because this storage type is intended for recently
  received and verified blocks.
- **Scope**: The `Files Recent Persistence` Retention Policy will delete
  currently stored blocks when new blocks are received, verified and persisted.
  In this way, we achieve rolling history.

## Test Scenarios

> _**NOTE**: for the purpose of the below test definitions, we will have the
> following root definition (this could be configured to be different, but
> whatever is configured is of no relevance to the outcome of the tests, simply
> we need a reference point so an example could be shown):_
> - `Files Recent Root`: `/blocks`
>
> _**NOTE**: the trie structure for `Files Recent Root` is resolving
> all the digits of a `long` (max. 19 digits). We have three digits per node
> or directory up to a max. depth of 16, and then we have the full
> blockNumber (`long`) as the file name of any persisted block with all leading
> zeros. So for example, if the blockNumber is 1234567890123456789 and the root
> is `/blocks` then the path will be
> `/blocks/123/456/789/012/345/6/1234567890123456789.blk.zstd`._
>
> _**NOTE**: assume that before each test the Block-Node under test is expecting
> the next block to be received to be with number 0000000000000000000 which
> resolves to `/blocks/000/000/000/000/000/0/0000000000000000000.blk.zstd`.
> We start with a clean root and we will be persisting an arbitrary amount of
> blocks in order to pass/not pass the Retention Policy threshold._
>
> _**NOTE**: assume that we have a working verification because the
> `Files Recent Persistence` relies on blocks to be verified in order to execute
> any logic._
>
>> _**NOTE**: assume that we have set the Retention Policy threshold to 10
>> blocks._
>
> _**NOTE**: assume that the Block-Node under test is configured to have the
> compression algorithm set to `ZStandard` so the file extension for a persisted
> block will be `.blk.zstd`_

|                                Test Case ID | Test Name                                                   | Implemented ( :white_check_mark: / :x: ) |
|--------------------------------------------:|:------------------------------------------------------------|:----------------------------------------:|
|                                             | _**<br/>[Happy Path Tests](#happy-path-tests)<br/>&nbsp;**_ |                                          |
| [E2ETC_HP_FRPRP_0001](#E2ETC_HP_FRPRP_0001) | `Verify Retention Policy Successful Delete`                 |                   :x:                    |
|                                             | _**<br/>[Error Tests](#error-tests)<br/>&nbsp;**_           |                                          |
|   [E2ETC_E_FRPRP_0001](#E2ETC_E_FRPRP_0001) | `Verify Retention Policy Failure During Delete`             |                   :x:                    |

### Happy Path Tests

#### E2ETC_HP_FRPRP_0001

##### Test Name

`Verify Retention Policy Successful Delete`

##### Scenario Description

`Files Recent Persistence` will persist verified blocks. It will retain a
configured amount of blocks in storage, and it will delete the oldest blocks
when new blocks are received and persisted. The Retention Policy is a rolling
history that is applied only when new data is received.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The `Files Recent Persistence` MUST retain a configured amount of blocks and
achieve rolling history.<br/>
The `Files Recent Persistence` MUST NOT hinder new data incoming even if the
Retention Policy threshold is reached or exceeded.

##### Expected Behaviour

- It is expected that the Block-Node will delete an arbitrary amount of
  blocks that exceed the configured Retention Policy threshold when new data is
  received and persisted.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.

##### Input

- Valid blocks `0000000000000000000` to `0000000000000000009` are streamed as
  items, we verify that all are available.
- Valid blocks `0000000000000000010` to `0000000000000000014` are streamed as
  items.

##### Output

- Regular Readable Files:
  `/blocks/000/000/000/000/000/0/0000000000000000000.blk.zstd` to
  `/blocks/000/000/000/000/000/0/0000000000000000004.blk.zstd` do not exist.
- Blocks in range `0000000000000000000` to `0000000000000000004` are no longer
  available.
- Regular Readable Files:
  `/blocks/000/000/000/000/000/0/0000000000000000005.blk.zstd` to
  `/blocks/000/000/000/000/000/0/0000000000000000014.blk.zstd` exist.
- Blocks in range `0000000000000000005` to `0000000000000000014` are
  available.

##### Other

N/A

---

### Error Tests

#### E2ETC_E_FRPRP_0001

##### Test Name

`Verify Retention Policy Failure During Delete`

##### Scenario Description

`Files Recent Persistence` will persist verified blocks. It will retain a
configured amount of blocks in storage, and it will delete the oldest blocks
when new blocks are received and persisted. It is expected that sometimes there
will be an error during the delete operation of the Retention Policy. In such
cases, the `Files Recent Persistence` must continue to function and to accept
new data.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The `Files Recent Persistence` MUST retain a configured amount of blocks and
achieve rolling history.<br/>
The `Files Recent Persistence` MUST NOT hinder new data incoming even if the
Retention Policy threshold is reached or exceeded.<br/>
The `Files Recent Persistence` MUST continue to function and accept new data
even if an error during the delete operation of the Retention Policy occurs.

##### Expected Behaviour

- It is expected that the Block-Node will delete an arbitrary amount of
  blocks that exceed the configured Retention Policy threshold when new data is
  received and persisted.
- It is expected that the Block-Node will continue to function and accept new
  data even if an error during the delete operation of the Retention Policy
  occurs.
- It is expected that the Block-Node will log the error during the delete
  operation of the Retention Policy.
- It is expected that the Block-Node will increment a metric to track failed
  delete operations of the Retention Policy.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.
- A way to simulate an error during the delete operation of the Retention
  Policy.

##### Input

- Valid blocks `0000000000000000000` to `0000000000000000009` are streamed as
  items, we verify that all are available.
- Valid blocks `0000000000000000010` to `0000000000000000014` are streamed as
  items. (simulate an error during delete here)
- Valid blocks `0000000000000000015` to `0000000000000000019` are streamed as
  items. (verify that new data can still be accepted)

##### Output

- An error is logged during the delete operation of the Retention Policy.
- A metric is incremented to track failed delete operations of the Retention
  Policy.
- Subsequent attempts to send new blocks are successful, and the
  `Files Recent Persistence` continues to function normally (write).

##### Other

N/A

---
