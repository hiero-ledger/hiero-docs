# UncheckedSubmit

Submit an arbitrary (serialized) Transaction to the network without prechecks. Requires superuser privileges.

## UncheckedSubmitBody

| Field              | Type  | Description                                                               |
| ------------------ | ----- | ------------------------------------------------------------------------- |
| `transactionBytes` | bytes | The serialized bytes of the Transaction to be submitted without prechecks |
