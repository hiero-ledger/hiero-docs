# Get account balance

A query that returns the account balance for the specified account. Requesting an account balance is currently free of charge. Queries do not change the state of the account or require network consensus. The information is returned from a single node processing the query.

In Services release 0.50, returning token balance from the consensus node was deprecated with HIP-367. This query returns token information by requesting the information from the Hedera Mirror Node APIs via   [/api/v1/accounts/{id}/tokens](https://mainnet-public.mirrornode.hedera.com/api/v1/docs/#/accounts/listTokenRelationshipByAccountId). Token symbol is not returned in the response.

**Query Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost.

**Query Signing Requirements**

* The client operator private key is required to sign the query request.

### Methods

<table><thead><tr><th width="322.3333333333333">Method</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>setAccountId(&#x3C;accountId>)</code></td><td>AccountID</td><td>The account ID to return the current balance for.</td></tr><tr><td><code>setContractId(&#x3C;contractId>)</code></td><td>ContractID</td><td>The contract ID to return the current balance for.</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the account balance query
AccountBalanceQuery query = new AccountBalanceQuery()
     .setAccountId(accountId);

//Sign with client operator private key and submit the query to a Hedera network
AccountBalance accountBalance = query.execute(client);

//Print the balance of hbars
System.out.println("The hbar account balance for this account is " +accountBalance.hbars);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the account balance query
const query = new AccountBalanceQuery()
     .setAccountId(accountId);

//Submit the query to a Hedera network
const accountBalance = await query.execute(client);

//Print the balance of hbars
console.log("The hbar account balance for this account is " +accountBalance.hbars);

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the account balance query
query := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Sign with client operator private key and submit the query to a Hedera network
accountBalance, err := query.Execute(client)
if err != nil {
    panic(err)
}

//Print the balance of hbars
fmt.Println("The hbar account balance for this account is ", accountBalance.Hbars.String())
//v2.0.0
```
{% endtab %}
{% endtabs %}
