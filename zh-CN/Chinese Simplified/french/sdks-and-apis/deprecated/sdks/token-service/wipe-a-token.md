# Wipe a token

Wipes the provided amount of fungible or non-fungible tokens from the specified Hedera account. This transaction does not delete tokens from the treasury account. This transaction must be signed by the token's Wipe Key. Wiping an account's tokens burns the tokens and decreases the total supply.

- If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.
- If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`
- If the provided token is not found, the transaction will resolve to `INVALID_TOKEN_ID`.
- If the provided token has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.
- If an Association between the provided token and account is not found, the transaction will resolve to `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`.
- If Wipe Key is not present in the Token, the transaction results in `TOKEN_HAS_NO_WIPE_KEY`.
- If the provided account is the token's Treasury Account, the transaction results in `CANNOT_WIPE_TOKEN_TREASURY_ACCOUNT`
- On success, tokens are removed from the account and the total supply of the token is decreased by the wiped amount.
- The amount provided is in the lowest denomination possible.
  - Example: Token A has 2 decimals. In order to wipe 100 tokens from an account, one must provide an amount of 10000. In order to wipe 100.55 tokens, one must provide an amount of 10055.
- This transaction accepts zero unit token wipe operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564))

**Transaction Signing Requirements:**

- Wipe key
- Transaction fee payer account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                                      |
| ---------------------------- | ------------------------------------------------ |
| `new TokenWipeTransaction()` | Initializes a TokenWipeAccountTransaction object |

```java
new TokenWipeAccountTransaction()
```

## Methods

{% tabs %}
{% tab title="V1" %}

| Method                    | Type         | Description                                                                                                                                                                                                                                                       | Requirement |
| ------------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`   | TokenId      | The ID of the token to wipe from the account                                                                                                                                                                                                                      | Required    |
| `setAmount(<amount>)`     | long         | The amount of token to wipe from the specified account. Amount must be a positive non-zero number in the lowest denomination possible, not bigger than the token balance of the account (0; balance] | Required    |
| `setAccount(<accountId>)` | AccountId    | The account ID to wipe the tokens from                                                                                                                                                                                                                            | Required    |
| `setSerials(<serials>)`   | List\<long> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The list of NFTs to wipe.                                                                                                                                                      | Optional    |
| `addSerial(<serial>)`     | long         | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE.`The NFT to wipe.                                                                                                                                                                               | Optional    |

{% code title="Java" %}

```java
//Wipe 100 tokens from an account
TokenWipeTransaction transaction = new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100);

//Build the unsigned transaction, sign with the private key of the account that is being wiped, sign with the wipe private key of the token, submit the transaction to a Hedera network
TransactionId transactionId = transaction.build(client).sign(accountKey).sign(wipeKey).execute(client);

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
//Wipe 100 tokens from an account
const transaction = new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100);

//Build the unsigned transaction, sign with the account private key of the token, sign with the wipe private key, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(accountKey).sign(wipeKey).execute(client);

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
