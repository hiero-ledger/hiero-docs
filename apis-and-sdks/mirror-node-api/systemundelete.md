# SystemUndelete

## SystemUndeleteTransactionBody

Undelete a file or smart contract that was deleted by SystemDelete; requires a Hedera administrative multisignature.

| Field        | Type                                                                                                                                          | Description                                                              |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `id`         |                                                                                                                                               |                                                                          |
| **one of:**  |                                                                                                                                               |                                                                          |
| `fileID`     | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md)     | The file ID to undelete, in the format used in transactions              |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/miscellaneous/broken-reference/README.md) | The contract ID instance to undelete, in the format used in transactions |
