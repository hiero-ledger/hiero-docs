# Create a schedule transaction

A `ScheduleCreateTransaction` is a consensus node transaction that creates a schedule entity on the Hedera network. The entity ID for a schedule transaction is called a `ScheduleID`. After successfully executing a schedule create transaction, you can retrieve the network assigned `ScheduleID` by requesting the transaction receipt. The receipt also includes the scheduled transaction ID, which can be used to request the record of the scheduled transaction after its successful execution.

When creating a transaction to schedule you do not need to use `.freezeWith(client)` method.

Example:

```java
TransferTransaction transactionToSchedule = new TransferTransaction()
        .addHbarTransfer(newAccountId, Hbar.fromTinybars(-1))
        .addHbarTransfer(myAccountId, Hbar.fromTinybars(1));
```

{% hint style="info" %}
Refer to this [page](../../../core-concepts/scheduled-transaction.md#overview) to view the types of transactions that can be scheduled.&#x20;
{% endhint %}

**Schedule Transaction Duplicate**

If two users submit the same `ScheduleCreateTransaction`, the first transaction to reach consensus will create the **schedule ID**.

For example, if User A and User B submit the same scheduled transaction and User A's transaction reaches consensus first, User B's transaction will return the status `IDENTICAL_SCHEDULE_ALREADY_CREATED`. If User B is required to sign the scheduled transaction, they must submit and sign a `ScheduleSignTransaction` to add their signature.

**Schedule Transaction Deletion**

To retain the ability to delete a schedule transaction, you will need to set the admin key field when creating a schedule transaction. The admin key will be required to sign the `ScheduleDeleteTransaction` to delete the scheduled transaction from the network. If you do not assign an admin key during the creation of the schedule transaction, you will have an immutable schedule transaction.

**Transaction Signing Requirements**

* The signature of the account paying for the creation of the schedule transaction
* The signature of the payer account responsible for covering the execution fees of the scheduled transaction. For example, if you are scheduling a transfer transaction, the assigned transaction fee payer must provide a signature. If no transaction fee payer account is specified, the schedule's transaction fee payer account will be used by default to cover the execution fees.
* The admin key signature, if set
* You can optionally sign with any of the required signatures for the scheduled transaction. Freeze the schedule transaction and call the `.sign()` method to add signatures in this transaction.

**Transaction Properties**

| Field                            | Description                                                                                                                                                                                                                                                                                                                      |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Schedulable Transaction Body** | The transaction body of the transaction that is being scheduled. Ex. Transfer Transaction                                                                                                                                                                                                                                        |
| **Admin Key**                    | A key that can delete the schedule transaction prior to execution or expiration                                                                                                                                                                                                                                                  |
| **Payer Account ID**             | The account which is going to pay the transaction fees for the execution of the scheduled transaction. If not populated, the account paying for the schedule transaction will be charged (optional).                                                                                                                             |
| **Expiration Time**              | A timestamp for specifying when the transaction should be evaluated for execution and then expire (optional). The maximum allowed value is 62 days ([5356800 seconds](https://github.com/hashgraph/hedera-services/blob/develop/hedera-node/hedera-config/src/main/java/com/hedera/node/config/data/SchedulingConfig.java#L35)). |
| **Wait for Expiry**              | Set the transaction to execute at the specified expiration time. The default behavior is to execute at the time the minimum number of signatures are received (optional).                                                                                                                                                        |
| **Memo**                         | Publicly visible information about the schedule entity, up to 100 bytes. No guarantee of uniqueness (optional).                                                                                                                                                                                                                  |

### Methods

<table><thead><tr><th width="427">Method</th><th width="164.33333333333331">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setScheduledTransaction(&#x3C;transaction>)</code></td><td>Transaction&#x3C;?> </td><td>Required</td></tr><tr><td><code>setAdminKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setPayerAccountId(&#x3C;id>)</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>setScheduleMemo(&#x3C;memo>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setExpirationTime(expirationTime)</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>setWaitForExpiry()</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>getAdminKey()</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>getPayerAccountId()</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>getScheduleMemo()</code></td><td>String</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
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
{% endtab %}

{% tab title="JavaScript" %}
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
{% endtab %}

{% tab title="Go" %}
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
{% endtab %}
{% endtabs %}

