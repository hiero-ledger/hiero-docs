# E2E Test Plan - `Files Recent Persistence`

## Overview

Persistence is a key component of the Block-Node. We need to ensure that we have
good and reliable persistence plugins that can be used by the Block-Node. We
support local file system persistence for recently received and verified blocks
which we call `Files Recent Persistence`. Also, complementary to the persistence
capability itself, we have the ability to retrieve (read) blocks that have been
persisted. `Block Provision` is another key component of the Block-Node.
The `Files Recent Persistence` is however an optional component, meaning that
the system is able to operate without it present and loaded.

This E2E test plan is designed to highlight the key scenarios that need to be
tested for end results in order to ensure the correctness and proper working of
the `Files Recent Persistence` as well as the complementary `Block Provision`
logic. Essentially, this test plan describes the intended behavior of the
`Files Recent Persistence` and the expected results it produces.

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
- **Scope**: The `Files Recent Persistence` will accept verified blocks through
  the node's messaging system and will store them under its root.
  The `Files Recent Persistence` will keep a limited number of blocks in its
  root path and will then proceed to delete the oldest blocks (rolling history).
  That is the full scope of the `Files Recent Persistence`. While the blocks are
  stored and available, they can be queried by other components of the system.
- **Persistence Results**: `Files Recent Persistence` publishes persistence
  results to the node's messaging system. These results will be pushed to
  subscribed handlers.

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
> the next block to be received to be with number 0000000000001234567 which
> resolves to `/blocks/000/000/000/000/123/4/0000000000001234567.blk.zstd`. This
> is enough to illustrate the path resolution logic as well. Also, assume that
> the block is valid, has content and will successfully pass verification.
> Finally, assume that the above-mentioned block is NOT persisted before the
> start of each test._
>
> _**NOTE**: assume that we have a working verification because the
> `Files Recent Persistence` relies on blocks to be verified in order to execute
> any logic._
>
> _**NOTE**: assume that the Block-Node under test is configured to have the
> compression algorithm set to `ZStandard` so the file extension for a persisted
> block will be `.blk.zstd`_

|                            Test Case ID | Test Name                                                               | Implemented ( :white_check_mark: / :x: ) |
|----------------------------------------:|:------------------------------------------------------------------------|:----------------------------------------:|
|                                         | _**<br/>[Happy Path Tests](#happy-path-tests)<br/>&nbsp;**_             |                                          |
| [E2ETC_HP_FRP_0001](#E2ETC_HP_FRP_0001) | `Verify Acknowledgement Returned After Successful Persistence`          |                   :x:                    |
| [E2ETC_HP_FRP_0002](#E2ETC_HP_FRP_0002) | `Verify Persisted Block File Location`                                  |                   :x:                    |
| [E2ETC_HP_FRP_0003](#E2ETC_HP_FRP_0003) | `Verify Persisted Block File Content`                                   |                   :x:                    |
| [E2ETC_HP_FRP_0004](#E2ETC_HP_FRP_0004) | `Verify Persisted Block File Discoverable After Successful Persistence` |                   :x:                    |
| [E2ETC_HP_FRP_0005](#E2ETC_HP_FRP_0005) | `Verify Persisted Block File can be Overwritten`                        |                   :x:                    |
| [E2ETC_HP_FRP_0006](#E2ETC_HP_FRP_0006) | `Verify Persisted Block in Rapid Succession`                            |                   :x:                    |
|                                         | _**<br/>[Error Tests](#error-tests)<br/>&nbsp;**_                       |                                          |
|   [E2ETC_E_FRP_0001](#E2ETC_E_FRP_0001) | `Verify EndOfStream returned on IO Failure`                             |                   :x:                    |
|   [E2ETC_E_FRP_0002](#E2ETC_E_FRP_0002) | `Verify Persisted Block File Cleanup on IO Failure`                     |                   :x:                    |

### Happy Path Tests

#### E2ETC_HP_FRP_0001

##### Test Name

`Verify Acknowledgement Returned After Successful Persistence`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified. It will then publish a persistence notification to the internal
messaging system.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The Block-Node MUST return an acknowledgement to the publisher, granted that
there are no issues with the persistence process.

##### Expected Behaviour

- It is expected that the Block-Node will respond with an acknowledgement to the
  publisher's request in this situation.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- An acknowledgement is returned to the publisher.

##### Other

N/A

---

#### E2ETC_HP_FRP_0002

##### Test Name

`Verify Persisted Block File Location`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
Blocks MUST be persisted on the filesystem at a specific resolved location.

##### Expected Behaviour

- It is expected that a regular file is written at the properly resolved
  location.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- Regular Readable File:
  `/blocks/000/000/000/000/123/4/0000000000001234567.blk.zstd` exists.

##### Other

N/A

---

#### E2ETC_HP_FRP_0003

##### Test Name

`Verify Persisted Block File Content`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
Persisted blocks MUST have the exact same binary content as the original block
streamed to the Block-Node and received by the`Files Recent Persistence`.

##### Expected Behaviour

- It is expected that a regular file is written at the properly resolved
  location
- It is expected that the content of the file is the same as the original block
  streamed.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- Regular Readable File:
  `/blocks/000/000/000/000/123/4/0000000000001234567.blk.zstd` has the same
  binary content as the binary data received as block items for the specified
  block.

##### Other

N/A

---

#### E2ETC_HP_FRP_0004

##### Test Name

`Verify Persisted Block File Discoverable After Successful Persistence`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
Persisted blocks MUST be discoverable through the Block-Node's public API.<br/>
Persisted blocks MUST be readable and accessible through the Block-Node's
public API.<br/>
A persisted blocks MUST be discoverable after it has been fully persisted.<br/>
A persisted block MUST NOT be discoverable before the block is fully persisted.

##### Expected Behaviour

- It is expected that after the persistence of the given block is successful,
  the block will be accessible via the node's public API.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.
- A client that can call the public APIs to read the block (i.e. `getBlock`).
- Client receives not found if initially attempts to read the block under test
  (expected).

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- Client is able to use the node's public API to read the persisted block
  (i.e. `getBlock`).

##### Other

N/A

---

#### E2ETC_HP_FRP_0005

##### Test Name

`Verify Persisted Block File can be Overwritten`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified. If the received and verified block's resolved path already exists,
no matter what the reason is, it must be overwritten with the received block's
data. An Acknowledged block is deemed final and immutable (especially when the
next block is received, verified, persisted and acknowledged), but that is
outside the scope of the `Files Recent Persistence`. As long as data comes in to
the`Files Recent Persistence` it will be persisted at the resolved path. The
`Files Recent Persistence` is not a decision maker, it simply persists all data
it receives.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
If a file is present at the resolved location of a block, the plugin MUST
truncate and overwrite that file with the received data.<br/>
The `Files Recent Persistence` MUST NOT decide which data to retain, but MUST
overwrite any pre-existing file.<br/>
The plugin MUST persist all data it receives via the messaging facility.

##### Expected Behaviour

- It is expected that when the Block-Node receives the next in sequence block
  and then verifies it, the `Files Recent Persistence` will attempt to persist
  it at the resolved path.
- If a file already exists at that path, it will be truncated and overwritten
  with the received data.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.
- A regular file exists at the resolved location of the block under test with
  some arbitrary test data.
- A client that can call the public API to read the block (i.e. `getBlock`).

##### Input

- Valid block `0000000000001234567` is streamed as items, waiting for
  Acknowledgement (ensure it is persisted).

##### Output

- Regular Readable File:
  `/blocks/000/000/000/000/123/4/0000000000001234567.blk.zstd` exists.
- The file's binary content is the same as the binary data received via the
  stream
- A client is able to read the persisted block through the public
  API (i.e. `getBlock`).

##### Other

N/A

---

#### E2ETC_HP_FRP_0006

##### Test Name

`Verify Persisted Block in Rapid Succession`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The `Files Recent Persistence` MUST be able to persist blocks in rapid
succession with no apparent issues.

##### Expected Behaviour

- It is expected that a regular file is written at the properly resolved
  location for each block streamed.
- It is expected that an Acknowledgement is returned to the publisher for each
  block streamed when everything is successful.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.

##### Input

- Valid blocks `0000000000001234000` to `0000000000001235000` are rapidly
  streamed as items.

##### Output

- Regular Readable Files:
  `/blocks/000/000/000/000/123/4/0000000000001234000.blk.zstd` to
  `/blocks/000/000/000/000/123/5/0000000000001235000.blk.zstd` exist.
- These files have the same binary content as the original sent blocks.
- The persisted blocks are discoverable through public API calls.
- The persisted blocks are readable through public API calls.

##### Other

N/A

---

### Error Tests

#### E2ETC_E_FRP_0001

##### Test Name

`Verify EndOfStream returned on IO Failure`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.</br>
IO failures MAY occur during the write process.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The Block-Node MUST return an EndOfStream with PERSISTENCE_FAILED to the
publisher when an IO failure occurs.

##### Expected Behaviour

- It is expected that the Block-Node will return and EndOfStream with
  PERSISTENCE_FAILED if an IO failure occurs during write.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.
- A way to simulate an IO issue for the resolved block.

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- An EndOfStream with PERSISTENCE_FAILED is returned to the publisher.

##### Other

N/A

---

#### E2ETC_E_FRP_0002

##### Test Name

`Verify Persisted Block File Cleanup on IO Failure`

##### Scenario Description

`Files Recent Persistence` will persist a block after it has been received and
verified.</br>
IO failures MAY occur during the write process.</br>
Files and data related to the current block SHALL NOT be present on the
filesystem if an IO failure occurs during the write process.

##### Requirements

The `Files Recent Persistence` MUST persist blocks.<br/>
The `Files Recent Persistence` MUST ensure that no files or data related to the
current block are left on the filesystem after an IO failure occurs during the
write process.

##### Expected Behaviour

- It is expected that all data potentially written to the filesystem is cleaned
  if an IO failure occurs during write.
- No files or data related to the current block must be present after such
  failure.

##### Preconditions

- A publisher that is able to stream to the Block-Node under test.
- A way to simulate an IO issue for the resolved block.

##### Input

- Valid block `0000000000001234567` is streamed as items.

##### Output

- Regular Readable File:
  `/blocks/000/000/000/000/123/4/0000000000001234567.blk.zstd` does not exist.
- No data of the received through the stream block is present on the filesystem.

##### Other

N/A

---
