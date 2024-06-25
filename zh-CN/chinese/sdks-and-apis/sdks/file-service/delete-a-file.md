# Delete a file

A transaction that deletes a file from a Hedera network. When deleted, a file's contents are truncated to zero length and it can no longer be updated or appended to, or its expiration time extended. When you request the contents or info of a deleted file, the network will return FILE\_DELETED.

**Transaction Signing Requirements**

- The key(s) on the file are required to sign the transaction
- If you do not sign with the key(s) on the file, you will receive an INVALID\_SIGNATURE network error

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                   | Description                                  |
| ----------------------------- | -------------------------------------------- |
| `new FileDeleteTransaction()` | Initializes the FileDeleteTransaction object |

```java
new FileDeleteTransaction()
```

### Methods

| Method                | Type   | Description                                                                  |
| --------------------- | ------ | ---------------------------------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to delete in x.y.z format |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
FileDeleteTransaction transaction = new FileDeleteTransaction()
    .setFileId(newFileId);

//Modify the default max transaction fee to from 1 to 2 hbars
FileDeleteTransaction modifyMaxTransactionFee = transaction.setMaxTransactionFee(new Hbar(2));

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionResponse txResponse = modifyMaxTransactionFee.freezeWith(client).sign(key).execute(client);

//Request the receipt
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " + transactionStatus);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = await new FileDeleteTransaction()
    .setFileId(fileId)
    .setMaxTransactionFee(new Hbar(2))
    .freezeWith(client);

//Sign with the file private key
const signTx = await transaction.sign(fileKey);

//Sign with the client operator private key and submit to a Hedera network
const submitTx = await signTx.execute(client);

//Request the receipt
const receipt = await submitTx.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus3.toString());

//v2.0.5
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileDeleteTransaction().
	  SetFileID(fileId)

//Modify the default max transaction fee to from 1 to 2 hbars
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//Prepare the transaction for signing
freezeTransaction, err := modifyMaxTransactionFee.FreezeWith(client)
if err != nil {
		panic(err)
}

//Sign with the key on the file, sign with the client operator key and submit to a Hedera network
txResponse, err := freezeTransaction.Sign(fileKey).Execute(client)
if err != nil {
		panic(err)
}

//Request the receipt
receipt, err := txResponse.GetReceipt(client)
if err != nil {
		panic(err)
}

//Get the transaction status
transactionStatus := receipt.Status

fmt.Println("The transaction consensus status is ", transactionStatus)

//v2.0.0
```

{% endtab %}
{% endtabs %}

## Get transaction values

| Method                | Type   | Description                                                                             |
| --------------------- | ------ | --------------------------------------------------------------------------------------- |
| `getFileId(<fileId>)` | FileId | The ID of the file to delete (x.z.y) |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
FileDeleteTransaction transaction = new FileDeleteTransaction()
    .setFileId(newFileId);

//Get the file ID
FileId getFileId = transaction.getFileId();
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new FileDeleteTransaction()
    .setFileId(newFileId);
    
//Get the file ID
FileId getFileId = transaction.getFileId();
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileDeleteTransaction().
	  SetFileID(fileId)
	
//Get the file ID
getFileId := transaction.GetFileID()
```

{% endtab %}
{% endtabs %}
