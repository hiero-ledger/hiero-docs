# Get file contents

A query to get the contents of a file. Queries do not change the state of the file or require network consensus. The information is returned from a single node processing the query.

**Query Signing Requirements**

- The client operator private key is required to sign the query request

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Constructor               | Description                            |
| ------------------------- | -------------------------------------- |
| `new FileContentsQuery()` | Initializes a FileContentsQuery object |

```java
new FileContentsQuery()
```

### Methods

| Method                | Type   | Description                                                                                       |
| --------------------- | ------ | ------------------------------------------------------------------------------------------------- |
| `setFileId(<fileId>)` | FileId | The ID of the file to get contents for (x.z.y) |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
FileContentsQuery query = new FileContentsQuery()
    .setFileId(newFileId);

//Sign with client operator private key and submit the query to a Hedera network
ByteString contents = query.execute(client);

//Change to Utf-8 encoding
String contentsToUtf8 = contents.toStringUtf8();

System.out.println(contentsToUtf8);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the query
const query = new FileContentsQuery()
    .setFileId(newFileId);

//Sign with client operator private key and submit the query to a Hedera network
const contents = await query.execute(client);

console.log(contents.toString());

//v2.0.7
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the query
query := hedera.NewFileContentsQuery().
		SetFileID(newFileId)

//Sign with client operator private key and submit the query to a Hedera network
contents, err := query.Execute(client)

fmt.Println(string(contents))

//v2.0.0
```

{% endtab %}
{% endtabs %}

## Get query values

| Method        | Type   | Description                                                                                       |
| ------------- | ------ | ------------------------------------------------------------------------------------------------- |
| `getFileId()` | FileId | The ID of the file to get contents for (x.z.y) |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
FileContentsQuery query = new FileContentsQuery()
    .setFileId(newFileId);

//Get file ID
FileId getFileId = query.getFileId();

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the query
const query = new FileContentsQuery()
    .setFileId(newFileId);

//Get file ID
const getFileId = query.getFileId();
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
