# Unfreeze an account

Unfreezes transfers of the specified token for the account. The transaction must be signed by the token's Freeze Key.

* If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.
* If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
* If no Freeze Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_FREEZE\_KEY.
* Once executed the Account is marked as Unfrozen and will be able to receive or send tokens. The operation is idempotent.

**Transaction Signing Requirements**

* Freeze key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

### Methods

| Method                      | Type      | Description                            | Requirement |
| --------------------------- | --------- | -------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`     | TokenId   | The token for this account to unfreeze | Required    |
| `setAccountId(<accountId>)` | AccountId | The account to unfreeze                | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Unfreeze an account
TokenUnfreezeTransaction transaction = new TokenUnfreezeTransaction()
     .setAccountId(accountId)
     .setTokenId(tokenId);

//Freeze the unsigned transaction, sign with the sender freeze private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(freezeKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt8.status;

System.out.print("The transaction consensus status is " +transactionStatus);

//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Unfreeze an account and freeze the unsigned transaction for signing
const transaction = await new TokenUnfreezeTransaction()
     .setAccountId(accountId)
     .setTokenId(tokenId)
     .freezeWith(client);

//Sign with the freeze private key of the token 
const signTx = await transaction.sign(freezeKey);

//Submit the transaction to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Obtain the transaction consensus status
const transactionStatus = receipt8.status;

console.log("The transaction consensus status is " +transactionStatus.toString());

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
//Unfreeze an account and freeze the unsigned transaction for signing
transaction, err = hedera.NewUnTokenFreezeTransaction().
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
