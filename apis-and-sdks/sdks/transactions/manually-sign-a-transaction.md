# Manually sign a transaction

Sign a transaction using the private key(s) required to sign the transaction. You cannot sign the transaction with a public key. If your client operator account private key is the key used in the key field(s) of a transaction, you do not need to manually sign the transaction. The `execute(client)` method signs the transaction with the client operator account private key before it is submitted to a Hedera network.

| **Method**                                 | **Type**                     | **Description**                                                                                             |
| ------------------------------------------ | ---------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `sign(<privateKey>)`                       | PrivateKey                   | Sign the transaction with an Ed25519 private key                                                            |
| `signWith(<publicKey, transactionSigner>)` | PublicKey, TransactionSigner | Sign the transaction with a callback that may block waiting for user confirmation.                          |
| `signWithOperator(<client>)`               | Client                       | Sign the transaction with the client                                                                        |
| `signWithSigner(<signer>)`                 |                              | Sign the transaction with a local wallet. Local wallet available in Hedera JavaScript SDK only. >=`v2.11.0` |

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
    .sign(PrivateKey.fromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5c");

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
    .sign(PrivateKey.fromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5c");
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
