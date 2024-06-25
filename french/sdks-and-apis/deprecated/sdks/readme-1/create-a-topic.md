# Create a topic

A transaction that creates a new topic recognized by the Hedera network. The newly generated topic can be referenced by its `topicId`. The `topicId` is used to identify a unique topic to submit messages to. You can obtain the new topic ID by requesting the receipt of the transaction. All messages within a topic are sequenced with respect to one another and are provided a unique sequence number.

#### Private topic

You can also create a private topic where only authorized parties can submit messages to that topic. To create a private topic you would need to set the `submitKey` property of the transaction. The `submitKey` value is then shared with the authorized parties and is required to successfully submit messages to the private topic.

#### Topic Properties

| Field                  | Description                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin Key**          | Access control for updateTopic/deleteTopic. Anyone can increase the topic's expirationTime regardless of the adminKey. If no adminKey is specified, updateTopic may only be used to extend the topic's expirationTime, and deleteTopic is disallowed.                                                                                                                                                    |
| **Submit Key**         | Access control for submitMessage. If unspecified, no access control is performed to submit messages (all submissions are allowed).                                                                                                                                                                                                                                                                       |
| **Topic Memo**         | Set a short publicly visible memo on the new topic and is stored with the topic. (100 bytes)                                                                                                                                                                                                                                                                                                             |
| **Auto Renew Account** | Optional account to be used at the topic's expirationTime to extend the life of the topic (once autoRenew functionality is supported by HAPI). The topic lifetime will be extended up to a maximum of the autoRenewPeriod or however long the topic can be extended using all funds on the account (whichever is the smaller duration/amount and if any extension is possible with the account's funds). |
| **Auto Renew Period**  | The initial lifetime of the topic and the amount of time to attempt to extend the topic's lifetime by automatically at the topic's expirationTime, if the autoRenewAccount is configured (once autoRenew functionality is supported by HAPI).                                                                                                                                                            |

**Transaction Signing Requirements:**

* If an admin key is specified, the admin key must sign the transaction
* If not admin key is specified the topic is immutable
* If an auto renew account is specified, that account must also sign this transaction

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

{% tabs %}
{% tab title="V1" %}
| Constructor                             | Description                                            |
| --------------------------------------- | ------------------------------------------------------ |
| `new ConsensusTopicCreateTransaction()` | Initializes the ConsensusTopicCreateTransaction object |

```java
new ConsensusTopicCreateTransaction()
```

**Methods**

| Method                                     | Type      | Requirements |
| ------------------------------------------ | --------- | ------------ |
| `setAdminKey(<adminKey>)`                  | PublicKey | Optional     |
| `setSubmitKey(<submitKey>)`                | PublicKey | Optional     |
| `setTopicMemo(<memo>)`                     | String    | Optional     |
| `setAutoRenewAccountId(<accountId>)`       | AccountId | Optional     |
| `setAutoRenewPeriod(<autoRenewAccountId>)` | Duration  | Optional     |

{% code title="Java" %}
```java
//Create the transaction
ConsensusTopicCreateTransaction transaction = new ConsensusTopicCreateTransaction();
 
//Submit the transaction to a Hedera network, store the transaction ID
TransactionId txId = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the topic ID
ConsensusTopicId newTopicId = receipt.getConsensusTopicId();

System.out.println("New topic created: " + newTopicId);
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new ConsensusTopicCreateTransaction();
 
//Submit the transaction to a Hedera network, store the transaction ID
const txId = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the topic ID
const newTopicId = receipt.getConsensusTopicId();

console.log("New topic created: " + newTopicId);
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}
| Method                                     | Type      | Requirements |
| ------------------------------------------ | --------- | ------------ |
| `getAdminKey(<adminKey>)`                  | Key       | Optional     |
| `getSubmitKey(<submitKey>)`                | Key       | Optional     |
| `getTopicMemo(<memo>)`                     | String    | Optional     |
| `getAutoRenewAccountId(<accountId>)`       | AccountId | Disabled     |
| `getAutoRenewPeriod(<autoRenewAccountId>)` | Duration  | Disabled     |

{% code title="Java" %}
```java
//Create the transaction
TopicCreateTransaction transaction = new TopicCreateTransaction()
    .setAdminKey(adminKey);
    
//Get the admin key from the transaction    
Key getKey = transaction.getAdminKey();

//V2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await TopicCreateTransaction()
    .setAdminKey(adminKey);
    
//Get the admin key from the transaction    
const getKey = transaction.getAdminKey();
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewTopicCreateTransaction().
    SetAdminKey(adminKey)

getKey := transaction.GetAdminKey()

//V2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}

##
