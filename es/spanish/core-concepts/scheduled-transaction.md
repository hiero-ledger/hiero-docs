# Schedule Transaction

## Overview

A **schedule transaction** is a transaction with the ability to collect the required signatures on a Hedera network in preparation for its execution. Unlike other Hedera transactions, this allows you to queue a transaction for execution in the event you do not have all the required signatures for the network to immediately process the transaction. A scheduled transaction is used to create a scheduled transaction. This feature is ideal for transactions that require multiple signatures.

When a user creates a scheduled transaction, the network creates a scheduled entity. The scheduled entity receives an entity ID just like accounts, tokens, etc called a schedule ID. The schedule ID is used to reference the scheduled transaction that was created. The transaction that is being scheduled is referenced by a scheduled transaction ID.&#x20

Signatures are appended to the scheduled transaction by submitting a `ScheduleSign` transaction. The `ScheduleSign` transaction requires the schedule ID of the scheduled transaction the signatures will be appended to. In its current design, a scheduled transaction has 30 minutes to collect all required signatures before the scheduled transaction can be executed or will be deleted from the network. You can delete a scheduled transaction by setting an admin key to delete a scheduled transaction before it is executed or deleted by the network.

You can request the current state of a scheduled transaction by querying the network for `ScheduleGetInfo`. The request will return the following information:

- Schedule ID
- Account ID that created the scheduled transaction
- Account ID that paid for the creation of the scheduled transaction
- Transaction body of the inner transaction
- Transaction ID of the inner transaction
- Current list of signatures
- Admin key (if any)
- Expiration time
- The timestamp of when the transaction was deleted, if true

The design document for this feature can be referenced [here](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md).

**Schedule Transaction ID**

Hedera Transaction IDs are composed of the account ID submitting the transaction and the transaction valid start time in seconds.nanoseconds (`0.0.1234@1615422161.673238162`). The transaction ID for a scheduled transaction will include "`?schedule`" at the end of the transaction ID which identifies the transaction as a scheduled transaction i.e. `0.0.1234@1615422161.673238162?scheduled`. The transaction ID of the scheduled (inner) transaction inherits the transaction valid start time and account ID from the scheduled (outer) transaction.

**Schedule Transaction Receipts**

The transaction receipt for a schedule that was created contains the new schedule entity ID and the scheduled transaction ID. The scheduled transaction ID is used to request records for the inner transaction upon successful execution.

**Schedule Transaction Records**

Transaction records are created when the scheduled transaction is created, for each signature that was appended, when the scheduled transaction is executed, and if the scheduled transaction was deleted by a user. The record of a scheduled transaction includes a schedule reference property which is the ID of the schedule the record is associated with. To get the transaction record for the inner transaction after successful execution, you can do the following:

1. Poll the network for the specified scheduled transaction ID. Once the scheduled transaction executes the scheduled transaction successfully, request the record for the scheduled transaction using the scheduled transaction ID.
2. Query a Hedera mirror node for the scheduled transaction ID.
3. Run your own mirror node and query for the scheduled transaction ID.

## FAQ

<details>

<summary>What is the difference between a schedule transaction and scheduled transaction?</summary>

A _**schedule transaction**_ is a transaction that can schedule any Hedera transaction with the ability to collect the required signatures on the Hedera network in preparation for its execution.

A _**scheduled transaction**_ is a transaction that has already been scheduled.

</details>

<details>

<summary>Is there an entity ID assigned to a schedule transaction?</summary>

Yes, the entity ID is referred to as the schedule ID which is returned in the receipt of the ScheduleCreate transaction.

</details>

<details>

<summary>What transactions can be scheduled?</summary>

In its early iteration, a small subset of transactions will be schedulable. You check out [this](../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md) page for a list of transaction types that are supported today. All other transaction types will be available to schedule in future releases. The complete list of transactions that users can schedule in the future can be found here.

</details>

<details>

<summary>How can I find a schedule transaction that requires my signature?</summary>

- The creator of the scheduled transaction can provide you a schedule ID which you specify in the ScheduleSign transaction to submit your signature.

<!---->

- You can query a mirror node to return all schedule transactions that have your public key associated with it. This option is not available today, but is planned for the future.

</details>

<details>

<summary>What happens if the scheduled transaction does not have sufficient balance?</summary>

If the scheduled transaction (inner transaction) fee payer does not have sufficient balance then the inner transaction will fail while the schedule transaction (outer transaction) will be successful.

</details>

<details>

<summary>Can you delay a transaction once it has been scheduled?</summary>

No, you cannot delay or modify a scheduled transaction once it's been submitted to a network. You would need to delete the schedule transaction and create a new one with the modifications.

</details>

<details>

<summary>What happens if multiple users create the same schedule transaction?</summary>

- The first transaction to reach consensus will create the schedule transaction and provide the schedule entity ID
- The other users will get the schedule ID in the receipt of the transaction that was submitted. The receipt status will result in `IDENTICAL_SCHEDULE_ALREADY_CREATED`. These users would need to submit a ScheduleSign transaction to append their signatures to the schedule transaction.

</details>

<details>

<summary>When does the scheduled transaction execute?</summary>

The scheduled transaction executes when the last signature is received.

</details>

<details>

<summary>How often does the network check to see if the scheduled transaction (inner transaction) has met the signature requirement?</summary>

Every time the schedule transaction is signed.

</details>

<details>

<summary>How do you get information about a schedule transaction?</summary>

You can submit a [schedule info query](../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) request to the network.

</details>

<details>

<summary>When does a scheduled transaction expire?</summary>

A scheduled transaction expires in 30 minutes. In future implementations, we will allow the user to set the time at which the scheduled transaction should execute at, and the transaction will expire at that time.

</details>

<details>

<summary>What does a schedule transaction receipt contain?</summary>

The transaction receipt for a schedule that was created contains the new schedule entity ID and the scheduled transaction ID.

</details>
