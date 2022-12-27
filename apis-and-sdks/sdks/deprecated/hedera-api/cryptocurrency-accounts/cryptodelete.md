# CryptoDelete

## CryptoDeleteTransactionBody

Mark an account as deleted, moving all its current hbars to another account. It will remain in the ledger, marked as deleted, until it expires. Transfers into it a deleted account fail. But a deleted account can still have its expiration extended in the normal way.

| Field               | Type                                                                                                                                                   | Description                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------- |
| `transferAccountID` | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | The account ID which will receive all remaining hbars |
| `deleteAccountID`   | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | The account ID which should be deleted                |

##
