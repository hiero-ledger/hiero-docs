# Create a file

A transaction that creates a new file on a Hedera network. The file is referenced by its file ID which can be obtained from the receipt or record once the transaction reaches consensus on a Hedera network. The file does not have a file name. If the file is too big to create with a single `FileCreateTransaction()`, the file can be appended with the remaining content multiple times using the `FileAppendTransaction()`.

{% hint style="info" %}
The maximum file size is 1,024 kB.
{% endhint %}

**Transaction Signing Requirements**

* The key on the file is required to sign the transaction if different than the client operator account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

#### File Properties

| Field               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Key(s)**          | Set the keys which must sign any transactions modifying this file (the owner(s) of the file). All keys must sign to modify the file's contents or keys. No key is required to sign for extending the expiration time (except the one for the operator account paying for the transaction). The network currently requires a file to have at least one key (or key list or threshold key) but this requirement may be lifted in the future. |
| **Contents**        | The contents of the file. The file contents can be recovered from requesting the FileContentsQuery. Note that the total size for a given transaction is limited to 6KiB (as of March 2020) by the network; if you exceed this you may receive a TRANSACTION\_OVERSIZE error.                                                                                                                                                               |
| **Expiration Time** | Set the instant at which this file will expire, after which its contents will no longer be available. Defaults to 1/4 of a Julian year from the instant was invoked.                                                                                                                                                                                                                                                                       |
| **Memo**            | Short publicly visible memo about the file. No guarantee of uniqueness. (100 characters max)                                                                                                                                                                                                                                                                                                                                               |

### Methods

| Constructor                   | Description                                  |
| ----------------------------- | -------------------------------------------- |
| `new FileCreateTransaction()` | Initializes the FileCreateTransaction object |

```java
new FileCreateTransaction()
```

{% hint style="info" %}
The default max transaction fee (1 hbar) is not enough to create a a file. Use `setDefaultMaxTransactionFee()`to change the default max transaction fee from 1 hbar to 2 hbars.
{% endhint %}

| Method                                | Type       | Requirement |
| ------------------------------------- | ---------- | ----------- |
| `setKeys(<keys>)`                     | Key        | Required    |
| `setContents(<contents>)`             | String     | Optional    |
| `setContents(<bytes>)`                | bytes \[ ] | Optional    |
| `setExpirationTime(<expirationTime>)` | Instant    | Optional    |
| `setFileMemo(<memo>)`                 | String     | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
FileCreateTransaction transaction = new FileCreateTransaction()
    .setKeys(fileKey) 
    .setContents(fileContents);
        
//Change the default max transaction fee to 2 hbars
FileCreateTransaction modifyMaxTransactionFee = transaction.setMaxTransactionFee(new Hbar(2)); 

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionResponse txResponse = modifyMaxTransactionFee.freezeWith(client).sign(fileKey).execute(client);

//Request the receipt
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the file ID
FileId newFileId = receipt.fileId;

System.out.println("The new file ID is: " + newFileId);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new FileCreateTransaction()
    .setKeys([filePublicKey]) //A different key then the client operator key
    .setContents("the file contents")
    .setMaxTransactionFee(new Hbar(2))
    .freezeWith(client);

//Sign with the file private key
const signTx = await transaction.sign(fileKey);

//Sign with the client operator private key and submit to a Hedera network
const submitTx = await signTx.execute(client);

//Request the receipt
const receipt = await submitTx.getReceipt(client);

//Get the file ID
const newFileId = receipt.fileId;

console.log("The new file ID is: " + newFileId);

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction := hedera.NewFileCreateTransaction().
		SetKeys(filePublicKey).
		SetContents([]byte("Hello, World"))

//Change the default max transaction fee to 2 hbars
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//Prepare transaction for signing, 
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

//Get the file ID
newFileId := *receipt.FileID

fmt.Printf("The new file ID is %v\n", newFileId)
//v2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method                | Type       | Requirement |
| --------------------- | ---------- | ----------- |
| `getKeys()`           | Key        | Optional    |
| `getContents()`       | ByteString | Optional    |
| `getExpirationTime()` | Instant    | Optional    |
| `getFileMemo()`       | String     | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
FileCreateTransaction transaction = new FileCreateTransaction()
    .setKeys(key)
    .setContents(fileContents);
        
//Get the file contents
ByteString getContents = transaction.getContents();
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new FileCreateTransaction()
    .setKeys(key)
    .setContents(fileContents);
        
//Get the file contents
const getContents = transaction.getContents();
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction := hedera.NewFileCreateTransaction().
		SetKeys(filePublicKey).
		SetContents([]byte("Hello, World"))

//Get the file contents
getContents := transaction.GetContents()
```
{% endtab %}
{% endtabs %}
