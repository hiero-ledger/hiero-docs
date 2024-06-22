# Get file contents

A query to get the contents of a file. Queries do not change the state of the file or require network consensus. The information is returned from a single node processing the query.

**Query Signing Requirements**

- The client operator private key is required to sign the query request

**Query Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Constructor               | Description                            |
| ------------------------- | -------------------------------------- |
| `new FileContentsQuery()` | Initializes a FileContentsQuery object |

```java
new FileContentsQuery()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                | Type   | Description                                                                                       |
| --------------------- | ------ | ------------------------------------------------------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to get contents for (x.z.y) |

{% code title="Java" %}

```java
//Create the query
FileContentsQuery query = new FileContentsQuery()
    .setFileId(newFileId);

//Sign with client operator private key and submit the query to a Hedera network
byte [] contents = query.execute(client);

//Change to Utf-8 encoding
String contentsToUtf8 = contents.toStringUtf8();

System.out.println(contentsToUtf8);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the query
const query = new FileContentsQuery()
    .setFileId(newFileId);

//Sign with client operator private key and submit the query to a Hedera network
const contents = await query.execute(client);

console.log(contents);
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
FileContentsQuery query = new FileContentsQuery()
    .setFileId(newFileId);

//Get file ID
FileId getFileId = query.getFileId();

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the query
const query = new FileContentsQuery()
    .setFileId(newFileId);

//Get file ID
const getFileId = query.getFileId();
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
