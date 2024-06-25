# Create a file

A transaction that creates a new file on a Hedera network. The file is referenced by its file ID which can be obtained from the receipt or record once the transaction reaches consensus on a Hedera network. The file does not have a file name. If the file is too big to create with a single `FileCreateTransaction()`, the file can be appended with the remaining content multiple times using the `FileAppendTransaction()`.

{% hint style="info" %}
The maximum file size is 1,024 kB.
{% endhint %}

**Transaction Signing Requirements**

- The key on the file is required to sign the transaction if different than the client operator account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

#### File Properties

| Field                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Key(s)** | Set the keys which must sign any transactions modifying this file (the owner(s) of the file). All keys must sign to modify the file's contents or keys. No key is required to sign for extending the expiration time (except the one for the operator account paying for the transaction). The network currently requires a file to have at least one key (or key list or threshold key) but this requirement may be lifted in the future. |
| **Contents**                  | The contents of the file. The file contents can be recovered from requesting the FileContentsQuery. Note that the total size for a given transaction is limited to 6KiB (as of March 2020) by the network; if you exceed this you may receive a TRANSACTION\_OVERSIZE error.                                                                                                                                                                                                                  |
| **Expiration Time**           | Set the instant at which this file will expire, after which its contents will no longer be available. Defaults to 1/4 of a Julian year from the instant was invoked.                                                                                                                                                                                                                                                                                                                                                                                   |
| **Memo**                      | Short publicly visible memo about the file. No guarantee of uniqueness. (100 characters max)                                                                                                                                                                                                                                                                                                                                                                                                                                        |

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

{% tabs %}
{% tab title="V1" %}

| Method                            | Type                                                          | Description                                                                                                                        | Requirement |   |
| --------------------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ----------- | - |
| `addKey(<key>)`                   | ​PublicKey​                                                   | The public key of the owner of the file                                                                                            | Required    |   |
| `setContents(<contents>)`         | bytes\[] | The file contents in byte representation                                                                                           | Optional    |   |
| `setContents(<contents>)`         | String                                                        | The file contents in string representation                                                                                         | Optional    |   |
| `setExpirationTime(<expiration>)` | Instant                                                       | The time at which this file should expire (unless FileUpdateTransaction is used before then to extend its life) | Optional    |   |

{% code title="Java" %}

```java
//Create the transaction
FileCreateTransaction transaction = new FileCreateTransaction()
    .addkey(fileKey) 
    .setContents(fileContents);
        
//Change the default max transaction fee to 2 hbars
FileCreateTransaction modifyMaxTransactionFee = transaction.setMaxTransactionFee(new Hbar(2)); 

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
TransactionId txId = modifyMaxTransactionFee.build(client).sign(fileKey).execute(client);

//Request the receipt
TransactionReceipt receipt = txId.getReceipt(client);

//Get the file ID
FileId newFileId = receipt.getFileId();

System.out.println("The new file ID is: " + newFileId);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new FileCreateTransaction()
    .addkey(fileKey) 
    .setContents(fileContents);
        
//Change the default max transaction fee to 2 hbars
const modifyMaxTransactionFee = transaction.setMaxTransactionFee(new Hbar(2)); 

//Prepare transaction for signing, sign with the key on the file, sign with the client operator key and submit to a Hedera network
const txId = await modifyMaxTransactionFee.build(client).sign(fileKey).execute(client);

//Request the receipt
const receipt = await txId.getReceipt(client);

//Get the file ID
const newFileId = receipt.getFileId();

console.log("The new file ID is: " + newFileId);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                | Type       | Requirement |
| --------------------- | ---------- | ----------- |
| `getKeys()`           | Key        | Optional    |
| `getContents()`       | ByteString | Optional    |
| `getExpirationTime()` | Instant    | Optional    |
| `getFileMemo()`       | String     | Optional    |

{% code title="Java" %}

```java
//Create the transaction
FileCreateTransaction transaction = new FileCreateTransaction()
    .setKeys(key)
    .setContents(fileContents);
        
//Get the file contents
ByteString getContents = transaction.getContents();
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new FileCreateTransaction()
    .setKeys(key)
    .setContents(fileContents);
        
//Get the file contents
const getContents = transaction.getContents();
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewFileCreateTransaction().
		SetKeys(filePublicKey).
		SetContents([]byte("Hello, World"))

//Get the file contents
getContents := transaction.GetContents()
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
