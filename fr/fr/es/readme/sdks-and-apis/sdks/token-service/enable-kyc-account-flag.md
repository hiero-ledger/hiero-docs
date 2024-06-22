# Enable KYC account flag

Grants KYC to the Hedera accounts for the given Hedera token. This transaction must be signed by the token's KYC Key.

- If the provided account is not found, the transaction will be resolved to `INVALID_ACCOUNT_ID`.
- If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.
- If the provided token is not found, the transaction will resolve to `INVALID_TOKEN_ID`.
- If the provided token has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.
- If an Association between the provided token and the account is not found, the transaction will resolve to `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`.
- If no KYC Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_KYC\_KEY.
- Once executed, the Account is marked as KYC Granted.

**Transaction Signing Requirements**

- KYC key
- Transaction fee payer account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost.

### Methods

| Method                      | Type      | Description                                   | Required |
| --------------------------- | --------- | --------------------------------------------- | -------- |
| `setTokenId(<tokenId>)`     | TokenId   | The token for this account to have passed KYC | Required |
| `setAccountId(<accountId>)` | AccountId | The account for this token to have passed KYC | Required |

{% tabs %}
{% tab title="Java" %}

```java
//Enable KYC flag on account
TokenGrantKycTransaction transaction = new TokenGrantKycTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId);

//Freeze the unsigned transaction, sign with the kyc private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(kycKey).execute(client);
    
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
//Enable KYC flag on account and freeze the transaction for manual signing
const transaction = await new TokenGrantKycTransaction()
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
//Enable KYC flag on account and freeze the transaction for manual signing
transaction, err = hedera.NewTokenGrantKycTransaction().
		SetAccountID(accountId).
		SetTokenID(tokenId).
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
