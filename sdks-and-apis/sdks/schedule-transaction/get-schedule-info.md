# Get schedule info

`ScheduleInfoQuery` is a consensus node query that returns information about the current state of a schedule transaction on a Hedera network.

**Schedule Info Response**

| Field                          | Description                                                                                                                         |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| **Schedule ID**                | The ID of the schedule transaction.                                                                                                 |
| **Creator Account ID**         | The Hedera account that created the schedule transaction in x.y.z format.                                                           |
| **Payer Account ID**           | The Hedera account paying for the execution of the scheduled transaction in x.y.z format.                                           |
| **Scheduled Transaction Body** | The transaction body of the transaction that is being scheduled by the schedule transaction.                                        |
| **Signatories**                | The public keys that have signed the transaction.                                                                                   |
| **Admin Key**                  | The key that can delete the schedule transaction, if set                                                                            |
| **Expiration Time**            | The date and time the schedule transaction will expire                                                                              |
| **Executed Time**              | The time the schedule transaction was executed. If the schedule transaction has not executed this field will be left null.          |
| **Deletion Time**              | The consensus time the schedule transaction was deleted. If the schedule transaction was not deleted, this field will be left null. |
| **Memo**                       | Publicly visible information about the Schedule entity, up to 100 bytes. No guarantee of uniqueness.                                |

**Query Signing Requirements**

* The transaction fee payer account key is required to sign

### Methods

<table><thead><tr><th width="420.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setScheduleId(&#x3C;scheduleId>)</code></td><td>ScheduleId</td><td>Required</td></tr><tr><td><code>&#x3C;ScheduleInfo>.scheduleId</code></td><td>ScheduleId</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.scheduledTransactionId</code></td><td>TransactionId</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.creatorAccountId</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.payerAccountId</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.adminKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.signatories</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.deletedAt</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.expirationAt</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.memo</code></td><td>String</td><td>Optional</td></tr><tr><td><code>&#x3C;ScheduleInfo>.waitForExpiry</code></td><td>boolean</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the query
ScheduleInfoQuery query = new ScheduleInfoQuery()
     .setScheduleId(scheduleId);

//Sign with the client operator private key and submit the query request to a node in a Hedera network
ScheduleInfo info = query.execute(client);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the query
const query = new ScheduleInfoQuery()
     .setScheduleId(scheduleId);

//Sign with the client operator private key and submit the query request to a node in a Hedera network
const info = await query.execute(client);
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the query
query := hedera.NewScheduleInfoQuery().
		SetScheduleID(scheduleId)

//Sign with the client operator private key and submit to a Hedera network
scheduleInfo, err := query.Execute(client)

if err != nil {
		panic(err)
}
```
{% endtab %}
{% endtabs %}
