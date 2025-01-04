# Claim a token

The `TokenClaimAirdropTransaction` allows an intended airdrop recipient to claim a pending token transfer, requiring signatures from both the receiver and transaction payer. When no auto-association slots are available, this transaction handles both the `TokenAssociate` and `CryptoTransfer` steps. If the receiver account has auto-association slots available, the token is automatically associated and transferred, making the claim transaction unnecessary. The receiver pays only the transaction fee, while the sender covers association and custom fees. Once claimed, the transfer is irreversible and removed from network state. All referenced pending airdrops must succeed for the transaction to complete.

{% hint style="info" %}
* Claimed airdrops result in token transfers recorded in the `token_transfer_list` in the transaction record.
* Claimed pending airdrops are removed from the network state and cannot be claimed again.
{% endhint %}

#### Transaction Signing Requirements

* The receiver account key.
* The transaction fee payer account key  if it differs from the sender.

#### Transaction Fees

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

<table><thead><tr><th width="259">Method</th><th width="206">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addPendingAirdropId(&#x3C;pendingAirdropId)</code></td><td>List&#x3C;PendingAirdropId> </td><td>Adds the ID of a pending airdrop to the transaction, indicating which specific airdrop the receiver wants to claim. This method is used to reference and claim one or more pending airdrops by their unique identifiers. The transaction can have up to 10 entries and must no have any duplicates. </td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
{% code overflow="wrap" %}
```java
// Create the token claim airdrop transaction
TokenClaimAirdropTransaction transaction = new TokenClaimAirdropTransaction()
    .addPendingAirdropId(pendingAirdropId)
    .freezeWith(client);

// Sign with the sender account key and submit the transaction to a Hedera network
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
{% code overflow="wrap" %}
```javascript
// Create the token claim airdrop transaction
const transaction = await new TokenClaimAirdropTransaction()
	.addPendingAirdropId(pendingAirdropId)
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
{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code overflow="wrap" %}
```go
// Create the token claim airdrop transaction
transaction, err := hedera.NewTokenClaimAirdropTransaction().
    AddPendingAirdropId(pendingAirdropId).
    FreezeWith(client)
if err != nil {
    panic(err)
}

// Sign with the sender account key and submit the transaction to the Hedera network
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

fmt.Println("The transaction consensus status:", transactionStatus)

// v2.51.0
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Reference:** [**Token Airdrop Example**](https://github.com/hashgraph/hedera-sdk-js/blob/main/examples/token-airdrop-example.js)
