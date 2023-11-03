# Get file info

A query that returns the current state of a file. Queries do not change the state of the file or require network consensus. The information is returned from a single node processing the query.

**Query Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

**File Info Response**

| **Field**           | Description                                                                                       |
| ------------------- | ------------------------------------------------------------------------------------------------- |
| **File ID**         | The Hedera ID of the file                                                                         |
| **Key(s)**          | The current admin key(s) on the account                                                           |
| **Size**            | The number of bytes in the file contents                                                          |
| **Expiration Time** | The current time at which the file is set to expire                                               |
| **Deleted**         | Whether or not the file has been deleted                                                          |
| **Ledger ID**       | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198). |
| **Memo**            | A short description, if any                                                                       |

\
**Query Signing Requirements**

* The client operator account paying for the query fees is required to sign

| Constructor           | Description                          |
| --------------------- | ------------------------------------ |
| `new FileInfoQuery()` | Initializes the FileInfoQuery object |

```java
new FileInfoQuery()
```

### Methods

| Method                | Type   | Description                                       |
| --------------------- | ------ | ------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to get information for (x.y.z) |

{% tabs %}
{% tab title="Java" %}
```java
//Create the query
FileInfoQuery query = new FileInfoQuery()
  .setFileId(fileId);

//Sign the query with the client operator private key and submit to a Hedera network
FileInfo getInfo = query.execute(client);

System.out.println("File info response: " +getInfo);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the query
const query = new FileInfoQuery()
  .setFileId(fileId);

//Sign the query with the client operator private key and submit to a Hedera network
const getInfo = await query.execute(client);

console.log("File info response: " + getInfo);
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the query
query := hedera.NewFileInfoQuery().
    SetFileID(newFileId)

//Sign the query with the client operator private key and submit to a Hedera network
getInfo, err := query.Execute(client)

fmt.Println(getInfo)
```
{% endtab %}
{% endtabs %}

**Sample Output:**

```
FileInfo{
     fileId=0.0.104926, 
     size=26, 
     expirationTime=2021-02-10T17:48:15Z, 
     deleted=false, 
     keys=[ 302a300506032b6570032100100059296cc51f5d362a3859d3c3c74c6a480cffad9d669a10c1d447ce56e5bf
     ]
}
```

## Get query values

| Method        | Type   | Description                                    |
| ------------- | ------ | ---------------------------------------------- |
| `getFileId()` | FileId | The ID of the file to get contents for (x.z.y) |

{% tabs %}
{% tab title="Java" %}
```java
//Create the query
FileInfoQuery query = new FileInfoQuery()
  .setFileId(fileId);

//Get file ID
FileId getFileId = query.getFileId();

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the query
const query = new FileInfoQuery()
  .setFileId(fileId);

//Get file ID
const getFileId = query.getFileId();

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the query
query := hedera.NewFileContentsQuery().
		SetFileID(newFileId)
		
//Get file ID
getFileId := query.GetFileID()

//v2.0.0
```
{% endtab %}
{% endtabs %}
