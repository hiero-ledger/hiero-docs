# Submit a message

A transaction that submits a topic message to the Hedera network. To access the messages submitted to a topic ID, subscribe to the topic via a mirror node. The mirror node will publish the ordered messages to subscribers. Once the transaction is successfully executed, the receipt of the transaction will include the topic's updated sequence number and topic running hash.

**Max Chunks**

The **max chunks** setting defines the maximum number of chunks into which a given message can be split. The default value is **20 chunks**, meaning a message can consist of up to 20 chunks by default. This value can be modified using the `setMaxChunks` method.\
\
**Max Chunk Size**

{% hint style="info" %}
🚨 **NOTE:** Max size of an HCS message: 1024 bytes (1 kb).
{% endhint %}

The **max chunk size** refers to the maximum size (in bytes) of each individual chunk of a message. By default, the max chunk size is **1024 bytes (1 KB)**. This value can be modified using the `setChunkSize` method.

**Transaction Signing Requirements**

* Anyone can submit a message to a public topic
* The submitKey is required to sign the transaction for a private topic

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

#### Methods

<table><thead><tr><th width="298">Method</th><th>Type</th><th>Description</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTopicId(&#x3C;topicId>)</code></td><td>TopicId</td><td>The topic ID to submit the message to</td><td>Required</td></tr><tr><td><code>setMessage(&#x3C;message>)</code></td><td>String</td><td>The message in a String format</td><td>Optional</td></tr><tr><td><code>setMessage(&#x3C;message>)</code></td><td>byte [ ]</td><td>The message in a byte array format</td><td>Optional</td></tr><tr><td><code>setMessage(&#x3C;message>)</code></td><td>ByteString</td><td>The message in a ByteString format</td><td>Optional</td></tr><tr><td><code>setChunkSize()</code></td><td>int</td><td>The max size of individual chunk for a given message. Default: 1024 bytes</td><td>Optional</td></tr><tr><td><code>setMaxChunks()</code></td><td>int</td><td>The max number of chunks a given message can be split into. Default: 20</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
TopicMessageSubmitTransaction transaction = new TopicMessageSubmitTransaction()
    .setTopicId(newTopicId)
    .setMessage("hello, HCS! ");

//Sign with the client operator key and submit transaction to a Hedera network, get transaction ID
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
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
await new TopicMessageSubmitTransaction({
        topicId: createReceipt.topicId,
        message: "Hello World",
    }).execute(client);

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction := hedera.NewTopicSubmitTransaction().
        SetTopicID(topicID).
        SetMessage([]byte(content))

//Sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.Execute(client)
if err != nil {
        panic(err)
}

//Request the receipt of the transaction
transactionReceipt, err := txResponse.GetReceipt(client)
if err != nil {
        panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", transactionStatus)
//v2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method                    | Type       | Description                           |
| ------------------------- | ---------- | ------------------------------------- |
| `getTopicId()`            | TopicId    | The topic ID to submit the message to |
| `getMessage()`            | ByteString | The message being submitted           |
| `getAllTransactionHash()` | byte \[ ]  | The hash for each transaction         |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
TopicMessageSubmitTransaction transaction = new TopicMessageSubmitTransaction()
    .setTopicId(newTopicId)
    .setMessage("hello, HCS! ");

//Get the transaction message
ByteString getMessage = transaction.getMessage();
//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new TopicMessageSubmitTransaction()
    .setTopicId(newTopicId)
    .setMessage("hello, HCS! ");

//Get the transaction message
const getMessage = transaction.getMessage();

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction := hedera.NewTopicSubmitTransaction().
        SetTopicID(topicID).
        SetMessage([]byte(content))

//Get the transaction message
getMessage := transaction.GetMessage()
//v2.0.0
```
{% endtab %}
{% endtabs %}
