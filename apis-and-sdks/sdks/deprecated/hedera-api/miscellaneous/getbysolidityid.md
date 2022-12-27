# GetBySolidityID

## GetBySolidityIDQuery

Get the IDs in the format used by transactions, given the ID in the format used by Solidity. If the Solidity ID is for a smart contract instance, then both the ContractID and associated AccountID will be returned.

| Field        | Type                                                                                                                                           | Description                                                                                                                                         |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `solidityID` | string                                                                                                                                         | The ID in the format used by Solidity                                                                                                               |

## GetBySolidityIDResponse

Response when the client sends the node GetBySolidityIDQuery

| Field        | Type                                                                                                                                              | Description                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header`     | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `accountID`  | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)      | The Account ID for the cryptocurrency account                                                                    |
| `fileID`     | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)         | The file Id for the file                                                                                         |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)     | A smart contract ID for the instance (if this is included, then the associated accountID will also be included)  |
