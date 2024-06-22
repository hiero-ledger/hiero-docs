# Get schedule info

A query that returns information about the current state of a schedule transaction on a Hedera network.

**Schedule Info Response**

| Field                          | Description                                                                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Schedule ID**                | The ID of the schedule transaction                                                                                                                                  |
| **Creator Account ID**         | The Hedera account that created the schedule transaction in x.y.z format                                                            |
| **Payer Account ID**           | The Hedera account paying for the execution of the schedule transaction in x.y.z format                                             |
| **Scheduled Transaction Body** | The scheduled transaction (inner transaction).                                                                                   |
| **Signatories**                | The signatories that have provided signatures so far for the schedule transaction                                                                                   |
| **Admin Key**                  | The Key which is able to delete the schedule transaction if set                                                                                                     |
| **Expiration Time**            | The date and time the schedule transaction will expire                                                                                                              |
| **Executed Time**              | The time the schedule transaction was executed. If the schedule transaction has not executed this field will be left null.          |
| **Deletion Time**              | The consensus time the schedule transaction was deleted. If the schedule transaction was not deleted, this field will be left null. |
| **Memo**                       | Publicly visible information about the Schedule entity, up to 100 bytes. No guarantee of uniqueness.                                |

**Query Signing Requirements**

- The client operator key is required to sign the query request

### Methods

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
