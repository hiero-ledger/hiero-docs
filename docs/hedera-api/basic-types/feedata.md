# FeeData

The total fees charged for a transaction. It contains three parts namely node data, network data and service data

| Field         | Type                                                                                                                                           | Description                                                                                            |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `nodedata`    | [FeeComponents](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md) | Fee charged by Node for this functionality                                                             |
| `networkdata` | [FeeComponents](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md) | Fee charged for network operations by Hedera                                                           |
| `servicedata` | [FeeComponents](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md) | Fee charged for providing service by Hedera                                                            |
| `subType`     | [SubType](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)       | SubType distinguishing between different types of FeeData, correlating to the same HederaFunctionality |

####
