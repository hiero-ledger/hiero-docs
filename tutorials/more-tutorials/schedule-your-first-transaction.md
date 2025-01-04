# Schedule Your First Transaction

## Summary

In this tutorial, you'll learn how to create and sign a scheduled transaction. Scheduled Transactions enable multiple parties to easily, inexpensively, and natively schedule and execute any type of Hedera transaction together. Once a transaction is scheduled, additional signatures can be submitted via a ScheduleSign transaction. After the last signature is received within the allotted timeframe, the scheduled transaction will execute.

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](broken-reference).
* Set up your environment [here](broken-reference).

***

## Table of Contents

1. [Create Transaction](schedule-your-first-transaction.md#1.-create-a-transaction-to-schedule)
2. [Schedule Transaction](schedule-your-first-transaction.md#2.-schedule-the-transfer-transaction)
3. [Submit Signatures](schedule-your-first-transaction.md#3.-submit-signatures)
4. [Verify Schedule was Triggered](schedule-your-first-transaction.md#4.-verify-the-schedule-was-triggered)
5. [Verify Scheduled Transaction Executed](schedule-your-first-transaction.md#5.-verify-the-scheduled-transaction-executed)

***

## 1. Create a transaction to schedule

First, you will need to build the transaction to schedule. In the example below, you will create a transfer transaction. The sender account has a [threshold key](../../sdks-and-apis/sdks/keys/create-a-threshold-key.md) structure that requires 2 out of the 3 keys to sign the transaction to authorize the transfer amount.

{% tabs %}
{% tab title="Java" %}
```java
//Create a transaction to schedule
TransferTransaction transaction = new TransferTransaction()
     .addHbarTransfer(senderAccount, Hbar.fromTinybars(-1))
     .addHbarTransfer(recipientAccount, Hbar.fromTinybars(1));
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a transaction to schedule
const transaction = new TransferTransaction()
     .addHbarTransfer(senderAccount, Hbar.fromTinybars(-1))
     .addHbarTransfer(recipientAccount, Hbar.fromTinybars(1));
```
{% endtab %}

{% tab title="Go" %}
```go
//Create a transaction to schedule
transaction := hedera.NewTransferTransaction().
     AddHbarTransfer(senderAccount, hedera.HbarFromTinybar(-1)).
     AddHbarTransfer(recipientAccount, hedera.HbarFromTinybar(1))
     
```
{% endtab %}
{% endtabs %}

***

## 2. Schedule the transfer transaction

Next, you will schedule the transfer transaction by submitting a ScheduleCreate transaction to the network. Once the transfer transaction is scheduled, you can obtain the schedule ID from the receipt of the ScheduleCreate transaction. The schedule ID identifies the schedule that scheduled the transfer transaction. The schedule ID can be shared with the three signatories. The schedule is immutable unless the admin key is specified during creation.

The scheduled transaction ID of the transfer transaction can also be returned from the receipt of the ScheduleCreate transaction. You will notice that the transaction ID for a scheduled transaction includes a `?scheduled` flag e.g. `0.0.9401@1620177544.531971543?scheduled.` All transactions that have been scheduled will include this flag.

You can optionally add signatures you may have during the creation of the ScheduleCreate transaction by calling `.freezeWith(client)` and `.sign()` methods. This might make sense if you are one of the required signatures for the scheduled transaction.

Visit the page below to view additional properties that can be set when building a ScheduleCreate transaction.

{% content-ref url="../../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md" %}
[create-a-schedule-transaction.md](../../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md)
{% endcontent-ref %}

{% tabs %}
{% tab title="Java" %}
```java
//Schedule a transaction
TransactionResponse scheduleTransaction = new ScheduleCreateTransaction()
     .setScheduledTransaction(transaction)
     .execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = scheduleTransaction.getReceipt(client);
     
//Get the schedule ID
ScheduleId scheduleId = receipt.scheduleId;
System.out.println("The schedule ID is " +scheduleId);

//Get the scheduled transaction ID
TransactionId scheduledTxId = receipt.scheduledTransactionId;
System.out.println("The scheduled transaction ID is " +scheduledTxId);
```
{% endtab %}

{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">//Schedule a transaction
<strong>const scheduleTransaction = await new ScheduleCreateTransaction()
</strong>     .setScheduledTransaction(transaction)
     .execute(client);

//Get the receipt of the transaction
const receipt = await scheduleTransaction.getReceipt(client);
     
//Get the schedule ID
const scheduleId = receipt.scheduleId;
console.log("The schedule ID is " +scheduleId);

//Get the scheduled transaction ID
const scheduledTxId = receipt.scheduledTransactionId;
console.log("The scheduled transaction ID is " +scheduledTxId);
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
//Schedule a transaction
scheduleTransaction, err := hedera.NewScheduleCreateTransaction().
		SetScheduledTransaction(transaction)
if err != nil {
	panic(err)
}

submitScheduleTx, err := scheduleTransaction.Execute(client)
if err != nil {
	panic(err)
}

//Get the receipt of the transaction
receipt, err := submitScheduleTx.GetReceipt(client)
if err != nil {
	panic(err)
}
	
//Get the schedule ID
scheduleId := receipt.ScheduleID
fmt.Printf("The schedule ID %v\n", scheduleId)

//Get the scheduled transaction ID
scheduleTxId := receipt1.ScheduledTransactionID
fmt.Printf("The scheduled transaction ID is %v\n", scheduleTxId)
```
{% endtab %}
{% endtabs %}

***

## 3. Submit Signatures

### Submit one of the required signatures for the transfer transaction

The signatures are submitted to the network via a [ScheduleSign](../../sdks-and-apis/sdks/schedule-transaction/sign-a-schedule-transaction.md) transaction. The [ScheduleSign ](../../sdks-and-apis/sdks/schedule-transaction/sign-a-schedule-transaction.md)transaction requires the schedule ID of the schedule and the signature of one or more of the required keys. The scheduled transaction has 30 minutes from the time it is scheduled to receive all of its signatures; if the signature requirements are not met, the scheduled transaction will expire.

In the example below, you will submit one signature, confirm the transaction was successful, and get the [schedule info](../../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) to verify the signature was added to the schedule. To verify the signature was added, you can compare the public key of the submitted signature to the public key that is returned from the [schedule info](../../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) request.

{% tabs %}
{% tab title="Java" %}
```java
//Submit the first signatures
TransactionResponse signature1 = new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(signerKey1)
     .execute(client);
     
//Verify the transaction was successful and submit a schedule info request
TransactionReceipt receipt1 = signature1.getReceipt(client);
System.out.println("The transaction status is " +receipt1.status);

ScheduleInfo query1 = new ScheduleInfoQuery()
     .setScheduleId(scheduleId)
     .execute(client);

//Confirm the signature was added to the schedule  
System.out.println(query1);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Submit the first signature
const signature1 = await (await new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(signerKey1))
     .execute(client);
     
//Verify the transaction was successful and submit a schedule info request
const receipt1 = await signature1.getReceipt(client);
console.log("The transaction status is " +receipt1.status.toString());

const query1 = await new ScheduleInfoQuery()
     .setScheduleId(scheduleId)
     .execute(client);

//Confirm the signature was added to the schedule   
console.log(query1);
```
{% endtab %}

{% tab title="Go" %}
```go
//Submit the first signature
signature1, err := hedera.NewScheduleSignTransaction().
		SetScheduleID(scheduleId).
		FreezeWith(client)
if err != nil {
	panic(err)
}

//Verify the transaction was successful and submit a schedule info request
submitTx, err := signature1.Sign(signerKey1).Execute(client)
if err != nil {
	panic(err)
}

receipt1, err := submitTx.GetReceipt(client)
if err != nil {
	panic(err)
}

status := receipt1.Status

fmt.Printf("The transaction status is %v\n", status)

query1, err := hedera.NewScheduleInfoQuery().
	SetScheduleID(scheduleId).
	Execute(client)
	
//Confirm the signature was added to the schedule   
fmt.Print(query1)
```
{% endtab %}
{% endtabs %}

### Submit the second signature

Next, you will submit the second signature and verify the transaction was successful by requesting the receipt. For example purposes, you have access to all three signing keys. But the idea here is that each signer can independently submit their signature to the network.

{% tabs %}
{% tab title="Java" %}
{% code title="Java" %}
```java
//Submit the second signature
TransactionResponse signature2 = new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(signerKey2)
     .execute(client);
     
//Verify the transaction was successful
TransactionReceipt receipt2 = signature2.getReceipt(client);
System.out.println("The transaction status" +receipt2.status);
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Submit the second signature
const signature2 = await (await new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(signerKey2))
     .execute(client);
     
//Verify the transaction was successful
const receipt2 = await signature2.getReceipt(client);
console.log("The transaction status " +receipt2.status.toString());
```
{% endtab %}

{% tab title="Go" %}
```go
//Submit the second signature
signature2, err := hedera.NewScheduleSignTransaction().
		SetScheduleID(scheduleId).
		FreezeWith(client)
if err != nil {
	panic(err)
}

//Verify the transaction was successful and submit a schedule info request
submitTx2, err := signature2.Sign(signerKey2).Execute(client)
if err != nil {
	panic(err)
}

receipt2, err := submitTx2.GetReceipt(client)
if err != nil {
	panic(err)
}

status2 := receipt2.Status

fmt.Printf("The transaction status is %v\n", status2)
```
{% endtab %}
{% endtabs %}

***

## 4. Verify the schedule was triggered

The schedule is triggered after it meets its minimum signing requirements. As soon as the last required signature is submitted, the schedule executes the scheduled transaction. To verify the schedule was triggered, query for the schedule info. When the schedule info is returned, you should notice both public keys that signed in the `signatories` field and the timestamp recorded for when the schedule transaction was executed in the `executedAt` field.

{% tabs %}
{% tab title="Java" %}
```java
//Get the schedule info
ScheduleInfo query2 = new ScheduleInfoQuery()
    .setScheduleId(scheduleId)
    .execute(client);
    
System.out.println(query2);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Get the schedule info
const query2 = await new ScheduleInfoQuery()
    .setScheduleId(scheduleId)
    .execute(client);
    
console.log(query2);
```
{% endtab %}

{% tab title="Go" %}
```go
//Get the schedule info
query2, err := hedera.NewScheduleInfoQuery().
		SetScheduleID(scheduleId).
		Execute(client)

fmt.Print(query2)
```
{% endtab %}
{% endtabs %}

***

## 5. Verify the scheduled transaction executed

When the scheduled transaction (transfer transaction) executes a record is produced that contains the transaction details. The scheduled transaction record can be requested immediately after the transaction has executed and includes the corresponding schedule ID. If you do not know when the scheduled transaction will execute, you can always query a [mirror node](../../core-concepts/mirror-nodes/hedera-mirror-node.md) using the scheduled transaction ID without the `?scheduled` flag to get a copy of the transaction record.

{% tabs %}
{% tab title="Java" %}
```java
//Get the scheduled transaction record
TransactionRecord scheduledTxRecord = TransactionId.fromString(scheduledTxId.toString()).getRecord(client);
System.out.println("The scheduled transaction record is: " +scheduledTxRecord);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Get the scheduled transaction record
const scheduledTxRecord = await TransactionId.fromString(scheduledTxId.toString()).getRecord(client);
console.log("The scheduled transaction record is: " +scheduledTxRecord);
```
{% endtab %}

{% tab title="Go" %}
```go
scheduledTxRecord, err := scheduledTransactionId.GetRecord(client)
fmt.Printf("The scheduled transaction record is %v\n", scheduledTxRecord)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
