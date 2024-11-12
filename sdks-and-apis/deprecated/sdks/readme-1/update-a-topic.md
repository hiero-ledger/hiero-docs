# Update a topic

A transaction that updates the properties of an existing topic. This includes the topic memo, admin key, submit key, auto renew account, and auto renew period.

#### Topic Properties

| Field                  | Description                                                                                         |
| ---------------------- | --------------------------------------------------------------------------------------------------- |
| **Topic ID**           | Update the topic ID                                                                                 |
| **Admin Key**          | Set a new admin key that authorizes update topic and delete topic transactions.                     |
| **Submit Key**         | Set a new submit key for a topic that authorizes sending messages to this topic.                    |
| **Topic Memo**         | Set a new short publicly visible memo on the new topic and is stored with the topic. (100 bytes)    |
| **Auto Renew Account** | Set a new auto-renew account ID for this topic (once autoRenew functionality is supported by HAPI). |
| **Auto Renew Period**  | Set a new auto -enew period for this topic (once autoRenew functionality is supported by HAPI).     |

**Transaction Signing Requirements**

* If an admin key is updated, the transaction must be signed by the pre-update admin key and post-update admin key.
* If the admin key was set during the creation of the topic, the admin key must sign the transaction to update any of the topic's properties
* If no adminKey was defined during the creation of the topic, you can only extend the expirationTime.

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

{% tabs %}
{% tab title="V1" %}
| Constructor                             | Description                                            |
| --------------------------------------- | ------------------------------------------------------ |
| `new ConsensusTopicUpdateTransaction()` | Initializes the ConsensusTopicUpdateTransaction object |

```java
new ConsensusTopicUpdateTransaction()
```

**Methods**

| Method                                     | Type      | Requirements |
| ------------------------------------------ | --------- | ------------ |
| `setTopicId(<topicId>)`                    | TopicId   | Required     |
| `setAdminKey(<adminKey>)`                  | Key       | Optional     |
| `setSubmitKey(<submitKey>)`                | Key       | Optional     |
| `setExpirationTime(<expirationTime>)`      | Instant   | Optional     |
| `setTopicMemo(<memo>)`                     | String    | Optional     |
| `setAutoRenewAccountId(<accountId>)`       | AccountId | Optional     |
| `setAutoRenewPeriod(<autoRenewAccountId>)` | Duration  | Optional     |
| `clearAdminKey()`                          |           | Optional     |
| `clearSubmitKey()`                         |           | Optional     |
| `clearTopicMemo()`                         |           | Optional     |
| `clearAutoRenewAccountId()`                |           | Optional     |

{% code title="Java" %}
```java
//Create a transaction to add a submit key
ConsensusTopicUpdateTransaction transaction = new ConsensusTopicUpdateTransaction()
    .setSubmitKey(submitKey);

//Sign the transaction with the admin key to authorize the update
ConsensusTopicUpdateTransaction signTx = transaction.build(client).sign(adminKey);

//Sign the transaction with the client operator, submit to a Hedera network, get the transaction ID
TransactionId txId = signTx.execute(client);

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
//Create a transaction to add a submit key
const transaction = await new ConsensusTopicUpdateTransaction()
    .setSubmitKey(submitKey)
    .build(client);

//Sign the transaction with the admin key to authorize the update
const signTx = await transaction.sign(adminKey);

//Sign the transaction with the client operator, submit to a Hedera network, get the transaction ID
const txId = await signTx.execute(client);

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
| Method                    | Type      | Requirements |
| ------------------------- | --------- | ------------ |
| `getTopicId()`            | TopicId   | Optional     |
| `getAdminKey()`           | Key       | Optional     |
| `getSubmitKey()`          | Key       | Optional     |
| `getTopicMemo()`          | String    | Optional     |
| `getAutoRenewAccountId()` | AccountId | Disabled     |
| `getAutoRenewPeriod()`    | Duration  | Disabled     |

{% code title="Java" %}
```java
 //Create a transaction to add a submit key
TopicUpdateTransaction transaction = new TopicUpdateTransaction()
    .setSubmitKey(submitKey);

//Get submit key
transaction.getSubmitKey()

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
 //Create a transaction to add a submit key
const transaction = new TopicUpdateTransaction()
    .setSubmitKey(submitKey);

//Get submit key
transaction.getSubmitKey()

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewTopicUpdateTransaction()
    SetSubmitKey()

transaction := transaction.GetSubmitKey()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}

##
