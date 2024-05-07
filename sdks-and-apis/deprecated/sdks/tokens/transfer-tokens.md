# Transfer tokens

Transfer tokens from some accounts to other accounts. The transaction must be signed by the sending account. Each negative amount is withdrawn from the corresponding account (a **sender**), and each positive one is added to the corresponding account (a **receiver**). All amounts must have a sum of zero. This does not apply to NFT token transfers. Each amount is a number with the lowest denomination possible for a token. Example: Token X has 2 decimals. Account A transfers an amount of 100 tokens by providing 10000 as the amount in the TransferList. If Account A wants to send 100.55 tokens, he must provide 10055 as the amount. If any sender account fails to have a sufficient token balance, then the entire transaction fails and none of the transfers occur, though the transaction fee is still charged. This transaction accepts zero unit token transfer operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)).

**Custom Fee Tokens**

Custom fee tokens are tokens that have a unique custom fee schedule associated to them. The sender account is required to pay for the custom fee(s) associated with the token that is being transferred. The sender account must have the amount of the custom fee token being transferred and the custom fee amounts to successfully process the transaction. You can check to see if the token has a custom fee schedule by requesting the [token info query](../../../sdks/token-service/get-token-info.md). Token with custom fees allow up two levels of nesting in a transfer transaction.

{% hint style="warning" %}
* A max of 10 balance adjustments in its hbar transferList
* A max of 10 token fungible balance adjustments across all its tokenTransferList’s
* A max of 10 NFT ownership changes across all its tokenTransferList’s
* There’s also a maximum of 20 balance adjustments or NFT ownership changes implied by a transaction (including custom fees)
* If you are transferring a token with custom fees, only two levels of nesting of fees are allowed
* The sending account is responsible to pay for the custom token fees
{% endhint %}

#### Transaction Signing Requirements

* The key of the account sending the tokens
* The transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                 | Description                              |
| --------------------------- | ---------------------------------------- |
| `new TransferTransaction()` | Initializes a TransferTransaction object |

```java
new TransferTransaction()
```

## Methods

{% tabs %}
{% tab title="V1" %}
| Method                                         | Type                        | Description                                                                                                                                                                                                                                            |   |
| ---------------------------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | - |
| `addHbarTransfer(<accountId, value>)`          | AccountID, Hbar/long        | Add the from and to account to transfer hbars (you will need to call this method twice). The sending account must sign the transaction. The sender and recipient values must net zero.                                                                 |   |
| `addTokenTransfer(<tokenId, accountId,value>)` | TokenId, AccountId, long    | Add the from and to account to transfer tokens (you will need to call this method twice).The ID of the token, the account ID to transfer the tokens from or to, and the value of the token to transfer. The sender and recipient values must net zero. |   |
| `addNftTransfer(<nftId, sender, receiver)`     | NftId, AccountId, AccountId | The NFT ID being transferred, the account ID the NFT owner, the account ID of the receiver of the NFT.                                                                                                                                                 |   |

{% code title="Java" %}
```java
//Create the transfer transaction
TransferTransaction transaction = new TransferTransaction()
    .addTokenTransfer(tokenId, accountId, -10)
    .addTokenTransfer(tokenId, OPERATOR_ID, 10);


//Sign with the client operator key and submit the transaction to a Hedera network
TransactionId txId = transaction.build(client).sign(accountKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Transfer 100 tokens between two accounts
const transaction = new TransferTransaction()
    .addTokenTransfer(tokenId, accountId, -100)
    .addTokenTransfer(tokenId, OPERATOR_ID, 100);

//Build the unsigned transaction, sign with sender account private key, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(accountKey).execute(client);

//Request the receipt of the transaction
const getReceipt = await transactionId.getReceipt(client);

//Obtain the transaction consensus status
const transactionStatus = getReceipt.status;

console.log("The transaction consensus status " +transactionStatus);
```
{% endcode %}
{% endtab %}
{% endtabs %}
