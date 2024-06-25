# Sign a scheduled transaction

A transaction that appends signatures to a scheduled transaction. You will need to know the schedule ID to reference the scheduled transaction to submit signatures. A record will be generated for each ScheduleSign transaction that is successful and the scheduled entity will subsequently update with the public keys that have signed the scheduled transaction. To view the keys that have signed the scheduled transaction, you can query the network for the schedule info. Once a scheduled transaction receives the last required signature, the scheduled transaction executes.

**Transaction Signing Requirements**

* The key of the account paying for the transaction

**Transaction Properties**

| Field           | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| **Schedule ID** | The ID of the scheduled transaction to submit the signature for |

## Methods

<table><thead><tr><th width="351.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setScheduleId(&#x3C;scheduleId>)</code></td><td>ScheduleId</td><td>Required</td></tr><tr><td><code>clearScheduleId(&#x3C;scheduleId>)</code></td><td>ScheduleId</td><td>Optional</td></tr><tr><td><code>getScheduleId()</code></td><td>ScheduleId</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
{% code title="Java" %}
```java
//Create the transaction
ScheduleSignTransaction transaction = new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(privateKeySigner1);

//Sign with the client operator key to pay for the transaction and submit to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction status
Status transactionStatus = receipt.status;
System.out.println("The transaction consensus status is " +transactionStatus);
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new ScheduleSignTransaction()
     .setScheduleId(scheduleId)
     .freezeWith(client)
     .sign(privateKeySigner1);

//Sign with the client operator key to pay for the transaction and submit to a Hedera network
const txResponse = await transaction.execute(client);

//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction status
const transactionStatus = receipt.status;
console.log("The transaction consensus status is " +transactionStatus);
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and freeze the unsigned transaction
transaction, err := hedera.NewScheduleSignTransaction().
            SetScheduleID(scheduleId).
            FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with one of the required signatures, sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.Sign(privateKeySigner1).Execute(client)

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
{% endtab %}
{% endtabs %}
