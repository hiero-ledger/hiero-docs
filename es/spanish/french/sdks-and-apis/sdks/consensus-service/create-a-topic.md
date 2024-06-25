# Create a topic

A transaction that creates a new topic recognized by the Hedera network. The newly generated topic can be referenced by its `topicId`. The `topicId` is used to identify a unique topic to submit messages to. You can obtain the new topic ID by requesting the receipt of the transaction. All messages within a topic are sequenced with respect to one another and are provided a unique sequence number.

#### Private topic

You can also create a private topic where only authorized parties can submit messages to that topic. To create a private topic you would need to set the `submitKey` property of the transaction. The `submitKey` value is then shared with the authorized parties and is required to successfully submit messages to the private topic.

#### Topic Properties

| Field                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Admin Key**          | Access control for updateTopic/deleteTopic. If no adminKey is specified, anyone can increase the topic's expirationTime with updateTopic, but they cannot use deleteTopic. However, if an adminKey is specified, both updateTopic and deleteTopic can be used.                                                                                                                                                                                                                       |
| **Submit Key**         | Access control for submitMessage. No access control will be performed specified, allowing all message submissions.                                                                                                                                                                                                                                                                                                                                                                                   |
| **Topic Memo**         | Store the new topic with a short publicly visible memo. (100 bytes)                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **Auto Renew Account** | At the topic's expirationTime, the optional account can be used to extend the lifetime up to a maximum of the autoRenewPeriod or duration/amount that all funds on the account can extend (whichever is the smaller). Currently, rent is not enforced for topics so no auto-renew payments will be made.                                                                                                                                                                          |
| **Auto Renew Period**  | <p>The initial lifetime of the topic and the amount of time to attempt to extend the topic's lifetime by automatically at the topic's expirationTime. Currently, rent is not enforced for topics so auto-renew payments will not be made.<br><br><strong>NOTE:</strong> The minimum period of time is approximately 30 days (2592000 seconds) and the maximum period of time is approximately 92 days (8000001 seconds). Any other value outside of this range will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</p> |

**Transaction Signing Requirements:**

- If an admin key is specified, the admin key must sign the transaction.
- If an admin key is not specified, the topic is immutable.
- If an auto-renew account is specified, that account must also sign this transaction.

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

#### Methods

| Method                                  | Type      | Requirements |
| --------------------------------------- | --------- | ------------ |
| `setAdminKey(<adminKey>)`               | Key       | Optional     |
| `setSubmitKey(<submitKey>)`             | Key       | Optional     |
| `setTopicMemo(<memo>)`                  | String    | Optional     |
| `setAutoRenewAccountId(<accountId>)`    | AccountId | Optional     |
| `setAutoRenewPeriod(<autoRenewPeriod>)` | Duration  | Optional     |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
TopicCreateTransaction transaction = new TopicCreateTransaction();

//Sign with the client operator private key and submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the topic ID
TopicId newTopicId = receipt.topicId;

System.out.println("The new topic ID is " + newTopicId);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new TopicCreateTransaction();

//Sign with the client operator private key and submit the transaction to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the topic ID
const newTopicId = receipt.topicId;

console.log("The new topic ID is " + newTopicId);

//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the transaction
transaction := hedera.NewTopicCreateTransaction()

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

//Get the topic ID
newTopicID := *transactionReceipt.TopicID

fmt.Printf("The new topic ID is %v\n", newTopicID)

//v2.0.0
```

{% endtab %}
{% endtabs %}

## Get transaction values

| Method                      | Type      | Requirements |
| --------------------------- | --------- | ------------ |
| `getAdminKey(<adminKey>)`   | Key       | Optional     |
| `getSubmitKey(<submitKey>)` | Key       | Optional     |
| `getTopicMemo(<memo>)`      | String    | Optional     |
| `getAutoRenewAccountId()`   | AccountId | Required     |
| `getAutoRenewPeriod()`      | Duration  | Required     |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
TopicCreateTransaction transaction = new TopicCreateTransaction()
    .setAdminKey(adminKey);
    
//Get the admin key from the transaction    
Key getKey = transaction.getAdminKey();

//V2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new TopicCreateTransaction()
    .setAdminKey(adminKey);
    
//Get the admin key from the transaction    
const topicAdminKey = transaction.adminKey;
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the transaction
transaction := hedera.NewTopicCreateTransaction().
    SetAdminKey(adminKey)

getKey := transaction.GetAdminKey()

//V2.0.0
```

{% endtab %}
{% endtabs %}
