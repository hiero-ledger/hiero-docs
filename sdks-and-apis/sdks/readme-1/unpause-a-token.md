# Unpause a token

A token unpause transaction is a transaction that unpauses the token that was previously disabled from participating in transactions. The token's pause key is required to sign the transaction. Once the unpause transaction is submitted the token pause status is updated to `unpause`.

**Transaction Signing Requirements:**

* The pause key of the token
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Method                  | Type    | Description                  | Requirement |
| ----------------------- | ------- | ---------------------------- | ----------- |
| `setTokenId(<tokenId>)` | TokenId | The ID of the token to pause | Required    |

## Methods

{% tabs %}
{% tab title="Java" %}
```java
//Create the token unpause transaction and specify the token to pause
TokenUnpauseTransaction transaction = new TokenUnpauseTransaction()
    .setTokenId(tokenId);

//Freeze the unsigned transaction, sign with the pause key, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(pauseKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is: " +transactionStatus);
//v2.2.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the token unpause transaction, specify the token to pause, freeze the unsigned transaction for signing
const transaction = new TokenUnpauseTransaction()
     .setTokenId(tokenId);
     .freezeWith(client);

//Sign with the pause key 
const signTx = await transaction.sign(pauseKey);

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

//v2.2.0
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the token unpause transaction, specify the token to pause, freeze the unsigned transaction for signing
transaction, err := hedera.NewTokenUnpauseTransaction().
        SetTokenID(tokenId).
        FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the pause key 
txResponse, err = transaction.Sign(pauseKey).Execute(client)

if err != nil {
    panic(err)
}

//Get the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)

//v2.3.0
```
{% endtab %}
{% endtabs %}
