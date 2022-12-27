# CryptoGetAccountRecords

## CryptoGetAccountRecordsQuery

Get all the records for an account for any transfers into it and out of it, that were above the threshold, during the last 25 hours.

| Field       | Type                                                                                                                                                     | Description                                                                                                                                         |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`    | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `accountID` | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)   | The account ID for which the records should be retrieved                                                                                            |

## CryptoGetAccountRecordsResponse

Returns records of all transactions for which the given account was the effective payer in the last 3 minutes of consensus time and `ledger.keepRecordsInState=true` was true during `handleTransaction.`

| Field       | Type                                                                                                                                                                     | Description                                                                                                      |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `header`    | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)              | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `accountID` | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)                   | The account that this record is for                                                                              |
| `records`   | repreated [TransactionRecord](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | List of records                                                                                                  |
