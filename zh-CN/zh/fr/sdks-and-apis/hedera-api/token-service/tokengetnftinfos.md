# TokenGetNftInfos

{% hint style="warning" %}
This query is deprecated.
{% endhint %}

## TokenGetNftInfosQuery

Applicable only to tokens of type `NON_FUNGIBLE_UNIQUE`. Gets info on NFTs N through M on the list of NFTs associated with a given `NON_FUNGIBLE_UNIQUE` Token. Example: If there are 10 NFTs issued, having start=0 and end=5 will query for the first 5 NFTs. Querying +all 10 NFTs will require start=0 and end=10.

| Field     | Type                                           | Description                                                                                                                                                                                                   |
| --------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`  | [QueryHeader](../miscellaneous/queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither)                                         |
| `tokenID` | [TokenID](../basic-types/tokenid.md)           | The ID of the token for which information is requested                                                                                                                                                        |
| `start`   | int64                                          | Specifies the start index (inclusive) of the range of NFTs to query for. Value must be in the range \[0; ownedNFTs-1] |
| `end`     | int64                                          | Specifies the end index (exclusive) of the range of NFTs to query for. Value must be in the range (start; ownedNFTs]          |

## TokenGetNftInfosResponse

| Field     | Type                                                     | Description                                                                                                                      |
| --------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `header`  | [ResponseHeader](../miscellaneous/responseheader.md)     | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `tokenID` | [TokenID](../basic-types/tokenid.md)                     | The Token with type `NON_FUNGIBLE` that this record is for                                                                       |
| `nfts`    | repeated [TokenNftInfo](tokengetnftinfo.md#tokennftinfo) | List of NFTs associated to the specified token                                                                                   |
