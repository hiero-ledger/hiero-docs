# Call a smart contract function

The transaction that calls a function of the given smart contract instance, giving it `functionParameters` as its inputs. The call can use at maximum the given amount of gas – the paying account will not be charged for any unspent gas.

If this function results in data being stored, an amount of gas is calculated that reflects this storage burden.

The amount of gas used, as well as other attributes of the transaction, e.g. size, number of signatures to be verified, determine the fee for the transaction – which is charged to the paying account.

**Transaction Signing Requirements**

* The key of the transaction fee paying account

**Transaction Fees**

* Please see the transaction and query [fees](../../../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                         | Description                                     |
| ----------------------------------- | ----------------------------------------------- |
| `new ContractExecuteTransaction ()` | Initializes a ContractExecuteTransaction object |

```java
new ContractExecuteTransaction()
```

{% tabs %}
{% tab title="V2" %}
| Method                        | Type                               | Description                                                            | Requirement |
| ----------------------------- | ---------------------------------- | ---------------------------------------------------------------------- | ----------- |
| `setContractId(<contractId>)` | ContractID                         | The ID of the contract to call.                                        | Required    |
| `setFunction(<name, params>)` | String, ContractFunctionParameters | Which function to call and the parameters to pass to the function.     | Required    |
| `setGas(<gas>)`               | long                               | The maximum amount of gas to use for the call.                         | Required    |
| `setPayableAmount(<amount>)`  | Hbar                               | Number of hbars sent (the function must be payable if this is nonzero) | Optional    |

{% code title="Java" %}
```java
//Create the transaction
ContractCreateTransaction transaction = new ContractExecuteTransaction()
     .setContractId(newContractId)
     .setGas(100_000_000)
     .setFunction("set_message", new ContractFunctionParameters()
           .addString("hello from hedera again!"))

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new ContractExecuteTransaction()
     .setContractId(newContractId)
     .setGas(100_000_000)
     .setFunction("set_message", new ContractFunctionParameters()
           .addString("hello from hedera again!"))

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```go
//Create the transaction
transaction, err := hedera.NewContractExecuteTransaction().
     SetContractID(newContractID).
     SetGas(100000000).
     SetFunction("setMessage", contractFunctionParams)

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
txResponse, err := transaction.Execute(client)
if err != nil {
	panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
	panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status %v\n", transactionStatus)

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}

\\
