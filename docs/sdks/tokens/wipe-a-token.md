# Wipe a token

Wipes the provided amount of fungible or non-fungible tokens from the specified Hedera account. This transaction does not delete tokens from the treasury account. This transaction must be signed by the token's Wipe Key. Wiping an account's tokens burns the tokens and decreases the total supply.

* If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.
* If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.
* If Wipe Key is not present in the Token, the transaction results in TOKEN\_HAS\_NO\_WIPE\_KEY.
* If the provided account is the token's Treasury Account, the transaction results in CANNOT\_WIPE\_TOKEN\_TREASURY\_ACCOUNT
* On success, tokens are removed from the account and the total supply of the token is decreased by the wiped amount.
* The amount provided is in the lowest denomination possible.&#x20;
  * Example: Token A has 2 decimals. In order to wipe 100 tokens from an account, one must provide an amount of 10000. In order to wipe 100.55 tokens, one must provide an amount of 10055.

**Transaction Signing Requirements:**

* Wipe key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                                      |
| ---------------------------- | ------------------------------------------------ |
| `new TokenWipeTransaction()` | Initializes a TokenWipeAccountTransaction object |

```java
new TokenWipeAccountTransaction()
```

## Methods

{% tabs %}
{% tab title="V2" %}
| Method                    | Type        | Description                                                                                                                                                                                                                                  | Requirement |
| ------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`   | TokenId     | The ID of the token to wipe from the account                                                                                                                                                                                                 | Required    |
| `setAmount(<amount>)`     | long        | Applicable to tokens of type  `FUNGIBLE_COMMON`.The amount of token to wipe from the specified account. The amount must be a positive non-zero number in the lowest denomination possible, not bigger than the token balance of the account. | Optional    |
| `setAccount(<accountId>)` | AccountId   | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The account ID to wipe the NFT from.                                                                                                                                                      | Required    |
| `setSerials(<serials>)`   | List\<long> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The list of NFTs to wipe.                                                                                                                                                                 | Optional    |
| `addSerial(<serial>)`     | long        | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE.`The NFT to wipe.                                                                                                                                                                          | Optional    |

{% code title="Java" %}
```java
//Wipe 100 tokens from an account
TokenWipeTransaction transaction = new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100);

//Freeze the unsigned transaction, sign with the private key of the account that is being wiped, sign with the wipe private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(accountKey).sign(wipeKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//v2.0.1
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Wipe 100 tokens from an account and freeze the unsigned transaction for manual signing
const transaction = await new TokenWipeTransaction()
    .setAccountId(accountId)
    .setTokenId(tokenId)
    .setAmount(100)
    .freezeWith(client);

//Sign with the private key of the account that is being wiped, sign with the wipe private key of the token
const signTx = await (await transaction.sign(accountKey)).sign(wipeKey);    

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Obtain the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());
```
{% endcode %}

{% code title="Go" %}
```go
//Wipe 100 tokens and freeze the unsigned transaction for manual signing
transaction, err = hedera.NewTokenBurnTransaction().
        SetAccountId(accountId).
        SetTokenID(tokenId).
        SetAmount(1000).
        FreezeWith(client)

if err != nil {
        panic(err)
}

//Sign with the private key of the account that is being wiped, sign with the wipe private key of the token
txResponse, err := transaction.Sign(accountKey).Sign(wipeKey).Execute(client)

if err != nil {
        panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)

if err != nil {
        panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)

//v2.1.0
```
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method                    | Type        | Description                                                                                                                                                                                          | Requirement |
| ------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`   | TokenId     | The ID of the token to wipe from the account                                                                                                                                                         | Required    |
| `setAmount(<amount>)`     | long        | The amount of token to wipe from the specified account. Amount must be a positive non-zero number in the lowest denomination possible, not bigger than the token balance of the account (0; balance] | Required    |
| `setAccount(<accountId>)` | AccountId   | The account ID to wipe the tokens from                                                                                                                                                               | Required    |
| `setSerials(<serials>)`   | List\<long> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The list of NFTs to wipe.                                                                                                                         | Optional    |
| `addSerial(<serial>)`     | long        | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE.`The NFT to wipe.                                                                                                                                  | Optional    |

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
