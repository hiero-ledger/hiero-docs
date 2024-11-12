# Airdrop a token

The `TokenAirdropTransaction` allows users to transfer tokens to multiple accounts, handling both fungible tokens and NFTs. Unlike standard token transfers, if the receiver's account lacks available auto-association slots and is not already associated with the token, the transfer intent is captured as a pending transfer rather than failing. If the receiver has available auto-association slots, one is used, and the transaction payer covers the association fee and a fee for a full auto-renewal period. If resulting in a pending airdrop, the airdrop is stored in the network state until claimed by the intended receiver or canceled by the airdrop sender. It also differs from standard `CryptoTransfer` transactions by not supporting HBAR transfers.

{% hint style="info" %}
Restrictions on transfer lists and aggregation in `TokenAirdrop` are consistent with those applied to `CryptoTransfer`. Specifically:

* Supports only token transfers (no HBAR transfers).
* A maximum of 10 balance adjustments in the `tokenTransferList` and up to 20 combined balance adjustments or NFT ownership changes per transaction, including custom fees.
* When transferring tokens with custom fees, only two levels of fee nesting are allowed.
* The sender is responsible for paying all custom fees and the first auto-renewal period's rent for any automatic association.
  * An exception exists for treasury accounts for which custom fees are not assessed, and therefore, fallback fees are not applicable.
{% endhint %}

#### Transaction Signing Requirements

* The sender account key.
* The transaction fee payer account key if it differs from the sender.

#### Transaction Fees

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

<table><thead><tr><th width="273">Method</th><th width="140">Type</th><th width="320">Description</th></tr></thead><tbody><tr><td><code>addTokenTransfer(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;amount>)</code></td><td>TokenId, AccountId, int64</td><td>Add the from (sender) and to (receiver) account to transfer tokens. You will need to use this method twice. One will specify the the account sending the airdrop and the second will specify the account receiving the airdrop. The sending account must sign the transaction. The sender and recipient values must net zero.</td></tr><tr><td><code>addNftTransfer(&#x3C;nftId>, &#x3C;sender>, &#x3C;receiver>)</code></td><td>NftId, AccountId, AccountId</td><td>Adds an NFT transfer to the transaction, specifying the NFT ID, the sender's account, and the receiver's account.</td></tr><tr><td><code>addTokenTransferWithDecimals(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;amount>, &#x3C;decimals>)</code></td><td>TokenId, AccountId, int64, uint32</td><td>Adds a token transfer to the transaction with specified decimal precision, detailing the token ID, account, amount, and decimal places.</td></tr><tr><td><code>addApprovedTokenTransfer(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;amount>)</code></td><td>TokenId, AccountId, int64</td><td>The owner account ID and token the spender is authorized to transfer from. The debiting account is the owner account. <em>Applicable to allowance transfers only.</em></td></tr><tr><td><code>addApprovedNftTransfer(&#x3C;nftId>, &#x3C;sender>, &#x3C;receiver>)</code></td><td>NftId, AccountId, AccountId</td><td>The NFT ID the spender is authorized to transfer. The sender is the owner account and receiver is the receiving account. <em>Applicable to allowance transfers only.</em></td></tr><tr><td><code>addApprovedTokenTransferWithDecimals(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;amount>, &#x3C;decimals>)</code></td><td>TokenId, AccountId, int64, uint32</td><td>Adds an approved token transfer with decimal precision, specifying the owner account ID, token ID, and sender's account. <em>Applicable to allowance transfers only.</em></td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
{% code overflow="wrap" %}
```java
// Create the token airdrop transaction for fungible token
TokenAirdropTransaction transaction = new TokenAirdropTransaction()
        .addTokenTransfer(tokenId, accountId1, -1)
        .addTokenTransfer(tokenId, accountId2, 1)
        .addTokenTransfer(tokenId, accountId1, -1)
        .addTokenTransfer(tokenId, accountId3, 1)
        .freezeWith(client);

// Sign the transaction with the sender account key and submit it to the Hedera network.
TransactionResponse txResponse = transaction.sign(accountKey).execute(client);

// Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

// Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status: " + transactionStatus.toString());

// v2.51.0
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create the token airdrop transaction for fungible token
const transaction = await new TokenAirdropTransaction()
      .addTokenTransfer(tokenId, accountId1, -1)
      .addTokenTransfer(tokenId, accountId2, 1)
      .addTokenTransfer(tokenId, accountId1, -1)
      .addTokenTransfer(tokenId, accountId3, 1)
      .freezeWith(client);
      
// Sign with the sender account key and submit the transaction to a Hedera network
const txResponse = await transaction.sign(accountKey).execute(client);

// Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);
    
// Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

// v2.51.0
```
{% endtab %}

{% tab title="Go" %}
{% code overflow="wrap" %}
```go
// Create the token airdrop transaction for fungible token
transaction, err := hedera.NewTokenAirdropTransaction().
        AddTokenTransfer(tokenId, accountId1, -1).
        AddTokenTransfer(tokenId, accountId2, 1).
        AddTokenTransfer(tokenId, accountId1, -1).
        AddTokenTransfer(tokenId, accountId3, 1).
        FreezeWith(client)
        
if err != nil {
    panic(err)
}

// Sign with the sender account key and submit the transaction to a Hedera network
txResponse, err := transaction.Sign(accountKey).Execute(client)
if err != nil {
    panic(err)
}

// Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

// Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Println("The transaction consensus status:", transactionStatus.String())

// v2.51.0
```
{% endcode %}
{% endtab %}
{% endtabs %}
