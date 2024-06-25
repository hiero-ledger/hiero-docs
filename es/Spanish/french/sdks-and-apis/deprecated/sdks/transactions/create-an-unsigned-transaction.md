# Create an unsigned transaction

These methods allow you to build a transaction that requires further processing before it is submitted to a Hedera network. After you freeze the transaction you can use `.sign(privateKey)` to sign the transaction with multiple keys or convert the transaction to bytes for further processing.

{% tabs %}
{% tab title="V2" %}

| **Method**                   | **Type** | **Description**                                                                                                                                                                                                                                                             |
| ---------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `freeze()`                   |          | Freeze this transaction from further modification to prepare for signing or serialization. You will need to set the node account ID (`setNodeAccountId()`) and transaction ID (`setTransactionId()`). |
| `freezeWith(<client>)`       | Client   | Freeze this transaction from further modification to prepare for signing or serialization. Will use the 'Client', if available, to generate a default Transaction ID and select 1/3 nodes to prepare this transaction for.                  |
| `freezeWithSigner(<signer>)` |          | Freeze the transaction with a local wallet. Local wallet available in Hedera JavaScript SDK only. >=`v2.11.0`                                                                                                                               |

{% code title="Java" %}

```java
//Create an unsigned transaction 
AccountCreateTransaction transaction = new AccountCreateTransaction()
    .setKey(newPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000));

//Freeze the transaction for signing
//The transaction cannot be modified after this point
AccountCreateTransaction freezeTransaction = transaction.freezeWith(client);

System.out.println(freezeTransaction);

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```java
//Create an unsigned transaction 
const transaction = new AccountCreateTransaction()
    .setKey(newPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000));

//Freeze the transaction for signing
//The transaction cannot be modified after this point
const freezeTransaction = transaction.freezeWith(client);

console.log(freezeTransaction);

//v2.0.5
```

{% endcode %}

{% code title="Go" %}

```go
//Create an unsigned transaction 
transaction := hedera.NewAccountCreateTransaction().
    SetKey(newKey.PublicKey()).
    SetInitialBalance(hedera.NewHbar(1000))
    
//Freeze the transaction for signing
//The transaction cannot be modified after this point
freezeTransaction, err := transaction.FreezeWith(client)
if err != nil {
	panic(err)
}

println(freezeTransaction.String())

//v2.0.0
```

{% endcode %}

**Sample Output:**

`crypto_create_account {`\
`auto_renew_period {`\
`seconds: 7776000`\
`}`\
`initial_balance: 1000`\
`key {`\
`ed25519: "\272g\374\310f\354\274\273bU\256\v\032$e\311\021p\216*L\332\277Y\343\230\277PUmy\373"`\
`}`\
`receive_record_threshold: 9223372036854775807`\
`send_record_threshold: 9223372036854775807`\
`node_account_i_d {`\
`account_num: 6`\
`realm_num: 0`\
`shard_num: 0`\
`}`\
`transaction_fee: 100000000`\
`transaction_i_d {`\
`account_i_d {`\
`account_num: 9401`\
`realm_num: 0`\
`shard_num: 0`\
`}`\
`transaction_valid_start {`\
`nanos: 469101387`\
`seconds: 1604559135`\
`}`\
`}`\
`transaction_valid_duration {`\
`seconds: 120`\
`}`\
`}`
{% endtab %}

{% tab title="V1" %}

| **Method**        | **Type** | **Description**                                                                                                                                                                                                                                                             |
| ----------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `build()`         |          | Freeze this transaction from further modification to prepare for signing or serialization. You will need to set the node account ID (`setNodeAccountId()`) and transaction ID (`setTransactionId()`). |
| `build(<client>)` | Client   | Freeze this transaction from further modification to prepare for signing or serialization. Will use the 'Client', if available, to generate a default Transaction ID and select 1/3 nodes to prepare this transaction for.                  |

{% code title="Java" %}

```java
//Create an unsigned transaction 
AccountCreateTransaction transaction = new AccountCreateTransaction()
    .setKey(newPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000));

//Freeze the transaction for signing
//The transaction cannot be modified after this point
AccountCreateTransaction unsignedTransaction = transaction.build(client);

System.out.println(unsignedTransaction);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create an unsigned transaction 
const transaction = new AccountCreateTransaction()
    .setKey(newPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000));

//Freeze the transaction for signing
//The transaction cannot be modified after this point
const unsignedTransaction = transaction.build(client);

System.out.println(unsignedTransaction);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
