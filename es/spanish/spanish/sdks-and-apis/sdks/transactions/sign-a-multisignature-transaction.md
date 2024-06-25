# Sign a multisignature transaction

Hedera supports multisignature transactions. This means a Hedera transaction can require more than one key to sign a transaction in order for it to be processed on a Hedera network. These keys can be set up as a key list where all the keys in the specified list are required to sign the transaction or a threshold key where only a subset of the keys from a specified list are required to sign the transaction. The example below shows how you can use multiple keys to sign and submit a transaction.

{% hint style="info" %}
_**Note:** This example uses version 2.0 of the SDKs._
{% endhint %}

## 1. Create the transaction

In this example, we will use a transfer transaction that requires 3 keys to sign the transaction. If all 3 keys do not sign the transaction, the transaction will not execute and an "`INVALID_SIGNATURE`" response will be returned from the network. The `senderAccountId` is a Hedera account that was created with a key list of 3 keys. Since the sender account is required to sign in a transfer transaction, all three keys are required to sign to complete the transfer of hbars from the sender account to the recipient account. The recipient account is not required to sign the transaction.

After you create the transfer transaction, you will need to freeze (`freezeWith(client)`) the transaction from further modification so that transaction cannot be tampered with. This ensures each signer is signing the same exact transaction. The `transaction` is then shared with each of the three signers to sign with their private keys.

{% hint style="info" %}
It is required to set the account ID of the node(s) the transaction will be submitted to when freezing a transaction for signatures. To set the node account ID(s) you apply the `.setNodeAccountIds()` method.
{% endhint %}

{% tabs %}
{% tab title="Java" %}

```java
//The node account ID to submit the transaction to. You can add more than 1 node account ID to the list.
List<AccountId> nodeId = Collections.singletonList(new AccountId(3));

//Create the transfer transaction
TransferTransaction transferTransaction = new TransferTransaction()
        .addHbarTransfer(senderAccountId, new Hbar(1))
        .addHbarTransfer(receiverAccountId, new Hbar(-1)
        .setNodeAccountIds(nodeId);

//Freeze the transaction from any further modifications
TransferTransaction transaction = transferTransaction.freezeWith(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//The node account ID to submit the transaction to. You can add more than 1 node account ID to the list
const nodeId = [];
nodeId.push(new AccountId(3));

//Create the transfer transaction
const transferTransaction = new TransferTransaction()
    .addHbarTransfer(senderAccountId, new Hbar(1))
    .addHbarTransfer(receiverAccountId, new Hbar(-1))
    .setNodeAccountIds(nodeId);

//Freeze the transaction from further modifications
const transaction = await transferTransaction.freezeWith(client);
```

{% endtab %}

{% tab title="Go" %}

```go
nodeAccountId, err := hedera.AccountIDFromString("0.0.3")
if err != nil {
}

nodeIdList := []hedera.AccountID{nodeAccountId}

//Create the transfer transaction
transferTransaction := hedera.NewTransferTransaction().
    AddHbarTransfer(senderAccountId, hedera.NewHbar(1)).
        AddHbarTransfer(receiverAccountId, hedera.NewHbar(-1)).
        SetNodeAccountIDs(nodeIdList)

//Freeze the transaction from any further modifications
transaction, err := transferTransaction.FreezeWith(client)
```

{% endtab %}
{% endtabs %}

## 2. Collect required signatures

Each signer can sign the `transaction`with their private key. The signed transactions are then returned to you from each of the 3 signers, resulting in 3 separate signature bytes. The sample code below is for illustrative purposes as you would not have the private keys to sign the transaction for each signer.

{% tabs %}
{% tab title="Java" %}

```java
//Signer one signs the transaction with their private key
byte[] signature1 = privateKeySigner1.signTransaction(transaction);

//Signer two signs the transaction with their private key
byte[] signature2 = privateKeySigner2.signTransaction(transaction);

//Signer three signs the transaction with their private key
byte[] signature3 = privateKeySigner3.signTransaction(transaction);

//signature1, signature2, and signature3 are returned back to you
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Signer one signs the transaction with their private key
const signature1 = privateKeySigner1.signTransaction(transaction);

//Signer two signs the transaction with their private key
const signature2 = privateKeySigner2.signTransaction(transaction);

//Signer three signs the transaction with their private key
const signature3 = privateKeySigner3.signTransaction(transaction);

//signature1, signature2, and signature3 are returned back to you
```

{% endtab %}

{% tab title="Go" %}

```go
//Signer one signs the transaction with their private key
signature1, err := privateKeySigner1.SignTransaction(transaction.Transaction)
if err != nil {
}

//Signer two signs the transaction with their private key
signature2, err := privateKeySigner2.SignTransaction(transaction.Transaction)
if err != nil {
}

//Signer three signs the transaction with their private key
signature3, err := privateKeySigner3.SignTransaction(transaction.Transaction)
if err != nil {
}

//signature1, signature2, and signature3 are returned back to you
```

{% endtab %}
{% endtabs %}

## 3. Create a single transaction with all three signatures

Once you have collected all three signature bytes (`signature1`, `signature2`, `signature 3`), you will then apply them to the transaction (`transaction`) to create a single transaction with all three signatures to submit to the network. You will take the `transaction` that we started with and apply the signature bytes to the transaction using `addSignature()`. You will need the public keys of each of the signers to include in the method.

You can add as many signatures to a single transaction as long as the transaction size does not exceed 6,144 kb. Each additional signature applied to the transaction will increase the transaction fee from its base cost of $0.0001. If the transaction fee increases beyond the SDK's default max transaction fee of 1 hbar, you will need to update the max transaction fee value by calling the `.setMaxTransactionFee()` before submitting the transaction to the network.

{% tabs %}
{% tab title="Java" %}

```java
//Collate all three signatures with the transaction
TransferTransaction signedTransaction = transaction.addSignature(publicKey1, signature1).addSignature(publicKey2, signature2).addSignature(publicKey3, signature3);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Collate all three signatures with the transaction
const signedTransaction = transaction.addSignature(publicKey1, signature1).addSignature(publicKey2, signature2).addSignature(publicKey3, signature3);
```

{% endtab %}

{% tab title="Go" %}

```go
//Collate all three signatures with the transaction
signedTransaction := transaction.AddSignature(publicKey1, signature1).AddSignature(publicKey2, signature2).AddSignature(publicKey3, signature3)
```

{% endtab %}
{% endtabs %}

## 4. Verify the required signers public keys

Before you submit the transaction to a Hedera network, you may want to verify the keys that signed the transaction. You can view the public keys that signed the transaction by calling `.getSignatures()` to `signedTransaction`. You can then compare the public keys returned from the `signedTransaction.getSignatures()` to the public keys of each of the signers to verify the required keys have signed. Both the public key and public key bytes are returned.

{% tabs %}
{% tab title="Java" %}

```java
//Print all public keys that signed the transaction
System.out.println("The public keys that signed the transaction " +signedTransaction.getSignatures());
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Print all public keys that signed the transaction
console.log("The public keys that signed the transaction  " +signedTransaction.getSignatures());
```

{% endtab %}

{% tab title="Go" %}

```go
//Print all public keys that signed the transaction
signatures, err := signedTransaction.GetSignatures()
fmt.Println("The public keys that signed the transaction ", signatures)
```

{% endtab %}
{% endtabs %}

## 5. Submit the transaction

We are now ready to submit the transfer transaction to a Hedera network. To submit the transaction, we will apply the `.execute()` method to `signedTransaction`. After the transaction is submitted, we will print the transaction ID to the console. You can use the transaction ID to search for transaction details in a mirror node explorer like [DragonGlass](https://app.dragonglass.me/hedera/home), [Hashscan](https://hashscan.io/#/mainnet/dashboard), or [Ledger Works](https://explore.lworks.io). Be sure to select the correct network when searching for the transaction in a mirror node explorer. You can also check the status of a transaction by requesting the [receipt](get-a-transaction-receipt.md) of the transaction and obtaining the status.

{% tabs %}
{% tab title="Java" %}

```java
//Submit the transaction to a Hedera network
TransactionResponse submitTx = signedTransaction.execute(client);

//Get the transaction ID
TransactionId txId = submitTx.transactionId;

//Print the transaction ID to the console
System.out.println("The transaction ID " +txId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Submit the transaction to a Hedera network
const submitTx = await signedTransaction.execute(client);

//Get the transaction ID
const txId = submitTx.transactionId.toString();

//Print the transaction ID to the console
console.log("The transaction ID " +txId);
```

{% endtab %}

{% tab title="Go" %}

```go
//Submit the transaction to a Hedera network
submitTx, err := signedTransaction.Execute(client)

//Get the transaction ID
txId := submitTx.TransactionID

//Print the transaction ID to the console
fmt.Println("The transaction ID ", txId)
```

{% endtab %}
{% endtabs %}
