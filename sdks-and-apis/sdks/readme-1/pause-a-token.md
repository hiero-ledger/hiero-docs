# Pause a token

A token pause transaction prevents the token from being involved in any kind of operation. The token's pause key is required to sign the transaction. This is a key that is specified during the creation of a token. If a token has no pause key, you will not be able to pause the token. If the pause key was not set during the creation of a token, you will not be able to update the token to add this key.

The following operations cannot be performed when a token is paused and will result in a `TOKEN_IS_PAUSED` status.

* Updating the token
* Transfering the token
* Transferring any other token where it has its paused key in a custom fee schedule
* Deleting the token
* Minting or burning a token
* Freezing or unfreezing an account that holds the token
* Enabling or disabling KYC
* Associating or disassociating a token
* Wiping a token

Once a token is paused, token status will update to `paused`. To verify if the token's status has been updated to `paused`, you can request the token info via the SDK or use the token info mirror node query. If the token is not paused the token status will be `unpaused`. The token status for tokens that do not have an assigned pause key will state `PauseNotApplicable`.

**Transaction Signing Requirements**

* The pause key of the token
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

| Method                  | Type    | Description                  | Requirement |
| ----------------------- | ------- | ---------------------------- | ----------- |
| `setTokenId(<tokenId>)` | TokenId | The ID of the token to pause | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the token pause transaction and specify the token to pause
TokenPauseTransaction transaction = new TokenPauseTransaction()
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
//Create the token pause transaction, specify the token to pause, freeze the unsigned transaction for signing
const transaction = new TokenPauseTransaction()
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
//Create the token pause transaction, specify the token to pause, freeze the unsigned transaction for signing
transaction, err := hedera.NewTokenPauseTransaction().
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
