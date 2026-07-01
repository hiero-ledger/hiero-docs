# E2E Test Plan - `Files Historic Persistence` Retention Policy

## Overview

Retention Policy is a concept that concerns Persistence Plugins.
The `Files Historic Persistence` is a Persistence Plugin that is concerned with
the Retention Policy. The Policy is a configurable threshold, measured in
amount of blocks (block count) that defines the maximum amount of blocks that
can be stored (rolling history). The Policy should not block new data incoming.
Also, it should be utilized only if new data is received.

This E2E test plan is designed to highlight the key scenarios that need to be
tested for end results in order to ensure the correctness and proper working of
the `Files Historic Persistence` Retention Policy. Essentially, this test plan
describes the intended behavior of the `Files Historic Persistence` Retention
Policy and the expected results it produces.

## Key Considerations for `Files Historic Persistence`

- **Batching of Blocks to Archive**: `Files Historic Persistence` will archive
  blocks in batches. This means that it will collect a number of blocks
  (configurable amount) and then archive them together in a single zip.
- **Batch Size**: The configurable batch size is a power of 10. This done to
  complement a trie data structure that is used for path resolution. Also,
  this means that a batch will always start with a number that is a product of
  the configured batch size and will end with `startNumber + batchSize - 1`,
  e.g. if batch size is 1000, we will see 0-999, 1000-1999, 2000-2999 etc.
- **No Gaps Allowed**: The `Files Historic Persistence` will not allow gaps in
  the zips. This means that we have a predictable start and end number for each
  batch and also, we are certain that if a zip is created successfully, it
  contains all the blocks from the batch.
- **Path Resolution Logic**: `Files Historic Persistence` relies on path
  resolution logic which determines the location of a given zip for each
  blockNumber.
- **Trie Data Structure for Path Resolution**: `Files Historic Persistence`
  relies on a trie data structure for path resolution. The trie structure
  is designed to efficiently resolve paths for blocks based on their
  blockNumber. Each block is stored in a zip file, and the path to that zip
  is determined by the blockNumber, based on configured batch size. For example,
  if we have a batch size of 1000, the path for the zip file where block with
  blockNumber `0000000000000001234` resides would be same as the path for block
  with blockNumber `0000000000000001235`, as they both belong to the same batch
  (1000-1999). An example path for that zip file could be:
  `/blocks/000/000/000/000/000/1000.zip` if the batch size is 1000.
- **Files Historic Root Path**: This is the root path (configurable) where all
  blocks will be archived. Generally, the stored blocks are long-lived as this
  type of persistence is used for long-term storage of blocks.
- **Scope**: The `Files Historic Persistence` will handle Persisted
  Notifications that are arriving via the node's messaging system.
  The Retention Policy will delete the oldest archived batch (zip file) if the
  excess amount of blocks that are higher than the configured threshold is
  greater than the batch size.

## Test Scenarios

> _**NOTE**: for the purpose of the below test definitions, we will have the
> following root definition (this could be configured to be different, but
> whatever is configured is of no relevance to the outcome of the tests, simply
> we need a reference point so an example could be shown):_
> `Files Historic Root`: `/blocks`
>
> _**NOTE**: the trie structure for `Files Historic Root` is resolving
> all the digits of a `long` (max. 19 digits). We have three digits per node
> or directory. Based on archive size, the max. depth of directories vary. After
> we have reached our max. depth, we will then have an arbitrary number of zip
> files, each named with the batch's start number suffixed with `.zip`.
> As an example, if we have a batch size of 1000, we will have the following
> resolutions:
> </br>
> `/blocks/000/000/000/000/000/0000.zip` (first thousand 0-999)
> </br>
> `/blocks/000/000/000/000/000/1000.zip` (second thousand 1000-1999)
> </br>
> etc._
>
> _**NOTE**: assume that for each test the configured batch size is `10` in
> order to have a more manageable environment. An example resolution for a
> zip would be:
> </b>
> `/blocks/000/000/000/000/000/00/00.zip` (first ten 0-9)
> `/blocks/000/000/000/000/000/00/10.zip` (second ten 10-19)
> </b>
> etc._
>
> _**NOTE**: assume that the Block-Node under test is configured to have the
> `Files Historic Persistence`'s Retention Policy threshold set to `10`._
>
> _**NOTE**: assume that for each test there are no blocks persisted initially._
>
> _**NOTE**: assume that the Block-Node under test is configured to have the
> compression algorithm set to `ZStandard` so the file extension for an archived
> block will be `.blk.zstd`._

|                                Test Case ID | Test Name                                                   | Implemented ( :white_check_mark: / :x: ) |
|--------------------------------------------:|:------------------------------------------------------------|:----------------------------------------:|
|                                             | _**<br/>[Happy Path Tests](#happy-path-tests)<br/>&nbsp;**_ |                                          |
| [E2ETC_HP_FHPRP_0001](#E2ETC_HP_FHPRP_0001) | `Verify Retention Policy Successful Delete`                 |                   :x:                    |
|                                             | _**<br/>[Error Tests](#error-tests)<br/>&nbsp;**_           |                                          |
|   [E2ETC_E_FRPRP_0001](#E2ETC_E_FRPRP_0001) | `Verify Retention Policy Failure During Delete`             |                   :x:                    |

### Happy Path Tests

#### E2ETC_HP_FHPRP_0001

##### Test Name

`Verify Retention Policy Successful Delete`

##### Scenario Description

Blocks are persisted in range `0-9` (the only ones currently in existence).
A Persisted Notification is published to the node's messaging system for blocks
persisted in range `0-9` with priority higher than the
`Files Historic Persistence`'s . The `Files Historic Persistence` will then
proceed to archive the batch of blocks in a zip file. These blocks are
available. After that we persist the next 10 blocks (10-19), publish a
Persisted Notification for blocks persisted in range `10-19` with priority
higher than the `Files Historic Persistence`'s, and then the
`Files Historic Persistence` will archive the next batch of blocks in a zip.
Then, the Retention Policy will delete the oldest archived batch (zip file)
because the threshold will be exceeded.

##### Requirements

The `Files Historic Persistence` MUST archive blocks in batches.</br>
Each batch MUST be archived in a single zip file.</br>
The created zip file MUST be located at the resolved path.<br/>
The `Files Historic Persistence` MUST delete the oldest archived batch(es)
(zip file(s)) once the Retention Policy threshold is exceeded.</br>

##### Expected Behaviour

- It is expected that the `Files Historic Persistence` will archive a batch
  successfully, creating a zip file at the resolved location.
- It is expected that the `Files Historic Persistence` will delete the oldest
  archived batch(es) (zip file(s)) once the Retention Policy threshold is
  exceeded.

##### Preconditions

- Blocks in range `0-9` are persisted (the only ones currently in existence).
  - The system-wide block provision facility is able to provide these blocks
    with priority higher than the `Files Historic Persistence`'s.
- A Persisted Notification is published to the node's messaging system for
  blocks persisted in range `0-9` with priority higher than the
  `Files Historic Persistence`'s.
  - Blocks in range `0-9` are verified available.
- Blocks in range `10-19` are persisted.
  - The system-wide block provision facility is able to provide these blocks
    with priority higher than the `Files Historic Persistence`'s.
- A Persisted Notification is published to the node's messaging system for
  blocks persisted in range `10-19` with priority higher than the
  `Files Historic Persistence`'s.
  - Blocks in range `10-19` are verified available.

##### Input

- Persisted Notification for blocks persisted in range `0-9` with priority
  higher than the `Files Historic Persistence`'s.
- Persisted Notification for blocks persisted in range `10-19` with priority
  higher than the `Files Historic Persistence`'s.

##### Output

- Regular file: `/blocks/000/000/000/000/000/00/00.zip` does not exist.
- Blocks in range `0-9` are not available.
- Regular file: `/blocks/000/000/000/000/000/00/10.zip` exists.
- Blocks in range `10-19` are available.

##### Other

N/A

---

### Error Tests

#### E2ETC_E_FRPRP_0001

##### Test Name

`Verify Retention Policy Failure During Delete`

##### Scenario Description

The `Files Historic Persistence` will attempt to delete the oldest archived
batches (zip files) when the Retention Policy threshold is exceeded.
It is expected that sometimes the delete operation will fail due to an error.
In such cases, the `Files Historic Persistence` must continue to function and
accept new data. It must also log the error and increment a metric to track
failed delete operations of the Retention Policy.

##### Requirements

The `Files Historic Persistence` MUST persist blocks.<br/>
The `Files Historic Persistence` MUST retain a configured amount of blocks and
achieve rolling history.<br/>
The `Files Historic Persistence` MUST NOT hinder new data incoming even if the
Retention Policy threshold is reached or exceeded.<br/>
The `Files Historic Persistence` MUST continue to function and accept new data
even if an error during the delete operation of the Retention Policy occurs.
</br>
The `Files Historic Persistence` MUST log the error that occurred during the
delete operation of the Retention Policy.</br>
The `Files Historic Persistence` MUST increment a metric to track failed
delete operations of the Retention Policy when an error occurs.</br>

##### Expected Behaviour

- It is expected that the `Files Historic Persistence` will delete an arbitrary
  amount of blocks that exceed the configured Retention Policy threshold when
  new data is received and persisted.
- It is expected that the `Files Historic Persistence` will continue to function
  and accept new data even if an error during the delete operation of the
  Retention Policy occurs.
- It is expected that the `Files Historic Persistence` will log the error
  during the delete operation of the Retention Policy.
- It is expected that the `Files Historic Persistence` will increment a metric
  to track failed delete operations of the Retention Policy.

##### Preconditions

- Blocks in range `0-9` are persisted (the only ones currently in existence).
  - The system-wide block provision facility is able to provide these blocks
    with priority higher than the `Files Historic Persistence`'s.
- A Persisted Notification is published to the node's messaging system for
  blocks persisted in range `0-9` with priority higher than the
  `Files Historic Persistence`'s.
  - Blocks in range `0-9` are verified available.
- Blocks in range `10-19` are persisted.
  - The system-wide block provision facility is able to provide these blocks
    with priority higher than the `Files Historic Persistence`'s.
- A Persisted Notification is published to the node's messaging system for
  blocks persisted in range `10-19` with priority higher than the
  `Files Historic Persistence`'s.
  - Simulate an error during the delete operation of the Retention Policy.

##### Input

- Persisted Notification for blocks persisted in range `0-9` with priority
  higher than the `Files Historic Persistence`'s.
- Persisted Notification for blocks persisted in range `10-19` with priority
  higher than the `Files Historic Persistence`'s. Simulate an error during the
  delete operation of the Retention Policy.

##### Output

- An error is logged during the delete operation of the Retention Policy.
- A metric is incremented to track failed delete operations of the Retention
  Policy.
- Subsequent attempts to send new notifications are successful, and the
  `Files Historic Persistence` continues to function normally (write).
