# FeeData

The total fees charged for a transaction. It contains three parts namely node data, network data and service data

| Field         | Type                              | Description                                                                                            |
| ------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `nodedata`    | [FeeComponents](feecomponents.md) | Fee charged by Node for this functionality                                                             |
| `networkdata` | [FeeComponents](feecomponents.md) | Fee charged for network operations by Hedera                                                           |
| `servicedata` | [FeeComponents](feecomponents.md) | Fee charged for providing service by Hedera                                                            |
| `subType`     | [SubType](subtype.md)             | SubType distinguishing between different types of FeeData, correlating to the same HederaFunctionality |

####
