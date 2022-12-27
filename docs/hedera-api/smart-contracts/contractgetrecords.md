# ContractGetRecords

## ContractGetRecordsQuery

Get all the records for a smart contract instance, for any function call (or the constructor call) during the last 25 hours, for which a Record was requested.

| Field        | Type                                                                                                                                             | Description                                                                                                                                         |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md)  | The smart contract instance for which the records should be retrieved                                                                               |

## ContractGetRecordsResponse

Response when the client sends the node ContractGetRecordsQuery

| Field        | Type                                                                                                                                                   | Description                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `header`     | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md)    | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md)        | The smart contract instance that this record is for                                                              |
| `records`    | [TransactionRecord](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md) | List of records, each with contractCreateResult or contractCallResult as its body                                |
