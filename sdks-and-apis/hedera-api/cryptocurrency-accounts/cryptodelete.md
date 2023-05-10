# CryptoDelete

## CryptoDeleteTransactionBody

Mark an account as deleted, moving all its current HBAR to another account. It will remain in the ledger, marked as deleted, until it expires. Transfers into it a deleted account fail. But a deleted account can still have its expiration extended in the normal way.

| Field               | Type                                     | Description                                           |
| ------------------- | ---------------------------------------- | ----------------------------------------------------- |
| `transferAccountID` | [AccountID](../basic-types/accountid.md) | The account ID which will receive all remaining hbars |
| `deleteAccountID`   | [AccountID](../basic-types/accountid.md) | The account ID which should be deleted                |
