# ScheduleService

## Transactions and queries for the Schedule Service

The Schedule Service allows transactions to be submitted without all the required signatures and allows anyone to provide the required signatures independently after a transaction has already been created.

**Execution:**

Scheduled Transactions are executed once all required signatures are collected and witnessed. Every time new signature is provided, a check is performed on the "readiness" of the execution.

The Scheduled Transaction will be executed immediately after the transaction that triggered it and will be externalized in a separate Transaction Record.

**Transaction Record:**

The timestamp of the Scheduled Transaction will be equal to consensusTimestamp + 1 nano, where consensusTimestamp is the timestamp of the transaction that triggered the execution.

The Transaction ID of the Scheduled Transaction will have the scheduled property set to true and inherit the transactionValidStart and accountID from the ScheduleCreate transaction.

The scheduleRef property of the transaction record will be populated with the ScheduleID of the Scheduled Transaction.

**Post execution:**

Once a given Scheduled Transaction executes, it will be removed from the ledger and any upcoming operation referring the ScheduleID will resolve to INVALID\_SCHEDULE\_ID.

**Expiry:**

Scheduled Transactions have a global expiry time txExpiryTimeSecs (Currently set to 30 minutes). If txExpiryTimeSecs pass and the Scheduled Transaction haven't yet executed, it will be removed from the ledger as if ScheduleDelete operation is executed.

## ScheduleService

| RPC               | Request                                        | Response                                                       | Comments                                             |
| ----------------- | ---------------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------- |
| `createSchedule`  | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Creates a new Schedule by submitting the transaction |
| `signSchedule`    | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Signs a new Schedule by submitting the transaction   |
| `deleteSchedule`  | [Transaction](../miscellaneous/transaction.md) | [TransactionResponse](../miscellaneous/transactionresponse.md) | Deletes a new Schedule by submitting the transaction |
| `getScheduleInfo` | [Query](../miscellaneous/query.md)             | [Response](../miscellaneous/response.md)                       | Retrieves the metadata of a schedule entity          |
