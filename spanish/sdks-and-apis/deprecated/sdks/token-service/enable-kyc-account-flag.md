# Enable KYC account flag

Grants KYC to the Hedera accounts for the given Hedera token. This transaction must be signed by the token's KYC Key.

* If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.
* If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
* If no KYC Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_KYC\_KEY.
* Once executed the Account is marked as KYC Granted.

**Transaction Signing Requirements**

* KYC key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new TokenGrantKycTransaction()` | Initializes the TokenGrantKycTransaction object |

```java
new TokenGrantKycTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                      | Type      | Description                                   | Requirement |
| --------------------------- | --------- | --------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`     | TokenId   | The token for this account to have passed KYC | Required    |
| `setAccountId(<accountId>)` | AccountId | The account for this token to have passed KYC | Required    |

{% code title="Java" %}
```java
//Enable KYC flag on account
TokenGrantKycTransaction transaction = new TokenGrantKycTransaction()
    .setTokenId(newTokenId)
    .setAccountId(newAccountId);

//Build the unsigned transaction, sign with the kyc private key of the token, submit the transaction to a Hedera network
TransactionId transactionId = transaction.build(client).sign(kycKey).execute(client);
    
//Request the receipt of the transaction
TransactionReceipt getReceipt = transactionId.getReceipt(client);
    
//Obtain the transaction consensus status
Status transactionStatus = getReceipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//Version: 1.2.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Enable KYC flag on account
const transaction = new TokenGrantKycTransaction()
    .setTokenId(newTokenId)
    .setAccountId(newAccountId);

//Build the unsigned transaction, sign with the kyc private key of the token, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(kycKey).execute(client);
    
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
