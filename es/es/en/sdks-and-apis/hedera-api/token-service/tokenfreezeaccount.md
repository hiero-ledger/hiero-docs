# TokenFreezeAccount

Freezes transfers of the specified token for the account. Must be signed by the Token's freezeKey.

If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.

If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.

If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.

If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.

If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.

If no Freeze Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_FREEZE\_KEY.

Once executed the Account is marked as Frozen and will not be able to receive or send tokens unless unfrozen. The operation is idempotent.

## TokenFreezeAccountTransactionBody

| Field     | Type                                     | Description                                                                                                                                                                     |
| --------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`   | [TokenID](../basic-types/tokenid.md)     | The token for which this account will be frozen. If token does not exist, transaction results in INVALID\_TOKEN\_ID |
| `account` | [AccountID](../basic-types/accountid.md) | The account to be frozen                                                                                                                                                        |
