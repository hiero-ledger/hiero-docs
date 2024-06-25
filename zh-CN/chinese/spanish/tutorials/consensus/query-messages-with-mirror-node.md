# Query Messages with Mirror Node

## Summary

In the first tutorial, "Submit Your First Message," you have learned how to submit a message to a topic.

In this tutorial, you will learn how to query the Hedera Mirror Node API to retrieve and filter messages.

***

## Prerequisites

We recommend that you complete the "Submit Your First Message" tutorial [here](submit-your-first-message.md) to get a basic understanding of the Hedera Consensus Service. **This example does not build upon the previous examples.**

***

## Table of Contents

1. [Create Topic and Submit Messages](query-messages-with-mirror-node.md#1.-create-a-topic-and-submit-three-messages)
2. [Query Hedera Mirror Node API](query-messages-with-mirror-node.md#2.-query-the-hedera-mirror-node-api)
3. [Retrieve Message](query-messages-with-mirror-node.md#3.-retrieve-a-specific-message-by-sequence-number)
4. [Advanced Filtering](query-messages-with-mirror-node.md#4.-advanced-filtering-methods-for-hcs-messages)

***

## 1. Create a topic and submit three messages

For this tutorial, create a new topic and submit three messages to this topic on testnet. You will use the retrieved topic ID to query for messages via the Hedera Mirror Node API.

Copy and execute the following code. Make sure to write down your `topic ID`. The topic ID will be in `0.0.topicId` format (ex: `0.0.1234`).

{% tabs %}
{% tab title="Java" %}

```java
// Create a new topic
TransactionResponse txResponse = new TopicCreateTransaction()
    .setSubmitKey(myPrivateKey.getPublicKey())
    .execute(client);

// Get the receipt
TransactionReceipt receipt = txResponse.getReceipt(client);
        
// Get the topic ID
TopicId topicId = receipt.topicId;

// Log the topic ID
System.out.println("Your topic ID is: " +topicId);

// Submit messages
TransactionResponse submitMessage1 = new TopicMessageSubmitTransaction()
    .setTopicId(topicId)
    .setMessage("Message 1")
    .execute(client);

TransactionResponse submitMessage2 = new TopicMessageSubmitTransaction()
    .setTopicId(topicId)
    .setMessage("Message 2")
    .execute(client);

TransactionResponse submitMessage3 = new TopicMessageSubmitTransaction()
    .setTopicId(topicId)
    .setMessage("Message 3")
    .execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Create a new public topic
let txResponse = await new TopicCreateTransaction().execute(client);

// Grab the newly generated topic ID
let receipt = await txResponse.getReceipt(client);
let topicId = receipt.topicId;
console.log(`Your topic ID is: ${topicId}`);

// Submit messages
await new TopicMessageSubmitTransaction({
  topicId: topicId,
  message: "Message 1",
}).execute(client);

await new TopicMessageSubmitTransaction({
  topicId: topicId,
  message: "Message 2",
}).execute(client);

await new TopicMessageSubmitTransaction({
  topicId: topicId,
  message: "Message 3",
}).execute(client);
```

{% endtab %}

{% tab title="Go" %}

```go
// Create a new topic
transactionResponse, err := hedera.NewTopicCreateTransaction().
	SetSubmitKey(myPrivateKey.PublicKey()).
	Execute(client)

if err != nil {
	println(err.Error(), ": error creating topic")
	return
}

// Get the topic create transaction receipt
transactionReceipt, err := transactionResponse.GetReceipt(client)

if err != nil {
	println(err.Error(), ": error getting topic create receipt")
	return
}

// Get the topic ID from the transaction receipt
topicID := *transactionReceipt.TopicID

// Log the topic ID to the console
fmt.Printf("Your topic ID is: %v\n", topicID)

// Submit messages
submitMessage1, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Message 1")).
	SetTopicID(topicID).
	Execute(client)

if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}

submitMessage2, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Message 2")).
	SetTopicID(topicID).
	Execute(client)

if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}

submitMessage3, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Message 3")).
	SetTopicID(topicID).
	Execute(client)

if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}
```

{% endtab %}
{% endtabs %}

The output in your console should look like this after executing the above setup code:

```
Your topic ID is: 0.0.<4603900>
```

Next, let's query the mirror node to retrieve data.

***

## 2. Query the Hedera Mirror Node API

Now all three messages have been submitted to your topic ID on testnet, let's query the mirror node. Let's use the testnet endpoint for the Hedera Mirror Node API to query for all messages for your topic ID. Make sure to replace the topic ID with the topic ID you've written down and execute the request in your browser or tool of choice.

<pre><code><strong>// Replace <topicId>
</strong><strong>https://testnet.mirrornode.hedera.com/api/v1/topics/<topicID>/messages
</strong>
// Example
https://testnet.mirrornode.hedera.com/api/v1/topics/0.0.4603900/messages
</code></pre>

The result should look similar to the API result below, with three messages being returned. The actual message contents are base64 encoded. If you want to verify the message contents, you can use this [decoder website](https://www.base64decode.org/) or decode it using code yourself.

<details>

<summary><a href="https://emojipedia.org/check-mark-button/">✅ </a>API result</summary>

```json
{
  "links": { "next": null },
  "messages": [
    {
      "consensus_timestamp": "1683553059.977315003",
      "message": "TWVzc2FnZSAx",
      "payer_account_id": "0.0.2617920",
      "running_hash": "vKnW8bYSjhYtHtMNOJWrkfyv77kKc4EREDycKE2L37aU0SYlx+g6HB9ah7CTFSxl",
      "running_hash_version": 3,
      "sequence_number": 1,
      "topic_id": "0.0.4603900"
    },
    {
      "consensus_timestamp": "1683553060.092158003",
      "message": "TWVzc2FnZSAy",
      "payer_account_id": "0.0.2617920",
      "running_hash": "JM8LpmRwUVc+wiwzRHBgcCv8uJkbfan8BV2QPC0hBMgXA9KIvy8TwOoXsukgmeC+",
      "running_hash_version": 3,
      "sequence_number": 2,
      "topic_id": "0.0.4603900"
    },
    {
      "consensus_timestamp": "1683553060.328178003",
      "message": "TWVzc2FnZSAz",
      "payer_account_id": "0.0.2617920",
      "running_hash": "OL6EUxAayPsRiuhwflX5heRgIGEWmHcJU7bS2qBPeJNoOiMQmaY0H6G/pXJ7wGQz",
      "running_hash_version": 3,
      "sequence_number": 3,
      "topic_id": "0.0.4603900"
    }
  ]
}
```

</details>

***

## 3. Retrieve a specific message by sequence number

In this section, you'll learn how to query messages by a sequence number. Each message you submit to a topic receives a sequence number starting from 1.

If you take a look at the [REST API docs](https://docs.hedera.com/hedera/sdks-and-apis/rest-api#topics) for `Topics`, you'll find the first query `/api/v1/topics/{topicId}/messages`. If you expand this section, you'll find all query parameters.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-05-08 at 4.45.33 PM.png" alt=""><figcaption><p>Query parameters REST API</p></figcaption></figure>

Execute the below request to retrieve the message with sequence number 2.

```
// Replace <topicId>
https://testnet.mirrornode.hedera.com/api/v1/topics/<topicID>/messages?sequencenumber=2

// Example
https://testnet.mirrornode.hedera.com/api/v1/topics/0.0.4603900/messages?sequencenumber=2
```

<details>

<summary>✅ API result</summary>

Only message two is returned by the Hedera Mirror Node.

```json
{
  "links": { "next": null },
  "messages": [
    {
      "consensus_timestamp": "1683553060.092158003",
      "message": "TWVzc2FnZSAy",
      "payer_account_id": "0.0.2617920",
      "running_hash": "JM8LpmRwUVc+wiwzRHBgcCv8uJkbfan8BV2QPC0hBMgXA9KIvy8TwOoXsukgmeC+",
      "running_hash_version": 3,
      "sequence_number": 2,
      "topic_id": "0.0.4603900"
    }
  ]
}
```

</details>

***

## 4. Advanced filtering methods for HCS messages

This section explores advanced filtering methods using query modifiers. The [OpenAPI specification](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml) for the Hedera Mirror Node REST API shows all details for query parameters (e.g. `timestampQueryParam`).

Possible query modifiers are:

- Greater than (gt) / greater than or equal (gte)
- Lower than (lt) / lower than or equal (lte)
- Equal to (eq)
- Not equal to (ne)

You can use these modifiers for query parameters like `sequencenumber` and `timestamp` (consensus timestamp).

In this step, let's query all messages with a **sequence number greater than or equal to 2**. To do so, let's use the `gte` query parameter modifier and assign it the value 2, like this: `sequencenumber=gte:2`.

```
// Replace <topicId>
https://testnet.mirrornode.hedera.com/api/v1/topics/<topicID>/messages?sequencenumber=gte:2

// Example
https://testnet.mirrornode.hedera.com/api/v1/topics/0.0.4603900/messages?sequencenumber=gte:2
```

<details>

<summary>✅ API result</summary>

Only message two is returned by the Hedera Mirror Node.

```json
{
  "links": { "next": null },
  "messages": [
    {
      "consensus_timestamp": "1683553060.092158003",
      "message": "TWVzc2FnZSAy",
      "payer_account_id": "0.0.2617920",
      "running_hash": "JM8LpmRwUVc+wiwzRHBgcCv8uJkbfan8BV2QPC0hBMgXA9KIvy8TwOoXsukgmeC+",
      "running_hash_version": 3,
      "sequence_number": 2,
      "topic_id": "0.0.4603900"
    },
    {
      "consensus_timestamp": "1683553060.328178003",
      "message": "TWVzc2FnZSAz",
      "payer_account_id": "0.0.2617920",
      "running_hash": "OL6EUxAayPsRiuhwflX5heRgIGEWmHcJU7bS2qBPeJNoOiMQmaY0H6G/pXJ7wGQz",
      "running_hash_version": 3,
      "sequence_number": 3,
      "topic_id": "0.0.4603900"
    }
  ]
}
```

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr><tr><td align="center"><p>Editor: Simi, Sr. Software Manager</p><p><a href="https://github.com/SimiHunjan">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/shunjan">https://www.linkedin.com/in/shunjan</a></td></tr></tbody></table>
