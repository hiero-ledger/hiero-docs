# TransferList

A list of accounts and amounts to transfer out of each account (negative) or into it (positive).

| Field            | Type                                                                                                                                                     | Description                                                                                                                             |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `accountAmounts` | ​repeated [AccountAmount](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md) | Multiple list of AccountAmount pairs, each of which has an account and an amount to transfer into it (positive) or out of it (negative) |
