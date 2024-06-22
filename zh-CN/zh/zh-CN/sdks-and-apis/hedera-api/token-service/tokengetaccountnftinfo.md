# TokenGetAccountNftInfo

{% hint style="warning" %}
This query is currently deprecated.
{% endhint %}

## TokenGetAccountNftInfoQuery

Applicable only to tokens of type `NON_FUNGIBLE_UNIQUE`. Gets info on NFTs N through M owned by the specified accountId. Example: If Account A owns 5 NFTs (might be of different Token Entity), having start=0 and end=5 will return all of the NFTs

| Field       | Type                                           | Description                                                                                                                                                                                                   |
| ----------- | ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`    | [QueryHeader](../miscellaneous/queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither)                                         |
| `accountID` | [AccountID](../basic-types/accountid.md)       | The Account for which information is requested                                                                                                                                                                |
| `start`     | int64                                          | Specifies the start index (inclusive) of the range of NFTs to query for. Value must be in the range \[0; ownedNFTs-1] |
| `end`       | int64                                          | Specifies the end index (exclusive) of the range of NFTs to query for. Value must be in the range (start; ownedNFTs]          |

## TokenGetAccountNftInfoResponse

Response when the client sends the node TokenGetInfoQuery

| Field    | Type                                                     | Description                                                                                                                      |
| -------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [ResponseHeader](../miscellaneous/responseheader.md)     | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `nfts`   | repeated [TokenNftInfo](tokengetnftinfo.md#tokennftinfo) | List of NFTs associated to the account                                                                                           |
