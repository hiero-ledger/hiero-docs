# Delete an allowance

A transaction called by the token owner to delete allowances for NFTs only. In order to delete an existing hbar or fungible token allowance the `AccountAllowanceApproveTransaction` API should be used with an `amount` of 0.

The total number of NFT serial number deletions contained within the transaction body cannot exceed 20.

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

* The transaction must be signed by the owner's account
* The transaction must be signed by the transaction fee paying account if different than the owner's account
* If the owner's account and transaction fee paying account are the same only one signature is required

**Reference:** [HIP-336](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-336.md)

| **Constructor**                           | **Description**                                          |
| ----------------------------------------- | -------------------------------------------------------- |
| `new AccountAllowanceDeleteTransaction()` | Initializes the AccountAllowanceDeleteTransaction object |

### Methods

| **Method**                                          | **Type**                                                                               | **Description**                                     |
| --------------------------------------------------- | -------------------------------------------------------------------------------------- | --------------------------------------------------- |
| `deleteAllNftAllowances(<nftId>, <ownerAccountId>)` | <p>NFT ID,<br><a href="../../../sdks/specialized-types.md#accountid">AccountId</a></p> | Removes the NFT allowance from the spender account. |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
AccountAllowanceAdjustTransaction transaction = new AccountAllowanceAdjustTransaction()
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
