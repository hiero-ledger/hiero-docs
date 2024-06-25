# Get topic messages

Subscribe to a topic ID's messages from a mirror node. You will receive all messages for the specified topic or within the defined start and end time.

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

### Methods

| Method                       | Type               | Description                                         | Requirement |
| ---------------------------- | ------------------ | --------------------------------------------------- | ----------- |
| `setTopicId(<topicId>)`      | TopicId            | The topic ID to subscribe to                        | Required    |
| `setStartTime(<startTime>)`  | Instant            | The time to start subscribing to a topic's messages | Optional    |
| `setEndTime(<endTime>)`      | Instant            | The time to stop subscribing to a topic's messages  | Optional    |
| `setLimit(<limit>)`          | long               | The number of messages to return                    | Optional    |
| `subscribe(<client, onNext)` | SubscriptionHandle | Client, Consumer\<TopicMessage>                    | Required    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
new TopicMessageQuery()
    .setTopicId(newTopicId)
    .subscribe(client, topicMessage -> {
        System.out.println("at " + topicMessage.consensusTimestamp + " ( seq = " + topicMessage.sequenceNumber + " ) received topic message of " + topicMessage.contents.length + " bytes");
    });

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the query
new TopicMessageQuery()
        .setTopicId(topicId)
        .setStartTime(0)
        .subscribe(
            client,
            (message) => console.log(Buffer.from(message.contents, "utf8").toString())
        );
//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the query
_, err = hedera.NewTopicMessageQuery().
    SetTopicID(topicID).
    Subscribe(client, func(message hedera.TopicMessage) {
        if string(message.Contents) == content {
        wait = false
    }
})

if err != nil {
    panic(err)
}

//v2.0.0
```

{% endtab %}
{% endtabs %}
