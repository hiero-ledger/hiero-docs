# Append to a file

A transaction that appends new file content to the end of an existing file. The contents of the file can be viewed by submitting a FileContentsQuery request.

**Transaction Signing Requirements**

- The key on the file is required to sign the transaction if different than the client operator account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor               | Description                                  |
| ------------------------- | -------------------------------------------- |
| `FileAppendTransaction()` | Initializes the FileAppendTransaction object |

```java
new FileAppendTransaction()
```

{% hint style="info" %}
The default max transaction fee (1 hbar) is not enough to create a file. Use `setMaxTransactionFee()`to change the default max transaction fee from 1 hbar to 2 hbars. The default chunk size is 2,048 bytes.
{% endhint %}

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                   | Type                                                           | Description                  | Requirement |
| ------------------------ | -------------------------------------------------------------- | ---------------------------- | ----------- |
| `setFileId(<fileId>)`    | FileId                                                         | The ID of the file to append | Required    |
| `setContents(<content>)` | byte \[ ] | The content in byte format   | Optional    |
| `setContents(<content>)` | String                                                         | The content in string format | Optional    |

{% code title="Java" %}

```java
FileAppendTransaction transaction = new FileAppendTransaction()
       .setFileId(newFileId)
       .setContents("The appended contents")
       .setMaxTransactionFee(new Hbar(2));

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionId txId = transaction.build(client).sign(key).execute(client);

//Request the receipt
TransactionReceipt receipt1 = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus)
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
const transaction = new FileAppendTransaction()
       .setFileId(newFileId)
       .setContents("The appended contents")
       .setMaxTransactionFee(new Hbar(2));

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
const txId = await transaction.build(client).sign(key).execute(client);

//Request the receipt
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus)
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method          | Type   | Description                    | Requirement |
| --------------- | ------ | ------------------------------ | ----------- |
| `getFileId()`   | FileId | The file ID in the transaction | Optional    |
| `getContents()` | String | The content in the transaction | Optional    |

{% code title="Java" %}

```java
//Create the transaction
FileAppendTransaction transaction = new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents");

//Get the contents
ByteString getContents = transaction.getContents();

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```java
//Create the transaction
const transaction = new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents");

//Get the contents
const getContents = transaction.getContents();
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction2 := hedera.NewFileAppendTransaction().
    SetFileID(newFileId).
        SetContents([]byte("The appended contents"))

//Get the contents
getContents2 := transaction2.GetContents()

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}
