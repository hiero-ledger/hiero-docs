# FixedFee

A fixed number of units (hbar or token) to assess as a fee during a CryptoTransfer that transfers units of the token to which this fixed fee is attached.

| Field                   | Type                                    | Description                                              |
| ----------------------- | --------------------------------------- | -------------------------------------------------------- |
| `amount`                | int64                                   | The number of units to assess as a fee                   |
| `denominating_token_id` | [TokenID](../../basic-types/tokenid.md) | The denomination of the fee; taken as hbar if left unset |
