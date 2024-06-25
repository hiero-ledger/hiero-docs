# Create a scheduled transaction

{% hint style="info" %}
TokenTransfer, ConsensusSubmitMessage, CryptoApproveAllowance, TokenMint and TokenBurn transactions are the transaction types that can be scheduled. Additional schedulable transactions will be added in future releases.
{% endhint %}

A transaction that creates a schedule entity on a Hedera network. The entity ID for a schedule transaction is referred to as the ScheduleID. After successfully executing a schedule create transaction, you can obtain the ScheduleID by requesting the receipt of the transaction immediately after the transaction was executed. The receipt also contains the scheduled transaction ID. The scheduled transaction ID is used to to request the record of the scheduled transaction if it is successfully executed.

When creating a transaction to schedule you do not need to use `.freezeWith(client)` method.

Example:

```java
TransferTransaction transactionToSchedule = new TransferTransaction()
        .addHbarTransfer(newAccountId, Hbar.fromTinybars(-1))
        .addHbarTransfer(myAccountId, Hbar.fromTinybars(1));
```

**Schedule Transaction Duplicate**

If two users submit the same schedule create transaction, the first one to reach consensus will create the schedule ID and the second one will have the schedule ID returned in the receipt of the transaction. The receipt status of the second identical schedule transaction will return a "`IDENTICAL_SCHEDULE_ALREADY_CREATED`" response from the network. The user who submits the second transaction would need to submit a ScheduleSign transaction to add their signature to the schedule transaction.

**Schedule Transaction Deletion**

To retain the ability to delete a schedule transaction, you will need to populate the admin key field when creating a schedule transaction. The admin key will be required to sign the ScheduleDelete transaction to delete the scheduled transaction from the network. If you do not assign an admin key during the creation of the schedule transaction, you will have an immutable schedule transaction.

**Transaction Signing Requirements**

- The key of the account paying for the creation of the schedule transaction
- The key of the payer account ID paying for the execution of the scheduled transaction. If the payer account is not specified, the operator account will be used to pay for the execution by default.
- The admin key if set
- You can optionally sign with any of the required signatures for the scheduled (inner) transaction. Freeze the schedule transaction and call the `.sign()` method to add signatures.

**Transaction Properties**

{% hint style="info" %}
Note: If you do not set the payer account ID the schedule transaction is immutable.
{% endhint %}

| Field                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Schedulable Transaction Body** | The transaction body of the transaction that is being scheduled                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Admin Key**                    | A key that can delete the schedule transaction prior to execution or expiration                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Payer Account ID**             | The account which is going to pay for the execution of the scheduled transaction. If not populated, the scheduling account is charged (optional).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Memo**                         | Publicly visible information about the schedule entity, up to 100 bytes. No guarantee of uniqueness (optional).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Expiration Time**              | <p><em><mark style="background-color:yellow;">Coming soon...</mark></em><br>An optional timestamp for specifying when the transaction should be evaluated for execution and then expire. Defaults to 30 minutes after the transaction's consensus timestamp. <a href="https://hips.hedera.com/hip/hip-423">Reference HIP-423</a></p>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Wait for Expiry**              | <p><em><mark style="background-color:yellow;">Coming soon...</mark></em><br>When set to true, the transaction will be evaluated for execution at expiration_time instead of when all required signatures are received. When set to false, the transaction will execute immediately after sufficient signatures are received to sign the contained transaction. During the initial ScheduleCreate transaction or via ScheduleSign transactions.<br></p><p>Setting this to false does not necessarily mean that the transaction will never execute at expiration time.</p><p>For Example - If the signature requirements for a Scheduled Transaction change via external means (e.g. Account update) such that the scheduled transaction would be allowed to execute, it will do so autonomously at expiration time, unless a schedule sign transaction comes in to “poke” it and force it to go through immediately.<br><a href="https://hips.hedera.com/hip/hip-423">Reference HIP-423</a></p> |

| Constructor                       | Description                                      |
| --------------------------------- | ------------------------------------------------ |
| `new ScheduleCreateTransaction()` | Initializes the ScheduleCreateTransaction object |

```java
new ScheduleCreateTransaction()
```

### Methods

{% tabs %}
{% tab title="V2" %}

| Method                                   | Type              | Requirement |
| ---------------------------------------- | ----------------- | ----------- |
| `setScheduledTransaction(<transaction>)` | Transaction\\<?> | Required    |
| `setAdminKey(<key>)`                     | Key               | Optional    |
| `setPayerAccountId(<id>)`                | AccountId         | Optional    |
| `setScheduleMemo(<memo>)`                | String            | Optional    |
| `setExpirationTime(expirationTime)`      | Instant           | Optional    |
| `setWaitForExpiry(waitForExpiry)`        | boolean           | Optional    |
| `getAdminKey()`                          | Key               | Optional    |
| `getPayerAccountId()`                    | AccountId         | Optional    |
| `getScheduleMemo()`                      | String            | Optional    |

{% code title="Java" %}

```java
//Create a schedule transaction
ScheduleCreateTransaction transaction = new ScheduleCreateTransaction()
     .setScheduledTransaction(transactionToSchedule);

//Sign with the client operator key and submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the schedule ID
ScheduleId scheduleId = receipt.scheduleId;
System.out.println("The schedule ID of the schedule transaction is " +scheduleId);
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create a schedule transaction
const transaction = new ScheduleCreateTransaction()
     .setScheduledTransaction(transactionToSchedule);

//Sign with the client operator key and submit the transaction to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the schedule ID
const scheduleId = receipt.scheduleId;
console.log("The schedule ID of the schedule transaction is " +scheduleId);
```

{% endcode %}

{% code title="Go" %}

```go
	//Create a schedule transaction
	transaction, err := transactionToSchedule.Schedule()
	
	if err != nil {
	panic(err)
	}
	
//Sign with the client operator key and submit the transaction to a Hedera network
txResponse, err := transaction.Execute(client)

if err != nil {
	panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
	panic(err)
}

//Get the schedule ID from the receipt
scheduleId := *receipt.ScheduleID

fmt.Printf("The new token ID is %v\n", scheduleId)
```

{% endcode %}
{% endtab %}
{% endtabs %}
