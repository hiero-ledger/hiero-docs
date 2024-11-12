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

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

<table><thead><tr><th width="330.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTopicId(&#x3C;topicId>)</code></td><td>TopicId</td><td>Required</td></tr><tr><td><code>&#x3C;TopicInfo>.adminKey</code></td><td><a href="../keys/generate-a-new-key-pair.md">Key</a></td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.submitKey</code></td><td><a href="../keys/generate-a-new-key-pair.md">Key</a></td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.topicId</code></td><td><a href="../specialized-types.md#topicid">TopicId</a></td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.sequenceNumber</code></td><td>long</td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.runningHash</code></td><td>ByteString</td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.memo</code></td><td>String</td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.ledgerId</code></td><td>LedgerId</td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.expirationTime</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.autoRenewAccount</code></td><td><a href="../specialized-types.md#accountid">AccountId</a></td><td>Optional</td></tr><tr><td><code>&#x3C;TopicInfo>.autoRenewPeriod</code></td><td>Instant</td><td>Optional</td></tr></tbody></table>

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
