# Get a smart contract function

A query that calls a function of the given smart contract instance, giving it function parameters as its inputs. This is performed locally on the particular node that the client is communicating with. It cannot change the state of the contract instance (and so, cannot spend anything from the instance's cryptocurrency account). It will not have a consensus timestamp. It cannot generate a record or a receipt. The response will contain the output returned by the function call. This is useful for calling getter functions, which purely read the state and don't change it. It is faster and cheaper than a normal call because it is purely local to a single node.

Unlike a contract execution transaction, the node will consume the entire amount of provided gas in determining the fee for this query.

<table><thead><tr><th width="255">Field</th><th>Description</th></tr></thead><tbody><tr><td><strong>Contract ID</strong></td><td>The smart contract instance whose function was called.</td></tr><tr><td><strong>Contract Call Result</strong></td><td>The result returned by the function.</td></tr><tr><td><strong>Error Message</strong></td><td>The message in case there was an error during smart contract execution.</td></tr><tr><td><strong>Bloom</strong></td><td>The bloom filter for record.</td></tr><tr><td><strong>Gas Used</strong></td><td>The units of gas used to execute the contract.</td></tr><tr><td><strong>Log Info</strong></td><td>The log info for events returned by the function.</td></tr><tr><td><strong>EVM Address</strong></td><td>The new contract's 20-byte EVM address. Only populated after release 0.23, where each created contract will have its own record. (This is an important point--the field is not repeated because there will be a separate * child record for each created contract).</td></tr><tr><td><strong>Gas</strong></td><td>The amount of gas available for the call, aka the gasLimit.</td></tr><tr><td><strong>Amount</strong></td><td>Number of tinybars sent (the function must be payable if this is nonzero).</td></tr><tr><td><strong>Function Parameters</strong></td><td>The function to call and the parameters to pass to the function.</td></tr><tr><td><strong>Sender Account ID</strong></td><td>The account that is the "sender." If not present, it is the <code>accountId</code> from the transactionId.</td></tr><tr><td><strong>Signer Nonce</strong></td><td>The signer nonce field specifies what the value of the signer account nonce is post transaction execution. This value can be null.</td></tr></tbody></table>

**Query Signing Requirements**

- The client operator account's private key (fee payer) is required to sign this query

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

### Methods

<table><thead><tr><th width="270">Method</th><th width="138">Type</th><th width="212">Description</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setContractId(<contractId>)</code></td><td><a href="../specialized-types.md#contractid">ContractId</a></td><td>Sets the contract instance to call, in the format used in transactions (x.z.y).</td><td>Required</td></tr><tr><td><code>setGas(<gas>)</code></td><td>int64</td><td>Sets the amount of gas to use for the call.</td><td>Required</td></tr><tr><td><code>setFunction(<name>)</code></td><td>String</td><td>Sets the function name to call. The function will be called with no parameters.</td><td>Required</td></tr><tr><td><code>setFunction(<name, params>)</code></td><td>String,<br>bytes</td><td>Sets the function to call, and the parameters to pass to the function.</td><td>Optional</td></tr><tr><td><p><code>setSenderAccountId(<accountId)</code></p><p><br></p></td><td><a href="../specialized-types.md#accountid">AccountId</a></td><td>The account that is the "sender." If not present it is the accountId from the transactionId. Typically a different value than specified in the transactionId requires a valid signature over either the Hedera transaction or foreign transaction data.</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

```java
//Contract call query
ContractCallQuery query = new ContractCallQuery()
    .setContractId(contractId)
    .setGas(600)
    .setFunction("greet"); 

//Sign with the client operator private key to pay for the query and submit the query to a Hedera network
ContractFunctionResult contractCallResult = query.execute(client);

// Get the function value
String message = contractCallResult.getString(0);
System.out.println("contract message: " + message);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Contract call query
const query = new ContractCallQuery()
    .setContractId(contractId)
    .setGas(600)
    .setFunction("greet");

//Sign with the client operator private key to pay for the query and submit the query to a Hedera network
const contractCallResult = await query.execute(client);

// Get the function value
const message = contractCallResult.getString(0);
console.log("contract message: " + message);
```

{% endtab %}

{% tab title="Go" %}

```java
//Contract call query
query := hedera.NewContractCallQuery().
		SetContractID(contractID).
		SetGas(600).
		SetFunction("greet", nil)

//Sign with the client operator private key to pay for the query and submit the query to a Hedera network
contractFunctionResult, err := query.Execute(client)

if err != nil {
		panic(err)
}

//Print the query results to the console
println(contractFunctionResult)
//v2.0.0
```

{% endtab %}
{% endtabs %}

**Sample Output:**

```
ContractFunctionResult{
     contractId=0.0.104984
     rawResult=000000000000000000000000000000000000000000000000000000000000002
        0000000000000000000000000000000000000000000000000000000000000000d48656c
        6c6f2c20776f726c642100000000000000000000000000000000000000, 
     bloom=, 
     gasUsed=581, 
     errorMessage=null, 
     logs=[],
     signerNonce=null,
}
```

- Reference: [HIP-844](https://hips.hedera.com/hip/hip-844)
