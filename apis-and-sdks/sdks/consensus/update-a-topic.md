# Update a topic

A transaction that updates the properties of an existing topic. This includes the topic memo, admin key, submit key, auto-renew account, and auto rauto-renewenew period.

#### Topic Properties

| Field                  | Description                                                                                         |
| ---------------------- | --------------------------------------------------------------------------------------------------- |
| **Topic ID**           | Update the topic ID                                                                                 |
| **Admin Key**          | Set a new admin key that authorizes update topic and delete topic transactions.                     |
| **Submit Key**         | Set a new submit key for a topic that authorizes sending messages to this topic.                    |
| **Topic Memo**         | Set a new short publicly visible memo on the new topic and is stored with the topic. (100 bytes)    |
| **Auto Renew Account** | Set a new auto-renew account ID for this topic (once autoRenew functionality is supported by HAPI). |
| **Auto Renew Period**  | Set a new auto-renew period for this topic (once autoRenew functionality is supported by HAPI).     |

**Transaction Signing Requirements**

* If an admin key is updated, the transaction must be signed by the pre-update admin key and post-update admin key.
* If the admin key was set during the creation of the topic, the admin key must sign the transaction to update any of the topic's properties
* If no adminKey was defined during the creation of the topic, you can only extend the expirationTime.

**Transaction Fees**

* Please see the transaction and query [fees](broken-reference) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                    | Description                                   |
| ------------------------------ | --------------------------------------------- |
| `new TopicUpdateTransaction()` | Initializes the TopicUpdateTransaction object |

```java
new TopicUpdateTransaction()
```

#### Methods

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

{% tabs %}
{% tab title="Java" %}
```java
 //Create a transaction to add a submit key
TopicUpdateTransaction transaction = new TopicUpdateTransaction()
    .setTopicId(topicId)
    .setSubmitKey(submitKey);

//Sign the transaction with the admin key to authorize the update
TopicUpdateTransaction signTx = transaction.freezeWith(client).sign(adminKey);

//Sign the transaction with the client operator, submit to a Hedera network, get the transaction ID
TransactionResponse txResponse = signTx.execute(client);

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
//Create a transaction to add a submit key
const transaction = await new TopicUpdateTransaction()
    .setTopicId(topicId)
    .setSubmitKey(submitKey)
    .freezeWith(client);

//Sign the transaction with the admin key to authorize the update
const signTx = await transaction.sign(adminKey);
    
//Sign with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction := hedera.NewTopicUpdateTransaction().
		SetTopicId(topicId).
    		SetTopicMemo("new memo")

//Sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.FreezeWith(client).Sign(adminkey)Execute(client)

if err != nil {
		panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)

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

| Method                    | Type      | Requirements |
| ------------------------- | --------- | ------------ |
| `getTopicId()`            | TopicId   | Optional     |
| `getAdminKey()`           | Key       | Optional     |
| `getSubmitKey()`          | Key       | Optional     |
| `getTopicMemo()`          | String    | Optional     |
| `getAutoRenewAccountId()` | AccountId | Disabled     |
| `getAutoRenewPeriod()`    | Duration  | Disabled     |

{% tabs %}
{% tab title="Java" %}
```java
 //Create a transaction to add a submit key
TopicUpdateTransaction transaction = new TopicUpdateTransaction()
    .setSubmitKey(submitKey);

//Get submit key
transaction.getSubmitKey()

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
 //Create a transaction to add a submit key
const transaction = new TopicUpdateTransaction()
    .setSubmitKey(submitKey);

//Get submit key
transaction.getSubmitKey()

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction
transaction := hedera.NewTopicUpdateTransaction()
    SetSubmitKey()

transaction := transaction.GetSubmitKey()

//v2.0.0
```
{% endtab %}
{% endtabs %}
