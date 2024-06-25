# Submit a message

A transaction that submits a topic message to the Hedera network. To access the messages submitted to a topic ID, subscribe to the topic via a mirror node. The mirror node will publish the ordered messages to subscribers. Once the transaction is successfully executed, the receipt of the transaction will include the topic's updated sequence number and topic running hash.

**Transaction Signing Requirements**

- Anyone can submit a message to a public topic
- The submitKey is required to sign the transaction for a private topic

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

{% hint style="info" %}
HCS message size: 1024 bytes
{% endhint %}

{% tabs %}
{% tab title="V1" %}

| Constructor                               | Description                                            |
| ----------------------------------------- | ------------------------------------------------------ |
| `new ConsensusMessageSubmitTransaction()` | Initializes a ConsensusMessageSubmitTransaction object |

```java
new ConsensusMessageSubmitTransaction()
```

**Methods**

| Method                                                                                                                 | Type                                                           | Description                                                                                                                                                                                                                                     | Requirement |
| ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTopicId(<topicId>)`                                                                                                | TopicId                                                        | The topic ID to submit the message to                                                                                                                                                                                                           | Required    |
| `setMessage(<message>)`                                                                                                | String                                                         | The message in a String format                                                                                                                                                                                                                  | Optional    |
| `setMessage(<message>)`                                                                                                | byte \[ ] | The message in a byte array format                                                                                                                                                                                                              | Optional    |
| `setMessage(<message>)`                                                                                                | ByteString                                                     | The message in a ByteString format                                                                                                                                                                                                              | Optional    |
| `setMaxChunk(<maxChunks>)`                                                                                             | int                                                            | The number of chunks to break the message into. Default:10                                                                                                                                                      | Optional    |
| <p><code>setMaxChunkInfo(&#x3C;initial</code><br><code>Transactionid</code><br><code>,totalNumber, number>)</code></p> | TransactionId, int, int                                        | <p>initialId: TransactionID of the first chunk, gets copied to every subsequent chunk in a fragmented message.</p><p>total: total number of chunks</p><p>number: The sequence number (from 1 to total) of the current chunk in the message.</p> | Optional    |

{% code title="Java" %}

```java
//Submits a message to a public topic 
new ConsensusMessageSubmitTransaction()
    .setTopicId(topicId)
    .setMessage("hello, HCS! " + i)
    .build(client)
    .execute(client)
    .getReceipt(client);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Submits a message to a public topic 
await new ConsensusMessageSubmitTransaction()
    .setTopicId(topicId)
    .setMessage("hello, HCS! " + i)
    .build(client)
    .execute(client)
    .getReceipt(client);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                    | Type                                                           | Description                           |
| ------------------------- | -------------------------------------------------------------- | ------------------------------------- |
| `getTopicId()`            | TopicId                                                        | The topic ID to submit the message to |
| `getMessage()`            | ByteString                                                     | The message being submitted           |
| `getAllTransactionHash()` | byte \[ ] | The hash for each transaction         |

{% code title="Java" %}

```java
//Create the transaction
TopicMessageSubmitTransaction transaction = new TopicMessageSubmitTransaction()
    .setTopicId(newTopicId)
    .setMessage("hello, HCS! ");

//Get the transaction message
ByteString getMessage = transaction.getMessage();
//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = await new TopicMessageSubmitTransaction()
    .setTopicId(newTopicId)
    .setMessage("hello, HCS! ");

//Get the transaction message
const getMessage = transaction.getMessage();

//v2.0.0
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewTopicSubmitTransaction().
        SetTopicID(topicID).
        SetMessage([]byte(content))

//Get the transaction message
getMessage := transaction.GetMessage()
//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}
