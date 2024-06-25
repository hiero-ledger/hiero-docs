# TransactionGetFastRecord

## TransactionGetFastRecordQuery

Get the tx record of a transaction, given its transaction ID. Once a transaction reaches consensus, then information about whether it succeeded or failed will be available until the end of the receipt period. Before and after the receipt period, and for a transaction that was never submitted, the receipt is unknown. This query is free (the payment field is left empty).

| Field           | Type                                             | Description                                                                                                                                         |
| --------------- | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`        | [QueryHeader](queryheader.md)                    | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `transactionID` | [TransactionID](../basic-types/transactionid.md) | The ID of the transaction for which the record is requested.                                                                                        |

## TransactionGetFastRecordResponse

Response when the client sends the node TransactionGetFastRecordQuery. If it created a new entity (account, file, or smart contract instance) then one of the three ID fields will be filled in with the ID of the new entity. Sometimes a single transaction will create more than one new entity, such as when a new contract instance is created, and this also creates the new account that it owned by that instance.

| Field               | Type                                      | Description                                                                                                      |
| ------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header`            | [ResponseHeader](responseheader.md)       | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `transactionRecord` | [TransactionRecord](transactionrecord.md) | The requested transaction records                                                                                |
