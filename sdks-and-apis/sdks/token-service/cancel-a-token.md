# Cancel a token

The `TokenCancelAirdropTransaction` allows a sender to cancel one or more pending airdrops they initiated. This transaction removes the specified pending airdrops from the network state, making them unavailable for future claims by the intended recipients. To execute this transaction, the sender must sign it, confirming their intent to cancel the pending transfers. All specified pending airdrops in the transaction must be successfully canceled for the transaction to complete. The cancellation does not provide a refund of any fees previously paid when creating the airdrop.

#### Transaction Signing Requirements

* The sender account key.
* The transaction fee payer account key  if it differs from the sender.

#### Transaction Fees

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

<table><thead><tr><th width="259">Method</th><th width="206">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>addPendingAirdropId(&#x3C;pendingAirdropId)</code></td><td>List&#x3C;PendingAirdropId> </td><td>Adds the ID of a pending airdrop to the transaction, indicating which specific airdrop the sender wants to cancel. This method is used to reference and cancel one or more pending airdrops by their unique identifiers. The transaction can have up to 10 entries and must no have any duplicates. </td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
{% code overflow="wrap" %}
```java
// Create the token cancel airdrop transaction
TokenCancelAirdropTransaction transaction = new TokenCancelAirdropTransaction()
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
// Create the token cancel airdrop transaction
const transaction = new TokenCancelAirdropTransaction()
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
// Create the token cancel airdrop transaction
transaction, err := hedera.NewTokenCancelAirdropTransaction().
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
