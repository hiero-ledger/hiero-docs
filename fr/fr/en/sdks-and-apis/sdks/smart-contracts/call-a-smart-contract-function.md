# Call a smart contract function

The transaction calls a function of the given smart contract instance, giving it `functionParameters` as its input. The call can use at maximum the given amount of gas – the paying account will not be charged for any unspent gas.

If this function results in data being stored, an amount of gas is calculated that reflects this storage burden.

The amount of gas used, as well as other attributes of the transaction, e.g. size, and number of signatures to be verified, determine the fee for the transaction – which is charged to the paying account.

**Transaction Signing Requirements**

- The key of the transaction fee-paying account

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

<table><thead><tr><th width="275">Method</th><th width="143">Type</th><th width="203">Description</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setContractId(<contractId>)</code></td><td>ContractID</td><td>The ID of the contract to call.</td><td>Required</td></tr><tr><td><code>setFunction(<name, params>)</code></td><td>String, ContractFunctionParameters</td><td>Which function to call and the parameters to pass to the function.</td><td>Required</td></tr><tr><td><code>setGas(<gas>)</code></td><td>long</td><td>The maximum amount of gas to use for the call.</td><td>Required</td></tr><tr><td><code>setPayableAmount(<amount>)</code></td><td>Hbar</td><td>Number of HBARs sent (the function must be payable if this is nonzero)</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

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

{% endtab %}

{% tab title="JavaScript" %}

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

{% endtab %}

{% tab title="Go" %}

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

{% endtab %}
{% endtabs %}
