# Manually sign a transaction

Sign a transaction using the private key(s) required to sign the transaction. You cannot sign the transaction with a public key. If your client operator account private key is the key used in the key field(s) of a transaction, you do not need to manually sign the transaction. The `execute(client)` method signs the transaction with the client operator account private key before it is submitted to a Hedera network.

<table data-header-hidden><thead><tr><th></th><th width="179.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>sign(<privateKey>)</code></td><td>PrivateKey</td><td>Sign the transaction with an ED25519 private key</td></tr><tr><td><code>signWith(<publicKey, transactionSigner>)</code></td><td>PublicKey, TransactionSigner</td><td>Sign the transaction with a callback that may block waiting for user confirmation.</td></tr><tr><td><code>signWithOperator(<client>)</code></td><td>Client</td><td>Sign the transaction with the client</td></tr><tr><td><code>signWithSigner(<signer>)</code></td><td></td><td>Sign the transaction with a local wallet. Local wallet available in Hedera JavaScript SDK only. >=<code>v2.11.0</code></td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

```java
//Create any transaction
AccountUpdateTransaction transaction = new AccountUpdateTransaction()
     .setAccountId(accountId)
     .setKey(key);

//Freeze the transaction for signing
AccountUpdateTransaction freezeTransaction = transaction.freezeWith(client);

//Sign the transaction with a private key
AccountCreateTransaction signedTransaction = freezeTransaction
    .sign(PrivateKey.fromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5c"));

//v2.0.0    
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create any transaction
const transaction = await new AccountUpdateTransaction()
     .setAccountId(accountId)
     .setKey(key)
     .freezeWith(client);

//Sign the transaction with a private key
const signedTransaction = transaction
    .sign(PrivateKey.fromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5c"));
```

{% endtab %}

{% tab title="Go" %}

```go
//Create any transaction
transaction := hedera.NewAccountUpdateTransaction().
		SetAccountID(newAccountId).
		SetKey(updateKey.PublicKey())
	
//Freeze the transaction for signing
freezeTransaction, err := transaction.FreezeWith(client)
if err != nil {
    panic(err)
}

signedTransaction := freezeTransaction.Sign(hedera.PrivateKeyFromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5c"))
//v2.0.0
```

{% endtab %}
{% endtabs %}
