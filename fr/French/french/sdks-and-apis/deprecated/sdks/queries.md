# Queries

Queries are requests that do not require network consensus. Queries are processed only by the single node the request is sent to. Below is a list of network queries by service.

| Cryptocurrency Accounts                                                    | Consensus                                                              | Tokens                                                                     | File Service                                                      | Smart Contracts                                                                                                                      | Schedule Service                                                          |
| -------------------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| [AccountBalanceQuery](../../sdks/accounts-and-hbar/get-account-balance.md) | [TopicInfoQuery](../../sdks/consensus-service/get-topic-info.md)       | [TokenBalanceQuery](../../sdks/token-service/get-account-token-balance.md) | [FileContentsQuery](../../sdks/file-service/get-file-contents.md) | [ContractCallQuery](../../sdks/smart-contracts/get-smart-contract-bytecode.md)                                                       | [ScheduleInfoQuery](../../sdks/schedule-transaction/get-schedule-info.md) |
| [AccountInfoQuery](../../sdks/accounts-and-hbar/get-account-info.md)       | [TopicMessageQuery](../../sdks/consensus-service/get-topic-message.md) | [TokenInfoQuery](../../sdks/token-service/get-token-info.md)               | [FileInfoQuery](../../sdks/file-service/get-file-info.md)         | [ContractByteCodeQuery](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/sdks/broken-reference/README.md) |                                                                           |

## Get Query Cost

A query that returns the cost of a query prior to submitting the query to network node for processing. If the cost of the query greater than the default max query payment (1 hbar) you can use `setMaxQueryPayment(<hbar>)` to change the default.

{% tabs %}
{% tab title="V1" %}

| Method                                      | Type                                                  | Description                                      |
| ------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------ |
| `getCost(<client>)`                         | Client                                                | Get the cost of the query in long representation |
| `getCostAsync(<client, withCost, onError>)` | Client, Consumer\<long>, Consumer\<HederaThrowable> | Get the cost of a query asynchronously           |

{% code title="Java" %}

```java
//Create the query request
AccountBalanceQuery query = new AccountBalanceQuery()
     .setAccountId(accountId);

//Get the cost of the query
long queryCost = query.getCost(client);

System.out.println("The account balance query cost is " +queryCost);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the query request
const query = new AccountBalanceQuery()
     .setAccountId(accountId);

//Get the cost of the query
const queryCost = await query.getCost(client);

console.log("The account balance query cost is " +queryCost);
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
