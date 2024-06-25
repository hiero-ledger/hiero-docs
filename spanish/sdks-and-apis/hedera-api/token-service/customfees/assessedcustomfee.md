# AssessedCustomFee

A custom transfer fee that was assessed during the handling of a CryptoTransfer.

| Field                        | Type                                                 | Description                                                                                    |
| ---------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `amount`                     | int64                                                | The number of units assessed for the fee                                                       |
| `token_id`                   | [TokenID](../../basic-types/tokenid.md)              | The denomination of the fee; taken as hbar if left unset                                       |
| `fee_collector_account_id`   | [AccountID](../../basic-types/accountid.md)          | The account to receive the assessed fee                                                        |
| `effective_payer_account_id` | repeated [AccountID](../../basic-types/accountid.md) | The account(s) whose final balances would have been higher in the absence of this assessed fee |
