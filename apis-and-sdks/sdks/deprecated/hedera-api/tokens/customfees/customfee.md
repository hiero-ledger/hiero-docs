# CustomFee

A transfer fee to assess during a CryptoTransfer that transfers units of the token to which the fee is attached. A custom fee may be either fixed or fractional, and must specify a fee collector account to receive the assessed fees. Only positive fees may be assessed.

| Field                      | Type                                                                                                                                                        | Description                           |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| one of fee {               |                                                                                                                                                             |                                       |
| `fixed_fee`                | [FixedFee](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md)      | Fixed fee to be charged               |
| `fractional_fee`           | [FractionalFee](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md) | Fractional fee to be charged          |
| `royalty_fee`              | [RoyaltyFee](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md)    | Royalty fee to be charged             |
| }                          |                                                                                                                                                             |                                       |
| `fee_collector_account_id` | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md)     | The account to receive the custom fee |
