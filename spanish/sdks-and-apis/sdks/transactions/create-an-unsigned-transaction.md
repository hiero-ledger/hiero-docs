# Create an unsigned transaction

These methods allow you to build a transaction that requires further processing before it is submitted to a Hedera network. After you freeze the transaction you can use `.sign(privateKey)` to sign the transaction with multiple keys or convert the transaction to bytes for further processing.

<table data-header-hidden><thead><tr><th></th><th width="131.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>freeze()</code></td><td></td><td>Freeze this transaction from further modification to prepare for signing or serialization. You will need to set the node account ID (<code>setNodeAccountId()</code>) and transaction ID (<code>setTransactionId()</code>).</td></tr><tr><td><code>freezeWith(&#x3C;client>)</code></td><td>Client</td><td>Freeze this transaction from further modification to prepare for signing or serialization. Will use the 'Client', if available, to generate a default Transaction ID and select 1/3 nodes to prepare this transaction for.</td></tr><tr><td><code>freezeWithSigner(&#x3C;signer>)</code></td><td></td><td>Freeze the transaction with a local wallet. Local wallet available in Hedera JavaScript SDK only. >=<code>v2.11.0</code></td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
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
{% endtab %}

{% tab title="JavaScript" %}
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
{% endtab %}

{% tab title="Go" %}
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
{% endtab %}
{% endtabs %}

**Sample Output**

```bash
crypto_create_account {
auto_renew_period {
seconds: 7776000
}
initial_balance: 1000
key {
ed25519: "\272g\374\310f\354\274\273bU\256\v\032$e\311\021p\216*L\332\277Y\343\230\277PUmy\373"
}
receive_record_threshold: 9223372036854775807
send_record_threshold: 9223372036854775807
node_account_i_d {
account_num: 6
realm_num: 0
shard_num: 0
}
transaction_fee: 100000000
transaction_i_d {
account_i_d {
account_num: 9401
realm_num: 0
shard_num: 0
}
transaction_valid_start {
nanos: 469101387
seconds: 1604559135
}
}
transaction_valid_duration {
seconds: 120
}
}
```
