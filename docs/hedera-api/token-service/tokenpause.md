# TokenPause

Pauses the Token from being involved in any kind of Transaction until it is unpaused.

* Must be signed with the Token's pause key.
* If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.
* If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.
* If no Pause Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_PAUSE\_KEY.
* Once executed the Token is marked as paused and will be not able to be a part of any transaction.

## TokenPauseTransactionBody

| Field   | Type                                                                                                                                       | Description            |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------- |
| `token` | [TokenID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/broken-reference/README.md) | The token to be paused |
