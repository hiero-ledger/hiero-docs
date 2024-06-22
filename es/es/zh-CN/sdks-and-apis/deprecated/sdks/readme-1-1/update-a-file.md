# Update a file

A transaction that updates the state of an existing file on a Hedera network. Once the transaction has been processed, the network will be updated with the new field values of the file. If you need to access a previous state of the file, you can query a mirror node.

**Transaction Signing Requirements**

- The key or keys on the file are required to sign this transaction to modify the file properties
- If you are updating the keys on the file, you must sign with the old key and the new key
- If you do not sign with the key(s) on the file, you will receive an INVALID\_SIGNATURE network error

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

#### File Properties

| Field                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Key(s)** | Update the keys which must sign any transactions modifying this file. All keys must sign to modify the file's contents or keys. No key is required to sign for extending the expiration time (except the one for the operator account paying for the transaction). The network currently requires a file to have at least one key (or key list or threshold key) but this requirement may be lifted in the future. |
| **Contents**                  | The content to update the files with.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Expiration Time**           | If set, update the expiration time of the file. Must be in the future (may only be used to extend the expiration). To make a file inaccessible use FileDeleteTransaction.                                                                                                                                                                                                                                                                             |
| **Memo**                      | Short publicly visible memo about the file. No guarantee of uniqueness. (100 characters max)                                                                                                                                                                                                                                                                                                                                                                          |

| Constructor                   | Description                                  |
| ----------------------------- | -------------------------------------------- |
| `new FileUpdateTransaction()` | Initializes the FileUpdateTransaction object |

```java
new FileUpdateTransaction()
```

### Methods

{% hint style="info" %}
**Note:** The total size for a given transaction is limited to 6KiB. If you exceed this value you will need to submit a FileUpdateTransaction that is less than 6KiB and then submit a FileAppendTransaction to add the remaining content to the file.
{% endhint %}

{% tabs %}
{% tab title="V1" %}

| Method                            | Type                                                           | Requirement |
| --------------------------------- | -------------------------------------------------------------- | ----------- |
| `setFileId(<fileId>)`             | FileId                                                         | Required    |
| `addKey(<keys>)`                  | PublicKey                                                      | Optional    |
| `setContents(<bytes>)`            | byte \[ ] | Optional    |
| `setContents(<text>)`             | String                                                         | Optional    |
| `setExpirationTime(<expiration>)` | Instant                                                        | Optional    |

{% code title="Java" %}

```java
//Create the transaction
FileUpdateTransaction transaction = new FileUpdateTransaction()
    .addKey(newPublicKey)
    .setFileId(newFileId);

//Modify the default max transaction fee from 1 hbar to 2 hbars
FileUpdateTransaction txFee = transaction.setMaxTransactionFee(new Hbar(3));

//Build the transaction, sign with the original key, sign with the new key, sign with the client operator key and submit the transaction to a Hedera network
TransactionId txId = txFee.build(client).sign(key).sign(newKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new FileUpdateTransaction()
    .addKey(newPublicKey)
    .setFileId(newFileId);

//Modify the default max transaction fee from 1 hbar to 2 hbars
const txFee = transaction.setMaxTransactionFee(new Hbar(3));

//Build the transaction, sign with the original key, sign with the new key, sign with the client operator key and submit the transaction to a Hedera network
const txId = await txFee.build(client).sign(key).sign(newKey).execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                | Type       | Requirement |
| --------------------- | ---------- | ----------- |
| `getFileId()`         | FileId     | Optional    |
| `getKey()`            | Key        | Optional    |
| `setContents()`       | ByteString | Optional    |
| `getExpirationTime()` | Instant    | Optional    |
| `getFileMemo()`       | String     | Optional    |

{% code title="Java" %}

```java
//Create the transaction
FileUpdateTransaction transaction = new FileUpdateTransaction()
    .setFileId(fileId)
    .setKeys(newKey);

//Get the contents of a file
Key getKey = transaction.getKey();

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```java
//Create the transaction
const transaction = new FileUpdateTransaction()
    .setFileId(newFileId);

//Get the contents of a file
const getKey = transaction.getKey();
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileUpdateTransaction().
      SetFileID(fileId).
        SetKeys(newKey)

//Get the contents of a file
getKey := transaction.GetKeys()

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}
