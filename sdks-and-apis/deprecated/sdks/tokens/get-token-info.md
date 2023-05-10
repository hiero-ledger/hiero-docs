# Get token info

Gets information about a fungible or non-fungible token instance. The token info query returns the following information:

**Query Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Item                      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **TokenId**               | ID of the token instance                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Token Type**            | The type of token (fungible or non-fungible)                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Name**                  | The name of the token. It is a string of ASCII only characters                                                                                                                                                                                                                                                                                                                                                                                          |
| **Symbol**                | The symbol of the token. It is a UTF-8 capitalized alphabetical string                                                                                                                                                                                                                                                                                                                                                                                  |
| **Decimals**              | The number of decimal places a token is divisible by                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Total Supply**          | The total supply of tokens that are currently in circulation                                                                                                                                                                                                                                                                                                                                                                                            |
| **Treasury**              | The ID of the account which is set as Treasury                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Custom Fees**           | The custom fee schedule of the token, if any                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Fee Schedule Key**      | Fee schedule key, if any                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Admin Key**             | The key which can perform update/delete operations on the token. If empty, the token can be perceived as immutable (not being able to be updated/deleted)                                                                                                                                                                                                                                                                                               |
| **KYC Key**               | The key which can grant or revoke KYC of an account for the token's transactions. If empty, KYC is not required, and KYC grant or revoke operations are not possible.                                                                                                                                                                                                                                                                                   |
| **Freeze Key**            | The key which can freeze or unfreeze an account for token transactions. If empty, freezing is not possible                                                                                                                                                                                                                                                                                                                                              |
| **Wipe Key**              | The key which can wipe token balance of an account. If empty, wipe is not possible                                                                                                                                                                                                                                                                                                                                                                      |
| **Supply Key**            | The key which can change the supply of a token. The key is used to sign Token Mint/Burn operations                                                                                                                                                                                                                                                                                                                                                      |
| **Pause Key**             | The key that can pause or unpause the token from participating in transactions.                                                                                                                                                                                                                                                                                                                                                                         |
| **Pause Status**          | <p>Whether or not the token is paused.</p><p>false = not paused</p><p>true = paused</p>                                                                                                                                                                                                                                                                                                                                                                 |
| **Max Supply**            | The max supply of the token                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Supply Type**           | The supply type of the token                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Default Freeze Status** | <p>The default Freeze status (not applicable = null, frozen = false, or unfrozen = true) of Hedera accounts relative to this token. FreezeNotApplicable is returned if Token Freeze Key is empty. Frozen is returned if Token Freeze Key is set and defaultFreeze is set to true. Unfrozen is returned if Token Freeze Key is set and defaultFreeze is set to false.</p><p>FreezeNotApplicable = null;</p><p>Frozen = true;</p><p>Unfrozen = false;</p> |
| **Default KYC Status**    | <p>The default KYC status (KycNotApplicable or Revoked) of Hedera accounts relative to this token. KycNotApplicable is returned if KYC key is not set, otherwise Revoked.</p><p>KycNotApplicable = null;</p><p>Granted = false;</p><p>Revoked = true;</p>                                                                                                                                                                                               |
| **Auto Renew Account**    | An account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval                                                                                                                                                                                                                                                                                                                                             |
| **Auto Renew Period**     | The interval at which the auto-renew account will be charged to extend the token's expiry                                                                                                                                                                                                                                                                                                                                                               |
| **Expiry**                | The epoch second at which the token will expire; if an auto-renew account and period are specified, this is coerced to the current epoch second plus the autoRenewPeriod                                                                                                                                                                                                                                                                                |
| **Ledger ID**             | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198).                                                                                                                                                                                                                                                                                                                                                       |
| **Memo**                  | Short publicly visible memo about the token, if any                                                                                                                                                                                                                                                                                                                                                                                                     |

| Constructor            | Description                           |
| ---------------------- | ------------------------------------- |
| `new TokenInfoQuery()` | Initializes the TokenInfoQuery object |

```java
new TokenInfoQuery()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                                 | Type             | Requirement |
| -------------------------------------- | ---------------- | ----------- |
| `setTokenId(<tokenId>)`                | TokenId          | Required    |
| `<TokenInfoQuery>.tokenId`             | TokenId          | Optional    |
| `<TokenInfoQuery>.name`                | String           | Optional    |
| `<TokenInfoQuery>.symbol`              | String           | Optional    |
| `<TokenInfoQuery>.customFees`          | List\<CustomFee> | Optional    |
| `<TokenInfoQuery>.decimals`            | int              | Optional    |
| `<TokenInfoQuery>.totalSupply`         | long             | Optional    |
| `<TokenInfoQuery>.treasury`            | AccountId        | Optional    |
| `<TokenInfoQuery>.adminKey`            | PublicKey        | Optional    |
| `<TokenInfoQuery>.kycKey`              | PublicKey        | Optional    |
| `<TokenInfoQuery>.freezeKey`           | PublicKey        | Optional    |
| `<TokenInfoQuery>.freezeKey`           | PublicKey        | Optional    |
| `<TokenInfoQuery>.wipeKey`             | PublicKey        | Optional    |
| `<TokenInfoQuery>.supplyKey`           | PublicKey        | Optional    |
| `<TokenInfoQuery>.tokenType`           | TokenType        | Optional    |
| `<TokenInfoQuery>.supplyType`          | TokenSupplyType  | Optional    |
| `<TokenInfoQuery>.maxSupply`           | long             | Optional    |
| `<TokenInfoQuery>.defaultFreezeStatus` | boolean          | Optional    |
| `<TokenInfoQuery>.defaultKycStatus`    | boolean          | Optional    |
| `<TokenInfoQuery>.isDeleted`           | boolean          | Optional    |
| `<TokenInfoQuery>.autoRenewAccount`    | AccountId        | Optional    |
| `<TokenInfoQuery>.autoRenewPeriod`     | Duration         | Optional    |
| `<TokenInfoQuery>.expiry`              | Instant          | Optional    |

{% code title="Java" %}
```java
//Create the query
TokenInfoQuery tokenInfo = new TokenInfoQuery()
    .setTokenId(newTokenId);

//Submit the query to the network and obtain the token supply
long totalSupply = tokenInfo.execute(client).totalSupply;
System.out.println("The total supply of this token is " +totalSupply)
//Version: 1.2.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the query
const tokenInfo = new TokenInfoQuery()
    .setTokenId(newTokenId);

//Submit the query to the network and obtain the token supply
const totalSupply = await tokenInfo.execute(client).totalSupply;

console.log("The total supply of this token is " +totalSupply)

//Version 1.4.3
```
{% endcode %}
{% endtab %}
{% endtabs %}
