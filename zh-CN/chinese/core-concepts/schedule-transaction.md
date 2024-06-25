# Schedule Transaction

## Overview

A **schedule transaction** is a transaction that can schedule any Hedera transaction with the ability to collect the required signatures on a Hedera network in preparation for its execution. Unlike other Hedera transactions, this allows you to queue a transaction for execution in the event you do not have all required signatures for the network to immediately process the transaction. This feature is ideal for transactions that require multiple signatures.

When a user creates a schedule transaction, the network creates a schedule entity. The schedule entity receives an entity ID just like accounts, tokens, etc called a schedule ID. The schedule ID is used to reference the schedule transaction that was created. The transaction that is being scheduled is referenced by a scheduled transaction ID. The schedule transaction can be referred to as the outer transaction while the scheduled transaction can be referenced as the inner transaction.

Signatures are appended to the schedule transaction by submitting a `ScheduleSign` transaction. The `ScheduleSign` transaction requires the schedule ID of the schedule transaction the signatures will be appended to. In its current design, a schedule transaction has 30 minutes to collect all required signatures before the schedule transaction can be executed or will be deleted from the network. You can delete a schedule transaction by setting an admin key to delete a schedule transaction before it is executed or deleted by the network.

You can request the current state of the a schedule transaction by querying the network for `ScheduleGetInfo`. The request will return the following information:

- Schedule ID
- Account ID that created the schedule transaction
- Account ID that paid for the creation of the schedule transaction
- Transaction body of the inner transaction
- Transaction ID of the inner transaction
- Current list of signatures
- Admin key \(if any\)
- Expiration time
- The timestamp of when the transaction was deleted, if true

The design document for this feature can be referenced [here](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md).

**Schedule Transaction ID**

Hedera Transaction IDs are composed of the account ID submitting the transaction and the transaction valid start time in seconds.nanoseconds \(`0.0.1234@126534.126456`\). The transaction ID for a schedule transaction will include "`?schedule`" at the end of the transaction ID which identifies the transaction as a schedule transaction i.e. `0.0.1.2.3.4@1615422161.673238162?scheduled.` The transaction ID of the scheduled \(inner\) transaction inherits the transaction valid start time and account ID from the schedule \(outer\) transaction.

**Schedule Transaction Receipts**

The transaction receipt for a schedule that was created contains the new schedule entity ID and the scheduled transaction ID. The scheduled transaction ID is used to request records for the inner transaction upon successful execution.

**Schedule Transaction Records**

Transaction records are created when the schedule transaction is created, for each signature that was appended, when the scheduled transaction is executed, and if the schedule transaction was deleted by a user. The record of a schedule transaction includes a schedule reference property which is the ID of the schedule the record is associated with. To get the transaction record for the inner transaction after successful execution, you can do the following:

1. Poll the network for the specified scheduled transaction ID. Once the schedule transaction executes the scheduled transaction successfully, request the record for the scheduled transaction using the scheduled transaction ID.

2. Query a Hedera mirror node for the scheduled transaction ID

3. Run your own mirror node and query for the scheduled transaction ID
