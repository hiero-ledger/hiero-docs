# TokenAssociate

Associates the provided account with the provided tokens. Must be signed by the provided Account's key.

If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.

If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.

If any of the provided tokens is not found, the transaction will resolve to `INVALID_TOKEN_REF`.

If any of the provided tokens has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.

If an association between the provided account and any of the tokens already exists, the transaction will resolve to `TOKEN_ALREADY_ASSOCIATED_TO_ACCOUNT`.

If the provided account's associations count exceeds the constraint of maximum token associations per account, the transaction will resolve to `TOKENS_PER_ACCOUNT_LIMIT_EXCEEDED`.

On success, associations between the provided account and tokens are made and the account is ready to interact with the tokens.

## TokenAssociateTransactionBody

| Field     | Type                                          | Description                                                                                                                                                                                          |
| --------- | --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `account` | [AccountID](../basic-types/accountid.md)      | The account to be associated with the provided tokens                                                                                                                                                |
| `tokens`  | repeated [TokenID](../basic-types/tokenid.md) | The tokens to be associated with the provided account. In the case of `NON_FUNGIBLE_UNIQUE` Type, once an account is associated, it can hold any number of NFTs (serial numbers) of that token type. |
