# Modify transaction fields

When submitting a transaction to the Hedera network, various fields can be modified, such as the transaction ID, consensus time, memo field, account ID of the node, and the maximum fee. These values can be set using methods provided by the SDKs. However, they are not required as the SDK can automatically create or use default values.

{% hint style="info" %}
_**Note:** The total size for a given transaction is limited to 6KiB._
{% endhint %}

| **Fields**              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Transaction ID**      | <p>Set the ID for this transaction. The transaction ID includes the operator's account ( the account paying the transaction fee). If two transactions have the same transaction ID, they won't both have an effect. One will complete normally and the other will fail with a duplicate transaction status.</p><p><br>Normally, you should not use this method. Just before a transaction is executed, a<br>transaction ID will be generated from the operator on the client.</p> |
| **Valid Duration**      | <p>Set the duration that this transaction is valid for</p><p>Note: Max network valid duration is 180 seconds. SDK default value is 120 seconds</p>                                                                                                                                                                                                                                                                                                                                |
| **Memo**                | Set a note or description that should be recorded in the transaction record (maximum length of 100 characters). Anyone can view this memo on the network                                                                                                                                                                                                                                                                                                                          |
| **Node ID**             | Set the account ID of the node that this transaction will be submitted to.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Max transaction fee** | <p>Set the max transaction fee for the operator (transaction fee payer account) is willing to pay</p><p>Default: 2 hbar</p>                                                                                                                                                                                                                                                                                                                                                       |

{% hint style="info" %}
_**Note:** The SDKs do not require you to set these fields when submitting a transaction to a Hedera network. All methods below are optional and can be used to modify any fields._
{% endhint %}

| Method                                                  | Type             |
| ------------------------------------------------------- | ---------------- |
| `setTransactionID(<transactionId>)`                     | TransactionID    |
| `setTransactionValidDuration(<validDuration>)`          | Duration         |
| `setTransactionMemo(<memo>)`                            | String           |
| `setNodeAccountIds(<nodeAccountIds>)`                   | List\<AccountId> |
| `setMaxTransactionFee(<maxTransactionFee>)`             | Hbar             |
| `setGrpcDeadline(<grpcDeadline>)`                       | Duration         |
| `setRegenerateTransactionId(<regenerateTransactionId>)` | boolean          |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction and set the transaction properties
Transaction transaction = new AccountCreateTransaction() //Any transaction can be applied here
    .setKey(key)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .setMaxTransactionFee(new Hbar(2)) //Set the max transaction fee to 2 hbar
    .setTransactionMemo("Transaction memo"); //Set the node ID to submit the transaction to
    
//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and set the transaction properties
const transaction = await new AccountCreateTransaction() //Any transaction can be applied here
    .setKey(key)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .setMaxTransactionFee(new Hbar(2)) //Set the max transaction fee to 2 hbar
    .setTransactionMemo("Transaction memo"); //Set the node ID to submit the transaction to
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and set the transaction properties
transaction := hedera.NewAccountCreateTransaction(). //Any transaction can be applied here
    SetKey(newKey.PublicKey()).
    SetInitialBalance(hedera.NewHbar(1000)). 
    SetMaxTransactionFee(hedera.NewHbar(2)). //Set the max transaction fee to 2 hbar
    SetTransactionMemo("Transaction memo") //Set the transaction memo

//v2.0.0 
```
{% endtab %}
{% endtabs %}

## Get transaction properties

| Method                          | Type                                        |
| ------------------------------- | ------------------------------------------- |
| `getTransactionID()`            | TransactionID                               |
| `getTransactionValidDuration()` | Duration                                    |
| `getTransactionMemo()`          | String                                      |
| `getNodeAccountId()`            | AccountID                                   |
| `getMaxTransactionFee()`        | Hbar                                        |
| `getTransactionHash()`          | byte\[ ]                                    |
| `getTransactionHashPerNode()`   | Map\<AccountId, byte \[ ]>                  |
| `getSignatures()`               | Map\<AccountId, Map\<PublicKey, byte \[ ]>> |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction and set the transaction properties
Transaction transaction = new AccountCreateTransaction() //Any transaction can be applied here
    .setInitialBalance(Hbar.fromTinybars(1000))
    .setMaxTransactionFee(new Hbar(100)) //Set the max transaction fee to 100 hbar
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
    .setMaxTransactionFee(new Hbar(100)) //Set the max transaction fee to 100 hbar
    .setNodeAccountId(new AccountId(3)) //Set the node ID to submit the transaction to
    .setTransactionMemo("Transaction memo"); //Add a transaction memo
    
const maxTransactionFee = transaction.getMaxTransactionFee();
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction and set the transaction properties
transaction := hedera.NewAccountCreateTransaction(). //Any transaction can be applied here
    SetKey(newKey.PublicKey()).
    SetInitialBalance(hedera.NewHbar(1000)). 
    SetMaxTransactionFee(hedera.NewHbar(2)). //Set the max transaction fee to 2 hbar
    SetTransactionMemo("Transaction memo") //Add a transaction memo

maxtransactionFee := transaction.GetMaxTransactionFee()
//v2.0.0         
```
{% endtab %}
{% endtabs %}
