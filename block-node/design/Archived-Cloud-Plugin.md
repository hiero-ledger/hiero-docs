# Archive Cloud Storage Plugin

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

The Archive Cloud Storage Plugin (ACSP) is a data storage plugin for the block node
that stores block files in cloud storage systems aggregated into large `tar` archives.

## Goals

* The ACSP must store every block, as received, after verification.
* The ACSP must store each block as a single ZStandard-compressed file.
* The ACSP must aggregate blocks into large `tar` archives.
* The ACSP must adhere to a file pattern as defined below.
* The ACSP must store all `tar` archives as files or objects in a cloud storage
  system.
* The ACSP must not "stage" block data locally.
* The ACSP must write `tar` data directly to the remote file or object.
* The ACSP must recover from an interruption with the minimum practical data
  loss.
* The ACSP must not report success until data is stored such that it can be
  recovered if the local system fails unexpectedly, including a failure that
  results in complete and unrecoverable loss of all local storage.

## Terms

<dl>
  <dt>Cloud Storage</dt>
  <dd>Any storage API that stores data remotely with very high
      availability and reliability. Multiple such APIs may be supported
      by the plugin and controlled by configuration.<br/>
      An example of a common cloud storage API is S3 storage.</dd>
</dl>

## Entities

TBD.

## Design

1. The `ArchiveCloudPlugin.handleVerificationNotification()` receives a full
   block recently verified.
2. If a `BlockArchiveTask` does not exist, create one and start it.
   1. If the current _notified_ block is not the first block in a group,
      then the `BlockArchiveTask` must be marked as "temporary". This
      type of task stores blocks for future use in reconciliation.
3. A new `SingleBlockStoreTask` is created, provided with the verified block,
   and added to the current active `BlockArchiveTask`.
4. Each `SingleBlockStoreTask` calculates the correct file pattern, converts
   the block to a ZStandard compressed data stream, and provides that data to
   the `BlockArchiveTask` along with the file name.
5. The `BlockArchiveTask` will write file and index data to the remote `tar`
   file in block-number order.
6. The `BlockArchiveTask` will close the `tar` file when the last block of the
   group is written.
7. The `BlockArchiveTask` will send a `PerisistenceNotification` for each file
   added to the `tar` file.
   1. On failure a `PerisistenceNotification` will be published
      with `success=false`.
   2. On success a `PerisistenceNotification` will be published
      with `success=true`.
   3. The `BlockArchiveTask` will attempt to store each file with retry defined
      by configuration. Only after all retries have failed will a failure
      be reported.
   4. If a failure is reported, the `BlockArchiveTask` will also end and report
      a failed archive to the `ArchiveCloudPlugin`.
8. The `ArchiveCloudPlugin` may initiate a "recovery" process when a block
   fails or arrives out of order.
9. A "recovery" process gathers one or more "temporary" `tar` files to reorder
   and consolidate blocks that arrived out of order; continue a `tar` file that
   did not complete due to a block, plugin, or node failure; or both.
   1. A "recovery" process is initiated by creating and starting an
      `ArchiveRecoveryTask`, passing to it the group start, temporary `tar`
      files, and any additional data needed.
   2. While a "recovery" process is active, newly received verified blocks will
      be delivered to the `ArchiveRecoveryTask` if they fall within the
      group being recovered, or delivered to a `BlockArchiveTask` as normal
      otherwise.

File pattern _within_ a `tar` file

```text
19 digit block number with a suffix of '.blk.zstd'.
Block number split into groups of 4 digit folders, starting from the left.

examples:
Block         "0" = 0000/0000/0000/0000/000.blk.zstd
Block         "1" = 0000/0000/0000/0000/001.blk.zstd
Block "108273182" = 0000/0000/0010/8273/182.blk.zstd
```

File/object pattern for `tar` files in cloud storage

```text
Start with a 19 digit block number for the first block in the group.
Truncate the block number by removing digits from the right equal to the grouping level.
Add a suffix of ".tar".

Examples:
Grouping Factor of 100,000 blocks per group (Grouping Level = "5")
Blocks       0-99,999  = 0000/0000/0000/0.tar
Blocks 100,000-199,999 = 0000/0000/0000/1.tar
```

## Diagram

TBD.

## Configuration

TBD.

## Metrics

TBD.

## Exceptions

TBD.

## Acceptance Tests

TBD.
