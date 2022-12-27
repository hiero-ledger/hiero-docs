# Get topic info

Topic info returns the following values for a topic. Queries do not change the state of the topic or require network consensus. The information is returned from a single node processing the query.

**Topic Info Response:**

| **Field**              | **Description**                                                                                                                                                |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Topic ID**           | The ID of the topic                                                                                                                                            |
| **Admin Key**          | Access control for update/delete of the topic. Null if there is no key.                                                                                        |
| **Submit Key**         | Access control for ConsensusService.submitMessage. Null if there is no key.                                                                                    |
| **Sequence Number**    | Current sequence number (starting at 1 for the first submitMessage) of messages on the topic.                                                                  |
| **Running Hash**       | SHA-384 running hash                                                                                                                                           |
| **Expiration Time**    | Effective consensus timestamp at (and after) which submitMessage calls will no longer succeed on the topic and the topic will expire and be marked as deleted. |
| **Topic Memo**         | Short publicly visible memo about the topic. No guarantee of uniqueness.                                                                                       |
| **Auto Renew Period**  | The lifetime of the topic and the amount of time to extend the topic's lifetime by                                                                             |
| **Auto Renew Account** | Null if there is no autoRenewAccount.                                                                                                                          |
| **Ledger ID**          | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198).                                                              |

**Query Signing Requirements**

* The client operator private key is required to sign the query request

**Query Fees**

* Please see the transaction and query [fees](broken-reference) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Constructor            | Description                           |
| ---------------------- | ------------------------------------- |
| `new TopicInfoQuery()` | Initializes the TopicInfoQuery object |

```java
new TopicInfoQuery()
```

| Method                         | Type                                                           | Requirement |
| ------------------------------ | -------------------------------------------------------------- | ----------- |
| `setTopicId(<topicId>)`        | TopicId                                                        | Required    |
| `<TopicInfo>.adminKey`         | [Key](../keys/generate-a-new-key-pair.md)                      | Optional    |
| `<TopicInfo>.submitKey`        | [Key](../keys/generate-a-new-key-pair.md)                      | Optional    |
| `<TopicInfo>.topicId`          | [TopicId](../deprecated/sdks/specialized-types.md#topicid)     | Optional    |
| `<TopicInfo>.sequenceNumber`   | long                                                           | Optional    |
| `<TopicInfo>.runningHash`      | ByteString                                                     | Optional    |
| `<TopicInfo>.memo`             | String                                                         | Optional    |
| `<TopicInfo>.ledgerId`         | LedgerId                                                       | Optional    |
| `<TopicInfo>.expirationTime`   | Instant                                                        | Optional    |
| `<TopicInfo>.autoRenewAccount` | [AccountId](../deprecated/sdks/specialized-types.md#accountid) | Optional    |
| `<TopicInfo>.autoRenewPeriod`  | Instant                                                        | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the account info query
TopicInfoQuery query = new TopicInfoQuery()
    .setTopicId(newTopicId);

//Submit the query to a Hedera network
TopicInfo info = query.execute(client);

//Print the account key to the console
System.out.println(info);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the account info query
const query = new TopicInfoQuery()
    .setTopicId(newTopicId);

//Submit the query to a Hedera network
const info = await query.execute(client);

//Print the account key to the console
console.log(info);

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the account info query
query, err := hedera.NewTopicInfoQuery().
		SetTopicID(topicID)

//Submit the query to a Hedera network
info, err := query.Execute(client)
if err != nil {
		panic(err)
}

//Print the account key to the console
println(info)

//v2.0.0
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Sample Output:" %}
```
TopicInfo{
     topicId=0.0.102736, 
     topicMemo=, 
     runningHash=[ 
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,            0. 0, 0,0,0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,0, 0, 0, 0, 0, 0, 0
     ], 
     sequenceNumber=0, 
     expirationTime=2021-02-09T03:17:07.258292001Z, 
     adminKey=null, 
     submitKey=null, 
     autoRenewPeriod=PT2160H, 
     autoRenewAccountId=null
}
```
{% endtab %}
{% endtabs %}
