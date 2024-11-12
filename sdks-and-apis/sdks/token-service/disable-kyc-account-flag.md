# Disable KYC account flag

Revokes the KYC flag to the Hedera account for the given Hedera token. This transaction must be signed by the token's KYC Key. If this key is not set, you can submit a TokenUpdateTransaction to provide the token with this key.

* If the provided account is not found, the transaction will be resolved to `INVALID_ACCOUNT_ID`.
* If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.
* If the provided token is not found, the transaction will resolve to `INVALID_TOKEN_ID`.
* If the provided token has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.
* If an Association between the provided token and account is not found, the transaction will resolve to T`OKEN_NOT_ASSOCIATED_TO_ACCOUNT`.
* If no KYC Key is defined, the transaction will resolve to `TOKEN_HAS_NO_KYC_KEY`.
* Once executed, the Account is marked as KYC Revoked

**Transaction Signing Requirements**

* KYC key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

### Methods

| Method                         | Type      | Description                                                                 | Requirement |
| ------------------------------ | --------- | --------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`        | TokenId   | The token ID that is associated with the account to remove the KYC flag for | Required    |
| `setAccountId(<setAccountId>)` | AccountId | The account ID that is associated with the account to remove the KYC flag   | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Remove the KYC flag from an account
TokenRevokeKycTransaction transaction = new TokenRevokeKycTransaction()
    .setTokenId(tokenId)
    .setAccountId(accountId);

//Freeze the unsigned transaction, sign with the kyc private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(kycKey).execute(client);
    
//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);
    
//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//Version: 2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Remove the KYC flag on account and freeze the transaction for signing
const transaction = await new TokenRevokeKycTransaction()
     .setAccountId(accountId)
     .setTokenId(tokenId)
     .freezeWith(client);

//Sign with the kyc private key of the token
const signTx = await transaction.sign(kycKey);

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
//Remove the KYC flag from an account and freeze the transaction for signing
transaction, err = hedera.NewTokenRevokeKycTransaction().
		SetTokenID(tokenId).
		SetAccountID(accountId).
		FreezeWith(client)

if err != nil {
		panic(err)
}

//Sign with the kyc private key of the token, submit the transaction to a Hedera network
txResponse, err := transaction.Sign(kycKey).Execute(client)
		
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
