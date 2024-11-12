# Unfreeze an account

Unfreezes transfers of the specified token for the account. The transaction must be signed by the token's Freeze Key.

* If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.
* If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
* If no Freeze Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_FREEZE\_KEY.
* Once executed the Account is marked as Unfrozen and will be able to receive or send tokens. The operation is idempotent.

**Transaction Signing Requirements**

* Freeze key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new TokenUnfreezeTransaction()` | Initializes the TokenUnfreezeTransaction object |

```java
new TokenUnfreezeTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                      | Type      | Description                            | Requirement |
| --------------------------- | --------- | -------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`     | TokenId   | The token for this account to unfreeze | Required    |
| `setAccountId(<accountId>)` | AccountId | The account to unfreeze                | Required    |

{% code title="Java" %}
```java
//Unfreeze an account
TokenUnfreezeTransaction transaction = new TokenUnfreezeTransaction()
    .setAccountId(newAccountId)
    .setTokenId(newTokenId);

//Build the unsigned transaction, sign with the sender freeze private key of the token, submit the transaction to a Hedera network
TransactionId transactionId = transaction.build(client).sign(freezeKey).execute(client);
    
//Request the receipt of the transaction
TransactionReceipt getReceipt = transactionId.getReceipt(client);
    
//Obtain the transaction consensus status
Status transactionStatus = getReceipt.status;

System.out.print("The transaction consensus status is " +transactionStatus);
//Version: 1.2.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Unfreeze an account
const transaction = new TokenUnfreezeTransaction()
    .setAccountId(newAccountId)
    .setTokenId(newTokenId);

//Build the unsigned transaction, sign with the freeze private key of the token, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(freezeKey).execute(client);
    
//Request the receipt of the transaction
const getReceipt = await transactionId.getReceipt(client);
    
//Obtain the transaction consensus status
const transactionStatus = getReceipt.status;

console.log("The transaction consensus status is " +transactionStatus);
//Version 1.4.2
```
{% endcode %}
{% endtab %}
{% endtabs %}
