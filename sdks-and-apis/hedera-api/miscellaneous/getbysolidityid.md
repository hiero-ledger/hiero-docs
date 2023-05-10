# GetBySolidityID

## GetBySolidityIDQuery

Get the IDs in the format used by transactions, given the ID in the format used by Solidity. If the Solidity ID is for a smart contract instance, then both the ContractID and associated AccountID will be returned.

| Field        | Type                          | Description                                                                                                                                         |
| ------------ | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | [QueryHeader](queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `solidityID` | string                        | The ID in the format used by Solidity                                                                                                               |

## GetBySolidityIDResponse

Response when the client sends the node GetBySolidityIDQuery

| Field        | Type                                       | Description                                                                                                      |
| ------------ | ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `header`     | [ResponseHeader](responseheader.md)        | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `accountID`  | [AccountID](../basic-types/accountid.md)   | The Account ID for the cryptocurrency account                                                                    |
| `fileID`     | [FileID](../basic-types/fileid.md)         | The file Id for the file                                                                                         |
| `contractID` | [ContractID](../basic-types/contractid.md) | A smart contract ID for the instance (if this is included, then the associated accountID will also be included)  |
