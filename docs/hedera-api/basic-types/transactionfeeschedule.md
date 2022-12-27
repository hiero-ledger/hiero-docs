# TransactionFeeSchedule

The fees for a specific transaction or query based on the fee data.

| Field                 | Type                                                                                                                                                   | Description                                                    |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------- |
| `hederaFunctionality` | ​[HederaFunctionality](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)​ | A particular transaction or query                              |
| `feeData`             | ​[FeeData](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)​             | <p>Resource price coefficients</p><p>[deprecated]</p>          |
| `fees`                | repeated [FeeData](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)      | Resource price coefficients. Supports subtype price definition |

#### &#x20;<a href="#undefined" id="undefined"></a>
