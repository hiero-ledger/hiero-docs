# Append to a file

A transaction that appends new file content to the end of an existing file. The contents of the file can be viewed by submitting a FileContentsQuery request.

**Transaction Signing Requirements**

* The key on the file is required to sign the transaction if different than the client operator account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

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

| Method                         | Type      | Description                  | Requirement |
| ------------------------------ | --------- | ---------------------------- | ----------- |
| `setFileId(<fileId>)`          | FileId    | The ID of the file to append | Required    |
| `setContents(<text>)`          | String    | The content in String format | Optional    |
| `setContents(<content>)`       | byte \[ ] | The content in byte format   | Optional    |
| `setChunkSize(<chunkSize>)`    | int       | The chunk size               | Optional    |
| `setMaxChunkSize(<maxChunks>)` | int       | The max chunk size           | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
FileAppendTransaction transaction = new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents");

//Change the default max transaction fee to 2 hbars
FileCreateTransaction modifyMaxTransactionFee = transaction.setMaxTransactionFee(new Hbar(2)); 

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionResponse txResponse = modifyMaxTransactionFee.freezeWith(client).sign(key).execute(client);

//Request the receipt
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
const transaction = await new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents")
    .setMaxTransactionFee(new Hbar(2))
    .freezeWith(client);

//Sign with the file private key
const signTx = await transaction.sign(fileKey);

//Sign with the client operator key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction2 := hedera.NewFileAppendTransaction().
        SetFileID(newFileId).
        SetContents([]byte("The appended contents"))

//Change the default max transaction fee to 2 hbars
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//Prepare transaction for signing, 
freezeTransaction, err := modifyMaxTransactionFee.FreezeWith(client)
if err != nil {
        panic(err)
}

//Sign with the key on the file, sign with the client operator key and submit to a Hedera network
txResponse2 err := freezeTransaction.Sign(fileKey).Execute(client)
if err != nil {
        panic(err)
}

//Request the receipt
receipt, err := txResponse.GetReceipt(client)
if err != nil {
        panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Println("The transaction consensus status is ", transactionStatus)

//v2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method          | Type   | Description                    | Requirement |
| --------------- | ------ | ------------------------------ | ----------- |
| `getFileId()`   | FileId | The file ID in the transaction | Optional    |
| `getContents()` | String | The content in the transaction | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
FileAppendTransaction transaction = new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents");

//Get the contents
ByteString getContents = transaction.getContents();

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```java
//Create the transaction
const transaction = new FileAppendTransaction()
    .setFileId(newFileId)
    .setContents("The appended contents");

//Get the contents
const getContents = transaction.getContents();
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction2 := hedera.NewFileAppendTransaction().
    SetFileID(newFileId).
        SetContents([]byte("The appended contents"))

//Get the contents
getContents2 := transaction2.GetContents()

//v2.0.0
```
{% endtab %}
{% endtabs %}
