# Dissociate tokens from an account

Dissociates the provided Hedera account from the provided Hedera tokens. This transaction must be signed by the provided account's key. Once the association is removed, no token-related operation can be performed to that account. AccountBalanceQuery and AccountInfoQuery will not return anything related to the dissociated token.

* If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.
* If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.
* If any of the provided tokens is not found, the transaction will resolve to `INVALID_TOKEN_REF`.
* If an association between the provided account and any of the tokens does not exist, the transaction will resolve to `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`.
* If the provided account has a nonzero balance with any of the provided tokens, the transaction will resolve to `TRANSACTION_REQUIRES_ZERO_TOKEN_BALANCES`.
* On success, associations between the provided account and tokens are removed.

{% hint style="info" %}
The account is required to have a zero balance of the token you wish to dissociate. If a token balance is present, you will receive a `TRANSACTION_REQUIRES_ZERO_TOKEN_BALANCES` error.
{% endhint %}

**Transaction Signing Requirements**

* The key of the account the token is being dissociated with
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

| Method                      | Type      | Description                                            | Requirement |
| --------------------------- | --------- | ------------------------------------------------------ | ----------- |
| `setTokenIds(<tokenId>)`    | TokenId   | The tokens to be dissociated with the provided account | Required    |
| `setAccountId(<accountId>)` | AccountId | The account to be dissociated with the provided tokens | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Dissociate a token from an account
TokenDissociateTransaction transaction = new TokenDissociateTransaction()
    .setAccountId(accountId)
    .setTokenIds(tokenId);

//Freeze the unsigned transaction, sign with the private key of the account that is being dissociated from a token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(accountKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is: " +transactionStatus);
//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Dissociate a token from an account and freeze the unsigned transaction for signing
const transaction = await new TokenDissociateTransaction()
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

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```go
//Dissociate the token from an account and freeze the unsigned transaction for signing
transaction, err := hedera.NewTokenDissociateTransaction().
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
