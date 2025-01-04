# Sign a scheduled transaction

A `ScheduleSignTransaction` is a consensus node transaction that adds signatures to a scheduled transaction. When this transaction is successful:

* The signature is added to the scheduled transaction
* A record of the transaction is created

To view the signatures that have been added to a scheduled transaction, you can use a [`ScheduleInfoQuery`](get-schedule-info.md) to query the network. Once the scheduled transaction has received all the required signatures, it will execute immediately, unless it has been configured to execute at a specified expiration time.

**Transaction Signing Requirements**

* The signature of the account paying for the transaction fees
* The signature being applied to the scheduled transaction

**Transaction Properties**

| Field           | Description                                                    |
| --------------- | -------------------------------------------------------------- |
| **Schedule ID** | The ID of the schedule transaction to submit the signature for |

## Methods

<table><thead><tr><th width="351.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setScheduleId(&#x3C;scheduleId>)</code></td><td>ScheduleId</td><td>Required</td></tr></tbody></table>

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
