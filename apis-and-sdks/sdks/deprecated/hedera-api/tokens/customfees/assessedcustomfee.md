# AssessedCustomFee

A custom transfer fee that was assessed during the handling of a CryptoTransfer.

| Field                        | Type                                                                                                                                                             | Description                                                                                    |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `amount`                     | int64                                                                                                                                                            | The number of units assessed for the fee                                                       |
| `token_id`                   | [TokenID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md)            | The denomination of the fee; taken as hbar if left unset                                       |
| `fee_collector_account_id`   | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md)          | The account to receive the assessed fee                                                        |
| `effective_payer_account_id` | repeated [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md) | The account(s) whose final balances would have been higher in the absence of this assessed fee |
