# CryptoGetAccountRecords

## CryptoGetAccountRecordsQuery

Get all the records for an account for any transfers into it and out of it, that were above the threshold, during the last 25 hours.

| Field       | Type                                           | Description                                                                                                                                                                            |
| ----------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`    | [QueryHeader](../miscellaneous/queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `accountID` | [AccountID](../basic-types/accountid.md)       | The account ID for which the records should be retrieved                                                                                                                               |

## CryptoGetAccountRecordsResponse

Returns records of all transactions for which the given account was the effective payer in the last 3 minutes of consensus time and `ledger.keepRecordsInState=true` was true during `handleTransaction.`

| Field       | Type                                                                 | Description                                                                                                                      |
| ----------- | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `header`    | [ResponseHeader](../miscellaneous/responseheader.md)                 | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `accountID` | [AccountID](../basic-types/accountid.md)                             | The account that this record is for                                                                                              |
| `records`   | repreated [TransactionRecord](../miscellaneous/transactionrecord.md) | List of records                                                                                                                  |
