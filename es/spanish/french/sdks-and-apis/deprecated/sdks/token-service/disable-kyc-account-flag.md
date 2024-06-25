# Disable KYC account flag

Revokes the KYC flag to the Hedera account for the given Hedera token. This transaction must be signed by the token's KYC Key. If this key is not set, you can submit a TokenUpdateTransaction to provide the token with this key.

- If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.
- If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.
- If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
- If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
- If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
- If no KYC Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_KYC\_KEY.
- Once executed the Account is marked as KYC Revoked

**Transaction Signing Requirements**

- KYC key
- Transaction fee payer account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                       | Description                                      |
| --------------------------------- | ------------------------------------------------ |
| `new TokenRevokeKycTransaction()` | Initializes the TokenRevokeKycTransaction object |

```java
new TokenRevokeKycTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                         | Type      | Description                                                               | Requirement |
| ------------------------------ | --------- | ------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`        | TokenId   | The token ID that is associated with the account to remove the KYC flag   | Required    |
| `setAccountId(<setAccountId>)` | AccountId | The account ID that is associated with the account to remove the KYC flag | Required    |

{% code title="Java" %}

```java
//Remove the KYC flag from an account
TokenRevokeKycTransaction transaction = new TokenRevokeKycTransaction()
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
//Remove the KYC flag from an account
const transaction = new TokenRevokeKycTransaction()
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
