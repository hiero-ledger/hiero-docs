# Delete a file

A transaction that deletes a file from a Hedera network. When deleted, a file's contents are truncated to zero length and it can no longer be updated or appended to, or its expiration time extended. When you request the contents or info of a deleted file, the network will return FILE\_DELETED.

**Transaction Signing Requirements**

- The key(s) on the file are required to sign the transaction
- If you do not sign with the key(s) on the file, you will receive an INVALID\_SIGNATURE network error

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                   | Description                                  |
| ----------------------------- | -------------------------------------------- |
| `new FileDeleteTransaction()` | Initializes the FileDeleteTransaction object |

```java
new FileDeleteTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                | Type   | Description                                                                  |
| --------------------- | ------ | ---------------------------------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to delete in x.y.z format |

{% code title="Java" %}

```java
FileDeleteTransaction transaction = new FileDeleteTransaction()
    .setFileId(newFileId);

//Change the default transaction fee to 2 hbars
FileDeleteTransaction newMaxFee = transaction.setMaxTransactionFee(new Hbar(2));

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionId txId = newMaxFee.build(client).sign(key).execute(client);

//Request the receipt
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " + transactionStatus);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
const transaction = new FileDeleteTransaction()
    .setFileId(newFileId);

//Change the default transaction fee to 2 hbars
const newMaxFee = transaction.setMaxTransactionFee(new Hbar(2));

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
const txId = await newMaxFee.build(client).sign(key).execute(client);

//Request the receipt
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " + transactionStatus);

//v1.4.4 (issue: returns unauthorized)
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                | Type   | Description                                                                             |
| --------------------- | ------ | --------------------------------------------------------------------------------------- |
| `getFileId(<fileId>)` | FileId | The ID of the file to delete (x.z.y) |

{% code title="Java" %}

```java
//Create the transaction
FileDeleteTransaction transaction = new FileDeleteTransaction()
    .setFileId(newFileId);

//Get the file ID
FileId getFileId = transaction.getFileId();
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new FileDeleteTransaction()
    .setFileId(newFileId);
    
//Get the file ID
FileId getFileId = transaction.getFileId();
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileDeleteTransaction().
	  SetFileID(fileId)
	
//Get the file ID
getFileId := transaction.GetFileID()
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
