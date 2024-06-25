# TokenUnpause

Unpauses the Token. Must be signed with the Token's pause key.

* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED
* If no Pause Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_PAUSE\_KEY
* Once executed the Token is marked as Unpaused and can be used in Transactions
* The operation is idempotent - becomes a no-op if the Token is already unpaused

## TokenUnpauseTransactionBody

| Field   | Type                                 | Description              |
| ------- | ------------------------------------ | ------------------------ |
| `token` | [TokenID](../basic-types/tokenid.md) | The token to be unpaused |
