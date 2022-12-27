# Get smart contract bytecode

A query that returns the bytecode for a smart contract instance\*\*.\*\* Anyone can request the byte code of a smart contract instance on the network. Queries do not change the state of the smart contract or require network consensus. The information is returned from a single node processing the query.

**Query Signing Requirements**

* The client operator account's private key (fee payer) is required to sign this query

**Query Fees**

* Please see the transaction and query [fees](../../../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Constructor                   | Description                                |
| ----------------------------- | ------------------------------------------ |
| `new ContractByteCodeQuery()` | Initializes a ContractByteCodeQuery object |

```java
new ContractByteCodeQuery()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                        | Type       | Description                                       | Requirements |
| ----------------------------- | ---------- | ------------------------------------------------- | ------------ |
| `setContractId(<contractId>)` | ContractId | The ID of the contract to return the bytecode for | Required     |

{% code title="Java" %}
```java
ContractBytecodeQuery query = new ContractBytecodeQuery()
    .setContractId(newContractId);

byte[] byteCode = query.execute(client);
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const query = new ContractBytecodeQuery()
    .setContractId(newContractId);

const byteCode = await query.execute(client);
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get query values

{% tabs %}
{% tab title="V2" %}
| Method                        | Type       | Description                            | Requirements |
| ----------------------------- | ---------- | -------------------------------------- | ------------ |
| `getContractId(<contractId>)` | ContractId | Get the contract ID on the transaction | Required     |

{% code title="Java" %}
```java
//Create the query
ContractByteCodeQuery query = new ContractByteCodeQuery()
    .setContractId(newContractId);

//Get the contract ID
query.getContractId()

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```java
//Create the query
const query = new ContractByteCodeQuery()
    .setContractId(newContractId);

//Get the contract ID
query.getContractId()

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the query
query := hedera.NewContractByteCodeQuery().
    SetContractID(contractId)

query.GetContractID()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}
