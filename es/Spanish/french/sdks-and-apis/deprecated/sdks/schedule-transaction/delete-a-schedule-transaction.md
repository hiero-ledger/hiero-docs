# Delete a scheduled transaction

A transaction that deletes a schedule transaction from the network. You can delete a schedule transaction if only the admin key was set during the creation of the schedule transaction. If an admin key was not set, the attempted deletion will result in "`SCHEDULE_IS_IMMUTABLE`" response from the network. Once the schedule transaction is deleted, the schedule transaction will be marked as deleted with the consensus timestamp the schedule transaction was deleted at.

**Transaction Signing Requirements**

- The admin key of the schedule transaction

**Transaction Properties**

| Field           | Description                        |
| --------------- | ---------------------------------- |
| **Schedule ID** | The ID of the schedule transaction |

| Constructor                       | Description                                      |
| --------------------------------- | ------------------------------------------------ |
| `new ScheduleDeleteTransaction()` | Initializes the ScheduleDeleteTransaction object |

```java
new ScheduleDeleteTransaction()
```

## Methods

{% tabs %}
{% tab title="V2" %}

| Method                        | Type       | Requirement |
| ----------------------------- | ---------- | ----------- |
| `setScheduleId(<scheduleId>)` | ScheduleId | Required    |
| `getScheduleId()`             | ScheduleId | Optional    |

{% code title="Java" %}

```java
//Create the transaction and sign with the admin key
ScheduleDeleteTransaction transaction = new ScheduleDeleteTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(adminKey);

//Sign with the operator key and submit to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Get the transaction receipt
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction status
Status transactionStatus = receipt.status;
System.out.println("The transaction consensus status is " +transactionStatus);
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction and sign with the admin key
const transaction = await new ScheduleDeleteTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(adminKey);

//Sign with the operator key and submit to a Hedera network
const txResponse = await transaction.execute(client);

//Get the transaction receipt
const receipt = await txResponse.getReceipt(client);

//Get the transaction status
const transactionStatus = receipt.status;
console.log("The transaction consensus status is " +transactionStatus);
```

{% endcode %}

{% code title="Go" %}

```go
//Create the transaction and freeze the unsigned transaction
transaction, err := hedera.NewScheduleDeleteTransaction()
            SetScheduleID(scheduleId).
            FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the admin key, sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.Sign(adminKey).Execute(client)

if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

//Get the transaction consensus status
status:= *receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)
```

{% endcode %}
{% endtab %}
{% endtabs %}
