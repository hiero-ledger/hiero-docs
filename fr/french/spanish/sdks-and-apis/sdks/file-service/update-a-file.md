# Update a file

A transaction that updates the state of an existing file on a Hedera network. Once the transaction has been processed, the network will be updated with the new field values of the file. If you need to access a previous state of the file, you can query a mirror node.

**Transaction Signing Requirements**

- The key or keys on the file are required to sign this transaction to modify the file properties
- If you are updating the keys on the file, you must sign with the old key and the new key
- If you do not sign with the key(s) on the file, you will receive an `INVALID_SIGNATURE` network error

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
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

| Method                            | Type                                                           | Requirement |
| --------------------------------- | -------------------------------------------------------------- | ----------- |
| `setFileId(<fileId>)`             | FileId                                                         | Required    |
| `setKey(<keys>)`                  | Key                                                            | Optional    |
| `setContents(<bytes>)`            | byte \[ ] | Optional    |
| `setContents(<text>)`             | String                                                         | Optional    |
| `setExpirationTime(<expiration>)` | Instant                                                        | Optional    |
| `setFileMemo(<memo>)`             | String                                                         | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
FileUpdateTransaction transaction = new FileUpdateTransaction()
    .setFileId(fileId)
    .setKeys(newKey);

//Modify the max transaction fee
FileUpdateTransaction txFee = transaction.setMaxTransactionFee(new Hbar(3));

//Freeze the transaction, sign with the original key, sign with the new key, sign with the client operator key and submit the transaction to a Hedera network
TransactionResponse txResponse = txFee.freezeWith(client).sign(fileKey).sign(newKey).execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = await new FileUpdateTransaction()
    .setFileId(fileId)
    .setContents("The new contents")
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
transaction := hedera.NewFileUpdateTransaction().
      SetFileID(fileId).
        SetKeys(newKey)

//Modify the max transaction fee
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//Prepare the transaction for signing
freezeTransaction, err := modifyMaxTransactionFee.FreezeWith(client)
if err != nil {
        panic(err)
}

//Sign with the key on the file, sign with the client operator key and submit to a Hedera network
txResponse, err := freezeTransaction.Sign(fileKey).Sign(newKey).Execute(client)
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

| Method                | Type       | Requirement |
| --------------------- | ---------- | ----------- |
| `getFileId()`         | FileId     | Optional    |
| `getKey()`            | Key        | Optional    |
| `setContents()`       | ByteString | Optional    |
| `getExpirationTime()` | Instant    | Optional    |
| `getFileMemo()`       | String     | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
FileUpdateTransaction transaction = new FileUpdateTransaction()
    .setFileId(fileId)
    .setKeys(newKey);

//Get the contents of a file
Key getKey = transaction.getKey();

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```java
//Create the transaction
const transaction = new FileUpdateTransaction()
    .setFileId(newFileId);

//Get the contents of a file
const getKey = transaction.getKey();
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileUpdateTransaction().
      SetFileID(fileId).
        SetKeys(newKey)

//Get the contents of a file
getKey := transaction.GetKeys()

//v2.0.0
```

{% endtab %}
{% endtabs %}
