# Delete a topic

A transaction that deletes a topic from the Hedera network. Once a topic is deleted, the topic cannot be recovered to receive messages and all submitMessage calls will fail. Older messages can still be accessed, even after the topic is deleted, via the mirror node.

**Transaction Signing Requirements**

- If the adminKey was set upon the creation of the topic, the adminKey is required to sign to successfully delete the topic
- If no adminKey was set upon the creating of the topic, you cannot delete the topic and will receive an UNAUTHORIZED error

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

{% tabs %}
{% tab title="V1" %}

| Constructor                             | Description                                          |
| --------------------------------------- | ---------------------------------------------------- |
| `new ConsensusTopicDeleteTransaction()` | Initializes a ConsensusTopicDeleteTransaction object |

```java
new ConsensusTopicDeleteTransaction()
```

**Methods**

| Method                  | Type    | Description                   |
| ----------------------- | ------- | ----------------------------- |
| `setTopicId(<topicId>)` | TopicId | The ID of the topic to delete |

{% code title="Java" %}

```java
ConsensusTopicDeleteTransaction transaction = new ConsensusTopicDeleteTransaction()
     .setTopicId(newTopicId);

//Sign the transaction with the admin key, sign with the client operator and submit the transaction to a Hedera network, get the transaction ID
TransactionId txId = transaction.build(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
const transaction = new ConsensusTopicDeleteTransaction()
     .setTopicId(newTopicId);

//Sign the transaction with the admin key, sign with the client operator and submit the transaction to a Hedera network, get the transaction ID
const txId = await transaction.build(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                  | Type    | Description                   | Requirement |
| ----------------------- | ------- | ----------------------------- | ----------- |
| `getTopicId(<topicId>)` | TopicId | The ID of the topic to delete | Required    |

{% code title="Java" %}

```java
//Create the transaction
TopicDeleteTransaction transaction = new TopicDeleteTransaction()
    .setTopicId(newTopicId);

//Get topic ID
TopicId getTopicId = transaction.getTopicId(); 

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```java
//Create the transaction
const transaction = new TopicDeleteTransaction()
    .setTopicId(newTopicId);

//Get topic ID
const getTopicId = transaction.getTopicId();

//v2.0.0
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewTopicDeleteTransaction().
		SetTopicID(topicID)

//Get topic ID
getTopicId := transaction.GetTopicID()

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
