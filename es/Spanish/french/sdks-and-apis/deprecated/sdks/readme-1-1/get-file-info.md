# Get file info

A query that returns the current state of a file. Queries do not change the state of the file or require network consensus. The information is returned from a single node processing the query.

**Query Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

**File Info Response**

| **Field**                     | Description                                                                                                                       |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **File ID**                   | The Hedera ID of the file                                                                                                         |
| **Key(s)** | The current admin key(s) on the account                                                                        |
| **Size**                      | The number of bytes in the file contents                                                                                          |
| **Expiration Time**           | The current time at which the file is set to expire                                                                               |
| **Deleted**                   | Whether or not the file has been deleted                                                                                          |
| **Ledger ID**                 | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198). |
| **Memo**                      | A short description, if any                                                                                                       |

\
**Query Signing Requirements**

- The client operator account paying for the query fees is required to sign

| Constructor           | Description                          |
| --------------------- | ------------------------------------ |
| `new FileInfoQuery()` | Initializes the FileInfoQuery object |

```java
new FileInfoQuery()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                | Type   | Description                                                                                          |
| --------------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to get information for (x.y.z) |

{% code title="Java" %}

```java
//Create the query
FileInfoQuery query = new FileInfoQuery()
  .setFileId(fileId);

//Sign the query with the client operator private key and submit to a Hedera network
FileInfo getInfo = query.execute(client);

System.out.println("File info response: " +getInfo.keys);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the query
const query = new FileInfoQuery()
  .setFileId(fileId);

//Sign the query with the client operator private key and submit to a Hedera network
const getInfo = await query.execute(client);

console.log("File info response: " +getInfo.keys);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get query values

{% tabs %}
{% tab title="V2" %}

| Method        | Type   | Description                                                                                       |
| ------------- | ------ | ------------------------------------------------------------------------------------------------- |
| `getFileId()` | FileId | The ID of the file to get contents for (x.z.y) |

{% code title="Java" %}

```java
//Create the query
FileInfoQuery query = new FileInfoQuery()
  .setFileId(fileId);

//Get file ID
FileId getFileId = query.getFileId();

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the query
FileInfoQuery query = new FileInfoQuery()
  .setFileId(fileId);

//Get file ID
const getFileId = query.getFileId();

//v2.0.0
```

{% endcode %}

{% code title="Go" %}

```java
//Create the query
query := hedera.NewFileContentsQuery().
		SetFileID(newFileId)
		
//Get file ID
getFileId := query.GetFileID()

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
