# Modify transaction fields

When submitting a transaction to the Hedera network, various fields can be modified, such as the transaction ID, consensus time, memo field, account ID of the node, and the maximum fee. These values can be set using methods provided by the SDKs. However, they are not required as the SDK can automatically create or use default values.

{% hint style="info" %}
_**Note:** The total size for a given transaction is limited to 6KiB._
{% endhint %}

| **Fields**              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Transaction ID**      | <p>Set the ID for this transaction. The transaction ID includes the operator's account ( the account paying the transaction fee). If two transactions have the same transaction ID, they won't both have an effect. One will complete normally and the other will fail with a duplicate transaction status.</p><p><br>Normally, you should not use this method. Just before a transaction is executed, a<br>transaction ID will be generated from the operator on the client.</p> |
| **Valid Duration**      | <p>Set the duration that this transaction is valid for</p><p>Note: Max network valid duration is 180 seconds. SDK default value is 120 seconds. The minium valid duration period is 15 seconds.</p>                                                                                                                                                                                                                                                                               |
| **Memo**                | Set a note or description that should be recorded in the transaction record (maximum length of 100 characters). Anyone can view this memo on the network                                                                                                                                                                                                                                                                                                                          |
| **Node ID**             | Set the account ID of the node that this transaction will be submitted to.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Max transaction fee** | <p>Set the max transaction fee for the operator (transaction fee payer account) is willing to pay</p><p>Default: 2 hbar</p>                                                                                                                                                                                                                                                                                                                                                       |

{% hint style="info" %}
_**Note:** The SDKs do not require you to set these fields when submitting a transaction to a Hedera network. All methods below are optional and can be used to modify any fields._
{% endhint %}

<table><thead><tr><th width="432">Method</th><th width="314.3333333333333">Type</th></tr></thead><tbody><tr><td><code>setTransactionID(&#x3C;transactionId>)</code></td><td>TransactionID</td></tr><tr><td><code>setTransactionValidDuration(&#x3C;validDuration>)</code></td><td>Duration</td></tr><tr><td><code>setTransactionMemo(&#x3C;memo>)</code></td><td>String</td></tr><tr><td><code>setNodeAccountIds(&#x3C;nodeAccountIds>)</code></td><td>List&#x3C;AccountId></td></tr><tr><td><code>setMaxTransactionFee(&#x3C;maxTransactionFee>)</code></td><td>Hbar</td></tr><tr><td><code>setGrpcDeadline(&#x3C;grpcDeadline>)</code></td><td>Duration</td></tr><tr><td><code>setRegenerateTransactionId(&#x3C;regenerateTransactionId>)</code></td><td>boolean</td></tr></tbody></table>

{% hint style="warning" %}
#### Account Alias

If an alias is set during account creation, it becomes [immutable](../../../support-and-community/glossary.md#immutability), meaning it cannot be changed. If you plan to update or rotate keys in the future, do not set the alias at the time of initial account creation. The alias can be set after finalizing all key updates.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction and set the transaction properties
Transaction transaction = new AccountCreateTransaction() //Any transaction can be applied here
    .setKey(ecdsaPublicKey)
    //Do NOT set an alias if you need to update/rotate keys in the future
    .setAlias(ecdsaPublicKey.toEvmAddress())
    .setInitialBalance(new Hbar(1))
    .setMaxTransactionFee(new Hbar(2)) //Set the max transaction fee to 2 hbar
    .setTransactionMemo("Transaction memo"); //Set the node ID to submit the transaction to
    
//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and set the transaction properties
const transaction = await new AccountCreateTransaction() //Any transaction can be applied here
    .setKey(ecdsaPublicKey)
    //Do NOT set an alias if you need to update/rotate keys in the future
    .setAlias(ecdsaPublicKey.toEvmAddress())
    .setInitialBalance(new Hbar(1))
    .setMaxTransactionFee(new Hbar(2)) //Set the max transaction fee to 2 hbar
    .setTransactionMemo("Transaction memo"); //Set the node ID to submit the transaction to
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and set the transaction properties
transaction := hedera.NewAccountCreateTransaction(). //Any transaction can be applied here
    SetKey(ecdsaPublicKey).
    //Do NOT set an alias if you need to update/rotate keys in the future
    SetAlias(ecdsaPublicKey.ToEvmAddress()).
    SetInitialBalance(hedera.NewHbar(1)).
    SetMaxTransactionFee(hedera.NewHbar(2)). //Set the max transaction fee to 2 hbar
    SetTransactionMemo("Transaction memo") //Set the transaction memo

//v2.0.0 
```
{% endtab %}
{% endtabs %}

## Get transaction properties

<table><thead><tr><th width="340">Method</th><th>Type</th></tr></thead><tbody><tr><td><code>getTransactionID()</code></td><td>TransactionID</td></tr><tr><td><code>getTransactionValidDuration()</code></td><td>Duration</td></tr><tr><td><code>getTransactionMemo()</code></td><td>String</td></tr><tr><td><code>getNodeAccountId()</code></td><td>AccountID</td></tr><tr><td><code>getMaxTransactionFee()</code></td><td>Hbar</td></tr><tr><td><code>getTransactionHash()</code></td><td>byte[ ]</td></tr><tr><td><code>getTransactionHashPerNode()</code></td><td>Map&#x3C;AccountId, byte [ ]></td></tr><tr><td><code>getSignatures()</code></td><td>Map&#x3C;AccountId, Map&#x3C;PublicKey, byte [ ]>></td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction and set the transaction properties
Transaction transaction = new AccountCreateTransaction() //Any transaction can be applied here
    .setInitialBalance(Hbar.fromTinybars(1000))
    .setMaxTransactionFee(new Hbar(50)) //Set the max transaction fee to 50 hbar
    .setNodeAccountId(new AccountId(3)) //Set the node ID to submit the transaction to
    .setTransactionMemo("Transaction memo"); //Add a transaction memo
    
Hbar maxTransactionFee = transaction.getMaxTransactionFee();
//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and set the transaction properties
const transaction = await new AccountCreateTransaction() //Any transaction can be applied here
    .setInitialBalance(Hbar.fromTinybars(1000))
    .setMaxTransactionFee(new Hbar(50)) //Set the max transaction fee to 50 hbar
    .setNodeAccountId(new AccountId(3)) //Set the node ID to submit the transaction to
    .setTransactionMemo("Transaction memo"); //Add a transaction memo
    
const maxTransactionFee = transaction.getMaxTransactionFee();
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and set the transaction properties
transaction := hedera.NewAccountCreateTransaction(). //Any transaction can be applied here
    SetKey(newKey.PublicKey()).
    SetInitialBalance(hedera.NewHbar(100)).
    SetMaxTransactionFee(hedera.NewHbar(2)). //Set the max transaction fee to 2 hbar
    SetTransactionMemo("Transaction memo") //Add a transaction memo

maxtransactionFee := transaction.GetMaxTransactionFee()
//v2.0.0         
```
{% endtab %}
{% endtabs %}
