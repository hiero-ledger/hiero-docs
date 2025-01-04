# Schedule Transaction

## Overview

A scheduled transaction is a type of transaction that allows you to publicly collect all the required signatures on the network. For example, Transaction A requires signatures from Alice, Bob, and Carol. Alice can schedule and sign Transaction A using the schedule transaction.  Alice also specifies an expiry time for Transaction A during creation. Once the schedule transaction is successfully executed and posted on the network Alice can call Bob and Carol to sign the transaction. Bob and Carol can sign the schedule transaction by submitting a schedule sign transaction.

Transaction A will automatically execute once it receives the minimum required signatures. However, if the required signatures are not received by the specified expiry time, Transaction A will not execute.  Alice can optionally set the transaction to execute automatically at its expiry time. In this case, even if all required signatures are applied to the transaction, the transaction will wait until the expiry time to execute.

Unlike other Hedera transactions, this one allows you to queue a transaction for future execution (up to two months into the future). This feature is ideal for transactions that require multiple signatures and would benefit from being submitted on-chain.&#x20;

The transaction types that can be scheduled in a schedule transaction as of Hedera Services Release 0.57 are the following:

* `TransferTransaction`
* `TokenMintTransaction`
* `TokenBurnTransaction`
* `AccountCreateTransaction`
* `AccountUpdateTransaction`
* `FileUpdateTransaction`
* `SystemDeleteTransaction`
* `SystemUndeleteTransaction`
* `FreezeTransactions`
* `ContractExecuteTransaction`&#x20;
* `ContractCreateTransaction`
* `ContractUpdateTransaction`
* `ContractDeleteTransaction`

**Transaction Throttles**

Schedule transactions are throttled based on the transaction they contain. For example, a scheduled transaction containing the transaction type of “CryptoTransfer” would be throttled as defined [here](https://docs.hedera.com/hedera/~/changes/VqLt72ouijnkkuBU0ekK/networks/mainnet).

\[[Reference: HIP-423](https://hips.hedera.com/hip/hip-423)]

***

## **Creating a Schedule Transaction**

When a schedule transaction is created, the following information will need to be specified in the `ScheduleCreateTransaction`.

#### Scheduled Transaction ID

The Transaction ID of the transaction that needs to be scheduled. You will need to create the transaction that you would like to schedule prior to creating the schedule transaction. Once you have created the transaction you want to schedule,  you will need to specify that transaction ID in this field.

**Admin Key**

Setting an admin key on a schedule transaction allows the user to cancel or delete the schedule transaction, if needed. This key is optional to set. If this key is not set upon creation, the transaction cannot be deleted.&#x20;

**Expiration Time**

The expiration time is a timestamp for specifying when the transaction should be evaluated for execution and then expire. The maximum allowed value is 62 days ([5356800 seconds](https://github.com/hashgraph/hedera-services/blob/develop/hedera-node/hedera-config/src/main/java/com/hedera/node/config/data/SchedulingConfig.java#L35)).&#x20;

* Scheduled Transactions will execute at the earliest available consensus time after their expiration time on a best-effort basis.

**Wait for Expiry**

The default behavior for a scheduled transaction is to automatically execute when the required number signatures for the transaction are received . If the transaction should wait for the specified expiry time to send the transaction to the network, you can optionally enable the wait\_for\_expiry flag.&#x20;

* When set to true, the transaction will be evaluated for execution at expiration\_time instead of when all required signatures are received.
* When this flag is set to false, the transaction will execute immediately after sufficient signatures are received

**Payer Account ID**

The account ID of the account responsible for paying the transaction fees of the scheduled transaction. This field is optional. If not set, the transaction fee payer for the schedule transaction defaults to the transaction fee payer account of the scheduled transaction.

**Schedule Memo**

Publicly visible text that is stored with the schedule transaction and can be viewed in a network explorer up to 100 bytes and does not include the zero byte.&#x20;

{% content-ref url="../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md" %}
[create-a-schedule-transaction.md](../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md)
{% endcontent-ref %}

***

## **Signing and Submitting a Schedule Transaction**

Before submitting your scheduled transaction, you must sign it with the key of the account responsible for paying the schedule transaction fees and, optionally, the key of the transaction fee payer account for the scheduled transaction, if specified. Additionally, if your signature is required for the scheduled transaction, you can sign the `ScheduleCreateTransaction` using that key.

After a `ScheduleCreateTransaction` successfully executes, the receipt will include the schedule ID and the scheduled transaction ID. The **schedule ID** is a unique identifier used to reference the created schedule transaction. The **scheduled transaction ID** represents the transaction scheduled by the schedule transaction. Scheduled transaction IDs include a `?scheduled` flag at the end (e.g., `0.0.1234@1615422161.673238162?scheduled`), indicating it is a scheduled transaction. This ID inherits the valid start time and the account ID from the original schedule transaction.

You can request the current state of a schedule transaction by querying the network for `ScheduleGetInfoQuery` using the schedule ID. The request will return the following information:

* Schedule ID
* Account ID that created the schedule transaction
* Account ID that paid for the creation of the scheduled transaction
* Transaction body of the transaction that was scheduled
* Transaction ID for the transaction that was scheduled
* Current list of signatures
* Admin key (if any)
* Expiration time
* The timestamp of when the transaction was deleted, if true

{% content-ref url="../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md" %}
[get-schedule-info.md](../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md)
{% endcontent-ref %}

***

## **Signing the Scheduled Transaction**

After the schedule transaction is submitted, the scheduled transaction becomes available for on-chain signing. Required parties can use a `ScheduleSignTransaction` to add their signatures to the scheduled transaction. Once the minimum number of signatures is collected, the transaction will automatically execute, unless it is configured to wait until the expiry timestamp.

**Scheduled Transaction Record**

Once the schedule transaction successfully executes, the transaction record is made available. To get the transaction record for the scheduled transaction after successful execution, you can do the following:

1. Poll the network for the specified scheduled transaction ID. Once the schedule transaction executes the scheduled transaction successfully, request the record for the scheduled transaction using the scheduled transaction ID.
2. Query a Hedera mirror node for the scheduled transaction ID.
3. Run your own mirror node and query for the scheduled transaction ID.

{% content-ref url="../sdks-and-apis/sdks/schedule-transaction/sign-a-schedule-transaction.md" %}
[sign-a-schedule-transaction.md](../sdks-and-apis/sdks/schedule-transaction/sign-a-schedule-transaction.md)
{% endcontent-ref %}

***

## **Deleting a schedule transaction**

A schedule transaction can be deleted if an admin key was set during its creation. If no admin key was set, the schedule transaction cannot be deleted.

{% content-ref url="../sdks-and-apis/sdks/schedule-transaction/delete-a-schedule-transaction.md" %}
[delete-a-schedule-transaction.md](../sdks-and-apis/sdks/schedule-transaction/delete-a-schedule-transaction.md)
{% endcontent-ref %}

***

## Tutorial/Examples

{% content-ref url="../tutorials/more-tutorials/schedule-your-first-transaction.md" %}
[schedule-your-first-transaction.md](../tutorials/more-tutorials/schedule-your-first-transaction.md)
{% endcontent-ref %}

***

## FAQ

<details>

<summary>What is the difference between a schedule transaction and scheduled transaction?</summary>

A **schedule transaction** is a transaction that can schedule any Hedera transaction with the ability to collect the required signatures on the Hedera network in preparation for its execution.

A **scheduled transaction** is a transaction that was scheduled by the schedule transaction.

</details>

<details>

<summary>Is there an entity ID assigned to a schedule transaction?</summary>

Yes, the entity ID is referred to as the **schedule ID** which is returned in the receipt of the `ScheduleCreateTransaction`.

</details>

<details>

<summary>What transactions can be scheduled?</summary>

Refer to the Overview section of this page.

</details>

<details>

<summary>How can I find a scheduled transaction that requires my signature?</summary>

* The creator of the schedule transaction can provide you a schedule ID which you specify in the `ScheduleSignTransaction` to submit your signature.

</details>

<details>

<summary>What happens if the scheduled transaction does not have sufficient balance?</summary>

If the fee payer account for the scheduled transaction (e.g., a transfer transaction) does not have a sufficient balance, the scheduled transaction will fail. However, the schedule transaction itself will still be considered successful.

</details>

<details>

<summary>Can you delay a transaction once it has been scheduled?</summary>

No, you cannot delay or modify a scheduled transaction once it's been submitted to a network. You would need to delete the scheduled transaction and create a new one with the modifications. If the transaction cannot be deleted, the transaction will need to expire.

</details>

<details>

<summary>What happens if multiple users create the same schedule transaction?</summary>

* The first transaction to reach consensus will create the schedule transaction and provide the schedule entity ID
* The other users will get the schedule ID in the receipt of the transaction that was submitted. The receipt status will result in `IDENTICAL_SCHEDULE_ALREADY_CREATED`. These users would need to submit a `ScheduleSignTransaction` to append their signatures to the schedule transaction.

</details>

<details>

<summary>When does the scheduled transaction execute?</summary>

The scheduled transaction executes when the last signature is received. Unless, the wait for expiry flag was enabled.

</details>

<details>

<summary>How often does the network check to see if the scheduled transaction has met the signature requirement?</summary>

Every time the scheduled transaction is signed.

</details>

<details>

<summary>How do you get information about a schedule transaction?</summary>

You can submit a [schedule info query](../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) request to the network.

</details>

<details>

<summary>When does a schedule transaction expire?</summary>

A scheduled transaction expires at the specified expiration date/time.&#x20;

</details>

<details>

<summary>What does a schedule transaction receipt contain?</summary>

The transaction receipt for a schedule that was created contains the new schedule entity ID and the scheduled transaction ID.

</details>
