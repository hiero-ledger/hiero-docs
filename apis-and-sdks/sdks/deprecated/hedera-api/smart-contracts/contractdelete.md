# ContractDelete

## **ContractDeleteTransactionBody**

Delete a smart contract.

| Field                | Type                                                                                                                                            | Description                                                |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `contractID`         | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md) | The Contract ID instance to delete (this can't be changed) |
| `obtainers`          |                                                                                                                                                 |                                                            |
| **one of:**          |                                                                                                                                                 |                                                            |
| `transferAccountID`  | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md)  | The account ID which will receive all remaining hbars      |
| `transferContractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/smart-contracts/broken-reference/README.md) | The contract ID which will receive all remaining hbars     |
