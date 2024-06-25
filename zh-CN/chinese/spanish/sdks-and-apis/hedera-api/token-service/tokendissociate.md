# TokenDissociate

Dissociates the provided account with the provided tokens. Must be signed by the provided Account's key.

If the provided account is not found, the transaction will resolve to `INVALID_ACCOUNT_ID`.

If the provided account has been deleted, the transaction will resolve to `ACCOUNT_DELETED`.

If any of the provided tokens is not found, the transaction will resolve to `INVALID_TOKEN_REF`.

If any of the provided tokens has been deleted, the transaction will resolve to `TOKEN_WAS_DELETED`.

If an association between the provided account and any of the tokens does not exist, the transaction will resolve to `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`.

If the provided account has a nonzero balance with any of the provided tokens, the transaction will resolve to `TRANSACTION_REQUIRES_ZERO_TOKEN_BALANCES`.

## TokenDissociateTransactionBody

| Field     | Type                                          | Description                                            |
| --------- | --------------------------------------------- | ------------------------------------------------------ |
| `account` | [AccountID](../basic-types/accountid.md)      | The account to be dissociated with the provided tokens |
| `tokens`  | repeated [TokenID](../basic-types/tokenid.md) | The tokens to be dissociated with the provided account |
