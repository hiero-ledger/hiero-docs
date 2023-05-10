# Schedule FAQ

## 1. What is the difference between a schedule transaction and scheduled transaction?

A _**schedule transaction**_ is a transaction that can schedule any Hedera transaction with the ability to collect the required signatures on the Hedera network in preparation for its execution.

A _**scheduled transaction**_ is a transaction that has already been scheduled.

## 2. Is there an entity ID assigned to a schedule transaction?

Yes, the entity ID is referred to as the schedule ID which is returned in the receipt of the ScheduleCreate transaction.

## 3. What transactions can be scheduled?

In its early iteration, a small subset of transactions will be schedulable. You check out [this](../../../sdks/schedule-transaction/create-a-schedule-transaction.md) page for a list of transaction types that are supported today. All other transaction types will be available to schedule in future releases. The complete list of transactions that users can schedule in the future can be found here.

## 4. How can I find a schedule transaction that requires my signature?

* The creator of the scheduled transaction can provide you a schedule ID which you specify in the ScheduleSign transaction to submit your signature.
* You can query a mirror node to return all schedule transactions that have your public key associated with it. This option is not available today, but is planned for the future.

## 5. What happens if the scheduled transaction does not have sufficient balance?

If the scheduled transaction (inner transaction) fee payer does not have sufficient balance then the inner transaction will fail while the schedule transaction (outer transaction) will be successful.

## 6. Can you delay a transaction once it has been scheduled?

No, you cannot delay or modify a scheduled transaction once it's been submitted to a network. You would need to delete the schedule transaction and create a new one with the modifications.

## 7. What happens if multiple users create the same schedule transaction?

* The first transaction to reach consensus will create the schedule transaction and provide the schedule entity ID
* The other users will get the schedule ID in the receipt of the transaction that was submitted. The receipt status will result in `IDENTICAL_SCHEDULE_ALREADY_CREATED`. These users would need to submit a ScheduleSign transaction to append their signatures to the schedule transaction.

## 8. When does the scheduled transaction execute?

The scheduled transaction executes when the last signature is received.

## 9. How often does the network check to see if the scheduled transaction (inner transaction) has met the signature requirement?

Every time the schedule transaction is signed.

## 10. How do you get information about a schedule transaction?

You can submit a [schedule info query](../../../sdks/schedule-transaction/get-schedule-info.md) request to the network.

## 11. When does a scheduled transaction expire?

A scheduled transaction expires in 30 minutes. In future implementations, we will allow the user to set the time at which the scheduled transaction should execute at, and the transaction will expire at that time.
