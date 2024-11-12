# Update a token

A transaction that updates the properties of an existing token. The admin key must sign this transaction to update any of the token properties. With one exception. All secondary keys can sign a transaction to change themselves. The admin key can update exisitng keys, but cannot add new keys if they were not set during the creation of the token. If no value is given for a field, that field is left unchanged. If a TokenUpdateTx is performed we will keep returning `TOKEN_IS_IMMUTABLE` when it tries to change non-key properties or the expiry without an admin key.&#x20;

<table><thead><tr><th width="284">Property</th><th>Description</th></tr></thead><tbody><tr><td><strong>Name</strong></td><td>The new name of the token. The token name is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (<code>NUL</code>). Is not required to be unique.</td></tr><tr><td><strong>Symbol</strong></td><td>The new symbol of the token. The token symbol is specified as a string of UTF-8 characters in Unicode. UTF-8 encoding of this Unicode cannot contain the 0 byte (<code>NUL</code>). Is not required to be unique.</td></tr><tr><td><strong>Treasury Account</strong></td><td>The new treasury account of the token. If the provided treasury account is not existing or deleted, the response will be <code>INVALID_TREASURY_ACCOUNT_FOR_TOKEN</code>. If successful, the Token balance held in the previous Treasury Account is transferred to the new one.</td></tr><tr><td><strong>Admin Key</strong></td><td>The new admin key of the token. If the token is immutable (no Admin Key was assigned during token creation), the transaction will resolve to <code>TOKEN_IS_IMMUTABlE</code>. Admin keys cannot update to add new keys that were not specified during the creation of the token.</td></tr><tr><td><strong>KYC Key</strong></td><td>The new KYC key of the token. If the token does not have currently a KYC key, the transaction will resolve to <code>TOKEN_HAS_NO_KYC_KEY</code>.</td></tr><tr><td><strong>Freeze Key</strong></td><td>The new freeze key of the token. If the token does not have currently a freeze key, the transaction will resolve to <code>TOKEN_HAS_NO_FREEZE_KEY</code>.</td></tr><tr><td><strong>Fee Schedule Key</strong></td><td>If set, the new key to use to update the token's custom fee schedule; if the token does not currently have this key, transaction will resolve to <code>TOKEN_HAS_NO_FEE_SCHEDULE_KEY</code></td></tr><tr><td><strong>Pause Key</strong></td><td>Update the token's existing pause key. The pause key has the ability to pause or unpause a token.</td></tr><tr><td><strong>Wipe Key</strong></td><td>The new wipe key of the token. If the token does not have currently a wipe key, the transaction will resolve to <code>TOKEN_HAS_NO_WIPE_KEY</code>.</td></tr><tr><td><strong>Supply Key</strong></td><td>The new supply key of the token. If the token does not have currently a supply key, the transaction will resolve to <code>TOKEN_HAS_NO_SUPPLY_KEY</code>.</td></tr><tr><td><strong>Expiration Time</strong></td><td>The new expiry time of the token. Expiry can be updated even if the admin key is not set. If the provided expiry is earlier than the current token expiry, the transaction will resolve to <code>INVALID_EXPIRATION_TIME</code>.</td></tr><tr><td><strong>Auto Renew Account</strong></td><td>The new account which will be automatically charged to renew the token's expiration, at autoRenewPeriod interval.</td></tr><tr><td><strong>Auto Renew Period</strong></td><td>The new interval at which the auto-renew account will be charged to extend the token's expiry. The default auto-renew period is 7,890,000 seconds. Currently, rent is not enforced for tokens so auto-renew payments will not be made.<br><br><strong>NOTE:</strong> The minimum period of time is approximately 30 days (2592000 seconds) and the maximum period of time is approximately 92 days (8000001 seconds). Any other value outside of this range will return the following error: <code>AUTORENEW_DURATION_NOT_IN_RANGE</code>.</td></tr><tr><td><strong>Memo</strong></td><td>Short publicly visible memo about the token. No guarantee of uniqueness. (100 characters max)</td></tr><tr><td><strong>Metadata Key</strong></td><td>The desired new metadata key for the token. This value can be null.</td></tr><tr><td><strong>Metadata</strong></td><td>The desired new metadata for the token. This value can be null. The admin key or metadata key can be used to update this property.</td></tr></tbody></table>

{% hint style="info" %}
The sender pays the `max_auto_associations` fee and the rent for the first auto-renewal period for the association. This is in addition to the typical transfer fees. This ensures the receiver can receive token without association and makes it a smoother transfer process
{% endhint %}

#### Transaction Signing Requirements

* Admin key is required to sign to update any token properties. (except for the expiry and all low priority keys)
* A secondary key can update itself. Meaning it can sign a transaction that changes itself. Example: Wipe Key can sign a transaction that changes only the Wipe Key
* Updating the admin key requires the new admin key to sign.
* If a new treasury account is set, the new treasury key is required to sign.
* The account that is paying for the transaction fee.

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost.

## Methods

<table><thead><tr><th width="438">Method</th><th width="180.33333333333331">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTokenId(&#x3C;tokenId>)</code></td><td><a href="token-id.md">TokenId</a></td><td>Required</td></tr><tr><td><code>setTokenName(&#x3C;name>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setTokenSymbol(&#x3C;symbol>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setTreasuryAccountId(&#x3C;treasury>)</code></td><td><a href="../../deprecated/sdks/specialized-types.md#accountid">AccountId</a></td><td>Optional</td></tr><tr><td><code>setAdminKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setKycKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setFreezeKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setFeeScheduleKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setPauseKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setWipeKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setSupplyKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setExpirationTime(&#x3C;expirationTime>)</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>setTokenMemo(&#x3C;memo>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setAutoRenewAccountId(&#x3C;account>)</code></td><td><a href="../../deprecated/sdks/specialized-types.md#accountid">AccountId</a></td><td>Optional</td></tr><tr><td><code>setAutoRenewPeriod(&#x3C;period>)</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code>setMetadataKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setMetadata(&#x3C;bytes>)</code></td><td>bytes</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction 
TokenUpdateTransaction transaction = new TokenUpdateTransaction()
     .setTokenId(tokenId)
     .setMetadataKey(metadataKey)
     .setMetadata(newMetadata)
     .setTokenName("Your New Token Name");

//Freeze the unsigned transaction, sign with the admin private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(adminKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction and freeze for manual signing
const transaction = await new TokenUpdateTransaction()
     .setTokenId(tokenId)
     .setMetadataKey(metadataKey)
     .setMetadata(newMetadata)
     .setTokenName("Your New Token Name")
     .freezeWith(client);

//Sign the transaction with the admin key
const signTx = await transaction.sign(adminKey);

//Submit the signed transaction to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status.toString();

console.log("The transaction consensus status is " +transactionStatus);

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction and freeze for manual signing 
tokenUpdateTransaction, err := hedera.NewTokenUpdateTransaction().
      SetTokenID(tokenId).
      SetMetadataKey(metadataKey).
      SetMetadata(newMetadata).
      SetTokenName("Your New Token Name").
      FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign with the admin private key of the token, sign with the client operator private key and submit the transaction to a Hedera network
txResponse, err := tokenUpdateTransaction.Sign(adminKey).Execute(client)

if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)

//v2.1.0
```
{% endtab %}
{% endtabs %}
