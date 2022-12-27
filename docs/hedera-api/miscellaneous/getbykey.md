# GetByKey

## EntityID

the ID for a single entity (account, claim, file, or smart contract instance)

| Field        | Type                                                                                                                                          | Description                                    |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| entity       | \*\*\*\*                                                                                                                                      | The Account ID for the cryptocurrency account  |
| **one of:**  |                                                                                                                                               |                                                |
| `accountID`  | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)  | The Account ID for the cryptocurrency account  |
| `liveHash`   | LiveHash                                                                                                                                      | A uniquely identifying livehash of an acount   |
| `fileID`     | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)     | The file ID of the file                        |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | The smart contract ID that identifies instance |

## GetByKeyQuery

Get all accounts, claims, files, and smart contract instances whose associated keys include the given Key. The given Key must not be a contractID or a ThresholdKey. This is not yet implemented in the API, but will be in the future.

| Field    | Type                                                                                                                                           | Description                                                                                                                                         |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `key`    | [Key](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)         | The key to search for. It must not contain a contractID nor a ThresholdSignature.                                                                   |

## GetByKeyResponse

Response when the client sends the node GetByKeyQuery

| Field      | Type                                                                                                                                              | Description                                                                                                      |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header`   | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `entities` | EntityID                                                                                                                                          | The list of entities that include this public key in their associated Key list                                   |
