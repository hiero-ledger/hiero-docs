# SubType

Possible FeeData Object SubTypes. Supplementary to the main HederaFunctionality Type. When not explicitly specified, DEFAULT is used.

| Enum Name                                    | Description                                                                                              |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `DEFAULT`                                    | The resource prices have no special scope                                                                |
| `TOKEN_FUNGIBLE_COMMON`                      | The resource prices are scoped to an operation on a fungible common token                                |
| `TOKEN_NON_FUNGIBLE_UNIQUE`                  | The resource prices are scoped to an operation on a non-fungible unique token                            |
| `TOKEN_FUNGIBLE_COMMON_WITH_CUSTOM_FEES`     | The resource prices are scoped to an operation on a fungible common token with a custom fee schedule     |
| `TOKEN_NON_FUNGIBLE_UNIQUE_WITH_CUSTOM_FEES` | The resource prices are scoped to an operation on a non-fungible unique token with a custom fee schedule |
