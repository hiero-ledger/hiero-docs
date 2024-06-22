# CustomFee

A transfer fee to assess during a CryptoTransfer that transfers units of the token to which the fee is attached. A custom fee may be either fixed or fractional, and must specify a fee collector account to receive the assessed fees. Only positive fees may be assessed.

| Field                      | Type                                        | Description                           |
| -------------------------- | ------------------------------------------- | ------------------------------------- |
| one of fee {               |                                             |                                       |
| `fixed_fee`                | [FixedFee](fixedfee.md)                     | Fixed fee to be charged               |
| `fractional_fee`           | [FractionalFee](fractionalfee.md)           | Fractional fee to be charged          |
| `royalty_fee`              | [RoyaltyFee](royaltyfee.md)                 | Royalty fee to be charged             |
| }                          |                                             |                                       |
| `fee_collector_account_id` | [AccountID](../../basic-types/accountid.md) | The account to receive the custom fee |
