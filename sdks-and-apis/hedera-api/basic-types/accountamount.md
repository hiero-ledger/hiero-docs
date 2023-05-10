# AccountAmount

An account, and the amount that it sends or receives during a cryptocurrency or token transfer.

| Field         | Type                      | Description                                                                                                                                              |
| ------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `accountID`   | [AccountID](accountid.md) | The Account ID that sends/receives cryptocurrency or tokens                                                                                              |
| `amount`      | sint64                    | The amount of tinybars (for Crypto transfers) or in the lowest denomination (for Token transfers) that the account sends(negative) or receives(positive) |
| `is_approval` | bool                      | If true then the transfer is expected to be an approved allowance and the accountID is expected to be the owner. The default is false (omitted).         |

####
