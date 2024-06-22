# ExchangeRate

## ExchangeRate

An exchange rate between hbar and cents (USD) and the time at which the exchange rate will expire, and be superseded by a new exchange rate.

| Field            | Type                                              | Description                                                        |
| ---------------- | ------------------------------------------------- | ------------------------------------------------------------------ |
| `hbarEquiv`      |                                                   | Denominator in calculation of exchange rate between hbar and cents |
| `centEquiv`      |                                                   | Numerator in calculation of exchange rate between hbar and cents   |
| `expirationTime` | [TimestampSeconds](timestamp.md#timestampseconds) | Expiration time in seconds for this exchange rate                  |

## ExchangeRateSet

Two sets of exchange rate

| Field         | Type                                         | Description                                                         |
| ------------- | -------------------------------------------- | ------------------------------------------------------------------- |
| `currentRate` | [ExchangeRate](exchangerate.md#exchangerate) | Current exchange rate                                               |
| `nextRate`    | [ExchangeRate](exchangerate.md#exchangerate) | Next exchange rate which will take effect when current rate expires |
