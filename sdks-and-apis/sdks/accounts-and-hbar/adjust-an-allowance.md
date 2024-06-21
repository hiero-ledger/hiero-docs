# Delete an allowance

A transaction that deletes one or more non-fungible approved allowances from an owner's account. This operation will remove the allowances granted to one or more specific non-fungible token serial numbers. Each owner account listed as wiping an allowance must sign the transaction. HBAR and fungible token allowances can be removed by setting the amount to zero in `CryptoApproveAllowance`.

The total number of NFT serial number deletions within the transaction body cannot exceed 20.

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

* The transaction must be signed by the owner's account
* The transaction must be signed by the transaction fee-paying account if different than the owner's account
* If the owner's account and transaction fee-paying account are the same, only one signature is required

**Reference:** [HIP-336](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-336.md)

### Methods

| **Method**                                               | **Type**                                                                    | **Description**                                     |
| -------------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------- |
| `deleteAllTokenNftAllowances(<nftId>, <ownerAccountId>)` | <p>NFT ID,<br><a href="../specialized-types.md#accountid">AccountId</a></p> | Removes the NFT allowance from the spender account. |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
AccountAllowanceDeleteTransaction transaction = new AccountAllowanceDeleteTransaction()
    .deleteAllTokenNftAllowances(nftId , ownerAccountId);

//Sign the transaction with the owner account key  
TransactionResponse txResponse = transaction.freezeWith(client).sign(ownerAccountKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.12.0+
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new AccountAllowanceDeleteTransaction()
    .deleteAllTokenNftAllowances(nftId , ownerAccountId);

//Sign the transaction with the owner account key
const signTx = await transaction.sign(ownerAccountKey);

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());

//v2.13.0+
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction := hedera.NewAccountAllowanceDeleteTransaction().
     DeleteAllTokenNftAllowances(nftId , ownerAccountId)

if err != nil {
    panic(err)
}

//Sign the transaction with the owner account private key and submit to the network  
txResponse, err := transaction.Sign(ownerAccountKey).Execute(client)

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

println("The transaction consensus status is ", transactionStatus)

//v2.13.1+
```
{% endtab %}
{% endtabs %}
