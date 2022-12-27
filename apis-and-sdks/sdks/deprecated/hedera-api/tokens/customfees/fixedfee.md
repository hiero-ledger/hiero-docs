# FixedFee

A fixed number of units (hbar or token) to assess as a fee during a CryptoTransfer that transfers units of the token to which this fixed fee is attached.

| Field                   | Type                                                                                                                                                  | Description                                              |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| `amount`                | int64                                                                                                                                                 | The number of units to assess as a fee                   |
| `denominating_token_id` | [TokenID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/token-service/customfees/broken-reference/README.md) | The denomination of the fee; taken as hbar if left unset |
