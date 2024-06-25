# ContractGetByteCode

## ContractGetBytecodeQuery

Get the bytecode for a smart contract instance

| Field        | Type                                           | Description                                                                                                                                                                            |
| ------------ | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | [QueryHeader](../miscellaneous/queryheader.md) | standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `contractID` | [ContractID](../basic-types/contractid.md)     | the contract for which information is requested                                                                                                                                        |

## ContractGetBytecodeResponse

Response when the client sends the node ContractGetBytecodeQuery

| Field      | Type                                                 | Description                                                                                                                      |
| ---------- | ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `header`   | [ResponseHeader](../miscellaneous/responseheader.md) | standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `bytecode` | bytes                                                | the bytecode                                                                                                                     |
