# `Stream Publisher Plugin` Test Plan

## Overview

The `Stream Publisher Plugin` is an essential part of the Block Node.

The plugin enables publishers of the Block Stream to connect and publish
the Block Stream to the Block Node.

The plugin allows for multiple publishers to stream simultaneously to the Block
Node. A gRPC bidirectional stream is established between the publisher and the
Block Node on connect. This allows not only for the publisher to publish the
Block Stream, but also for the Block Node to send responses back to the
publisher.

This E2E test plan is designed to highlight the key scenarios that need to be
tested for end results in order to ensure the correctness and proper working of
the `Stream Publisher Plugin`. Essentially, this test plan describes the
intended behavior of the `Stream Publisher Plugin` and the expected results it
produces.

## Key Considerations

- **Protocol**: The plugin complies with and implements the
  [publishBlockStream protocol](../../design/communication-protocol/publish-block-stream.md).
- **Internal Communication**: Internally, the plugin relies on verification and
  persistence notifications in order to determine the responses it will send to
  publishers that are connected.
- **Publisher Handler**: For each connected publisher, the plugin will create a
  new, dedicated Publisher Handler. Each handler is responsible for receiving
  requests from the publisher it handles and to send responses to the same
  publisher.
- **Publisher Manager**: All handlers exchange messages with the Publisher
  Manager, which is responsible for coordinating all handlers by sending them
  actions the handler must perform. The Publisher Manager is also responsible
  for propagating the Block Stream to the Block Node's internal messaging
  system in block order (a monotonously incrementing sequence of block number,
  i.e. `long`). The manager follows it's internal state and is able to react to
  unfavorable events, such as a failed verification of a block, or a handler
  that disconnects abruptly, to name a few. The manager then determines what
  responses should be sent (if any) to publishers via their handlers (if any),
  what actions the handlers must perform or if to end the bidi stream/s.
- **Acknowledgements**: When a block is successfully verified and persisted,
  the plugin will send an acknowledgement to all connected publishers for that
  block, or for a block with a higher number. When an acknowledgement is
  received by a publisher, the publisher can then safely assume that all blocks
  up to and including the acknowledged block have been successfully verified
  and persisted.

## Test Scenarios

|            Test Case ID | Test Name                                                                     | Implemented ( :white_check_mark: / :x: ) |
|------------------------:|:------------------------------------------------------------------------------|:----------------------------------------:|
|                         | _**<br/>[Happy Path Tests](#happy-path-tests)<br/>&nbsp;**_                   |                                          |
| [TC-HP-001](#tc-hp-001) | Verify Single Publisher Acknowledgements                                      |            :white_check_mark:            |
| [TC-HP-002](#tc-hp-002) | Verify Multi Publisher Acknowledgements                                       |            :white_check_mark:            |
| [TC-HP-003](#tc-hp-003) | Verify Multi Publisher Skip                                                   |            :white_check_mark:            |
| [TC-HP-004](#tc-hp-004) | Verify Multi Publisher Duplicate Block                                        |            :white_check_mark:            |
| [TC-HP-005](#tc-hp-005) | Verify Block Node Behind                                                      |            :white_check_mark:            |
| [TC-HP-006](#tc-hp-006) | Verify Publisher EndStream                                                    |            :white_check_mark:            |
|                         | _**<br/>[Failed Verification Tests](#failed-verification-tests)<br/>&nbsp;**_ |                                          |
| [TC-FV-001](#tc-fv-001) | Verify Failed Verification Handling                                           |                   :x:                    |
|                         | _**<br/>[Error Tests](#error-tests)<br/>&nbsp;**_                             |                                          |
|   [TC-E-001](#tc-e-001) | Verify Response on Client Error (Block Items Request - Broken Header)         |                   :x:                    |
|   [TC-E-002](#tc-e-002) | Verify Response on Client Error (Block Items Request - Premature Header)      |                   :x:                    |
|   [TC-E-003](#tc-e-003) | Verify Response on Client Error (Block Items Request - No Items)              |                   :x:                    |
|   [TC-E-004](#tc-e-004) | Verify Response on Server Error                                               |                   :x:                    |
|   [TC-E-005](#tc-e-005) | Verify Publisher Disconnects Abruptly                                         |                   :x:                    |

> **Please note**: unless otherwise specified, all tests assume that the Block Node
> expects to receive next block number `0000000000000000000`. No Blocks have been
> published yet. No Blocks have been verified or persisted yet.

### Happy Path Tests

#### TC-HP-001

##### Test Name

`Verify Single Publisher Acknowledgements`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. If the published Blocks are valid, meaning they
pass verification, and then persisted properly, the publishers will receive
an acknowledgement for that Block, or for a Block with a higher number.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST send acknowledgements to all publishers
when a received Block is successfully verified and persisted.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and publishes
  valid Blocks, the publisher will receive an acknowledgement for Blocks that
  are successfully verified and persisted.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
  - A working Block Verification component which includes sending
    Verification Notifications with verification results.
  - A working Block Persistence component which includes sending
    Persistence Notifications with persistence results.
- A Publisher that is able to connect and stream valid Blocks to the
  Block Node.

##### Input

- A Publisher connects to the Block Node and streams valid Blocks starting from
  `0000000000000000000`.

##### Output

- The Publisher receives acknowledgements for Blocks that are successfully
  verified and persisted.

##### Other

N/A

---

#### TC-HP-002

##### Test Name

`Verify Multi Publisher Acknowledgements`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. If the published Blocks are valid, meaning they
pass verification, and then persisted properly, the publishers will receive
an acknowledgement for that Block, or for a Block with a higher number.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST send acknowledgements to all publishers
when a received Block is successfully verified and persisted.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and publishes
  valid Blocks, all publishers connected will receive an acknowledgement for
  Blocks that are successfully verified and persisted.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
  - A working Block Verification component which includes sending
    Verification Notifications with verification results.
  - A working Block Persistence component which includes sending
    Persistence Notifications with persistence results.
- Multiple Publishers (A, B and C) that are able to connect and stream valid
  Blocks to the Block Node.

##### Input

- Publisher A, B and C connect to the Block Node. Publisher A streams valid
  Blocks starting from `0000000000000000000`. All publishers are expected to
  receive acknowledgements for Blocks that are successfully verified and
  persisted.

##### Output

- All Publishers receive acknowledgements for Blocks that are successfully
  verified and persisted.

##### Other

N/A

---

#### TC-HP-003

##### Test Name

`Verify Multi Publisher Skip`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a Block is currently being streamed or is
already streamed in full, but still being processed (not acknowledged yet), the
plugin will respond with the SKIP message to any other publisher that attempts
to publish a Block with the same number as the Block currently being streamed or
processed.

The publisher that is currently streaming a Block can send subsequent
requests that consist of items, which are part of the Block it is currently
streaming. The plugin will then simply keep accepting those items.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST send acknowledgements to all publishers
when a received Block is successfully verified and persisted.<br/>
The `Stream Publisher Plugin` MUST respond with a SKIP message to any
publisher that attempts to publish a Block with the same number as a Block
that is currently being streamed or is already streamed in full, but still
being processed (not acknowledged yet).<br/>

##### Expected Behaviour

- It is expected that when multiple publishers connect to a Block Node and one
  publisher is streaming valid Blocks, any other publisher that attempts to
  stream a Block with the same number as the Block currently being streamed or
  processed will receive a SKIP message in response.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
  - A working Block Verification component which includes sending
    Verification Notifications with verification results.
  - A working Block Persistence component which includes sending
    Persistence Notifications with persistence results.
- Multiple Publishers (A, B and C) that are able to connect and stream valid
  Blocks to the Block Node.

##### Input

- Publisher A, B and C connect to the Block Node. Publisher A starts streaming
  valid Block `0000000000000000000`. While Publisher A is still streaming the
  Block, Publisher B and C attempt to stream the same Block
  `0000000000000000000`.

##### Output

- Publishers B and C receive a SKIP message in response to their
  attempt to stream Block `0000000000000000000`.

##### Other

N/A

---

#### TC-HP-004

##### Test Name

`Verify Multi Publisher Duplicate Block`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a Block is already acknowledged, meaning it
has been successfully verified and persisted, the plugin will respond with an
EndOfStream with the DUPLICATE_BLOCK code to any other publisher that attempts
to publish the same Block again. The connection with the publisher that sent
the duplicate Block will then be closed.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST send acknowledgements to all publishers
when a received Block is successfully verified and persisted.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
DUPLICATE_BLOCK code to any publisher that attempts to publish a Block that
has already been acknowledged.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that sent the duplicate Block.<br/>

##### Expected Behaviour

- It is expected that when multiple publishers connect to a Block Node
  and a publisher publishes a valid block that gets acknowledged, meaning it
  has been successfully verified and persisted, any other publisher that
  attempts to stream the same Block again will receive an EndOfStream with the
  DUPLICATE_BLOCK code in response and the connection with that publisher will
  be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
  - A working Block Verification component which includes sending
    Verification Notifications with verification results.
  - A working Block Persistence component which includes sending
    Persistence Notifications with persistence results.
- Multiple Publishers (A and B) that are able to connect and stream valid
  Blocks to the Block Node.

##### Input

- Publisher A and B connect to the Block Node. Publisher A streams valid Block
  `0000000000000000000` which gets acknowledged. Publisher B then attempts to
  stream the same Block `0000000000000000000`.

##### Output

- Publisher B receives an EndOfStream with the DUPLICATE_BLOCK code in
  response to its attempt to stream Block `0000000000000000000` and the
  connection is closed.

##### Other

N/A

---

#### TC-HP-005

##### Test Name

`Verify Block Node Behind`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a publisher sends a Block with a number
that is higher than the next expected Block number by the Block Node, the plugin
will respond with an EndOfStream with the BEHIND code to that publisher. The
connection with that publisher will then be closed.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
BEHIND code to any publisher that sends a Block with a number that is higher
than the next expected Block number by the Block Node.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that sent the Block with a number that is higher than the next expected
Block number by the Block Node.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and sends a
  Block with a number that is higher than the next expected Block number by
  the Block Node, the publisher will receive an EndOfStream with the BEHIND
  code in response and the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect and stream valid Blocks to the
  Block Node.

##### Input

- A Publisher connects to the Block Node and sends valid Block
  `0000000000000000001` while the Block Node expects to receive next Block
  number `0000000000000000000`.

##### Output

- The Publisher receives an EndOfStream with the BEHIND code in response to
  its attempt to stream Block `0000000000000000001` and the connection is
  closed.

##### Other

N/A

---

#### TC-HP-006

##### Test Name

`Verify Publisher EndStream`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a publisher sends an EndStream message with
any code, the plugin will close the connection with that publisher.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST close the connection with a publisher
that sends an EndStream message with any code.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and sends an
  EndStream message with any code, the connection with that publisher will be
  closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect and stream valid Blocks to the
  Block Node.

##### Input

- A Publisher connects to the Block Node and sends an EndStream message with
  any code.

##### Output

- The connection with the Publisher is closed.

##### Other

N/A

---

### Failed Verification Tests

#### TC-FV-001

##### Test Name

`Verify Failed Verification Handling`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a Block fails verification, the plugin
will notify all handlers of the event. Then, all handlers will respond with a
RESEND message to their respective publishers, except the handler that supplied
the invalid Block, which should respond with an EndOfStream with the
BAD_BLOCK_PROOF code. The connection with the publisher that supplied the
invalid Block will then be closed.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST notify all registered handlers when
a Block fails verification.<br/>
The `Stream Publisher Plugin` MUST respond with a RESEND message to all
publishers, except the publisher that supplied the invalid Block, which
should receive an EndOfStream with the BAD_BLOCK_PROOF code.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that supplied the invalid Block.<br/>

##### Expected Behaviour

- It is expected that when multiple publishers connect to a Block Node and one
  publisher streams an invalid Block, all other publishers will receive a
  RESEND message in response, while the publisher that supplied the invalid
  Block will receive an EndOfStream with the BAD_BLOCK_PROOF code. The
  connection with the publisher that supplied the invalid Block will then be
  closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
  - A working Block Verification component which includes sending
    Verification Notifications with verification results.
  - A working Block Persistence component which includes sending
    Persistence Notifications with persistence results.
- Multiple Publishers (A, B and C) that are able to connect to the Block Node.
- At least one Publisher (A) that is able to stream an invalid Block to the
  Block Node.

##### Input

- Publisher A, B and C connect to the Block Node. Publisher A starts streaming
  invalid Block `0000000000000000000`.

##### Output

- Publisher A receives an EndOfStream with the BAD_BLOCK_PROOF code and the
  connection is closed.
- Publishers B and C receive a RESEND message.

##### Other

N/A

---

### Error Tests

#### TC-E-001

##### Test Name

`Verify Response on Client Error (Block Items Request - Broken Header)`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When the client (publisher) sends an invalid
Block Items request, the plugin will respond with an EndOfStream with the
INVALID_REQUEST code and close the connection with the publisher. An invalid
Block Items request can be a request with a broken header which cannot be
parsed.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
INVALID_REQUEST code when the client (publisher) sends an invalid Block Items
request with a broken header.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that sent the invalid request.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and sends an
  invalid Block Items request with a broken header, the publisher will receive
  an EndOfStream with the INVALID_REQUEST code and the connection will be
  closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect to the Block Node and send an invalid
  Block Items request with a broken header.

##### Input

- A Publisher connects to the Block Node and sends an invalid Block Items
  request with a broken header.

##### Output

- The Publisher receives an EndOfStream with the INVALID_REQUEST code and the
  connection is closed.

##### Other

N/A

---

#### TC-E-002

##### Test Name

`Verify Response on Client Error (Block Items Request - Premature Header)`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When the client (publisher) sends an invalid
Block Items request, the plugin will respond with an EndOfStream with the
INVALID_REQUEST code and close the connection with the publisher. An invalid
Block Items request can be a request with a premature header, meaning that a
request is received with a header as first item, but the publisher handler has
not yet received all items for the previous Block (it is currently streaming
a Block).

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
INVALID_REQUEST code when the client (publisher) sends an invalid Block Items
request with a premature header.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that sent the invalid request.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and sends an
  invalid Block Items request with a premature header, the publisher will
  receive an EndOfStream with the INVALID_REQUEST code and the connection will
  be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect to the Block Node and send an invalid
  Block Items request with a premature header.

##### Input

- A Publisher connects to the Block Node and sends an invalid Block Items
  request with a premature header.

##### Output

- The Publisher receives an EndOfStream with the INVALID_REQUEST code and the
  connection is closed.

##### Other

N/A

---

#### TC-E-003

##### Test Name

`Verify Response on Client Error (Block Items Request - No Items)`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When the client (publisher) sends an invalid
Block Items request, the plugin will respond with an EndOfStream with the
INVALID_REQUEST code and close the connection with the publisher. An invalid
Block Items request can be a request with no items, meaning that a request is
received without any items.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
INVALID_REQUEST code when the client (publisher) sends an invalid Block Items
request with no items.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
that sent the invalid request.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and sends an
  invalid Block Items request with no items, the publisher will receive an
  EndOfStream with the INVALID_REQUEST code and the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect to the Block Node and send an invalid
  Block Items request with no items.

##### Input

- A Publisher connects to the Block Node and sends an invalid Block Items
  request with no items.

##### Output

- The Publisher receives an EndOfStream with the INVALID_REQUEST code and the
  connection is closed.

##### Other

N/A

---

#### TC-E-004

##### Test Name

`Verify Response on Server Error`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When the server encounters an unexpected error,
when handling the request of the publisher, the plugin will respond with an
EndOfStream with the ERROR code and close the connection with the publisher.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST respond with an EndOfStream with the
ERROR code when the server encounters an unexpected error when handling
a request from a publisher.<br/>
The `Stream Publisher Plugin` MUST close the connection with the publisher
when the server encounters an unexpected error while handling the request
of that publisher.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and the server
  encounters an unexpected error while handling a request from the publisher,
  the publisher will receive an EndOfStream with the ERROR code and the
  connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- A Publisher that is able to connect to the Block Node and send a request.
- A way to simulate an unexpected server error while handling a request
  from the publisher.

##### Input

- A Publisher connects to the Block Node and sends a request. An unexpected
  server error is simulated while handling the request.

##### Output

- The Publisher receives an EndOfStream with the ERROR code and the connection
  is closed.

##### Other

N/A

---

#### TC-E-005

##### Test Name

`Verify Publisher Disconnects Abruptly`

##### Scenario Description

`Stream Publisher Plugin` will allow publishers to connect and publish the
Block Stream to the Block Node. When a publisher disconnects abruptly, the
plugin will handle the disconnection gracefully without crashing or leaking
resources.

##### Requirements

The `Stream Publisher Plugin` MUST allow publishers to connect.<br/>
The `Stream Publisher Plugin` MUST accept valid Blocks streamed by
publishers.<br/>
The `Stream Publisher Plugin` MUST be able to detect and handle abrupt
disconnections from publishers gracefully without crashing or leaking
resources.<br/>
The `Stream Publisher Plugin` MUST clean up any resources associated
with the disconnected publisher.<br/>
The `Stream Publisher Plugin` MUST ensure that the disconnection of one
publisher does not affect the operation of other connected publishers.<br/>

##### Expected Behaviour

- It is expected that when a publisher connects to a Block Node and then
  disconnects abruptly, the plugin will detect and handle the disconnection
  gracefully without crashing or leaking resources. Any resources associated
  with the disconnected publisher will be cleaned up and the operation of other
  connected publishers will not be affected.

##### Preconditions

- A Block Node with:
  - A working `Stream Publisher Plugin`.
- Multiple Publishers (A and B) that are able to connect to the Block Node.
- At least one Publisher (A) that will disconnect abruptly after
  connecting to the Block Node.

##### Input

- Publisher A and B connect to the Block Node. Publisher A then disconnects
  abruptly. Publisher B continues to operate normally.

##### Output

- The plugin detects and handles the disconnection of Publisher A gracefully
  without crashing or leaking resources. Any resources associated with
  Publisher A are cleaned up and the operation of Publisher B is not affected.

##### Other

N/A

---
