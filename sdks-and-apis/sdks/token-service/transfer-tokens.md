# Transfer tokens

Transfer tokens from some accounts to other accounts. The transaction must be signed by the sending account. Each negative amount is withdrawn from the corresponding account (a **sender**), and each positive one is added to the corresponding account (a **receiver**). All amounts must have a sum of zero. This does not apply to NFT token transfers. Each amount is a number with the lowest denomination possible for a token. Example: Token X has 2 decimals. Account A transfers an amount of 100 tokens by providing 10000 as the amount in the TransferList. If Account A wants to send 100.55 tokens, he must provide 10055 as the amount. If any sender account fails to have a sufficient token balance, then the entire transaction fails and none of the transfers occur, though the transaction fee is still charged. This transaction accepts zero unit token transfer operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)).

**Custom Fee Tokens**

Custom fee tokens are tokens that have a unique custom fee schedule associated to them. The sender account is required to pay for the custom fee(s) associated with the token that is being transferred. The sender account must have the amount of the custom fee token being transferred and the custom fee amounts to successfully process the transaction. You can check to see if the token has a custom fee schedule by requesting the [token info query](get-token-info.md). Token with custom fees allow up two levels of nesting in a transfer transaction.

{% hint style="warning" %}
* A max of 10 balance adjustments in its HBAR transferList
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

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

| Method                                                                                                                        | Type                                                                                                    | Description                                                                                                                                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `addHbarTransfer(<accountId>, <value>)`                                                                                       | [AccountID](../specialized-types.md#accountid), Hbar/long                                               | Add the from and to account to transfer hbars (you will need to call this method twice). The sending account must sign the transaction. The sender and recipient values must net zero.                                                                  |
| `addTokenTransfer(<tokenId>, <accountId>,<value>)`                                                                            | [TokenId](token-id.md), AccountId, long                                                                 | Add the from and to account to transfer tokens (you will need to call this method twice). The ID of the token, the account ID to transfer the tokens from or to, and the value of the token to transfer. The sender and recipient values must net zero. |
| `addNftTransfer(<nftId>, <sender>, <receiver>)`                                                                               | NftId, [AccountId](../specialized-types.md#accountid), [AccountId](../specialized-types.md#accountid)   | The NFT ID being transferred, the account ID the NFT owner, the account ID of the receiver of the NFT.                                                                                                                                                  |
| <p><code>addApprovedHbarTransfer(&#x3C;ownerAccountId>,&#x3C;amount>)</code><br></p>                                          | [AccountId](../specialized-types.md#accountid), Hbar                                                    | <p>The owner account ID the spender is authorized to transfer from and the amount.<br>Applicable to allowance transfers only.</p>                                                                                                                       |
| <p><code>addApprovedTokenTransfer(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;value>)</code>(previewnet)<br></p>                  | [TokenId](token-id.md), [AccountId](../specialized-types.md#accountid), long                            | <p>The owner account ID and token the spender is authorized to transfer from. The debiting account is the owner account.<br>Applicable to allowance transfers only.<br></p>                                                                             |
| <p><code>addApprovedTokenTransferWithDecimals(&#x3C;tokenId>, &#x3C;accountId>, &#x3C;value>, &#x3C;decimals>)</code><br></p> | [TokenId](token-id.md), [AccountId](../specialized-types.md#accountid), long, int                       | <p>The owner account ID and token ID (with decimals) the spender is authorized to transfer from. The debit account is the account ID of the sender.<br>Applicable to allowance transfers only.</p>                                                      |
| <p><code>addApprovedNftTransfer(&#x3C;nftId>,&#x3C;sender>, &#x3C;receiver>)</code><br></p>                                   | [NftId](nft-id.md), [AcountId](../specialized-types.md), [AccountId](../specialized-types.md#accountid) | <p>The NFT ID the spender is authorized to transfer. The sender is the owner account and receiver is the receiving account.<br>Applicable to allowance transfers only.</p>                                                                              |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transfer transaction
TransferTransaction transaction = new TransferTransaction()
     .addTokenTransfer(tokenId, OPERATOR_ID, -1)
     .addTokenTransfer(tokenId, accountId, 1);

//Sign with the client operator key and submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transfer transaction
const transaction = await new TransferTransaction()
     .addTokenTransfer(tokenId, accountId1, -1)
     .addTokenTransfer(tokenId, accountId2, 1)
     .freezeWith(client);

//Sign with the sender account private key
const signTx = await transaction.sign(accountKey1);

//Sign with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Obtain the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transfer transaction and freeze the transaction from further modification
transaction, err := hedera.NewTransferTransaction().
        AddTokenTransfer(tokenId, accountId1, -1).
        AddTokenTransfer(tokenId, accountId2, 0).
        FreezeWith(client)

//Sign with the accountId1 private key, sign with the client operator key and submit to a Hedera network
txResponse, err := transaction.Sign(accountKey1).Execute(client)

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
//v2.1.0
```
{% endtab %}
{% endtabs %}
