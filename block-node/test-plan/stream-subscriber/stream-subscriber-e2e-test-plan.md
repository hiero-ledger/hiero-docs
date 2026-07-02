# `Stream Subscriber Plugin` Test Plan

## Overview

The `Stream Subscriber Plugin` allows clients to subscribe (issue a request for
gRPC server streaming) to a Block Node and receive a stream of Blocks based on
the specified request criteria.

Blocks could be streamed from the Block Node's history and/or from the live
Block Stream as new Blocks are being published to the Block Node.

## Key Considerations

- **SubscriberServicePlugin**: The plugin accepts gRPC requests from clients
  and then opens a `BlockStreamSubscriberSession` for each request. Each session
  runs on its own thread. The
  [subscribeBlockStream](../../../protobuf-sources/src/main/proto/block-node/api/block_stream_subscribe_service.proto)
  defines the gRPC API.
- **BlockStreamSubscriberSession**: Each session handles a single gRPC
  request from a client on a dedicated thread. The session supplies the Blocks
  to the client based on the request criteria. Blocks can be supplied from
  history and/or from the live Block Stream, depending on where the next Block
  to be sent to the client is located. Going to history and live is
  interchangeable.
- **Request Parameters**: A request specifies start and end Block numbers, both
  inclusive. Both start and end must be whole numbers, as only whole numbers
  are valid Block numbers. It is possible, however, to also specify -1L
  (signed long) to both or any of start and end. Note that the gRPC spec uses
  `uint64` for both values, so `uint64_max` (`0xFFFFFFFFFFFFFFFF`) is the
  equivalent of -1L signed long. A start of -1L means "start from first
  available Block", while an end of -1L means "stream indefinitely". When both
  start and end are specified as -1L, it means that the request is for the next
  complete live Block, whichever it may be, and then continue to stream
  indefinitely.
- **Request Validation**: Each request is validated to ensure that the
  specified criteria are valid.
- **Request Fulfillment Check**: After a request is validated, a
  check is performed to ensure that the request can be fulfilled. A request
  that is valid might not be fulfillable at the moment.
- **Maximum Request Duration**: There is a configured maximum duration for which
  a request can remain active, measured in count of Blocks sent. If the count of
  Blocks sent exceeds that value, the request is considered fulfilled and a
  SUCCESS code is sent to the client, even if the actual end criteria of the
  request have not been met yet. After sending the SUCCESS code, the connection
  is closed.
- **Future Requests**: It is possible to subscribe to Blocks that are not yet
  available in the Block Node. This only applies when the request has specified
  a start Block. A future request is one where the start Block is greater than
  the highest Block number currently available to be supplied by the Block Node,
  be that from history or the live stream. In that case, a check is made if the
  request is too far into the future. The last permitted start is a value that
  is calculated by adding a configured maximum future offset to the highest
  Block number currently available. If the start Block is greater than that
  value, the request is considered too far into the future and is rejected as
  unfulfillable. If the request is not too far into the future, the session
  will essentially _wait_ until the start Block becomes available, at which
  point the session will start streaming the Blocks to the client.
- **End Block**: After each block is streamed in full to the client, an
  EndBlock message is sent to the client to indicate that the Block has been
  fully sent.

## Test Scenarios

|              Test Case ID | Test Name                                                                                     | Implemented ( :white_check_mark: / :x: ) |
|--------------------------:|:----------------------------------------------------------------------------------------------|:----------------------------------------:|
|                           | _**<br/>[Positive Request Validation Tests](#positive-request-validation-tests)<br/>&nbsp;**_ |                                          |
| [TC-PRV-001](#tc-prv-001) | Verify Valid Request: Live Stream                                                             |                   :x:                    |
| [TC-PRV-002](#tc-prv-002) | Verify Valid Request: Closed Range                                                            |                   :x:                    |
| [TC-PRV-003](#tc-prv-003) | Verify Valid Request: Start First Available, End Specified                                    |                   :x:                    |
| [TC-PRV-004](#tc-prv-004) | Verify Valid Request: Start Specified, Continue indefinitely                                  |                   :x:                    |
| [TC-PRV-005](#tc-prv-005) | Verify Valid Request: Future Block                                                            |                   :x:                    |
| [TC-PRV-006](#tc-prv-006) | Verify Valid Request: Too Far Future Block                                                    |                   :x:                    |
| [TC-PRV-007](#tc-prv-007) | Verify Valid Request: Unfulfillable                                                           |                   :x:                    |
| [TC-PRV-008](#tc-prv-008) | Verify Valid Request: Stream Open Ranged Beyond Request Limit                                 |                   :x:                    |
| [TC-PRV-009](#tc-prv-009) | Verify Valid Request: Stream Close Ranged Beyond Request Limit                                |                   :x:                    |
|                           | _**<br/>[Negative Request Validation Tests](#negative-request-validation-tests)<br/>&nbsp;**_ |                                          |
| [TC-NRV-001](#tc-nrv-001) | Verify Invalid Request: Invalid Start Block                                                   |                   :x:                    |
| [TC-NRV-002](#tc-nrv-002) | Verify Invalid Request: Invalid End Block                                                     |                   :x:                    |
| [TC-NRV-003](#tc-nrv-003) | Verify Invalid Request: Invalid Closed Range                                                  |                   :x:                    |
|                           | _**<br/>[Error Tests](#error-tests)<br/>&nbsp;**_                                             |                                          |
| [TC-ERR-001](#tc-err-001) | Verify Abrupt Client Connection Termination                                                   |                   :x:                    |
| [TC-ERR-002](#tc-err-002) | Verify Server Error                                                                           |                   :x:                    |

> **Please note**: unless otherwise specified, all tests assume that no Blocks
> are historically available in the Block Node at the start of the test and also
> no Blocks have been published live yet. The client should not be concerned
> with whether Blocks are coming from history or live, as long as the Blocks are
> being streamed according to the request criteria. Simply, the client has
> subscribed for the stream and as long as the Block Node is able to provide the
> Blocks according to the request criteria, the client should be receiving them.

### Positive Request Validation Tests

> **Please note**: the positive request validation tests also cover positive
> and negative fulfillment check scenarios, as they are closely related and
> in order to assert the positive request validation, the fulfillment check
> must also be asserted because streaming or any other response can only happen
> if the request is valid and the fulfillment check is executed, whether
> the result thereof is positive or negative. When a request is valid, but the
> fulfillment check is negative, the request is rejected as unfulfillable and
> we expect a NOT_AVAILABLE response to be sent to the client.

#### TC-PRV-001

##### Test Name

`Verify Valid Request: Live Stream`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request for the live
  Block Stream, the `Stream Subscriber Plugin` will validate the request,
  perform a fulfillment check, and then start streaming the Blocks to the
  client based on the request criteria, granted that the request is valid and
  fulfillable.
  - A request for the live stream is always fulfillable since the plugin will
    supply the next available live Block, whichever it may be.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request for live Blocks.
- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000000` onwards.

##### Output

- The Subscriber Client receives the stream of Blocks starting from Block
  `0000000000000000000` onwards as they are published to the Block Node and
  receives a valid EndBlock message after each Block is received in full.

##### Other

N/A

---

#### TC-PRV-002

##### Test Name

`Verify Valid Request: Closed Range`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request for a closed
  range of Blocks (both start and end are whole numbers and end is greater than
  or equal to start), the `Stream Subscriber Plugin` will validate the request,
  perform a fulfillment check, and then start streaming the Blocks to the
  client based on the request criteria, granted that the request is valid and
  fulfillable.
  - Such request can only be fulfilled if the Block Node has at least one block
    historically available and the request is not too far into the future.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.
- It is expected that the client will receive the SUCCESS code after the request
  is fully served.
- It is expected that the connection will be closed after the SUCCESS code is
  received.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available, starting from
    Block `0000000000000000000` onwards.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request for a closed range of Blocks,
  starting from Block `0000000000000000000` to Block `0000000000000000009`.
- The requested Blocks will be published to the Block Node either historically
  or live.

##### Output

- The Subscriber Client receives the stream of Blocks starting from Block
  `0000000000000000000` to Block `0000000000000000009` as they are made
  available to the Block Node and receives a valid EndBlock message after each
  Block is received in full.
- The client receives the SUCCESS code after the request is fully served.
- The connection is closed after the SUCCESS code is received.

##### Other

N/A

---

#### TC-PRV-003

##### Test Name

`Verify Valid Request: Start First Available, End Specified`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request with start as
  first available (-1L) and end as a whole number (greater than or equal to 0),
  the `Stream Subscriber Plugin` will validate the request, perform a
  fulfillment check, and then start streaming the Blocks to the client based
  on the request criteria, granted that the request is valid and fulfillable.
  - Such request can only be fulfilled if the Block Node has at least one block
    historically available and the specified end is greater or equal to that
    fist available block.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.
- It is expected that the client will receive the SUCCESS code after the request
  is fully served.
- It is expected that the connection will be closed after the SUCCESS code is
  received.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available, starting from
    Block `0000000000000000000` onwards.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request with start as first available
  (-1L) and end as Block `0000000000000000009`.
- The requested Blocks will be published to the Block Node either historically
  or live.

##### Output

- The Subscriber Client receives the stream of Blocks starting from Block
  `0000000000000000000` to Block `0000000000000000009` as they are made
  available to the Block Node and receives a valid EndBlock message after each
  Block is received in full.
- The client receives the SUCCESS code after the request is fully served.
- The connection is closed after the SUCCESS code is received.

---

#### TC-PRV-004

##### Test Name

`Verify Valid Request: Start Specified, Continue indefinitely`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request with start as a
  whole number (greater than or equal to 0) and end as -1L (continue
  indefinitely), the `Stream Subscriber Plugin` will validate the request,
  perform a fulfillment check, and then start streaming the Blocks to the
  client based on the request criteria, granted that the request is valid and
  fulfillable.
  - Such request can only be fulfilled if the Block Node has at least one block
    historically available and the specified start is greater or equal to that
    fist available block.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available, starting from
    Block `0000000000000000000` onwards.
- A working Publisher component that will be publishing new Blocks to the
  Block Node which will then be available for streaming to clients.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request with start as Block
  `0000000000000000000` and end as -1L (continue indefinitely).
- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000001` onwards, as Block `0000000000000000000` is
  already available.
- The requested Blocks will be published to the Block Node either historically
  or live.

##### Output

- The Subscriber Client receives the stream of Blocks starting from Block
  `0000000000000000000` onwards as they are published to the Block Node
  and receives a valid EndBlock message after each Block is received in full.

---

#### TC-PRV-005

##### Test Name

`Verify Valid Request: Future Block`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request with start as a
  whole number (greater than or equal to 0) that is greater than the highest
  Block number currently available in the Block Node,
  the `Stream Subscriber Plugin` will validate the request, perform a
  fulfillment check, and then start streaming the Blocks to the client based
  on the request criteria, granted that the request is valid and fulfillable.
  - Such request can only be fulfilled if the specified start is not too far
    into the future. The last permitted start is a value that is calculated by
    adding a configured maximum future offset to the highest Block number
    currently available. If the start Block is greater than that value, the
    request is considered too far into the future and is rejected as
    unfulfillable.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available - for this test
    Block `0000000000000000000`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request with start as Block
  `0000000000000000005` (which is greater than the highest Block number
  currently available, which is `0000000000000000000`) and end as
  `0000000000000000010` (a closed range is sufficient for this assert).
  - The specified start Block (`0000000000000000005`) must not too far into the
    future.
- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000001` onwards, as Block `0000000000000000000` is
  already available.
- The requested Blocks will be published to the Block Node either historically
  or live.

##### Output

- The Subscriber Client receives the stream of Blocks starting from Block
  `0000000000000000005` to Block `0000000000000000010` as they are made
  available to the Block Node and receives a valid EndBlock message after each
  Block is received in full.
- The client receives the SUCCESS code after the request is fully served.
- The connection is closed after the SUCCESS code is received.

##### Other

N/A

---

#### TC-PRV-006

##### Test Name

`Verify Invalid Request: Future Block`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid but cannot be fulfilled, the request will be rejected and an
appropriate error message will be sent to the client. The connection will also
be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL reject requests that cannot be fulfilled
and send an appropriate error message to the client.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request with start as a
  whole number (greater than or equal to 0) that is greater than the highest
  Block number currently available in the Block Node,
  the `Stream Subscriber Plugin` will validate the request, perform a
  fulfillment check, and then determine that the request cannot be fulfilled
  if the specified start is too far into the future. The last permitted start
  is a value that is calculated by adding a configured maximum future offset to
  the highest Block number currently available. If the start Block is greater
  than that value, the request is considered too far into the future and is
  rejected as unfulfillable.
- It is expected that the request will be rejected by sending the
  NOT_AVAILABLE code to the client.
- It is expected that the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - The Block Node has at least one Block historically available - for this test
    Block `0000000000000000000`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request with start as Block
  `0000000000000010000` (which must greater than the highest Block number
  currently available, which is `0000000000000000000`) any valid end value.
  - The specified start Block (`0000000000000010000`) must be too far into the
    future.

##### Output

- The Subscriber Client receives the NOT_AVAILABLE error code indicating that
  the request cannot be fulfilled.
- The connection is closed.

##### Other

N/A

---

#### TC-PRV-007

##### Test Name

`Verify Valid Request: Unfulfillable`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid but cannot be fulfilled, the request will be rejected and an
appropriate error message will be sent to the client. The connection will also
be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL reject requests that cannot be fulfilled
and send an appropriate error message to the client.
The `Stream Subscriber Plugin` SHALL close the connection after rejecting an
unfulfillable request.

##### Expected Behaviour

- It is expected that when a client sends any valid gRPC request, the
  `Stream Subscriber Plugin` will validate the request, perform a fulfillment
  check, and then reject the request if it cannot be fulfilled.
- It is expected that the NOT_AVAILABLE code will be sent to the client to
  indicate that the request cannot be fulfilled.
- It is expected that the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - The Block Node has at least one Block historically available - for this test
    Block `0000000000000000000`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid gRPC request.
  - It must be ensured that the request is unfulfillable based on what the
    request criteria are.

##### Output

- The Subscriber Client receives the NOT_AVAILABLE error code indicating that
  the request cannot be fulfilled.
- The connection is closed.

##### Other

N/A

---

#### TC-PRV-008

##### Test Name

`Verify Valid Request: Stream Open Ranged Beyond Request Limit`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria. If the request is for an open
range (end is -1L), the client will continue to receive Blocks indefinitely as
they become available. However, the plugin imposes a limit on the maximum
duration (count of Blocks sent) for which a request can remain active. Once the
limit is exceeded, a SUCCESS code will be sent to the client and the connection
will be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.</br>
The `Stream Subscriber Plugin` SHALL send a SUCCESS code to the client and
close the connection if the maximum duration (count of Blocks sent) for which an
open range request remains active is exceeded.

##### Expected Behaviour

- It is expected that when a client sends a valid gRPC request with any start
  and end as -1L (continue indefinitely), the `Stream Subscriber Plugin` will
  validate the request, perform a fulfillment check, and then start streaming
  the Blocks to the client based on the request criteria, granted that the
  request is valid and fulfillable.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.
- It is expected that if the maximum duration (count of Blocks sent) for which
  an open range request remains active is exceeded, the SUCCESS code will be
  sent to the client and the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available, starting from
    Block `0000000000000000000` onwards if the request that will be made starts
    with a whole number, or no historical Blocks are needed if the request is
    for the live stream (start == end == -1L).
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - The maximum duration (count of Blocks sent) for which an open range request
    can remain active is configured to a low value for the purpose of this test.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid open ranged gRPC request with any start
  and end as -1L (continue indefinitely).
- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000000` onwards if the request that will be made
  starts with -1L, or from Block `0000000000000000001` onwards if the request
  starts with a whole number, as Block `0000000000000000000` should already be
  available.
- The requested Blocks will be published to the Block Node either historically
  or live.
- The maximum duration (count of Blocks sent) for which an open range request
  can remain active is exceeded.

##### Output

- The Subscriber Client receives the stream of Blocks as they are published
  to the Block Node and receives a valid EndBlock message after each Block is
  received in full.
- The client receives the SUCCESS code after the maximum duration (count of
  Blocks sent) for which an open range request can remain active is exceeded.
- The connection is closed after the SUCCESS code is received.

##### Other

N/A

---

#### TC-PRV-009

##### Test Name

`Verify Valid Request: Stream Close Ranged Beyond Request Limit`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is valid and can be fulfilled, the client will start receiving the
stream of Blocks based on the request criteria. If the request is for a closed
range (both start and end are whole numbers and end is greater or equal to
start), the client will receive the Blocks within that range. However, the
plugin imposes a limit on the maximum duration (count of Blocks sent) for which
a request can remain active. Once the limit is exceeded, a SUCCESS code will be
sent to the client and the connection will be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.</br>
The `Stream Subscriber Plugin` SHALL send a SUCCESS code to the client and
close the connection if the maximum duration (count of Blocks sent) for which a
request remains active is exceeded.

##### Expected Behaviour

- It is expected that when a client sends a valid closed range gRPC request with
  both start and end as whole numbers and end is greater or equal to start, the
  `Stream Subscriber Plugin` will validate the request, perform a fulfillment
  check, and then start streaming the Blocks to the client based on the
  request criteria, granted that the request is valid and fulfillable.
- It is expected that the client will receive a valid EndBlock message after
  each Block is received in full.
- It is expected that if the maximum duration (count of Blocks sent) for which a
  request remains active is exceeded, the SUCCESS code will be sent to the
  client and the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
  - The Block Node has at least one Block historically available, starting from
    Block `0000000000000000000` onwards.
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - The maximum duration (count of Blocks sent) for which a request can remain
    active is configured to a low value for the purpose of this test.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends a valid closed range gRPC request with start as
  Block `0000000000000000000` and end as Block `0000000000000001000`.
- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000001` onwards, as Block `0000000000000000000`
  should already be available.
- The requested Blocks will be published to the Block Node either historically
  or live.
- The maximum duration (count of Blocks sent) for which a request can remain
  active is exceeded.

##### Output

- The Subscriber Client receives the stream of Blocks as they are published
  to the Block Node and receives a valid EndBlock message after each Block is
  received in full.
- The client receives the SUCCESS code after the maximum duration (count of
  Blocks sent) for which a request can remain active is exceeded.
- The connection is closed after the SUCCESS code is received.

##### Other

N/A

---

### Negative Request Validation Tests

#### TC-NRV-001

##### Test Name

`Verify Invalid Request: Invalid Start Block`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is invalid, the request will be rejected and an appropriate error
message will be sent to the client. The connection will also be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` SHALL reject invalid requests and send an
appropriate error message to the client.</br>
The `Stream Subscriber Plugin` SHALL close the connection after rejecting an
invalid request.

##### Expected Behaviour

- It is expected that when a client sends an invalid gRPC request with an
  invalid start Block (blockNumber lower than -1L),
  the `Stream Subscriber Plugin` will validate the request, determine that it is
  invalid, and then reject the request by sending the INVALID_START_BLOCK_NUMBER
  code to the client.
- It is expected that the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends an invalid gRPC request with an invalid start Block
  (blockNumber lower than -1L).

##### Output

- The Subscriber Client receives the INVALID_START_BLOCK_NUMBER error code
  indicating that the request was invalid.
- The connection is closed.

##### Other

N/A

---

#### TC-NRV-002

##### Test Name

`Verify Invalid Request: Invalid End Block`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is invalid, the request will be rejected and an appropriate error
message will be sent to the client. The connection will also be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` SHALL reject invalid requests and send an
appropriate error message to the client.</br>
The `Stream Subscriber Plugin` SHALL close the connection after rejecting an
invalid request.

##### Expected Behaviour

- It is expected that when a client sends an invalid gRPC request with an
  invalid end Block (blockNumber lower than -1L),
  the `Stream Subscriber Plugin` will validate the request, determine that it is
  invalid, and then reject the request by sending the INVALID_END_BLOCK_NUMBER
  code to the client.
- It is expected that the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends an invalid gRPC request with an invalid end Block
  (blockNumber lower than -1L).

##### Output

- The Subscriber Client receives the INVALID_END_BLOCK_NUMBER error code
  indicating that the request was invalid.
- The connection is closed.

##### Other

N/A

---

#### TC-NRV-003

##### Test Name

`Verify Invalid Request: Invalid Closed Range`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
request is invalid, the request will be rejected and an appropriate error
message will be sent to the client. The connection will also be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` SHALL reject invalid requests and send an
appropriate error message to the client.</br>
The `Stream Subscriber Plugin` SHALL close the connection after rejecting an
invalid request.

##### Expected Behaviour

- It is expected that when a client sends an invalid gRPC request with a closed
  range - both start and end are whole numbers, but start is greater than end,
  the `Stream Subscriber Plugin` will validate the request, determine that it is
  invalid, and then reject the request by sending the INVALID_END_BLOCK_NUMBER
  code to the client.
- It is expected that the connection will be closed.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.

##### Input

- A Subscriber Client sends an invalid gRPC request with a closed range - both
  start and end are whole numbers, but start is greater than end.

##### Output

- The Subscriber Client receives the INVALID_END_BLOCK_NUMBER error code
  indicating that the request was invalid.
- The connection is closed.

##### Other

N/A

---

### Error Tests

#### TC-ERR-001

##### Test Name

`Verify Abrupt Client Connection Termination`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If the
client abruptly terminates the connection, the session handling the request
must handle the termination gracefully without causing any resource leaks or
other issues. The session must be cleaned up properly. Such abrupt termination
could happen at any time, be that while the request is being validated, during
the fulfillment check, while streaming Blocks to the client, or while waiting
for the next Block to become available. In no case may the abrupt termination
cause the `Stream Subscriber Plugin` or the Block Node to become unstable.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.</br>
The `Stream Subscriber Plugin` MUST handle abrupt client connection termination
gracefully without causing any resource leaks or other issues.</br>
The `Stream Subscriber Plugin` MUST clean up the session properly after an
abrupt client connection termination.</br>
The `Stream Subscriber Plugin` MUST remain stable after an abrupt client
connection termination.</br>
The `Stream Subscriber Plugin` SHALL log a clean and complete diagnostic message
including details of each abrupt termination.</br>
The `Stream Subscriber Plugin` SHALL update a metric to count abrupt client
terminations, but SHALL NOT include client labels in that metric.

##### Expected Behaviour

- It is expected that when a client sends any valid gRPC request, the
  `Stream Subscriber Plugin` will validate the request, perform a fulfillment
  check, and then start streaming the Blocks to the client based on the
  request criteria, granted that the request is valid and fulfillable.
- It is expected that if the client abruptly terminates the connection at any
  point in time, the session handling the request will handle the termination
  gracefully without causing any resource leaks or other issues. The session
  will be cleaned up properly.
- It is expected that the `Stream Subscriber Plugin` and the Block Node will
  remain stable after the abrupt client connection termination.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.
  - The Subscriber Client has the ability to abruptly terminate the connection
    at any point in time.

##### Input

- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000000` onwards.
- A Subscriber Client sends a valid gRPC request for live Blocks.
- The Subscriber Client abruptly terminates the connection at various points
  in time:
  - After beginning the request, but before sending the last byte of the
    request.
    - This timing might not be achievable, depending on the gRPC client used.
  - After completing a request, but before the first response message.
    - This is before the first block header is received.
  - After the first response message, but before the first EndBlock.
    - This is in the middle of the first block received.
  - After receiving a "batch" message, but before receiving an EndBlock message.
    - This is in the middle of a single block.
  - After an EndBlock message, but before the next "batch" message.
    - This is between blocks or while waiting for the next block.
  - In the middle of receiving a single "batch" message.
    - This timing might not be achievable, depending on the gRPC client used.

##### Output

- The `Stream Subscriber Plugin` handles the abrupt client connection
  termination gracefully without causing any resource leaks or other issues. The
  session and all network or system resources are cleaned up properly.
- The `Stream Subscriber Plugin` and the Block Node remain stable after the
  abrupt client connection termination.

##### Other

N/A

---

#### TC-ERR-002

##### Test Name

`Verify Server Error`

##### Scenario Description

`Stream Subscriber Plugin` accepts gRPC server streaming requests. If an
unexpected server error occurs while processing a request, the error must be
handled gracefully without causing the `Stream Subscriber Plugin` or the Block
Node to become unstable. An ERROR message must be sent to the client. The
session must be cleaned up properly. The connection must be closed.

##### Requirements

The `Stream Subscriber Plugin` MUST allow clients to send gRPC requests.</br>
The `Stream Subscriber Plugin` MUST validate the gRPC requests.</br>
The `Stream Subscriber Plugin` MUST perform a check to ensure that the
request can be fulfilled.</br>
The `Stream Subscriber Plugin` MUST start streaming the Blocks to the client
based on the request criteria if the request is valid and can be fulfilled.</br>
The `Stream Subscriber Plugin` SHALL send an EndBlock message to the client
after streaming a Block in full, for every Block streamed.</br>
The `Stream Subscriber Plugin` MUST handle unexpected server errors gracefully
without causing the `Stream Subscriber Plugin` or the Block Node to become
unstable.</br>
The `Stream Subscriber Plugin` SHALL send an ERROR message to the client if an
unexpected server error occurs while processing a request.</br>
The `Stream Subscriber Plugin` MUST clean up the session properly after an
unexpected server error occurs while processing a request.</br>
The `Stream Subscriber Plugin` SHALL close the connection after sending an ERROR
message to the client.</br>
The `Stream Subscriber Plugin` SHALL log a clean and complete diagnostic message
including details of each abrupt termination.</br>
The `Stream Subscriber Plugin` SHALL update a metric to count abrupt client
terminations, but SHALL NOT include client labels in that metric.

##### Expected Behaviour

- It is expected that when a client sends any valid gRPC request, the
  `Stream Subscriber Plugin` will validate the request, perform a fulfillment
  check, and then start streaming the Blocks to the client based on the
  request criteria, granted that the request is valid and fulfillable.
- It is expected that if an unexpected server error occurs while processing the
  request, the error will be handled gracefully without causing the
  `Stream Subscriber Plugin` or the Block Node to become unstable.
- It is expected that an ERROR message will be sent to the client.
- It is expected that the session will be cleaned up properly after the
  unexpected server error occurs while processing the request.
- It is expected that the connection will be closed after sending the ERROR
  message to the client.

##### Preconditions

- A Block Node with:
  - A working `Stream Subscriber Plugin`.
  - A working Publisher component that will be publishing new Blocks to the
    Block Node which will then be available for streaming to clients.
  - A working Block Provider component that will have the ability to
    provide historical Blocks if needed.
- A Subscriber Client that can send gRPC server streaming requests to the Block
  Node and receive responses.
- A way to simulate unexpected server errors in the `Stream Subscriber Plugin`
  while processing a request.

##### Input

- A Publisher component starts publishing new Blocks to the Block Node starting
  from Block `0000000000000000000` onwards.
- A Subscriber Client sends a valid gRPC request for live Blocks.
- An unexpected server error is simulated in the `Stream Subscriber Plugin`
  while processing the request.
  - This may require a "chaos testing" tool to inject memory corruption or
    similar unexpected errors in the running process.

##### Output

- The `Stream Subscriber Plugin` handles the unexpected server error gracefully
  without causing the `Stream Subscriber Plugin` or the Block Node to become
  unstable.
- The Subscriber Client receives an ERROR message indicating that an unexpected
  server error occurred while processing the request.
- The session is cleaned up properly after the unexpected server error occurs
  while processing the request.
- The connection is closed after sending the ERROR message to the client.

##### Other

N/A

---
