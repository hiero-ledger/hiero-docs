# Update NFT metadata

A `TokenUpdateNftsTransaction` updates the metadata property of non-fungible tokens (NFTs) on the Hedera network. The transaction requires signing with the metadata key and will fail otherwise. The new metadata must be a valid byte array and is limited to 100 bytes. All transactions are recorded on the network, providing an auditable history of changes. The metadata key allows updates to existing NFTs in a collection; if no value is provided for a field, it remains unchanged.

{% hint style="warning" %}
🚨 Metadata keys, like other [token keys](define-a-token.md#token-keys), must be set during the token creation. If metadata keys are not set when the token is created, they cannot be added later, and you won't be able to update the token's metadata.
{% endhint %}

<table><thead><tr><th width="248">Property</th><th>Description</th></tr></thead><tbody><tr><td><strong>Token ID</strong></td><td>The ID of the NFT to update.</td></tr><tr><td><strong>Serial Numbers</strong></td><td>The list of serial numbers to be updated.</td></tr><tr><td><strong>Metadata</strong></td><td>The new metadata of the NFT(s).</td></tr></tbody></table>

**Transaction Signing Requirements**

- Metadata key is required to sign.
- Transaction fee payer account key.

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost.

## Methods

<table><thead><tr><th width="340">Method</th><th width="132">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTokenId(<tokenId>)</code></td><td>TokenID</td><td>Required</td></tr><tr><td><code>setSerialNumbers(<[int64]>)</code></td><td>List<int64></td><td>Required</td></tr><tr><td><code>setMetadata(<bytes>)</code></td><td>bytes</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
{% code overflow="wrap" %}

```java
// Create the transaction
TokenUpdateNftsTransaction tokenUpdateNftsTx = new TokenUpdateNftsTransaction()
    .setTokenId(tokenId)
    .setSerialNumbers(nftSerials)
    .setMetadata(newMetadata)
    .freezeWith(client);

// Sign the transaction and execute it
TransactionReceipt tokenUpdateNftsResponse = tokenUpdateNftsTx.sign(metadataKey)).execute(client);

// Get receipt for update nfts metadata transaction
TokenUpdateNftsReceipt tokenUpdateNftsReceipt = tokenUpdateNftsResponse.getReceipt(client);

// Get the transaction consensus status
Status transactionStatus = tokenUpdateNftsReceipt.status;

// Print the token update metadata transaction status
System.out.println("Token metadata update status: " + transactionStatus);

//v2.32.0
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code overflow="wrap" %}

```javascript
// Create the transaction
const tokenUpdateNftsTx = await new TokenNftsUpdateTransaction()
    .setTokenId(tokenId)
    .setSerialNumbers([nftSerials])
    .setMetadata(newMetadata)
    .freezeWith(client);

// Sign and execute the transaction with the metadata key
const tokenUpdateNftsResponse = await ( 
    await tokenUpdateNftsTx.sign(metadataKey)
).execute(client);

// Get receipt for token update nfts metadata transaction and log the status
const tokenUpdateNftsReceipt = await tokenUpdateNftsResponse.getReceipt(client);

// Log the token nft update transaction status
console.log(
    `Token metadata update status: ${tokenUpdateNftsTxReceipt.status.toString()}`,
);

//v2.45.0
```

{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code overflow="wrap" %}

```go
//Create the transaction and freeze for manual signing 
tokenUpdateNftsTransaction, err := hedera.NewTokenUpdateNftsTransaction().
      SetTokenID(tokenId).
      SetSerialNumbers(nftSerials).
      SetMetadata(newMetadata).
      FreezeWith(client)
if err != nil {
    panic(err)
}

// Sign and execute the transaction
tokenUpdateNftsResponse, err := tokenUpdateNftsTx.Sign(metadataKey).Execute(client)
if err != nil {
    panic(err)
}

// Request the receipt of the transaction
receipt, err :=tokenUpdateNftsResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

// Print the token update metadata transaction status
fmt.Printf("Token metadata update status: ", receipt.Status)

//v2.37.0
```

{% endcode %}
{% endtab %}
{% endtabs %}

## FAQs

<details>

<summary>What is the transaction fee to update a token's metadata?</summary>

The transaction fee to update the metadata of 1 NFT is `$0.001` To update metadata for multiple NFTs in a single call is N x `$0.001` (N being the number of NFTs to update). See the full list of token transaction fees [here](../../../networks/mainnet/fees/#token-service).

</details>

<details>

<summary>What happens if I forget to add metadata keys during token creation?</summary>

If you don't set metadata keys during token creation, you won't be able to add them later or use them to update the token's metadata.

</details>

<details>

<summary>Are metadata keys required for all token types?</summary>

No, metadata keys are not required for all token types. If your use case will need the ability to update the metadata in the future, the metadata key must be set during token creation. [HIP-646](https://hips.hedera.com/hip/hip-646) introduces the token metadata field for fungible tokens, providing users the ability to update metadata for both token types (fungible and non-fungible) using the metadata key.

</details>

<details>

<summary>Can I still create a token without metadata keys?</summary>

Yes, you can create a token without metadata keys but you won't be able to add metadata keys or update the token's metadata.

</details>

<details>

<summary>Is it possible to remove metadata keys from a token after it has been created?</summary>

No, once a token is created with metadata keys, those keys become a permanent part of the token's configuration. They cannot be removed or modified after the token creation.

</details>

<details>

<summary>Can the metadata key update the token metadata if the token is <code>paused</code> ?</summary>

No, this is just like a regular [TokenUpdate](update-a-token.md). It will fail if token is paused.

</details>

<details>

<summary>Can NFT metadata be updated if the asset is held by an account that is <code>frozen</code> for operations with that token?</summary>

If the `tokenId` of the NFT is not paused, and if the token has `metadataKey` the metadata of NFT can still be updated.

</details>

- Reference: [HIP-657](https://hips.hedera.com/hip/hip-657), [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-765](https://hips.hedera.com/hip/hip-765)

***

**Contributors:** [MilanWR](https://github.com/MilanWR)
