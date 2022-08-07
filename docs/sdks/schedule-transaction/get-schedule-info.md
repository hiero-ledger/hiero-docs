# Get schedule info

A query that returns information about the current state of a schedule transaction on a Hedera network.

**Schedule Info Response**

| Field                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Schedule ID**                | The ID of the schedule transaction                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Creator Account ID**         | The Hedera account that created the schedule transaction in x.y.z format                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Payer Account ID**           | The Hedera account paying for the execution of the schedule transaction in x.y.z format                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **Scheduled Transaction Body** | The scheduled transaction (inner transaction).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Signatories**                | The signatories that have provided signatures so far for the schedule transaction                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Admin Key**                  | The Key which is able to delete the schedule transaction if set                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **Expiration Time**            | The date and time the schedule transaction will expire                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Executed Time**              | The time the schedule transaction was executed. If the schedule transaction has not executed this field will be left null.                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Deletion Time**              | The consensus time the schedule transaction was deleted. If the schedule transaction was not deleted, this field will be left null.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Memo**                       | Publicly visible information about the Schedule entity, up to 100 bytes. No guarantee of uniqueness.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Wait for Expiry**            | <p><em><mark style="background-color:yellow;">Coming soon...</mark></em><br><em><mark style="background-color:yellow;"></mark></em>When set to true, the transaction will be evaluated for execution at expiration_time instead of when all required signatures are received. When set to false, the transaction will execute immediately after sufficient signatures are received to sign the contained transaction. During the initial ScheduleCreate transaction or via ScheduleSign transactions. <a href="https://hips.hedera.com/hip/hip-423">Reference HIP-423</a></p> |

**Query Signing Requirements**

* The client operator key is required to sign the query request

| Constructor               | Description                              |
| ------------------------- | ---------------------------------------- |
| `new ScheduleInfoQuery()` | Initializes the ScheduleInfoQuery object |

```java
new ScheduleInfoQuery()
```

### Methods

{% tabs %}
{% tab title="V2" %}
| Method                                  | Type          | Requirement |
| --------------------------------------- | ------------- | ----------- |
| `setScheduleId(<scheduleId>)`           | ScheduleId    | Required    |
| `<ScheduleInfo>.scheduleId`             | ScheduleId    | Optional    |
| `<ScheduleInfo>.scheduledTransactionId` | TransactionId | Optional    |
| `<ScheduleInfo>.creatorAccountId`       | AccountId     | Optional    |
| `<ScheduleInfo>.payerAccountId`         | AccountId     | Optional    |
| `<ScheduleInfo>.adminKey`               | Key           | Optional    |
| `<ScheduleInfo>.signatories`            | Key           | Optional    |
| `<ScheduleInfo>.deletedAt`              | Instant       | Optional    |
| `<ScheduleInfo>.expirationAt`           | Instant       | Optional    |
| `<ScheduleInfo>.memo`                   | String        | Optional    |
| `<ScheduleInfo>.waitForExpiry`          | boolean       | Optional    |

{% code title="Java" %}
```java
//Create the query
ScheduleInfoQuery query = new ScheduleInfoQuery()
     .setScheduleId(scheduleId);

//Sign with the client operator private key and submit the query request to a node in a Hedera network
ScheduleInfo info = query.execute(client);
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the query
const query = new ScheduleInfoQuery()
     .setScheduleId(scheduleId);

//Sign with the client operator private key and submit the query request to a node in a Hedera network
const info = await query.execute(client);
```
{% endcode %}

{% code title="Go" %}
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
{% endcode %}
{% endtab %}
{% endtabs %}
