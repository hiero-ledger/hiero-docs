# Task Based Block File Writer

## General Approach

Upon receiving an event, the Handler checks if the first item is a Header, or
the last item is a Block Proof. Note, this assumes an event is a list of
block items, and conforms to the general contract for publishing data to
the Block Node.
Upon receiving an event, the Handler performs the following actions.
1. If the first item is a Block Header, create a new writer task, get the
transfer queue, assign the transfer queue to the _current transfer queue_,
and submit the new writer task to the _completion service_.
1. Send all items to the _current transfer queue_.
1. If the last item is a Block Proof, assign null to the
_current transfer queue_.
1. While the _completion service_ returns non-null when _polled_, get the
result and notify (or publish a _persistence result_) appropriately.

> Note, _writers_ should implement `Callable` to make this process simpler.

## Structure Diagram

![Task Persistence](../assets/Task-Persistence.svg)

## Expected Behavior

The Handler is relatively simple.  The Handler simply checks the input, and
does three things.  If there is a header (this starts a new block), create
a new writer and obtain the transfer queue.  In _all_ cases, add every item
in the list to the current transfer queue.  If there is a block proof (ending
the block), assign null to the current transfer queue.

### Considerations

1. What if a list both starts with a block header and ends with a block proof?
   * By performing the steps in order (header, send items, proof), this is
     handled. The writer will get header, items between, and proof at the end.
     The handler will end that _event_ ready for the next block header.
2. What if a block never completes (there is no block proof)?
   * If a new block header arrives while the _current transfer queue_ is not
     null, then the Handler can add a `null` to the transfer queue before
     creating a new writer as usual.
     * The _writer_ will respond to a null item by removing the partially
       written block and completing exceptionally.
     * When the _completion service_ is polled for results; that result,
       when available, will result in an error _persistence result_.
3. What if writers finish out of order?
   * This should be extremely rare, as blocks _must_ arrive in order
   * The _persistence result_ for the blocks will be published out of order
   * This **should not** be an error, and it is up to other services, such as
     a _publisher response service_ to decide how this situation is handled
     for their specific domains.
   * The _completion service_ will always return task results from the _poll_
     in approximately the order they completed.
4. What if a writer takes a very long time?
   * That task will be managed by the _completion service_.  The _poll_ does
     not block, however, so the slow writer is skipped until it finishes.
   * A result may not be published within a reasonable timeframe.  This is
     a concern for other services listening for the _persistence result_, but
     is not a concern for the persistence service.  In a particularly egregious
     situation the Block Node might not acknowledge a block within a reasonable
     time limit and fall _behind_, which could result in publishers resending
     blocks.
5. What if the node falls _behind_ and the responsible service determines that
   unverified or unpersisted blocks must be resent?
   * This is separate from the _writer_ specifically, and we have yet to design
     the appropriate component.  In general, however, each service should have
     a _reset_ mechanism (message based).  If things get off track enough, the
     services can all be _reset_, and processing resumes from the last persisted
     and verified block.
   * If persistence is _reset_, it must remove any unverified block files,
     terminate all in-progress processing, and start over
   * We will need to add _reset_ capability to all services when we get to
     adding handling for the _behind_, _restart_, and _recover_ conditions
     to the Block Node.
