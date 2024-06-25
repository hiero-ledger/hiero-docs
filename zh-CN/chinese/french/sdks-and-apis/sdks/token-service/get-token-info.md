# Get token info

Gets information about a fungible or non-fungible token instance.

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your query fee.

The token info query returns the following information:

<table><thead><tr><th width="267">Item</th><th>Description</th></tr></thead><tbody><tr><td><strong>TokenId</strong></td><td>ID of the token instance</td></tr><tr><td><strong>Token Type</strong></td><td>The type of token (fungible or non-fungible)</td></tr><tr><td><strong>Name</strong></td><td>The name of the token. It is a string of ASCII only characters</td></tr><tr><td><strong>Symbol</strong></td><td>The symbol of the token. It is a UTF-8 capitalized alphabetical string</td></tr><tr><td><strong>Decimals</strong></td><td>The number of decimal places a token is divisible by</td></tr><tr><td><strong>Total Supply</strong></td><td>The total supply of tokens that are currently in circulation</td></tr><tr><td><strong>Treasury</strong></td><td>The ID of the account which is set as Treasury</td></tr><tr><td><strong>Custom Fees</strong></td><td>The custom fee schedule of the token, if any</td></tr><tr><td><strong>Fee Schedule Key</strong></td><td>Fee schedule key, if any</td></tr><tr><td><strong>Admin Key</strong></td><td>The key which can perform update/delete operations on the token. If empty, the token can be perceived as immutable (not being able to be updated/deleted)</td></tr><tr><td><strong>KYC Key</strong></td><td>The key which can grant or revoke KYC of an account for the token's transactions. If empty, KYC is not required, and KYC grant or revoke operations are not possible.</td></tr><tr><td><strong>Freeze Key</strong></td><td>The key which can freeze or unfreeze an account for token transactions. If empty, freezing is not possible</td></tr><tr><td><strong>Wipe Key</strong></td><td>The key which can wipe token balance of an account. If empty, wipe is not possible</td></tr><tr><td><strong>Supply Key</strong></td><td>The key which can change the supply of a token. The key is used to sign Token Mint/Burn operations</td></tr><tr><td><strong>Pause Key</strong></td><td>The key that can pause or unpause the token from participating in transactions.</td></tr><tr><td><strong>Pause Status</strong></td><td><p>Whether or not the token is paused.</p><p>false = not paused</p><p>true = paused</p></td></tr><tr><td><strong>Max Supply</strong></td><td>The max supply of the token</td></tr><tr><td><strong>Supply Type</strong></td><td>The supply type of the token</td></tr><tr><td><strong>Default Freeze Status</strong></td><td><p>The default Freeze status (not applicable = null, frozen = false, or unfrozen = true) of Hedera accounts relative to this token. FreezeNotApplicable is returned if Token Freeze Key is empty. Frozen is returned if Token Freeze Key is set and defaultFreeze is set to true. Unfrozen is returned if Token Freeze Key is set and defaultFreeze is set to false.</p><p>FreezeNotApplicable = null;</p><p>Frozen = true;</p><p>Unfrozen = false;</p></td></tr><tr><td><strong>Default KYC Status</strong></td><td><p>The default KYC status (KycNotApplicable or Revoked) of Hedera accounts relative to this token. KycNotApplicable is returned if KYC key is not set, otherwise Revoked.</p><p>KycNotApplicable = null;</p><p>Granted = false;</p><p>Revoked = true;</p></td></tr><tr><td><strong>Auto Renew Account</strong></td><td>An account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval</td></tr><tr><td><strong>Auto Renew Period</strong></td><td>The interval at which the auto-renew account will be charged to extend the token's expiry</td></tr><tr><td><strong>Expiry</strong></td><td>The epoch second at which the token will expire; if an auto-renew account and period are specified, this is coerced to the current epoch second plus the autoRenewPeriod</td></tr><tr><td><strong>Ledger ID</strong></td><td>The ID of the network the response came from. See <a href="https://hips.hedera.com/hip/hip-198">HIP-198</a>.</td></tr><tr><td><strong>Memo</strong></td><td>Short publicly visible memo about the token, if any</td></tr><tr><td><strong>Metadata Key</strong></td><td>The key which can change the metadata of a token definition or individual NFT.</td></tr><tr><td><strong>Metadata</strong></td><td>The metadata for the token.</td></tr></tbody></table>

### Methods

<table><thead><tr><th width="392.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTokenId(<tokenId>)</code></td><td>TokenId</td><td>Required</td></tr><tr><td><code><TokenInfo>.tokenId</code></td><td>TokenId</td><td>Optional</td></tr><tr><td><code><TokenInfo>.name</code></td><td>String</td><td>Optional</td></tr><tr><td><code><TokenInfo>.symbol</code></td><td>String</td><td>Optional</td></tr><tr><td><code><TokenInfo>.decimals</code></td><td>int</td><td>Optional</td></tr><tr><td><code><TokenInfo>.customFees</code></td><td>List<CustomFee></td><td>Optional</td></tr><tr><td><code><TokenInfo>.totalSupply</code></td><td>long</td><td>Optional</td></tr><tr><td><code><TokenInfo>.treasuryAccountId</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code><TokenInfo>.adminKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.kycKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.freezeKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.feeScheduleKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.wipeKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.supplyKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.defaultFreezeStatus</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code><TokenInfo>.defaultKycStatus</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code><TokenInfo>.isDeleted</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code><TokenInfo>.tokenType</code></td><td>TokenType</td><td>Optional</td></tr><tr><td><code><TokenInfo>.supplyType</code></td><td>TokenSupplyType</td><td>Optional</td></tr><tr><td><code><TokenInfo>.maxSupply</code></td><td>long</td><td>Optional</td></tr><tr><td><code><TokenInfo>.pauseKey</code></td><td>Key</td><td>Optional</td></tr><tr><td><code><TokenInfo>.pauseStatus</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code><TokenInfo>.autoRenewAccount</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code><TokenInfo>.autoRenewPeriod</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code><TokenInfo>.ledgerId</code></td><td>LedgerId</td><td>Optional</td></tr><tr><td><code><TokenInfo>.expiry</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code><TokenInfo>.metadata</code></td><td>bytes</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

```java
//Create the query
TokenInfoQuery query = new TokenInfoQuery()
    .setTokenId(newTokenId);

//Sign with the client operator private key, submit the query to the network and get the token supply
long tokenSupply = query.execute(client).totalSupply;

System.out.println("The token info is " +tokenSupply);

//v2.0.14
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
