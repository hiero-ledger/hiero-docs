# Reject an airdrop

The `TokenRejectTransaction` allows users to reject and return unwanted airdrops to the treasury account without incurring custom fees. This transaction type supports rejecting the full balance of fungible tokens or individual NFT serial numbers with support for up to 10 token rejections in a single transaction. Rejection is _not_ supported if the token has been frozen or paused. Note that the transaction does not dissociate an account from the token. For dissociation, one must perform a `TokenDissociateTransaction`.

**Important Notes:**

* When a token is rejected, `used_auto_associations` is not decremented. This field tracks the total number of auto-associations made, not the current number of token types associated with the account.
* The `receiver_sig_required` setting is ignored on the treasury account when handling a TokenReject. Rejection will always proceed without custom fees.
* Token rejection is not supported if the token is frozen or paused.

#### Transaction Signing Requirements

* The key of the account rejecting the tokens
* The transaction fee payer account key

#### Transaction Fees

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

## Methods

<table><thead><tr><th width="268">Method</th><th width="154">Type</th><th width="320">Description</th></tr></thead><tbody><tr><td><code>setOwnerId(accountId)</code></td><td><a href="../specialized-types.md#accountid">AccountId</a></td><td>An account holding the tokens to be rejected.</td></tr><tr><td><code>addTokenId(&#x3C;tokenId>)</code></td><td>List&#x3C;TokenId></td><td>A list of one or more token IDs to be rejected.</td></tr><tr><td><code>addNftId(&#x3C;nftId>)</code></td><td>List&#x3C;NftId></td><td>A single specific serialized non-fungible/unique token.</td></tr></tbody></table>



{% tabs %}
{% tab title="Java" %}
```java
// Create the token reject transaction
TokenRejectTransaction transaction = new TokenRejectTransaction()
    .setOwnerId(accountId)
    .addTokenId(tokenId)
    .freezeWith(client);
  
// Sign with the account Id key and submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.sign(accountIdKey).execute(client);

// Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

// Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create the token reject transaction
const transaction = new TokenRejectTransaction()
      .setOwnerId(accountId)
      .addTokenId(tokenId)
      .freezeWith(client);
      
// Sign with the account Id key and submit the transaction to a Hedera network
const txResponse = await transaction.sign(accountIdKey).execute(client);

// Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);
    
// Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());
```
{% endtab %}

{% tab title="Go" %}
```go
// Create the token reject transaction
transaction := hedera.NewTokenRejectTransaction().
        SetAccountID(accountId).
        AddTokenIDs(tokenIds).
        FreezeWith(client)
        
if err != nil {
		panic(err)
}

// Sign with the account Id key and submit the transaction to a Hedera network
txResponse, err := transaction.
		Sign(accountIdKey).
		Execute(client)

if err != nil {
		panic(err)
}

// Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)

if err != nil {
		panic(err)
}

// Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)
```
{% endtab %}
{% endtabs %}
