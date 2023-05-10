# Delete a token

Deleting a token marks a token as deleted, though it will remain in the ledger. The operation must be signed by the specified Admin Key of the Token. If the Admin Key is not set, the Transaction will result in TOKEN\_IS\_IMMUTABlE. Once deleted update, mint, burn, wipe, freeze, unfreeze, grant KYC, revoke KYC and token transfer transactions will resolve to TOKEN\_WAS\_DELETED.

**NFTs**

You cannot delete a specific NFT. You can delete the class of the NFT specified by the token ID after you have burned all associated NFTs associated with the token class

#### Transaction Signing Requirements

* Admin key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

### Methods

| Method                  | Type                   | Description                   | Requirement |
| ----------------------- | ---------------------- | ----------------------------- | ----------- |
| `setTokenId(<tokenId>)` | [TokenId](token-id.md) | The ID of the token to delete | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
TokenDeleteTransaction transaction = new TokenDeleteTransaction()
     .setTokenId(tokenId);

//Freeze the unsigned transaction, sign with the admin private key of the account, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and freeze the unsigned transaction for manual signing
const transaction = await new TokenDeleteTransaction()
     .setTokenId(tokenId)
     .freezeWith(client);

//Sign with the admin private key of the token 
const signTx = await transaction.sign(adminKey);

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);
    
//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and freeze the unsigned transaction for manual signing
transaction, err = hedera.NewTokenDeleteTransaction().
		SetTokenID(tokenId).
		FreezeWith(client)

if err != nil {
		panic(err)
}

//Sign with the admin private key of the account, submit the transaction to a Hedera network
txResponse, err := transaction.Sign(adminKey).Execute(client)

if err != nil {
		panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)

if err != nil {
		panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)

//v2.1.0
```
{% endtab %}
{% endtabs %}
