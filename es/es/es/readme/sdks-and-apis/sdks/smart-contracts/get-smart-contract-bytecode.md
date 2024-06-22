# Get smart contract bytecode

A query that returns the bytecode for a smart contract instance\*\*.\*\* Anyone can request the byte code of a smart contract instance on the network. Queries do not change the state of the smart contract or require network consensus. The information is returned from a single node processing the query.

**Query Signing Requirements**

- The client operator account's private key (fee payer) is required to sign this query

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

### Methods

| Method                        | Type       | Description                                       | Requirements |
| ----------------------------- | ---------- | ------------------------------------------------- | ------------ |
| `setContractId(<contractId>)` | ContractId | The ID of the contract to return the bytecode for | Required     |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
ContractByteCodeQuery query = new ContractByteCodeQuery()
    .setContractId(contractId);

//Sign with the client operator private key and submit to a Hedera network
ByteString bytecode = query.execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the query
const query = new ContractByteCodeQuery()
    .setContractId(contractId);

//Sign with the client operator private key and submit to a Hedera network
const bytecode = await query.execute(client);
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the query
query := hedera.NewContractByteCodeQuery().
    SetContractID(contractId)

//Sign with the client operator private key to pay for the query and submit the query to a Hedera network
bytecode, err := query.Execute(client)

if err != nil {
        panic(err)
}
```

{% endtab %}
{% endtabs %}

## Get query values

| Method                        | Type       | Description                            | Requirements |
| ----------------------------- | ---------- | -------------------------------------- | ------------ |
| `getContractId(<contractId>)` | ContractId | Get the contract ID on the transaction | Required     |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
ContractByteCodeQuery query = new ContractByteCodeQuery()
    .setContractId(newContractId);

//Get the contract ID
query.getContractId()

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```java
//Create the query
const query = new ContractByteCodeQuery()
    .setContractId(newContractId);

//Get the contract ID
query.getContractId()

//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the query
query := hedera.NewContractByteCodeQuery().
    SetContractID(contractId)

query.GetContractID()

//v2.0.0
```

{% endtab %}
{% endtabs %}
