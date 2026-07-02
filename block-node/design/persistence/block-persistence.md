# Block Persistence

## Table of Contents

1. [Purpose](#purpose)
2. [Goals](#goals)
3. [Terminology](#terminology)
4. [Abstractions](#abstractions)
5. [Overview](#overview)
6. [Implementations](#implementations)
   1. [Block as Local Directory (a.k.a. `block-as-local-dir`)](#block-as-local-directory-aka-block-as-local-dir)
      1. [Purpose](#purpose-1)
      2. [Overview](#overview-1)
      3. [Configurable Parameters](#configurable-parameters)
   2. [Block as Local File (a.k.a. `block-as-local-file`)](#block-as-local-file-aka-block-as-local-file)
      1. [Purpose](#purpose-2)
      2. [Overview](#overview-2)
      3. [Trie Structure & Algorithm for Block Path Resolution](#trie-structure--algorithm-for-block-path-resolution)
      4. [Configurable Parameters](#configurable-parameters-1)
   3. [No Operation (a.k.a. `no-op`)](#no-operation-aka-no-op)
      1. [Purpose](#purpose-3)
      2. [Overview](#overview-3)

## Purpose

A major objective of the `hiero-block-node` project is to replace the storage
of Consensus Node artifacts (e.g. Blocks) on cloud storage buckets (e.g. GCS and
S3) with a solution managed by the Block Node server. This document aims to
describe the high-level design of how the Block Node persists and retrieves
Blocks and how it handles exception cases when they arise.

## Goals

1. `BlockItems` streamed from a producer (e.g. Consensus Node) must be collated
   and persisted as a `Block`. Per the specification, a `Block` is an ordered
   list of `BlockItems`. How the `Block` is persisted is an implementation
   detail.
2. A `Block` must be efficiently retrieved by block number.
3. Certain aspects of the `Block` persistence implementation must be
   configurable.

## Terminology

<dl>
  <dt>BlockItems</dt>
  <dd>The `Block` description and protobuf definition are maintained in the
  `hedera-protobuf` <a href="https://github.com/hashgraph/hedera-protobufs/blob/continue-block-node/documents/api/block/stream/block.md">project</a>.</dd>

  <dt>BlockItemUnparsed</dt>
  <dd>A partially parsed version of a `BlockItem` that can be stored, hashed, or
  retrieved, but retains detail data in byte array form.</dd>

  <dt>BlockUnparsed</dt>
  <dd>A partially parsed version of a `Block` that can be stored, hashed, or
  retrieved, but retains detail data in byte array form.</dd>

  <dt>BlockNumber</dt>
  <dd>A value that represents the unique number (identifier) of a given `Block`.
  It is a strictly increasing `long` value, starting from zero (`0`).</dd>

  <dt>StorageType</dt>
  <dd>A value that represents the type of storage used to persist/retrieve a `Block`.</dd>
</dl>

## Abstractions

<dl>
  <dt>BlockReader</dt>
  <dd>An interface defining methods used to read a `Block` from storage. It
  represents a lower-level component whose implementation is directly
  responsible for reading a `Block` from storage.</dd>

  <dt>BlockWriter</dt>
  <dd>An interface defining methods used to write `Blocks` to storage. It
  represents a lower-level component whose implementation is directly
  responsible for writing a `Block` to storage.</dd>

  <dt>BlockRemover</dt>
  <dd>An interface defining the methods used to remove a `Block` from storage.
  It represents a lower-level component whose implementation is directly
  responsible for removing a `Block` from storage.</dd>

  <dt>BlockPathResolver</dt>
  <dd>An interface defining methods used to resolve the path to a `Block` in
  storage. It represents a lower-level component whose implementation is
  directly responsible for resolving the path to a `Block` in storage, based on
  `StorageType`.</dd>
</dl>

## Overview

The design for `Block` persistence is fairly straightforward. `Block` server
objects should use the persistence abstractions to read, write and remove
`Block`s from storage, as well as resolve the paths to `Block`s.

`BlockItem`s streamed from a producer are read off the wire one by one and
passed to an implementation of `BlockWriter`. The `BlockWriter` is responsible
for collecting related `BlockItem`s into a `Block` and persisting the `Block` to
storage in a way that is efficient for both long-term storage and rapid
retrieval at a later time. The `BlockWriter` is also responsible for removing a
partially written `Block` if an exception occurs while writing it. For example,
if half the `BlockItem`s of a `Block` are written when an `IOException` occurs,
the `BlockWriter`should remove all the `BlockItem`s of the partially written
`Block` and pass the exception up to the caller.

Services requiring one or more`Block`s should use a `BlockReader`
implementation. The `BlockReader` will be able to efficiently retrieve a `Block`
by block number. The `BlockReader` will pass unrecoverable exceptions when
reading a `Block` up to the caller.

## Implementations

### Block as Local Directory (a.k.a. `block-as-local-dir`)

#### Purpose

The purpose of this implementation is to provide a simple, local storage. Used
mostly for testing and development purposes.

#### Overview

This type of storage implementation persists `Block`s to a local filesystem. A
`Block` is persisted as a directory containing a file for each `BlockItem` that
comprises the given `Block`. The storage has a root path where each directory
under the root path is a given block. The names of the directories (`Block`s)
are the respective `Block`'s `BlockNumber`.

#### Configurable Parameters

<!-- todo add basePath when defined -->
- `persistence.storage.liveRootPath` - the root path where all `Block`s are
  stored.

### Block as Local File (a.k.a. `block-as-local-file`)

#### Purpose

The purpose of this implementation is to provide a simple, fast to resolve,
local storage. Intended as a production default.

#### Overview

This type of storage implementation persists `Block`s to a local filesystem.
A`Block` is persisted as a file containing all the `BlockItem`s that comprise
the given `Block`. This storage option has a root directory. The root directory
is the directory that contains all the subdirectories containing actual block
data. A specific `Block` is stored as a file, resolved under the storage root by
utilizing a `trie` structure. The name of the file is the respective `Block`'s
`BlockNumber`. Each `Block` can be compressed before being written to storage,
and the `Block`s can be archived periodically in batches of configurable size.
Both compression and archive are optional.

#### Trie Structure & Algorithm for Block Path Resolution

The `Block` path resolution is based on a `trie` structure. The `trie` is
constructed as follows:

1. We have a `liveRootPath` to store the `Block`s under.
2. We have an `archiveRootPath` to store the archived `Block`s under.
3. The `BlockNumber` is a `long` which contains 19 digits maximum.
4. The `trie` structure is a folder for each digit in the `BlockNumber`, up to a
   depth of 18.
5. The last digit of the `BlockNumber` is part of the file name itself.
6. The `Block`s are compressed (configurable), `zstd` is used by default.
7. The `Block`s are archived (configurable) in batches, e.g. 1000 `Block`s.

Visual example:

Block files will have a long as a file name representing the block number, the trie structure will be a digit per
folder or all digits in a long, for brevity, here we showcase only 7 digits, the last digit is part of the block file
name itself, we zip every 100 block in a single zip.

```bash
BlockArchive/.../1/0/2/3/4.zip/0/1023400.blk.zstd -> zipped because we have configured every 100s to be zipped
BlockLive/.../1/0/2/3/5/0/1023500.blk.zstd -> new block written
BlockLive/.../1/0/2/3/5/0/1023501.blk.zstd -> new block written
...
BlockLive/.../1/0/2/3/5/9/1023599.blk.zstd -> new block written, reaching the 100th mark
BlockArchive/.../1/0/2/3/5.zip/0/1023500.blk.zstd -> block file ...1023500.blk.zstd to ...1023599.blk.zstd are now zipped
BlockLive/.../1/0/2/3/6/0/1023600.blk.zstd -> new block

/.../1/0/2/3/5/9/ + 9 (the last digit of the blk file, block number) = the whole block number ...1023599
```

The algorithm for resolving the path to a `Block` is defined roughly as follows:

1. When writing, initially we only need to write a new `Block` as a file to the
   local filesystem in the correct place, based on the resolved path from the
   `BlockNumber` utilizing the `trie` structure.
2. When writing a new `Block` as file, we need to check if the path to the file
   we want to write already exists, if it does, the block was previously written.
   We should handle this case directly in our service.
3. When reading, we should be able to resolve the path to a `Block` as file that
   we want to read, initially attempting to read the file before it would be
   zipped, if not found, we need to search it in the respective zip, else if not
   found, then the block is not available, we need to handle that case inside
   our service.
4. A separate process/thread will periodically go through our `trie` structure
   and will be zipping the `Block` files in configured groups as shown visually
   above.

#### Configurable Parameters

<!-- todo add basePath, archiveBatchSize, compressionMode, liveGroupSize, archiveGroupSize (or one group size if that should be the case) when defined -->
- `persistence.storage.liveRootPath` - the root path where all `Block`s are
  stored.
- `persistence.storage.archiveRootPath` - the root path where all `Block`s are
  archived.

### No Operation (a.k.a. `no-op`)

#### Purpose

The purpose of this implementation is to provide a no-op implementation, mainly
for testing and development purposes.

#### Overview

This type of storage implementation does nothing.
