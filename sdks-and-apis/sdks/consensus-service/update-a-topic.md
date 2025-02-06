# Update a topic

A transaction that updates the properties of an existing topic. This includes the topic memo, admin key, submit key, auto-renew account, and auto-renew period.

#### Topic Properties

| Field                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Topic ID**           | Update the topic ID                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Admin Key**          | Set a new admin key that authorizes update topic and delete topic transactions.                                                                                                                                                                                                                                                                                                                                               |
| **Submit Key**         | Set a new submit key for a topic that authorizes sending messages to this topic.                                                                                                                                                                                                                                                                                                                                              |
| **Topic Memo**         | Set a new short publicly visible memo on the new topic and is stored with the topic. (100 bytes)                                                                                                                                                                                                                                                                                                                              |
| **Auto Renew Account** | Set a new auto-renew account ID for this topic. Currently, rent is not enforced for topics so auto-renew payments will not be made.                                                                                                                                                                                                                                                                                           |
| **Auto Renew Period**  | <p>Set a new auto-renew period for this topic. Currently, rent is not enforced for topics so auto-renew payments will not be made.<br><br><strong>NOTE:</strong> The minimum period of time is approximately 30 days (2592000 seconds) and the maximum period of time is approximately 92 days (8000001 seconds). Any other value outside of this range will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</p> |

**Transaction Signing Requirements**

* If an admin key is updated, the transaction must be signed by the pre-update admin key and post-update admin key.
* If the admin key was set during the creation of the topic, the admin key must sign the transaction to update any of the topic's properties.
* If no `adminKey` was defined during the creation of the topic, you can only extend the expirationTime.

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost.

#### Methods

<table><thead><tr><th width="417.3333333333333">Method</th><th>Type</th><th>Requirements</th></tr></thead><tbody><tr><td><code>setTopicId(&#x3C;topicId>)</code></td><td>TopicId</td><td>Required</td></tr><tr><td><code>setAdminKey(&#x3C;adminKey>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setSubmitKey(&#x3C;submitKey>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setExpirationTime(&#x3C;expirationTime>)</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>setTopicMemo(&#x3C;memo>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setAutoRenewAccountId(&#x3C;accountId>)</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>setAutoRenewPeriod(&#x3C;autoRenewPeriod>)</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code>clearAdminKey()</code></td><td></td><td>Optional</td></tr><tr><td><code>clearSubmitKey()</code></td><td></td><td>Optional</td></tr><tr><td><code>clearTopicMemo()</code></td><td></td><td>Optional</td></tr><tr><td><code>clearAutoRenewAccountId()</code></td><td></td><td>Optional</td></tr></tbody></table>

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
| `getTopicId()`            | TopicId   | Required     |
| `getAdminKey()`           | Key       | Optional     |
| `getSubmitKey()`          | Key       | Optional     |
| `getTopicMemo()`          | String    | Optional     |
| `getAutoRenewAccountId()` | AccountId | Required     |
| `getAutoRenewPeriod()`    | Duration  | Required     |

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
