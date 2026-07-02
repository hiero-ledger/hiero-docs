# Retention Policy Design Document

## Table of Contents

1. [Purpose](#purpose)
2. [Goals](#goals)
3. [Terms](#terms)
4. [Design](#design)
5. [Configuration](#configuration)
6. [Metrics](#metrics)
7. [Acceptance Tests](#acceptance-tests)

## Purpose

The Retention Policy is a threshold, measured in amount of blocks (block count),
that defines how many blocks the Block-Node should keep in its storage.
Effectively, this policy will allow us to achieve rolling history.

## Goals

There are a few things that the Retention Policy aims to achieve:

1. Each distinct persistence plugin is completely independent and implements its
   own Retention Policy, this includes:
   - Each plugin will be responsible for respecting its own Retention Policy.
   - Each plugin will choose its own level of granularity by which it will
     remove blocks that exceed the threshold.
2. The Retention Policy should be configurable, allowing users to set the
   desired block count threshold.
3. The Retention Policy is **NOT** a hard limit, meaning that the Block-Node
   will keep accepting blocks even if the threshold is reached.
4. The unit of the Retention Policy is block count.
5. The Retention Policy is about limiting storage space - cleanup should happen
   when new data is received. There is no reason to clean data that is already
   there if no new data comes in.

## Terms

<dl>
  <dt>Retention Policy</dt>
  <dd>A configurable threshold, measured in amount of blocks (block count) that
      defines the maximum amount of blocks that can be stored (rolling history).
      The policy should not block new data incoming.</dd>
</dl>

## Design

The Retention Policy is a concept that concerns Persistence Plugins.
Each Persistence Plugin will implement its own Retention Policy in a way that
serves best the system. Each plugin however should respect the general rules as
listed in the [Goals section](#goals) above.

### `Files Recent Persistence`

The `Files Recent Persistence` implements the Retention Policy:

- A configurable parameter defines how many blocks to keep in storage.
- The Policy is implemented by removing the oldest N amount of blocks (based on
  the configured threshold).
- Blocks are removed only when new data is received.
- The granularity of the removal is one whole block.
- By default, the Retention Policy is set to `96400`, which is a bit more than
  a day when blocks are streamed at a rate of one per second.

### `Files Historic Persistence`

The `Files Historic Persistence` implements the Retention Policy:

- A configurable parameter defines how many blocks to keep in storage. This
  parameter determines how many zips (archived batches blocks) will be retained
  at any given time. Multiplying this value by the archive batch size gives us
  the total number of individual blocks that that will be effectively retained.
- The Policy is implemented by removing the oldest N amount of blocks (based on
  the configured threshold).
- Blocks are removed only when new data is received.
- The granularity of the removal is one whole zipped file, which contains one
  archived batch of blocks.
- By default, the Retention Policy is set to `0`, which means that there is no
  limit on the amount of blocks that can be stored.

## Configuration

The Retention Policy is a configurable parameter that is specific for each
Persistence Plugin. The parameter's unit is block count.

## Metrics

It is advised that each Persistence Plugin should keep live metrics about the
current state of its storage, like the current amount of blocks in storage,
data in bytes stored, etc. This allows operators to monitor the current state of
the storage and make appropriate decisions.

## Acceptance Tests

Acceptance tests for the Retention Policy should mean that whenever we configure
the Retention Policy to a certain value, the Block-Node should respect it and
keep the storage within the reasonable limit, but has no problem to keep
accepting new blocks even if the limit is reached, no matter how fast the new
data comes along.
