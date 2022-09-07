# Get account balance

A query that returns the account balance for the specified account. Requesting an account balance is currently free of charge. Queries do not change the state of the account or require network consensus. The information is returned from a single node processing the query.

**Query Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

**Query Signing Requirements**

* The client operator private key is required to sign the query request

| Constructor                 | Description                                |
| --------------------------- | ------------------------------------------ |
| `new AccountBalanceQuery()` | Initializes the AccountBalanceQuery object |

```java
new AccountBalanceQuery
```

### Methods

{% tabs %}
{% tab title="V2" %}
| Method                        | Type       | Description                                        |
| ----------------------------- | ---------- | -------------------------------------------------- |
| `setAccountId(<accountId>)`   | AccountID  | The account ID to return the current balance for.  |
| `setContractId(<contractId>)` | ContractID | The contract ID to return the current balance for. |

{% code title="Java" %}
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
{% endcode %}

{% code title="JavaScript" %}
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
{% endcode %}

{% code title="Go" %}
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
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method                      | Type      | Description                                       |
| --------------------------- | --------- | ------------------------------------------------- |
| `setAccountId(<accountId>)` | AccountID | The account ID to return the current balance for. |

{% code title="Java" %}
```java
//Create the query
AccountBalanceQuery query = new AccountBalanceQuery()
     .setAccountId(newAccountId);

//Sign with the client operator account private key and submit to a Hedera network
Hbar accountBalance = query.execute(client);

System.out.println(accountBalance);

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the query
const query = new AccountBalanceQuery()
     .setAccountId(newAccountId);

//Sign with the client operator account private key and submit to a Hedera network
Hbar accountBalance = await query.execute(client);

console.log(accountBalance);

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}
