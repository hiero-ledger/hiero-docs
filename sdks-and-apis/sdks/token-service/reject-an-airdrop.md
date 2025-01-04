# Reject a token

## TokenRejectTransaction

The `TokenRejectTransaction` allows users to reject and return unwanted airdrops to the treasury account without incurring custom fees. This transaction type supports rejecting the full balance of fungible tokens or individual NFT serial numbers with support for up to 10 token rejections in a single transaction. Rejection is _not_ supported if the token has been frozen or paused. Note that the transaction does not dissociate the account from the token. To both reject and dissociate, use [`TokenRejectFlow`](https://docs.hedera.com/hedera/sdks-and-apis/sdks/token-service/reject-an-airdrop#tokenrejectflow) (see below).

{% hint style="info" %}
* Each rejected token is transferred to the treasury, and the transfer is recorded in the `token_transfer_list` in the transaction record.
* The `receiver_sig_required` setting is ignored on the treasury account when handling a TokenReject. Rejection will always proceed without custom fees.
* Custom fees are waived during rejection, preventing the payer from incurring any extra costs.
* The transaction does not decrement `used_auto_associations`, as it does not remove the association with the token. This field tracks the total number of auto-associations made, not the current number of token types associated with the account.
{% endhint %}

**Transaction Signing Requirements**

* The receiver/owner account key.
* The transaction fee payer account key.

**Transaction Fees**

* Please see the transaction and query [fees](https://docs.hedera.com/hedera/networks/mainnet/fees#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

<table><thead><tr><th width="284">SDK Method</th><th width="152">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>setOwnerId(accountId)</code></td><td><a href="https://docs.hedera.com/hedera/sdks-and-apis/sdks/specialized-types#accountid">AccountId</a></td><td>Sets the ID of the account holding/owning the tokens.</td></tr><tr><td><code>addTokenId(&#x3C;tokenId>)</code></td><td>TokenId</td><td>Adds a token to be rejected.</td></tr><tr><td><code>setTokenIds(tokenIds)</code></td><td>List&#x3C;TokenId></td><td>Adds a list of one or more token IDs to be rejected.</td></tr><tr><td><code>addNftId(&#x3C;nftId>)</code></td><td>TokenId</td><td>Adds a NFT ID to be rejected.</td></tr><tr><td><code>setNftIds(&#x3C;nftIds>)</code></td><td>List&#x3C;NftId></td><td>Adds a list of one or more NFT IDs to be rejected.</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
// Create the token reject transaction for fungible token
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

// v2.50.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create the token reject transaction for fungible token
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

// v2.51.0
```
{% endtab %}

{% tab title="Go" %}
```go
// Create the token reject transaction for fungible token
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

// v2.51.0
```
{% endtab %}
{% endtabs %}

## TokenRejectFlow

The `TokenRejectFlow` simplifies the process of rejecting unwanted tokens and dissociating from them in a single `execute()` call. This flow combines both the `TokenRejectTransaction`, which transfers the tokens back to the treasury, and the `TokenDissociateTransaction`, which dissociates the account from the token. It streamlines the process by executing both actions in one single transaction.

**Transaction Signing Requirements**

* The receiver/owner account key.
* The transaction fee payer account key.

**Transaction Fees**

* Please see the transaction and query [fees](https://docs.hedera.com/hedera/networks/mainnet/fees#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

<table><thead><tr><th width="285">Method</th><th width="143">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>setOwnerId(accountId)</code></td><td><a href="https://docs.hedera.com/hedera/sdks-and-apis/sdks/specialized-types#accountid">AccountId</a></td><td>Sets the ID of the account holding/owning the tokens. This is the receiver account that will be rejecting the token.</td></tr><tr><td><code>addTokenId(&#x3C;tokenId>)</code></td><td>TokenId</td><td>Adds a token ID to be rejected and dissociated.</td></tr><tr><td><code>setTokenIds(tokenIds)</code></td><td>List&#x3C;TokenId></td><td>Sets a list of one or more token IDs to be rejected and dissociated.</td></tr><tr><td><code>addNftId(&#x3C;NftId>)</code></td><td>NftId</td><td>Adds a NFT ID to be rejected and dissociated.</td></tr><tr><td><code>setNftIds(&#x3C;nftIds>)</code></td><td>List&#x3C;NftId></td><td>Sets a list of one or more NFT IDs to be rejected and dissociated.</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
// Create the token reject transaction for fungible token
TokenRejectFlow transaction = new TokenRejectFlow()
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

// v2.50.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create the token reject transaction for fungible token
const transaction = await new TokenRejectFlow()
    .setOwnerId(accountId)
    .addTokenId(tokenId)
    .freezeWith(client);

// Sign with the account key and submit the transaction to the Hedera network
const txResponse = await transaction.sign(accountIdKey).execute(client);

// Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

// Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " + transactionStatus.toString());
```
{% endtab %}

{% tab title="Go" %}
```go
// Create the token reject transaction for fungible token
transaction := hedera.NewTokenRejectFlow().
        SetAccountID(accountId).
        AddTokenID(tokenId).
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

// v2.50.0
```
{% endtab %}
{% endtabs %}
