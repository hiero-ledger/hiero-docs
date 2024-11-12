# Update a token

A transaction that updates the properties of an existing token. The admin key must sign this transaction to update any of the token properties. The admin key can update exisitng keys, but cannot add new keys if they were not set during the creation of the token. If no value is given for a field, that field is left unchanged.

For an immutable token (that is, a token created without an admin key), only the expiry may be updated. Setting any other field, in that case, will cause the transaction status to resolve to `TOKEN_IS_IMMUTABlE`.

| Property               | Description                                                                                                                                                                                                                                                           |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**               | The new name of the token. The token name is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (`NUL`). Is not required to be unique.                                                                    |
| **Symbol**             | The new symbol of the token. The token symbol is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (`NUL`). Is not required to be unique.                                                                |
| **Treasury Account**   | The new treasury account of the token. If the provided treasury account is not existing or deleted, the response will be `INVALID_TREASURY_ACCOUNT_FOR_TOKEN`. If successful, the Token balance held in the previous Treasury Account is transferred to the new one.  |
| **Admin Key**          | The new admin key of the token. If the token is immutable (no Admin Key was assigned during token creation), the transaction will resolve to `TOKEN_IS_IMMUTABlE`. Admin keys cannot update to add new keys that were not specified during the creation of the token. |
| **KYC Key**            | The new KYC key of the token. If the token does not have currently a KYC key, the transaction will resolve to `TOKEN_HAS_NO_KYC_KEY`.                                                                                                                                 |
| **Freeze Key**         | The new freeze key of the token. If the token does not have currently a freeze key, the transaction will resolve to `TOKEN_HAS_NO_FREEZE_KEY`.                                                                                                                        |
| **Fee Schedule Key**   | If set, the new key to use to update the token's custom fee schedule; if the token does not currently have this key, transaction will resolve to `TOKEN_HAS_NO_FEE_SCHEDULE_KEY`                                                                                      |
| **Pause Key**          | Update the token's existing pause key. The pause key has the ability to pause or unpause a token.                                                                                                                                                                     |
| **Wipe Key**           | The new wipe key of the token. If the token does not have currently a wipe key, the transaction will resolve to `TOKEN_HAS_NO_WIPE_KEY`.                                                                                                                              |
| **Supply Key**         | The new supply key of the token. If the token does not have currently a supply key, the transaction will resolve to `TOKEN_HAS_NO_SUPPLY_KEY`.                                                                                                                        |
| **Expiration Time**    | The new expiry time of the token. Expiry can be updated even if the admin key is not set. If the provided expiry is earlier than the current token expiry, the transaction will resolve to `INVALID_EXPIRATION_TIME`.                                                 |
| **Auto Renew Account** | The new account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval.                                                                                                                                                     |
| **Auto Renew Period**  | The new interval at which the auto-renew account will be charged to extend the token's expiry. The default auto-renew period is 131,500 minutes.                                                                                                                      |
| **Memo**               | Short publicly visible memo about the token. No guarantee of uniqueness. (100 characters max)                                                                                                                                                                         |

#### Transaction Signing Requirements

* Admin key is required to sign to update any token properties
* Updating the admin key requires the new admin key to sign
* If a new treasury account is set, the new treasury key is required to sign
* The account that is paying for the transaction fee

**Transaction Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                    | Description                                 |
| ------------------------------ | ------------------------------------------- |
| `new TokenUpdateTransaction()` | Initializes a TokenUpdateTransaction object |

```java
new TokenUpdateTransaction()
```

## Methods

{% tabs %}
{% tab title="V1" %}
| Method                                | Type      | Requirement |
| ------------------------------------- | --------- | ----------- |
| `setTokenId(<tokenId>)`               | TokenId   | Required    |
| `setName(<name>)`                     | String    | Optional    |
| `setSymbol(<symbol>)`                 | String    | Optional    |
| `setTreasury(<treasury>)`             | AccountId | Optional    |
| `setAdminKey(<key>)`                  | PublicKey | Optional    |
| `setKycKey(<key>)`                    | PublicKey | Optional    |
| `setFreezeKey(<key>)`                 | PublicKey | Optional    |
| `setFeeScheduleKey(<key>)`            | PublicKey | Optional    |
| `setWipeKey(<key>)`                   | PublicKey | Optional    |
| `setSupplyKey(<key>)`                 | PublicKey | Optional    |
| `setExpirationTime(<expirationTime>)` | Instant   | Optional    |
| `setAutoRenewAccount(<account>)`      | AccountId | Optional    |
| `setAutoRenewPeriod(<period>)`        | Duration  | Optional    |

{% code title="Java" %}
```java
//Update the name of the token
TokenUpdateTransaction transaction = new TokenUpdateTransaction()
    .setTokenId(newTokenId)
    .setName("Your New Token Name");

//Build the unsigned transaction, sign with the admin private key of the token, submit the transaction to a Hedera network
TransactionId transactionId = transaction.build(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = transactionId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//Version: 1.2.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Update the name of the token
const transaction = new TokenUpdateTransaction()
    .setTokenId(newTokenId)
    .setName("Your New Token Name");

//Build the unsigned transaction, sign with the token admin private key of the token, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
const receipt = await transactionId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);
//Version: 1.4.2
```
{% endcode %}
{% endtab %}
{% endtabs %}
