# Update token custom fees

Update the custom fees for a given token. If the token does not have a fee schedule, the network response returned will be `CUSTOM_SCHEDULE_ALREADY_HAS_NO_FEES`. You will need to sign the transaction with the fee schedule key to update the fee schedule for the token. If you do not have a fee schedule key set for the token, you will not be able to update the fee schedule.

**Transaction Signing Requirements**

* Fee schedule key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Property         | Description                        |
| ---------------- | ---------------------------------- |
| **Fee Schedule** | The new fee schedule for the token |

## Methods

| Method                        | Type                                               | Requirement |
| ----------------------------- | -------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`       | [TokenId](token-id.md)                             | Required    |
| `setCustomFees(<customFees>)` | List<[CustomFee](custom-token-fees.md#custom-fee)> | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction 
TokenFeeScheduleUpdateTransaction transaction = new TokenFeeScheduleUpdateTransaction()
     .setTokenId(tokenId)
     .setCustomFees(customFee)

//Freeze the unsigned transaction, sign with the fee schedule key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(feeScheduleKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//Version: 2.0.9
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and freeze for manual signing
const transaction = await new TokenFeeScheduleUpdateTransaction()
     .setTokenId(tokenId)
     .setCustomFees(customFee)
     .freezeWith(client);

//Sign the transaction with the fee schedule key
const signTx = await transaction.sign(feeScheduleKey);

//Submit the signed transaction to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status.toString();

console.log("The transaction consensus status is " +transactionStatus);
//Version: 2.0.26
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and freeze for manual signing 
transaction, err := hedera.NewTokenFeeScheduleUpdateTransaction().
		SetCustomFees(customFees).
		SetTokenID(tokenId).
		FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the fee schedule key of the token, sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.Sign(feeScheduleKey).Execute(client)

if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)
//Version: 2.1.11
```
{% endtab %}
{% endtabs %}
