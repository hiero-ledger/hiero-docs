# TokenType

Possible Token Types (IWA Compatibility).\
\
Apart from fungible and non-fungible, Tokens can have either a common or unique representation. This distinction might seem subtle, but it is important when considering how tokens can be traced and if they can have isolated and unique properties.

| Enum Name             | Description                                                                                                                                                                                                                                                                            |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `FUNGIBLE_COMMON`     | Interchangeable value with one another, where any quantity of them has the same value as another equal quantity if they are in the same class. Share a single set of properties, not distinct from one another. Simply represented as a balance or quantity to a given Hedera account. |
| `NON_FUNGIBLE_UNIQUE` | Unique, not interchangeable with other tokens of the same type as they typically have different values. Individually traced and can carry unique properties (e.g. serial number).                                                                                                      |

####
