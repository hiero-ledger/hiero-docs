# Transfer cryptocurrency

A transaction that transfers hbars and tokens between Hedera accounts. You can enter multiple transfers in a single transaction. The net value of hbars between the sending accounts and receiving accounts must equal zero.\
For a CryptoTransferTransactionBody:

{% hint style="warning" %}
* A max of 10 balance adjustments in its hbar transferList
* A max of 10 token fungible balance adjustments across all its tokenTransferList’s
* A max of 10 NFT ownership changes across all its tokenTransferList’s
* There’s also a maximum of 20 balance adjustments or NFT ownership changes implied by a transaction (including custom fees)
* If you are transferring a token with custom fees, only two levels of nesting of fees are allowed
* The sending account is responsible to pay for the custom token fees
{% endhint %}

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Spender Account Allowances**

An account can have [another account](../../../sdks/accounts-and-hbar/approve-an-allowance.md) spend tokens on its behalf. If the delegated spender account is transacting tokens from the owner account that authorized the allowance, the owner account needs to be specified in the transfer transaction by calling one of the following:

* `addApprovedHbarTransfer()`
* `addApprovedTokenTransfer()`
* `addApprovedNftTransfer()`
* `addApprovedTokenTransferWithDecimals()`

The debiting account is the owner account when using this feature.

**Transaction Signing Requirements**

* The accounts the tokens are being debited from are required to sign the transaction
  * If an authorized spender account is spending on behalf of the account that owns the tokens then the spending account is required to sign
* The transaction fee paying account is required to sign the transaction

| Constructor                 | Description                                |
| --------------------------- | ------------------------------------------ |
| `new TransferTransaction()` | Initializes the TransferTransaction object |

```java
new TransferTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                                         | Type                     | Description                                                                                                                                     |
| ---------------------------------------------- | ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `addHbarTransfer(<accountId, value>)`          | AccountId, Hbar/long     | The account the transfer is being debited from. The sending account must sign the transaction. The sender and recipient values must net zero.   |
| `addTokenTransfer(<tokenId, accountId,value>)` | TokenId, AccountId, long | The ID of the token, the account ID to transfer the tokens from, value of the token to transfer. The sender and recipient values must net zero. |

{% code title="Java" %}
```java
//Create the transfer transaction
TransferTransaction transaction = new TransferTransaction()
    .addHbarTransfer(OPERATOR_ID, new Hbar(-10))
    .addHbarTransfer(newAccountId, new Hbar(10));

//Sign with the client operator key and submit the transaction to a Hedera network
TransactionId txId = transaction.execute(client);
        
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
//Create the transfer transaction
const transaction = new TransferTransaction()
    .addHbarTransfer(OPERATOR_ID, new Hbar(-10))
    .addHbarTransfer(newAccountId, new Hbar(10));

//Sign with the client operator key and submit the transaction to a Hedera network
const txId = await transaction.execute(client);
        
//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}
