# Create a token

{% hint style="info" %}
Check out "Getting Started with the Hedera Token Service" video tutorial in JavaScript [here](https://youtu.be/lp3mwdYEZEk).
{% endhint %}

Create a new fungible or non-fungible token (NFT) on the Hedera network. After you submit the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt.

You can also create, access, or transfer HTS tokens using smart contracts - see [Hedera Service Solidity Libraries](https://docs.hedera.com/guides/docs/sdks/smart-contracts/hedera-service-solidity-libraries) and [Supported ERC Token Standards](https://docs.hedera.com/guides/core-concepts/smart-contracts/supported-erc-token-standards).

{% hint style="warning" %}
**Token Keys**

- If any of the token key types (KYC key, Wipe key, etc) are not set during the creation of the token, you will not be able to update the token and add them in the future
- If any of the token key types (KYC key, Wipe key, etc) are set during the creation of the token, you will not be able to remove them in the future
  {% endhint %}

**NFTs**

For non-fungible tokens, the token ID represents a NFT class. Once the token is created, you will have to mint each NFT using the [token mint](../token-service/mint-a-token.md) operation.

{% hint style="warning" %}
Note: It is required to set the initial supply for an NFT to 0.
{% endhint %}

**Token Properties**

| Property               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**               | Set the publicly visible name of the token. The token name is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (`NUL`). The token name is not unique. Maximum of 100 characters.                                                                                                                                                                                |
| **Token Type**         | The type of token to create. Either fungible or non-fungible.                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **Symbol**             | The publicly visible token symbol. Set the publicly visible name of the token. The token symbol is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (`NUL`). The token symbol is not unique. Maximum of 100 characters.                                                                                                                         |
| **Decimal**            | The number of decimal places a token is divisible by. This field can never be changed.                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Initial Supply**     | Specifies the initial supply of fungible tokens to be put in circulation. The initial supply is sent to the Treasury Account. The maximum supply of tokens is `9,223,372,036,854,775,807`(`2^63-1`) tokens and is in the lowest denomination possible. For creating an NFT, you must set the initial supply to 0.                                                                                                                             |
| **Treasury Account**   | The account which will act as a treasury for the token. This account will receive the specified initial supply and any additional tokens that are minted. If tokens are burned, the supply will decreased from the treasury account.                                                                                                                                                                                                                                             |
| **Admin Key**          | The key which can perform token update and token delete operations on the token. The admin key has the authority to change the supply key, freeze key, pause key, wipe key, and KYC key. It can also update the treasury account of the token. If empty, the token can be perceived as immutable (not being able to be updated/deleted).                                                                                                      |
| **KYC Key**            | The key which can grant or revoke KYC of an account for the token's transactions. If empty, KYC is not required, and KYC grant or revoke operations are not possible.                                                                                                                                                                                                                                                                                                                            |
| **Freeze Key**         | The key which can sign to freeze or unfreeze an account for token transactions. If empty, freezing is not possible.                                                                                                                                                                                                                                                                                                                                                                              |
| **Wipe Key**           | The key which can wipe the token balance of an account. If empty, wipe is not possible.                                                                                                                                                                                                                                                                                                                                                                                                          |
| **Supply Key**         | The key which can change the total supply of a token. This key is used to authorize token mint and burn transactions. If this is left empty, minting/burning tokens is not possible.                                                                                                                                                                                                                                                                                             |
| **Fee Schedule Key**   | The key which can change the token's [custom fee](custom-token-fees.md) schedule. It must sign a TokenFeeScheduleUpdate transaction. A custom fee schedule token without a fee schedule key is immutable.                                                                                                                                                                                                                                                                        |
| **Pause Key**          | The key which has the authority to pause or unpause a token. Pausing a token prevents the token from participating in all transactions.                                                                                                                                                                                                                                                                                                                                                          |
| **Custom Fees**        | [Custom fees](custom-token-fees.md) to charge during a token transfer transaction that transfers units of this token. Custom fees can either be [fixed](custom-token-fees.md#fixed-fee), [fractional](custom-token-fees.md#fractional-fee), or [royalty](custom-token-fees.md#royalty-fee) fees. You can set up to a maximum of 10 custom fees.                                                                                                                                  |
| **Max Supply**         | <p>For tokens of type <code>FUNGIBLE_COMMON</code> - the maximum number of tokens that can be in circulation.<br>For tokens of type <code>NON_FUNGIBLE_UNIQUE</code> - the maximum number of NFTs (serial numbers) that can be minted. This field can never be changed.<br>You must set the token supply type to FINITE if you set this field.</p>                                                                                                                                                                               |
| **Supply Type**        | Specifies the token supply type. Defaults to INFINITE.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Freeze Default**     | The default Freeze status (frozen or unfrozen) of Hedera accounts relative to this token. If true, an account must be unfrozen before it can receive the token.                                                                                                                                                                                                                                                                                                               |
| **Expiration Time**    | The epoch second at which the token should expire; if an auto-renew account and period are specified, this is coerced to the current epoch second plus the autoRenewPeriod. The default expiration time is 7,890,000 seconds (90 days).                                                                                                                                                                                                                                       |
| **Auto Renew Account** | An account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval. This key is required to sign the transaction if present. Currently, rent is not enforced for tokens so auto-renew payments will not be made.                                                                                                                                                                                                                        |
| **Auto Renew Period**  | <p>The interval at which the auto-renew account will be charged to extend the token's expiry. The default auto-renew period is 7,890,000 seconds. Currently, rent is not enforced for tokens so auto-renew payments will not be made.<br><br><strong>NOTE:</strong> The minimum period of time is approximately 30 days (2592000 seconds) and the maximum period of time is approximately 92 days (8000001 seconds). Any other value outside of this range will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</p> |
| **Memo**               | A short publicly visible memo about the token.                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

**Transaction Signing Requirements**

- Treasury key is required to sign
- Admin key, if specified
- Transaction fee payer key

**Transaction Fees**

- For fungible tokens, a [`CryptoTransfer`](https://docs.hedera.com/hedera/mainnet/fees) fee is added to transfer the newly created token to the treasury account
- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

## Methods

| Method                                | Type                                               | Requirement |
| ------------------------------------- | -------------------------------------------------- | ----------- |
| `setTokenName(<name>)`                | String                                             | Required    |
| `setTokenType(<tokenType>)`           | [TokenType](token-types.md)                        | Optional    |
| `setTokenSymbol(<symbol>)`            | String                                             | Required    |
| `setDecimals(<decimal>)`              | int                                                | Optional    |
| `setInitialSupply(<initialSupply>)`   | int                                                | Optional    |
| `setTreasuryAccountId(<treasury>)`    | [AccountId](../specialized-types.md#accountid)     | Required    |
| `setAdminKey(<key>)`                  | Key                                                | Optional    |
| `setKycKey(<key>)`                    | Key                                                | Optional    |
| `setFreezeKey(<key>)`                 | Key                                                | Optional    |
| `setWipeKey(<key>)`                   | Key                                                | Optional    |
| `setSupplyKey(<key>)`                 | Key                                                | Optional    |
| `setPauseKey(<key>)`                  | Key                                                | Optional    |
| `setFreezeDefault(<freeze>`)          | boolean                                            | Optional    |
| `setExpirationTime(<expirationTime>)` | Instant                                            | Optional    |
| `setFeeScheduleKey(<key>)`            | Key                                                | Optional    |
| `setCustomFees(<customFees>)`         | List<[CustomFee](custom-token-fees.md#custom-fee)> | Optional    |
| `setSupplyType(<supplyType>)`         | TokenSupplyType                                    | Optional    |
| `setMaxSupply(<maxSupply>)`           | long                                               | Optional    |
| `setTokenMemo(<memo>)`                | String                                             | Optional    |
| `setAutoRenewAccountId(<account>)`    | [AccountId](../specialized-types.md#accountid)     | Optional    |
| `setAutoRenewPeriod(<period>)`        | Duration                                           | Optional    |

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
