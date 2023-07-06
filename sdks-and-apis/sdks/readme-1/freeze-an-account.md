# Freeze an account

Freezes transfers of the specified token for the account. The transaction must be signed by the token's Freeze Key.

* If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID. If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
* If no Freeze Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_FREEZE\_KEY.
* Once executed the Account is marked as Frozen and will not be able to receive or send tokens unless unfrozen.
* The operation is idempotent

**Transaction Signing Requirements**

* Freeze key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

### Methods

| Method                      | Type      | Description                             | Requirement |
| --------------------------- | --------- | --------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`     | TokenId   | The token for this account to be frozen | Required    |
| `setAccountId(<accountId>)` | AccountId | The account to be frozen                | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Freeze an account from transferring a token
TokenFreezeTransaction transaction = new TokenFreezeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId);

//Freeze the unsigned transaction, sign with the sender freeze private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(freezeKey).execute(client);
    
//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);
    
//Obtain the transaction consensus status
Status transactionStatus = receipt.status;
    
System.out.print("The transaction consensus status is " +transactionStatus);
//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Freeze an account from transferring a token
const transaction = await new TokenFreezeTransaction()
     .setAccountId(accountId)
     .setTokenId(tokenId)
     .freezeWith(client);

//Sign with the freeze key of the token 
const signTx = await transaction.sign(freezeKey);

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);
    
//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
//Freeze an account from transferring a token
transaction, err = hedera.NewTokenFreezeTransaction().
	  SetAccountID(accountId).
		SetTokenID(tokenId).
		FreezeWith(client)

if err != nil {
		panic(err)
}

//Sign with the freeze private key of the token, submit the transaction to a Hedera network
txResponse, err := transaction.Sign(freezeKey).Execute(client)
		
if err != nil {
		panic(err)
}

//Get the receipt of the transaction
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
