# TokenGetNftInfo

## NftID

Represents an NFT on the Ledger.

| Field          | Type                                 | Description                                               |
| -------------- | ------------------------------------ | --------------------------------------------------------- |
| `tokenID`      | [TokenID](../basic-types/tokenid.md) | The (non-fungible) token of which this NFT is an instance |
| `serialNumber` | int64                                | The unique identifier of this instance                    |

## TokenGetNftInfoQuery

Applicable only to tokens of type `NON_FUNGIBLE_UNIQUE`. Gets info on a NFT for a given TokenID (of type `NON_FUNGIBLE_UNIQUE`) and serial number.

| Field    | Type                                           | Description                                                                                                                                        |
| -------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [QueryHeader](../miscellaneous/queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither) |
| `nftID`  | [NftID](tokengetnftinfo.md#nftid)              | The ID of the NFT                                                                                                                                  |

## TokenNftInfo

| Field          | Type                                       | Description                                                                                                                          |
| -------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `nftID`        | [NftID](tokengetnftinfo.md#nftid)          | The ID of the NFT                                                                                                                    |
| `accountID`    | [AccountID](../basic-types/accountid.md)   | The current owner of the NFT                                                                                                         |
| `creationTime` | [Timestamp](../miscellaneous/timestamp.md) | The effective consensus timestamp at which the NFT was minted                                                                        |
| `metadata`     | bytes                                      | Represents the unique metadata of the NFT                                                                                            |
| `ledger_id`    | bytes                                      | The ledger ID the response was returned from; please see [HIP-198](https://hips.hedera.com/hip/hip-198) for the network-specific IDs |
| `spender_id`   | AccountID                                  | If an allowance is granted for the NFT, its corresponding spender account                                                            |

## TokenGetNftInfoResponse

| Field    | Type                                                 | Description                                                                                                      |
| -------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header` | [ResponseHeader](../miscellaneous/responseheader.md) | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `nft`    | [TokenNftInfo](tokengetnftinfo.md#tokennftinfo)      | The information about this NFT                                                                                   |
