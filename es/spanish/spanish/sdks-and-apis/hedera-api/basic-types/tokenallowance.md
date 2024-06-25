# TokenAllowance

An approved allowance of token transfers for a spender.

| Field     | Type      | Description                                                                                            |
| --------- | --------- | ------------------------------------------------------------------------------------------------------ |
| `TokenId` | TokenID   | The token that the allowance pertains to                                                               |
| `owner`   | AccountID | The account ID of the hbar owner (ie. the grantor of the allowance) |
| `spender` | AccountID | The account ID of the spender of the hbar allowance                                                    |
| `amount`  | int64     | The amount of the spender's token allowance                                                            |

####
