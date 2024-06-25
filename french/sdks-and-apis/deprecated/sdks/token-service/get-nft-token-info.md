# Get NFT info

A query that returns information about a non-fungible token (NFT). You request the info for an NFT by specifying the NFT ID.

**Token Allowances**

Only when a spender is set on an explicit NFT ID of a token, we return the spender ID in the`TokenNftInfoQuery` for the respective NFT. If `approveTokenNftAllowanceAllSerials` is used to approve all NFTs for a given token class and no NFT ID is specified, we will not return a spender ID for all the serial numbers of that token.

**Query Fees**

* Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

{% hint style="warning" %}
Requesting NFT info by Token ID or Account ID is deprecated.
{% endhint %}

The request returns the following information:

| Item              | Description                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------- |
| **NFT ID**        | The ID of the non-fungible token in x.y.z format.                                                               |
| **Account ID**    | The account ID of the current owner of the NFT                                                                  |
| **Creation Time** | The effective consensus timestamp at which the NFT was minted                                                   |
| **Metadata**      | Represents the unique metadata of the NFT                                                                       |
| **Ledger ID**     | The ID of the network (mainnet, testnet, previewnet). Reference [HIP-198](https://hips.hedera.com/hip/hip-198). |
| **Spender ID**    | The spender account ID for the NFT. This is only returned if the NFT ID was specifically approved.              |

| Constructor               | Description                              |
| ------------------------- | ---------------------------------------- |
| `new TokenNftInfoQuery()` | Initializes the TokenNftInfoQuery object |

```java
new TokenNftInfoQuery()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method             | Type                                           | Description                                                                                                                                        | Requirement |
| ------------------ | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `byNftId(<nftId>)` | [NftId](../../../sdks/token-service/nft-id.md) | Applicable only to tokens of type `NON_FUNGIBLE_UNIQUE`. Gets info on a NFT for a given TokenID (of type `NON_FUNGIBLE_UNIQUE`) and serial number. | Optional    |

{% code title="Java" %}
```java
//Returns the info for the specified NFT ID
List<TokenNftInfo> nftInfos = new TokenNftInfoQuery()
     .byNftId(nftId)
     .execute(client);
     
//v1.5.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Returns the info for the specified NFT ID
const nftInfos = await new TokenNftInfoQuery()
     .byNftId(nftId)
     .execute(client);

//v1.4.10
```
{% endcode %}
{% endtab %}
{% endtabs %}
