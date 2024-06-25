# Create a token

{% hint style="info" %}
Check out the "Getting Started with the Hedera Token Service" video tutorial in JavaScript [here](https://youtu.be/lp3mwdYEZEk).
{% endhint %}

Create a new fungible or non-fungible token (NFT) on the Hedera network. After you submit the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt.

You can also create, access, or transfer HTS tokens using smart contracts - see [Hedera Service Solidity Libraries](https://docs.hedera.com/guides/docs/sdks/smart-contracts/hedera-service-solidity-libraries) and [Supported ERC Token Standards](https://docs.hedera.com/guides/core-concepts/smart-contracts/supported-erc-token-standards).

{% hint style="warning" %}
**Token Keys**

- If any of the token key types (KYC key, Wipe key, Metadata key, etc) are not set during the creation of the token, you will not be able to update the token and add them in the future
- If any of the token key types (KYC key, Wipe key, Metadata key, etc) are set during the creation of the token, you will not be able to remove them in the future
  {% endhint %}

#### **NFTs**

For non-fungible tokens, the token ID represents an NFT class. Once the token is created, you must mint each NFT using the [token mint](mint-a-token.md) operation.

{% hint style="warning" %}
**Note**: The initial supply for an NFT is required to be set to 0.
{% endhint %}

#### **Token Properties**

<table><thead><tr><th width="282">Property</th><th>Description</th></tr></thead><tbody><tr><td><strong>Name</strong></td><td>Set the publicly visible name of the token. The token name is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (<code>NUL</code>). The token name is not unique. Maximum of 100 characters.</td></tr><tr><td><strong>Token Type</strong></td><td>The type of token to create. Either fungible or non-fungible.</td></tr><tr><td><strong>Symbol</strong></td><td>The publicly visible token symbol. Set the publicly visible name of the token. The token symbol is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (<code>NUL</code>). The token symbol is not unique. Maximum of 100 characters.</td></tr><tr><td><strong>Decimal</strong></td><td>The number of decimal places a token is divisible by. This field can never be changed.</td></tr><tr><td><strong>Initial Supply</strong></td><td>Specifies the initial supply of fungible tokens to be put in circulation. The initial supply is sent to the Treasury Account. The maximum supply of tokens is <code>9,223,372,036,854,775,807</code>(<code>2^63-1</code>) tokens and is in the lowest denomination possible. For creating an NFT, you must set the initial supply to 0.</td></tr><tr><td><strong>Treasury Account</strong></td><td>The account which will act as a treasury for the token. This account will receive the specified initial supply and any additional tokens that are minted. If tokens are burned, the supply will decreased from the treasury account.</td></tr><tr><td><strong>Admin Key</strong></td><td>The key which can perform token update and token delete operations on the token. The admin key has the authority to change the supply key, freeze key, pause key, wipe key, and KYC key. It can also update the treasury account of the token. If empty, the token can be perceived as immutable (not being able to be updated/deleted).</td></tr><tr><td><strong>KYC Key</strong></td><td>The key which can grant or revoke KYC of an account for the token's transactions. If empty, KYC is not required, and KYC grant or revoke operations are not possible.</td></tr><tr><td><strong>Freeze Key</strong></td><td>The key which can sign to freeze or unfreeze an account for token transactions. If empty, freezing is not possible.</td></tr><tr><td><strong>Wipe Key</strong></td><td>The key which can wipe the token balance of an account. If empty, wipe is not possible.</td></tr><tr><td><strong>Supply Key</strong></td><td>The key which can change the total supply of a token. This key is used to authorize token mint and burn transactions. If this is left empty, minting/burning tokens is not possible.</td></tr><tr><td><strong>Fee Schedule Key</strong></td><td>The key which can change the token's <a href="custom-token-fees.md">custom fee</a> schedule. It must sign a TokenFeeScheduleUpdate transaction. A custom fee schedule token without a fee schedule key is immutable.</td></tr><tr><td><strong>Pause Key</strong></td><td>The key which has the authority to pause or unpause a token. Pausing a token prevents the token from participating in all transactions.</td></tr><tr><td><strong>Custom Fees</strong></td><td><a href="custom-token-fees.md">Custom fees</a> to charge during a token transfer transaction that transfers units of this token. Custom fees can either be <a href="custom-token-fees.md#fixed-fee">fixed</a>, <a href="custom-token-fees.md#fractional-fee">fractional</a>, or <a href="custom-token-fees.md#royalty-fee">royalty</a> fees. You can set up to a maximum of 10 custom fees.</td></tr><tr><td><strong>Max Supply</strong></td><td>For tokens of type <code>FUNGIBLE_COMMON</code> - the maximum number of tokens that can be in circulation.<br>For tokens of type <code>NON_FUNGIBLE_UNIQUE</code> - the maximum number of NFTs (serial numbers) that can be minted. This field can never be changed.<br>You must set the token supply type to FINITE if you set this field.</td></tr><tr><td><strong>Supply Type</strong></td><td>Specifies the token supply type. Defaults to INFINITE.</td></tr><tr><td><strong>Freeze Default</strong></td><td>The default Freeze status (frozen or unfrozen) of Hedera accounts relative to this token. If true, an account must be unfrozen before it can receive the token.</td></tr><tr><td><strong>Expiration Time</strong></td><td>The epoch second at which the token should expire; if an auto-renew account and period are specified, this is coerced to the current epoch second plus the autoRenewPeriod. The default expiration time is 7,890,000 seconds (90 days).</td></tr><tr><td><strong>Auto Renew Account</strong></td><td>An account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval. This key is required to sign the transaction if present. Currently, rent is not enforced for tokens so auto-renew payments will not be made.</td></tr><tr><td><strong>Auto Renew Period</strong></td><td>The interval at which the auto-renew account will be charged to extend the token's expiry. The default auto-renew period is 7,890,000 seconds. Currently, rent is not enforced for tokens so auto-renew payments will not be made.<br><br><strong>NOTE:</strong> The minimum period of time is approximately 30 days (2592000 seconds) and the maximum period of time is approximately 92 days (8000001 seconds). Any other value outside of this range will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</td></tr><tr><td><strong>Memo</strong></td><td>A short publicly visible memo about the token.</td></tr><tr><td><strong>Metadata Key</strong></td><td>The key which can update the metadata of an NFT. This key is used to sign and authorize the transaction to update the metadata of dynamic NFTs. This value can be null.</td></tr><tr><td><strong>Metadata</strong></td><td>The metadata of the token. The admin key or metadata key can be used to update this property.</td></tr></tbody></table>

**Transaction Signing Requirements**

- Treasury key is required to sign
- Admin key, if specified
- Transaction fee payer key

**Transaction Fees**

- For fungible tokens, a [`CryptoTransfer`](https://docs.hedera.com/hedera/mainnet/fees) fee is added to transfer the newly created token to the treasury account
- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

<table><thead><tr><th width="447.3333333333333">Method</th><th>Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTokenName(<name>)</code></td><td>String</td><td>Required</td></tr><tr><td><code>setTokenType(<tokenType>)</code></td><td><a href="token-types.md">TokenType</a></td><td>Optional</td></tr><tr><td><code>setTokenSymbol(<symbol>)</code></td><td>String</td><td>Required</td></tr><tr><td><code>setDecimals(<decimal>)</code></td><td>int</td><td>Optional</td></tr><tr><td><code>setInitialSupply(<initialSupply>)</code></td><td>int</td><td>Optional</td></tr><tr><td><code>setTreasuryAccountId(<treasury>)</code></td><td><a href="../../deprecated/sdks/specialized-types.md#accountid">AccountId</a></td><td>Required</td></tr><tr><td><code>setAdminKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setKycKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setFreezeKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setWipeKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setSupplyKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setPauseKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setFreezeDefault(<freeze></code>)</td><td>boolean</td><td>Optional</td></tr><tr><td><code>setExpirationTime(<expirationTime>)</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>setFeeScheduleKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setCustomFees(<customFees>)</code></td><td>List<<a href="custom-token-fees.md#custom-fee">CustomFee</a>></td><td>Optional</td></tr><tr><td><code>setSupplyType(<supplyType>)</code></td><td>TokenSupplyType</td><td>Optional</td></tr><tr><td><code>setMaxSupply(<maxSupply>)</code></td><td>long</td><td>Optional</td></tr><tr><td><code>setTokenMemo(<memo>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setAutoRenewAccountId(<account>)</code></td><td><a href="../../deprecated/sdks/specialized-types.md#accountid">AccountId</a></td><td>Optional</td></tr><tr><td><code>setAutoRenewPeriod(<period>)</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code>setMetadataKey(<key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setMetadata(<bytes>)</code></td><td>bytes</td><td>Optional</td></tr></tbody></table>

{% hint style="info" %}
**Note**: Where the Admin, Pause, Freeze, and Wipe keys are left blank, the Supply key will be required as a minimum.
{% endhint %}

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
TokenCreateTransaction transaction = new TokenCreateTransaction()
        .setTokenName("Your Token Name")
        .setTokenSymbol("F")
        .setTreasuryAccountId(treasuryAccountId)
        .setInitialSupply(5000)
        .setAdminKey(adminKey.getPublicKey())
        .setMetadataKey(metadataKey)
        .setMetadata(metadata)
        .setMaxTransactionFee(new Hbar(30)); //Change the default max transaction fee

//Build the unsigned transaction, sign with admin private key of the token, sign with the token treasury private key, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(adminKey).sign(treasuryKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the token ID from the receipt
TokenId tokenId = receipt.tokenId;

System.out.println("The new token ID is " + tokenId);

//v2.0.1
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction and freeze for manual signing
const transaction = await new TokenCreateTransaction()
     .setTokenName("Your Token Name")
     .setTokenSymbol("F")
     .setTreasuryAccountId(treasuryAccountId)
     .setInitialSupply(5000)
     .setAdminKey(adminPublicKey)
     .setMetadataKey(metadataKey)
     .setMetadata(metadata)
     .setMaxTransactionFee(new Hbar(30)) //Change the default max transaction fee
     .freezeWith(client);

//Sign the transaction with the token adminKey and the token treasury account private key
const signTx =  await (await transaction.sign(adminKey)).sign(treasuryKey);

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the token ID from the receipt
const tokenId = receipt.tokenId;

console.log("The new token ID is " + tokenId);

//v2.0.5
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the transaction and freeze the unsigned transaction
tokenCreateTransaction, err := hedera.NewTokenCreateTransaction().
      SetTokenName("Your Token Name").
        SetTokenSymbol("F").
        SetTreasuryAccountID(treasuryAccountId).
        SetInitialSupply(1000).
        SetAdminKey(adminKey).
        SetMetadataKey(metadataKey).
        SetMetadata(metadata)
        SetMaxTransactionFee(hedera.NewHbar(30)). //Change the default max transaction fee
        FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the admin private key of the token, sign with the token treasury private key, sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := tokenCreateTransaction.Sign(adminKey).Sign(treasuryKey).Execute(client)

if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the token ID from the receipt
tokenId := *receipt.TokenID

fmt.Printf("The new token ID is %v\n", tokenId)

//v2.1.0
```

{% endtab %}
{% endtabs %}
