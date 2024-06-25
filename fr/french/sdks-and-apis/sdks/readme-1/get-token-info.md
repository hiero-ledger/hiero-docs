# Get token info

Gets information about a fungible or non-fungible token instance. The token info query returns the following information:

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

| Item                      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **TokenId**               | ID of the token instance                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Token Type**            | The type of token (fungible or non-fungible)                                                                                                                                                                                                                                                                                                                                                                                         |
| **Name**                  | The name of the token. It is a string of ASCII only characters                                                                                                                                                                                                                                                                                                                                                                          |
| **Symbol**                | The symbol of the token. It is a UTF-8 capitalized alphabetical string                                                                                                                                                                                                                                                                                                                                                                  |
| **Decimals**              | The number of decimal places a token is divisible by                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Total Supply**          | The total supply of tokens that are currently in circulation                                                                                                                                                                                                                                                                                                                                                                                            |
| **Treasury**              | The ID of the account which is set as Treasury                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Custom Fees**           | The custom fee schedule of the token, if any                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Fee Schedule Key**      | Fee schedule key, if any                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Admin Key**             | The key which can perform update/delete operations on the token. If empty, the token can be perceived as immutable (not being able to be updated/deleted)                                                                                                                                                                                                                                                            |
| **KYC Key**               | The key which can grant or revoke KYC of an account for the token's transactions. If empty, KYC is not required, and KYC grant or revoke operations are not possible.                                                                                                                                                                                                                                                   |
| **Freeze Key**            | The key which can freeze or unfreeze an account for token transactions. If empty, freezing is not possible                                                                                                                                                                                                                                                                                                                              |
| **Wipe Key**              | The key which can wipe token balance of an account. If empty, wipe is not possible                                                                                                                                                                                                                                                                                                                                                      |
| **Supply Key**            | The key which can change the supply of a token. The key is used to sign Token Mint/Burn operations                                                                                                                                                                                                                                                                                                                                      |
| **Pause Key**             | The key that can pause or unpause the token from participating in transactions.                                                                                                                                                                                                                                                                                                                                                         |
| **Pause Status**          | <p>Whether or not the token is paused.</p><p>false = not paused</p><p>true = paused</p>                                                                                                                                                                                                                                                                                                                                                                 |
| **Max Supply**            | The max supply of the token                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **Supply Type**           | The supply type of the token                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Default Freeze Status** | <p>The default Freeze status (not applicable = null, frozen = false, or unfrozen = true) of Hedera accounts relative to this token. FreezeNotApplicable is returned if Token Freeze Key is empty. Frozen is returned if Token Freeze Key is set and defaultFreeze is set to true. Unfrozen is returned if Token Freeze Key is set and defaultFreeze is set to false.</p><p>FreezeNotApplicable = null;</p><p>Frozen = true;</p><p>Unfrozen = false;</p> |
| **Default KYC Status**    | <p>The default KYC status (KycNotApplicable or Revoked) of Hedera accounts relative to this token. KycNotApplicable is returned if KYC key is not set, otherwise Revoked.</p><p>KycNotApplicable = null;</p><p>Granted = false;</p><p>Revoked = true;</p>                                                                                                                                                                                               |
| **Auto Renew Account**    | An account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval                                                                                                                                                                                                                                                                                                                                             |
| **Auto Renew Period**     | The interval at which the auto-renew account will be charged to extend the token's expiry                                                                                                                                                                                                                                                                                                                                                               |
| **Expiry**                | The epoch second at which the token will expire; if an auto-renew account and period are specified, this is coerced to the current epoch second plus the autoRenewPeriod                                                                                                                                                                                                                                                                                |
| **Ledger ID**             | The ID of the network the response came from. See [HIP-198](https://hips.hedera.com/hip/hip-198).                                                                                                                                                                                                                                                                                                                       |
| **Memo**                  | Short publicly visible memo about the token, if any                                                                                                                                                                                                                                                                                                                                                                                                     |

### Methods

| Method                            | Type              | Requirement |
| --------------------------------- | ----------------- | ----------- |
| `setTokenId(<tokenId>)`           | TokenId           | Required    |
| `<TokenInfo>.tokenId`             | TokenId           | Optional    |
| `<TokenInfo>.name`                | String            | Optional    |
| `<TokenInfo>.symbol`              | String            | Optional    |
| `<TokenInfo>.decimals`            | int               | Optional    |
| `<TokenInfo>.customFees`          | List\<CustomFee> | Optional    |
| `<TokenInfo>.totalSupply`         | long              | Optional    |
| `<TokenInfo>.treasuryAccountId`   | AccountId         | Optional    |
| `<TokenInfo>.adminKey`            | Key               | Optional    |
| `<TokenInfo>.kycKey`              | Key               | Optional    |
| `<TokenInfo>.freezeKey`           | Key               | Optional    |
| `<TokenInfo>.feeScheduleKey`      | Key               | Optional    |
| `<TokenInfo>.wipeKey`             | Key               | Optional    |
| `<TokenInfo>.supplyKey`           | Key               | Optional    |
| `<TokenInfo>.defaultFreezeStatus` | boolean           | Optional    |
| `<TokenInfo>.defaultKycStatus`    | boolean           | Optional    |
| `<TokenInfo>.isDeleted`           | boolean           | Optional    |
| `<TokenInfo>.tokenType`           | TokenType         | Optional    |
| `<TokenInfo>.supplyType`          | TokenSupplyType   | Optional    |
| `<TokenInfo>.maxSupply`           | long              | Optional    |
| `<TokenInfo>.pauseKey`            | Key               | Optional    |
| `<TokenInfo>.pauseStatus`         | boolean           | Optional    |
| `<TokenInfo>.autoRenewAccount`    | AccountId         | Optional    |
| `<TokenInfo>.autoRenewPeriod`     | Duration          | Optional    |
| `<TokenInfo>.ledgerId`            | LedgerId          | Optional    |
| `<TokenInfo>.expiry`              | Instant           | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
TokenInfoQuery query = new TokenInfoQuery()
    .setTokenId(newTokenId);

//Sign with the client operator private key, submit the query to the network and get the token supply
long tokenSupply = query.execute(client).totalSupply;

System.out.println("The token info is " +tokenSupply);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the query
const query = new TokenInfoQuery()
    .setTokenId(newTokenId);

//Sign with the client operator private key, submit the query to the network and get the token supply
const tokenSupply = (await query.execute(client)).totalSupply;

console.log("The total supply of this token is " +tokenSupply);

//v2.0.7
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the query
query := hedera.NewTokenInfoQuery().
		SetTokenID(tokenId)

//Sign with the client operator private key and submit to a Hedera network
tokenInfo, err := query.Execute(client)

if err != nil {
		panic(err)
}

fmt.Printf("The token info is %v\n", tokenInfo)

//v2.1.0
```

{% endtab %}
{% endtabs %}
