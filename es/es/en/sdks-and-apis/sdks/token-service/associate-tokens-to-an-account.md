# Associate tokens to an account

Associates the provided Hedera account with the provided Hedera token(s). Hedera accounts must be associated with a fungible or non-fungible token first before you can transfer tokens to that account. When you transfer a custom fungible or non-fungible token to the alias account ID, the token association step is skipped and the account will automatically be associated with the token upon creation. In the case of NON\_FUNGIBLE Type, once an account is associated, it can hold any number of NFTs (serial numbers) of that token type. The Hedera account that is associated with a token is required to sign the transaction.

- If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.
- If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.
- If any of the provided tokens is not found, the transaction will resolve to `INVALID_TOKEN_REF`.
- If any of the provided tokens has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.
- If an association between the provided account and any of the tokens already exists, the transaction will resolve to `TOKEN_ALREADY_ASSOCIATED_TO_ACCOUNT`.
- If the provided account's associations count exceeds the constraint of maximum token associations per account, the transaction will resolve to `TOKENS_PER_ACCOUNT_LIMIT_EXCEEDED`.
- On success, associations between the provided account and tokens are made and the account is ready to interact with the tokens.

{% hint style="info" %}
There is currently no limit on the number of token IDs that can be associated with an account (reference [HIP-367](https://hips.hedera.com/hip/hip-367)). Still, you can see `TOKENS_PER_ACCOUNT_LIMIT_EXCEEDED` responses for _pre-HIP-367_ transactions.
{% endhint %}

**Transaction Signing Requirements**

- The key of the account the token is being associated to
- Transaction fee payer account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

| Method                      | Type             | Description                                           | Requirement |
| --------------------------- | ---------------- | ----------------------------------------------------- | ----------- |
| `setAccountId(<accountId>)` | AccountId        | The account to be associated with the provided tokens | Required    |
| `setTokenIds(<tokens>)`     | List \<TokenId> | The tokens to be associated with the provided account | Required    |

{% tabs %}
{% tab title="Java" %}

```java
//Associate a token to an account
TokenAssociateTransaction transaction = new TokenAssociateTransaction()
        .setAccountId(accountId)
        .setTokenIds(Collections.singletonList(tokenId));

//Freeze the unsigned transaction, sign with the private key of the account that is being associated to a token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(accountKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status " +transactionStatus);
//v2.0.4
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Associate a token to an account and freeze the unsigned transaction for signing
const transaction = await new TokenAssociateTransaction()
     .setAccountId(accountId)
     .setTokenIds([tokenId])
     .freezeWith(client);

//Sign with the private key of the account that is being associated to a token 
const signTx = await transaction.sign(accountKey);

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
//Associate the token to an account and freeze the unsigned transaction for signing
transaction, err := hedera.NewTokenAssociateTransaction().
        SetAccountID(accountId).
        SetTokenIDs(tokenId).
        FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the private key of the account that is being associated to a token, submit the transaction to a Hedera network
txResponse, err = transaction.Sign(accountKey).Execute(client)

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
