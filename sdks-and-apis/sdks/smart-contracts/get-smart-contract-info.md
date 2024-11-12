# Get smart contract info

A query that returns the current state of a smart contract instance, including its balance. Queries do not change the state of the smart contract or require network consensus. The information is returned from a single node processing the query.

In Services release 0.50, Returning token balance information from the consensus node was deprecated with HIP-367. This query now returns token information by requesting the information from the Hedera Mirror Node APIs via [/api/v1/accounts/{id}/tokens](https://mainnet-public.mirrornode.hedera.com/api/v1/docs/#/accounts/listTokenRelationshipByAccountId). Token symbol is not returned in the response.

**Smart Contract Info Response**

| Field                   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Contract ID**         | ID of the contract instance, in the format used in transactions.                                                                                                                                                                                                                                                                                                                                                                                                |
| **Account ID**          | ID of the cryptocurrency account owned by the contract instance.                                                                                                                                                                                                                                                                                                                                                                                                |
| **Contract Account ID** | ID of both the contract instance and the cryptocurrency account owned by the contract.                                                                                                                                                                                                                                                                                                                                                                          |
| **Admin Key**           | The state of the instance and its fields can be modified arbitrarily if this key signs a transaction to modify it. If this is null, then such modifications are not possible, and there is no administrator that can override the normal operation of this smart contract instance. Note that if it is created with no admin keys, then there is no administrator to authorize changing the admin keys, so there can never be any admin keys for that instance. |
| **Expiration Time**     | The current time at which this contract instance (and its account) is set to expire.                                                                                                                                                                                                                                                                                                                                                                            |
| **Auto Renew Period**   | The expiration time will extend every this many seconds. If there are insufficient funds, then it extends as long as possible. If the account is empty when it expires, then it is deleted.                                                                                                                                                                                                                                                                     |
| **Storage**             | Number of bytes of storage being used by this instance (which affects the cost to extend the expiration time).                                                                                                                                                                                                                                                                                                                                                  |
| **Contract Memo**       | The memo associated with the contract (max 100 bytes).                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Balance**             | The current balance of the contract.                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Deleted**             | Whether the contract has been deleted.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Ledger ID**           | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198).                                                                                                                                                                                                                                                                                                                                                               |
| **Staking Info**        | <p>The staking metadata for this contract. This includes the staking period start, the pending reward, the account ID or the node ID, whether or not rewards were declined, and how many hbars are staked to this contract account, if any. <a href="https://hips.hedera.com/hip/hip-406">See HIP-406</a>.<br>Live on: <code>previewnet/testnet</code></p>                                                                                                      |
| **Token Relationships** | The metadata of the tokens associated to the contract.                                                                                                                                                                                                                                                                                                                                                                                                          |

**Query Signing Requirements**

* The client operator account's private key (fee payer) is required to sign this query

**Query Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee&#x20;

### Methods

<table><thead><tr><th width="346.3333333333333">Method</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>setContractId(&#x3C;contractId>)</code></td><td>ContractId</td><td>The ID of the smart contract to return the token for</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the query
ContractInfoQuery query = new ContractInfoQuery()
    .setContractId(contractId);

//Sign the query with the client operator private key and submit to a Hedera network
ContractInfo info = query.execute(client);

System.out.print(info);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the query
const query = new ContractInfoQuery()
    .setContractId(contractId);

//Sign the query with the client operator private key and submit to a Hedera network
const info = await query.execute(client);

console.log(info);
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the query
query := hedera.NewContractInfoQuery().
    SetContractID(contractId)

//Sign with the client operator private key to pay for the query and submit the query to a Hedera network
info, err := query.Execute(client)

if err != nil {
		panic(err)
}

//Print the account key to the console
println(info
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Sample Output:" %}
```
ContractInfo{
     contractId=0.0.104966, 
     accountId=0.0.104966, 
     contractAccountId=0000000000000000000000000000000000019a06,    
     adminKey=302a300506032b6570032100fcd7cce3eef78f76a538c5573ce8f00258 
          386741e03adc17c66075bf659b865d, 
     expirationTime=2021-02-10T22:27:15Z,    
     autoRenewPeriod=PT2160H, 
     storage=523, 
     contractMemo=, 
     balance=0 tℏ
}
```
{% endtab %}
{% endtabs %}
