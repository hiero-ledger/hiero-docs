# Freeze

## FreezeTransactionBody

Set the freezing period in which the platform will stop creating events and accepting transactions. This is used before safely shut down the platform for maintenance.

| Field                        | Description                                                                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `startHour[deprecated=true]` | The start hour (in UTC time), a value between 0 and 23                                                    |
| `startMin[deprecated=true]`  | The start minute (in UTC time), a value between 0 and 59                                                  |
| `endHour[deprecated=true]`   | The end hour (in UTC time), a value between 0 and 23                                                      |
| `endMin[deprecated=true]`    | The end minute (in UTC time), a value between 0 and 59                                                    |
| `update_file`                | If set, the file whose contents should be used for a network software update during the maintenance window.  |
| `file_hash`                  | If set, the expected hash of the contents of the update file (used to verify the update). |
| `start_time`                 | The consensus time at which the maintenance window should begin.                                             |
| `freeze_type`                | The type of network freeze or upgrade operation to perform.                                                  |

## FreezeService.proto

#### FreezeService

| RPC      | Request     | Response                                      | Comments                                                                                                         |
| -------- | ----------- | --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `freeze` | Transaction | [TransactionResponse](transactionresponse.md) | Freezes the nodes by submitting the transaction. The grpc server returns the TransactionResponse |
