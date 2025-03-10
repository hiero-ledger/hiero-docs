# Get topic info

Topic info returns the following values for a topic. Queries do not change the state of the topic or require network consensus. The information is returned from a single node processing the query.

**Topic Info Response:**

| **Field**              | **Description**                                                                                                                                                                                                                                                                                           |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Topic ID**           | The ID of the topic                                                                                                                                                                                                                                                                                       |
| **Admin Key**          | Access control for update/delete of the topic. Null if there is no key.                                                                                                                                                                                                                                   |
| **Submit Key**         | Access control for ConsensusService.submitMessage. Null if there is no key.                                                                                                                                                                                                                               |
| **Sequence Number**    | Current sequence number (starting at 1 for the first submitMessage) of messages on the topic.                                                                                                                                                                                                             |
| **Running Hash**       | SHA-384 running hash                                                                                                                                                                                                                                                                                      |
| **Expiration Time**    | Effective consensus timestamp at (and after) which submitMessage calls will no longer succeed on the topic and the topic will expire and be marked as deleted.                                                                                                                                            |
| **Topic Memo**         | Short publicly visible memo about the topic. No guarantee of uniqueness.                                                                                                                                                                                                                                  |
| **Auto Renew Period**  | The lifetime of the topic and the amount of time to extend the topic's lifetime by                                                                                                                                                                                                                        |
| **Auto Renew Account** | Null if there is no autoRenewAccount.                                                                                                                                                                                                                                                                     |
| **Ledger ID**          | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198).                                                                                                                                                                                                         |
| **Fee Schedule Key**   | The key that is authorized to modify the fee structure for submitting messages to this topic. If present, this key must be used to update the topic’s fee settings. If absent, the topic’s fee structure cannot be changed.                                                                               |
| **Fee Exempt Key**     | A list of accounts that do not have to pay a fee when submitting messages to the topic. If this field is present, it means certain accounts are allowed to submit messages for free. If absent, no exemptions exist.                                                                                      |
| **Fee Schedule**       | The current fee structure that applies to message submissions for this topic. It specifies the amount charged per message, which can be denominated in HBAR or a chosen fungible token. If this field is present, the topic enforces fees for message submissions; if absent, message submission is free. |

**Query Signing Requirements**

* The client operator private key is required to sign the query request

**Query Fees**

* Please see the transaction and query [fees](broken-reference) table for the base transaction fee
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

//Retrieve additional HIP-991 fields
Key feeScheduleKey = info.getFeeScheduleKey();
List<CustomFee> customFees = info.getCustomFees();
List<Key> feeExemptKeys = info.getFeeExemptKeyList();

//Print the account key to the console
System.out.println(info);
System.out.println("Fee Schedule Key: " + feeScheduleKey);
System.out.println("Custom Fees: " + customFees);
System.out.println("Fee Exempt Key List: " + feeExemptKeys);

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

//Retrieve additional HIP-991 fields
const feeScheduleKey = info.feeScheduleKey;
const customFees = info.customFees;
const feeExemptKeys = info.feeExemptKeyList;

//Print the account key to the console
console.log(info);
console.log("Fee Schedule Key:", feeScheduleKey);
console.log("Custom Fees:", customFees);
console.log("Fee Exempt Key List:", feeExemptKeys);

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

//Retrieve additional HIP-991 fields
feeScheduleKey := info.GetFeeScheduleKey()
customFees := info.GetCustomFees()
feeExemptKeys := info.GetFeeExemptKeyList()

//Print the account key to the console
println(info)
fmt.Println("Fee Schedule Key:", feeScheduleKey)
fmt.Println("Custom Fees:", customFees)
fmt.Println("Fee Exempt Key List:", feeExemptKeys)

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
