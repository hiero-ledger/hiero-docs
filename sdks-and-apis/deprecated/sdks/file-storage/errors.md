# Network Response Messages

Network response messages and their descriptions.

| Network Response                        | Description                                                              |
| --------------------------------------- | ------------------------------------------------------------------------ |
| `FEE_SCHEDULE_FILE_PART_UPLOADED`       | Fee Schedule Proto File Part uploaded                                    |
| `FILE_CONTENT_EMPT`                     | The contents of file are provided as empty.                              |
| `FILE_DELETED`                          | the file has been marked as deleted                                      |
| `FILE_SYSTEM_EXCEPTION`                 | Unexpected exception thrown by file system functions                     |
| `FILE_UPLOADED_PROTO_INVALID`           | Fee Schedule Proto uploaded but not valid (append or update is required) |
| `FILE_UPLOADED_PROTO_NOT_SAVED_TO_DISK` | Fee Schedule Proto uploaded but not valid (append or update is required) |
| `INVALID_EXCHANGE_RATE_FILE`            | Failed to update exchange rate file                                      |
| `INVALID_FEE_FILE`                      | Failed to update fee file                                                |
| `INVALID_FILE_ID`                       | The file id is invalid or does not exist                                 |
| `INVALID_FILE_WACL`                     | File WACL keys are invalid                                               |
| `MAX_FILE_SIZE_EXCEEDED`                | File size exceeded the currently allowable limit                         |
| `NO_WACL_KEY`                           | WriteAccess Control Keys are not provided for the file                   |
