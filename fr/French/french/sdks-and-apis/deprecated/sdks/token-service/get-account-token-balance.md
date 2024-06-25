# Get account token balance

To get the balance of tokens for an account, you can submit an account balance query. The account balance query will return the tokens the account holds in a list format.

**Query Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

{% tabs %}
{% tab title="V1" %}

| Constructor               | Description                              |
| ------------------------- | ---------------------------------------- |
| `new TokenBalanceQuery()` | Initializes the TokenBalanceQuery object |

```java
new TokenBalanceQuery()
```

| Method                      | Type      | Requirement |
| --------------------------- | --------- | ----------- |
| `setAccountId(<accountId>)` | AccountId | Required    |

{% code title="Java" %}

```java
Map<TokenId, Long> tokenBalance = new TokenBalanceQuery()
    .setAccountId(accountId)
    .execute(client);

System.out.println("The token balance(s) for this account: " +tokenBalance);
//Version: 1.2.2
```

{% endcode %}

{% code title="JavaScript " %}

```javascript
 const tokenBalance = await new TokenBalanceQuery()
    .setAccountId(accountId)
    .execute(client);

console.log("The token balance(s) for this account: " +tokenBalance.get("<tokenId>"));
//Version 1.4.2
```

{% endcode %}
{% endtab %}
{% endtabs %}
