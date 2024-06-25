# Wipe a token

Wipes the provided amount of fungible or non-fungible tokens from the specified Hedera account. This transaction does not delete tokens from the treasury account. This transaction must be signed by the token's Wipe Key. Wiping an account's tokens burns the tokens and decreases the total supply.

* If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.
* If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`
* If the provided token is not found, the transaction will resolve to `INVALID_TOKEN_ID`.
* If the provided token has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.
* If an Association between the provided token and the account is not found, the transaction will resolve to `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`.
* If Wipe Key is not present in the Token, the transaction results in `TOKEN_HAS_NO_WIPE_KEY`.
* If the provided account is the token's Treasury Account, the transaction results in `CANNOT_WIPE_TOKEN_TREASURY_ACCOUNT`
* On success, tokens are removed from the account and the total supply of the token is decreased by the wiped amount.
* The amount provided is in the lowest denomination possible.
  * Example: Token A has 2 decimals. In order to wipe 100 tokens from an account, one must provide an amount of 10000. In order to wipe 100.55 tokens, one must provide an amount of 10055.
* This transaction accepts zero-unit token wipe operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564))

**Transaction Signing Requirements:**

* Wipe key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

<table><thead><tr><th width="298">Method</th><th width="136">Type</th><th>Description</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTokenId(&#x3C;tokenId>)</code></td><td>TokenId</td><td>The ID of the fungible or non-fungible token to remove from the account.</td><td>Required</td></tr><tr><td><code>setAmount(&#x3C;amount>)</code></td><td>long</td><td>Applicable to tokens of type <code>FUNGIBLE_COMMON</code>.The amount of token to wipe from the specified account. The amount must be a positive non-zero number in the lowest denomination possible, not bigger than the token balance of the account.</td><td>Optional</td></tr><tr><td><code>setAccount(&#x3C;accountId>)</code></td><td>AccountId</td><td>The account the specified fungible or non-fungible token should be removed from.</td><td>Required</td></tr><tr><td><code>setSerials(&#x3C;serials>)</code></td><td>List&#x3C;long></td><td>Applicable to tokens of type <code>NON_FUNGIBLE_UNIQUE</code>.The list of NFTs to wipe.</td><td>Optional</td></tr><tr><td><code>addSerial(&#x3C;serial>)</code></td><td>long</td><td>Applicable to tokens of type <code>NON_FUNGIBLE_UNIQUE.</code>The NFT to wipe.</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Wipe 100 tokens from an account
TokenWipeTransaction transaction = new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100);

//Freeze the unsigned transaction, signing with the private key of the payer and the token's wipe key; submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(accountKey).sign(wipeKey).execute(client);

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
//Wipe 100 tokens from an account and freeze the unsigned transaction for manual signing
const transaction = await new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100)
    .freezeWith(client);

//Sign with the payer account private key, sign with the wipe private key of the token
const signTx = await (await transaction.sign(accountKey)).sign(wipeKey);    

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Obtain the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());
```
{% endtab %}

{% tab title="Go" %}
```go
//Wipe 100 tokens and freeze the unsigned transaction for manual signing
transaction, err = hedera.NewTokenBurnTransaction().
        SetAccountId(accountId).
        SetTokenID(tokenId).
        SetAmount(1000).
        FreezeWith(client)

if err != nil {
        panic(err)
}

//Sign with the payer account private key, sign with the wipe private key of the token
txResponse, err := transaction.Sign(accountKey).Sign(wipeKey).Execute(client)

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
