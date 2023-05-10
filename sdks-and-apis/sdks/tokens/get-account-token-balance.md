# Get account token balance

To get the balance of tokens for an account, you can submit an account balance query. The account balance query will return the tokens the account holds in a list format.

**Query Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Method                      | Type      | Requirement |
| --------------------------- | --------- | ----------- |
| `setAccountId(<accountId>)` | AccountId | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the query
AccountBalanceQuery query = new AccountBalanceQuery()
    .setAccountId(accountId);

//Sign with the operator private key and submit to a Hedera network
AccountBalance tokenBalance = query.execute(client);

System.out.println("The token balance(s) for this account: " +tokenBalance.tokens);

//v2.0.9
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the query
const query = new AccountBalanceQuery()
    .setAccountId(accountId);

//Sign with the client operator private key and submit to a Hedera network
const tokenBalance = await query.execute(client);

console.log("The token balance(s) for this account: " +tokenBalance.tokens.toString());

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the query
query := hedera.NewAccountBalanceQuery().
	 SetAccountID(accountId)
	
//Sign with the client operator private key and submit to a Hedera network
tokenBalance, err := query.Execute(client)

if err != nil {
		panic(err)
	}

fmt.Printf("The token balance(s) for this account: %v\n", tokenBalance)

//v2.1.0
```
{% endtab %}
{% endtabs %}
