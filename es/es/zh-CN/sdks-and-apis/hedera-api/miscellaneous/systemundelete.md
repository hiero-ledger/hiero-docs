# SystemUndelete

## SystemUndeleteTransactionBody

Undelete a file or smart contract that was deleted by SystemDelete; requires a Hedera administrative multisignature.

| Field        | Type                                       | Description                                                              |
| ------------ | ------------------------------------------ | ------------------------------------------------------------------------ |
| `fileID`     | [FileID](../basic-types/fileid.md)         | The file ID to undelete, in the format used in transactions              |
| `contractID` | [ContractID](../basic-types/contractid.md) | The contract ID instance to undelete, in the format used in transactions |
