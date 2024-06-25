# Transfer cryptocurrency

A transaction that transfers HBAR and tokens between Hedera accounts. You can enter multiple transfers in a single transaction. The net value of HBAR between the sending accounts and receiving accounts must equal zero.

For a CryptoTransferTransactionBody:

{% hint style="warning" %}
* Max of 10 balance adjustments in its HBAR transfer list.
* Max of 10 fungible token balance adjustments across all its token transfer list.
* Max of 10 NFT ownership changes across all its token transfer list.
* Max of 20 balance adjustments or NFT ownership changes implied by a transaction (including custom fees).
* If you are transferring a token with custom fees, only two levels of nesting fees are allowed.
* The sending account is responsible to pay for the custom token fees.
{% endhint %}

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Spender Account Allowances**

An account can have [another account](approve-an-allowance.md) spend tokens on its behalf. If the delegated spender account is transacting tokens from the owner account that authorized the allowance, the owner account needs to be specified in the transfer transaction by calling one of the following:

* `addApprovedHbarTransfer()`
* `addApprovedTokenTransfer()`
* `addApprovedNftTransfer()`
* `addApprovedTokenTransferWithDecimals()`

The debiting account is the owner's account when using this feature.

{% hint style="info" %}
**Note**: The allowance spender must pay the fee for the transaction.
{% endhint %}

**Transaction Signing Requirements**

* The accounts the tokens are being debited from are required to sign the transaction
  * If an authorized spender account is spending on behalf of the account that owns the tokens then the spending account is required to sign
* The transaction fee-paying account is required to sign the transaction

### Methods

| Method                                                                              | Type                                                                                                                     | Description                                                                                                                                                                                            |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `addHbarTransfer(<accountId>, <value>)`                                             | [AccountId](../specialized-types.md#accountid), HBAR                                                                     | <p>The account involved in the transfer and the number of hbars.<br><br>The sender and recipient values must net zero.</p>                                                                             |
| `addTokenTransfer(<tokenId>, <accountId>,<value>)`                                  | [TokenId](../token-service/token-id.md), [AccountId](../specialized-types.md#accountid), long                            | <p>The ID of the token, the account ID involved in the transfer, and the number of tokens to transfer.<br><br>The sender and recipient values must net zero.</p>                                       |
| `addNftTransfer(<nftId>, <sender>, <receiver>)`                                     | [NftId](../token-service/nft-id.md), [AcountId](../specialized-types.md), [AccountId](../specialized-types.md#accountid) | The NFT ID (token + serial number), the sending account, and receiving account.                                                                                                                        |
| `addTokenTransferWithDecimals(<tokenId>, <accountId>, <value>, <int>)`              | [TokenId](../token-service/token-id.md), AccountId, long, decimals                                                       | <p>The ID of the token, the account ID involved in the transfer, the number of tokens to transfer, the decimals of the token.<br><br>The sender and recipient values must net zero.</p>                |
| `addApprovedHbarTransfer(<ownerAccountId>,<amount>)`                                | [AccountId](../specialized-types.md#accountid), Hbar                                                                     | <p>The owner account ID the spender is authorized to transfer from and the amount.<br>Applicable to allowance transfers only.</p>                                                                      |
| `addApprovedTokenTransfer(<tokenId>, <accountId>, <value>)`                         | [TokenId](../token-service/token-id.md), [AccountId](../specialized-types.md#accountid), long                            | <p>The owner account ID and token the spender is authorized to transfer from. The debiting account is the owner account.<br>Applicable to allowance transfers only.<br></p>                            |
| `addApprovedTokenTransferWithDecimals(<tokenId>, <accountId>, <value>, <decimals>)` | [TokenId](../token-service/token-id.md), [AccountId](../specialized-types.md#accountid), long, int                       | <p>The owner account ID and token ID (with decimals) the spender is authorized to transfer from. The debit account is the account ID of the sender.<br>Applicable to allowance transfers only.<br></p> |
| `addApprovedNftTransfer(<nftId>,<sender>, <receiver>)`                              | [NftId](../token-service/nft-id.md), [AcountId](../specialized-types.md), [AccountId](../specialized-types.md#accountid) | <p>The NFT ID the spender is authorized to transfer. The sender is the owner account and receiver is the receiving account.<br>Applicable to allowance transfers only.</p>                             |

{% tabs %}
{% tab title="Java" %}
```java
// Create a transaction to transfer 1 HBAR 
TransferTransaction transaction = new TransferTransaction()
     .addHbarTransfer(OPERATOR_ID, new Hbar(-1))
     .addHbarTransfer(newAccountId, new Hbar(1));

//Submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//Version 2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create a transaction to transfer 1 HBAR
const transaction = new TransferTransaction()
    .addHbarTransfer(OPERATOR_ID, new Hbar(-1))
    .addHbarTransfer(newAccountId, new Hbar(1));
    
//Submit the transaction to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
// Create a transaction to transfer 1 HBAR
transaction := hedera.NewTransferTransaction().
		AddHbarTransfer(client.GetOperatorAccountID(), hedera.NewHbar(-1)).
		AddHbarTransfer(hedera.AccountID{Account: 3}, hedera.NewHbar(1))

//Submit the transaction to a Hedera network
txResponse, err := transaction.Execute(client)

if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", transactionReceipt.Status)

//Version 2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method                | Type                                 | Description                                              |
| --------------------- | ------------------------------------ | -------------------------------------------------------- |
| `getHbarTransfers()`  | Map\<AccountId, Hbar>                | Returns a list of the hbar transfers in this transaction |
| `getTokenTransfers()` | Map\<TokenId, Map\<AccountId, long>> | Returns the list of token transfers in the transaction   |

{% tabs %}
{% tab title="Java" %}
```java
// Create a transaction 
CryptoTransferTransaction transaction = new CryptoTransferTransaction()
    .addSender(OPERATOR_ID, new Hbar(1)
    .addRecipient(newAccountId, new Hbar(1));

//Get transfers
List<Transfer> transfers = transaction.getTransfers();

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create a transaction 
const transaction = new CryptoTransferTransaction()
    .addSender(OPERATOR_ID, new Hbar(1))
    .addRecipient(newAccountId, new Hbar(1));

//Get transfers
const transfers = transaction.getTransfers();

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```go
// Create a transaction 
transaction := hedera.NewTransferTransaction().
		AddHbarTransfer(client.GetOperatorAccountID(), hedera.NewHbar(-1)).
		AddHbarTransfer(hedera.AccountID{Account: 3}, hedera.NewHbar(1))
//Get transfers
transfers := transaction.GetTransfers()

//v2.0.0
```
{% endtab %}
{% endtabs %}
