# Get NFT info

A query that returns information about a non-fungible token (NFT). You request the info for an NFT by specifying the NFT ID.

**Token Allowances**

Only when a spender is set on an explicit NFT ID of a token, we return the spender ID in the`TokenNftInfoQuery` for the respective NFT. If `approveTokenNftAllowanceAllSerials` is used to approve all NFTs for a given token class and no NFT ID is specified, we will not return a spender ID for all the serial numbers of that token.

**Query Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

{% hint style="warning" %}
Requesting NFT info by Token ID or Account ID is deprecated.
{% endhint %}

The request returns the following information:

| Item              | Description                                                                                                                                                        |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **NFT ID**        | The ID of the non-fungible token in x.y.z format.                                                                  |
| **Account ID**    | The account ID of the current owner of the NFT                                                                                                                     |
| **Creation Time** | The effective consensus timestamp at which the NFT was minted                                                                                                      |
| **Metadata**      | Represents the unique metadata of the NFT                                                                                                                          |
| **Ledger ID**     | The ID of the network (mainnet, testnet, previewnet). Reference [HIP-198](https://hips.hedera.com/hip/hip-198). |
| **Spender ID**    | The spender account ID for the NFT. This is only returned if the NFT ID was specifically approved.                                 |

### Methods

| Method              | Type               | Description                                                                                                                                                                                           | Requirement |
| ------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setNftId(<nftId>)` | [NftId](nft-id.md) | Applicable only to tokens of type `NON_FUNGIBLE_UNIQUE`. Gets info on a NFT for a given TokenID (of type `NON_FUNGIBLE_UNIQUE`) and serial number. | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Returns the info for the specified NFT ID
List<TokenNftInfo> nftInfos = new TokenNftInfoQuery()
     .setNftId(nftId)
     .execute(client);

//v2.0.14
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Returns the info for the specified NFT ID
const nftInfos = await new TokenNftInfoQuery()
     .setNftId(nftId)
     .execute(client);

//v2.0.28
```

{% endtab %}

{% tab title="Go" %}

```go
//Returns the info for the specified NFT ID
nftInfo, err := NewTokenNftInfoQuery().
   SetNftID(nftID).
	 Execute(client)

//v2.1.16
```

{% endtab %}
{% endtabs %}
