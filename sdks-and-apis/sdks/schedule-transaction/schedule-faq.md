# Schedule FAQ

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

In its early iteration, a small subset of transactions will be schedulable. You check out [this](create-a-schedule-transaction.md) page for a list of transaction types that are supported today. All other transaction types will be available to schedule in future releases. The complete list of transactions that users can schedule in the future can be found here.

</details>

<details>

<summary>How can I find a schedule transaction that requires my signature?</summary>

* The creator of the scheduled transaction can provide you a schedule ID which you specify in the ScheduleSign transaction to submit your signature.

<!---->

* You can query a mirror node to return all schedule transactions that have your public key associated with it. This option is not available today, but is planned for the future.

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

* The first transaction to reach consensus will create the schedule transaction and provide the schedule entity ID
* The other users will get the schedule ID in the receipt of the transaction that was submitted. The receipt status will result in `IDENTICAL_SCHEDULE_ALREADY_CREATED`. These users would need to submit a ScheduleSign transaction to append their signatures to the schedule transaction.

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

You can submit a [schedule info query](get-schedule-info.md) request to the network.

</details>

<details>

<summary>When does a scheduled transaction expire?</summary>

A scheduled transaction expires in 30 minutes. In future implementations, we will allow the user to set the time at which the scheduled transaction should execute at, and the transaction will expire at that time.

</details>
