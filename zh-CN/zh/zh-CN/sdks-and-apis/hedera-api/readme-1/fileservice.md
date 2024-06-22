# FileService

| RPC              | Request                                        | Response                                                       | Comments                                                                |
| ---------------- | ---------------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `createFile`     | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Creates a file                                                          |
| `updateFile`     | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Updates a file                                                          |
| `deleteFile`     | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Deletes a file                                                          |
| `appendContent`  | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Appends the file                                                        |
| `getFileContent` | [Query](../miscellaneous/query.md)             | [Response](../miscellaneous/response.md)                       | Retrieves the file content                                              |
| `getFileInfo`    | [Query](../miscellaneous/query.md)             | [Response](../miscellaneous/response.md)                       | Retrieves the file information                                          |
| `systemDelete`   | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Deletes a file if the submitting account has network admin privileges   |
| `systemUndelete` | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Undeletes a file if the submitting account has network admin privileges |
