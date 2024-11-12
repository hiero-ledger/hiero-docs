# Network Response Messages

Network response messages and their descriptions.

| **Errors**                                                       | **Description**                                                                                                                      |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `ACCOUNT_ID_DOES_NOT_EXIST`                                      | The account id passed has not yet been created.                                                                                      |
| `ACCOUNT_UPDATE_FAILED`                                          | The update of the account failed                                                                                                     |
| `ACCOUNT_DELETED`                                                | The account has been marked as deleted                                                                                               |
| `INVALID_ACCOUNT_AMOUNTS`                                        | The crypto transfer credit and debit do not sum equal to 0                                                                           |
| `INVALID_INITIAL_BALANCE`                                        | Attempt to set the negative initial balance                                                                                          |
| `INVALID_RECEIVE_RECORD_THRESHOLD`                               | Attempt to set negative receive record threshold                                                                                     |
| `INVALID_SEND_RECORD_THRESHOLD`                                  | Attempt to set negative send record threshold                                                                                        |
| `SETTING_NEGATIVE_ACCOUNT_BALANCE`                               | Attempting to set negative balance value for the crypto account                                                                      |
| `TRANSFER_LIST_SIZE_LIMIT_EXCEEDED`                              | Exceeded the number of accounts (both from and to) allowed for crypto transfer list                                                  |
| `TRANSFER_ACCOUNT_SAME_AS_DELETE_ACCOUNT`                        | Transfer Account should not be same as Account to be deleted                                                                         |
| `NO_REMAINING_AUTOMATIC_ASSOCIATIONS`                            | The account has reached the limit on the automatic associations count.                                                               |
| `EXISTING_AUTOMATIC_ASSOCIATIONS_EXCEED_GIVEN_LIMIT`             | Already existing automatic associations are more than the new maximum automatic associations.                                        |
| `REQUESTED_NUM_AUTOMATIC_ASSOCIATIONS_EXCEEDS_ASSOCIATION_LIMIT` | Cannot set the number of automatic associations for an account more than the maximum allowed token associations tokens.maxPerAccount |
