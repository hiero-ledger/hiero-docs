# TokenBurn

Burns tokens from the Token's treasury Account. If no Supply Key is defined, the transaction will resolve to `TOKEN_HAS_NO_SUPPLY_KEY`.

The operation decreases the Total Supply of the Token. Total supply cannot go below zero.

The amount provided must be in the lowest denomination possible. Example:

Token A has 2 decimals. In order to burn 100 tokens, one must provide amount of 10000. In order to burn 100.55 tokens, one must provide amount of 10055.

## TokenBurnTransactionBody

| Field           | Type                                 | Description                                                                                                                                                                                                                                                                           |
| --------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`         | [TokenID](../basic-types/tokenid.md) | The token for which to burn tokens. If token does not exist, transaction results in INVALID\_TOKEN\_ID                                                                                                                    |
| `amount`        | uint64                               | The amount to burn from the Treasury Account. Amount must be a positive non-zero number, not bigger than the token balance of the treasury account (0; balance], represented in the lowest denomination. |
| `serialNumbers` | repeated int64                       | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. The list of serial numbers to be burned.                                                                                                                                                          |
