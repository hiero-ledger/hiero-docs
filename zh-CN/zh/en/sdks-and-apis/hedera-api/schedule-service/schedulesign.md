# ScheduleSign

Adds zero or more signing keys to a schedule. If the resulting set of signing keys satisfy the scheduled transaction's signing requirements, it will be executed immediately after the triggering ScheduleSign.

Upon SUCCESS, the receipt includes the scheduledTransactionID to use to query for the record of the scheduled transaction's execution (if it occurs).

Other notable response codes include INVALID\_SCHEDULE\_ID, SCHEDULE\_WAS\_DELETED, INVALID\_ACCOUNT\_ID, UNRESOLVABLE\_REQUIRED\_SIGNERS, SOME\_SIGNATURES\_WERE\_INVALID, and NO\_NEW\_VALID\_SIGNATURES. For more information please see the section of this documentation on the ResponseCode enum.

## ScheduleSignTransactionBody

| Field        | Type                                       | Description                    |
| ------------ | ------------------------------------------ | ------------------------------ |
| `scheduleID` | [ScheduleID](../basic-types/scheduleid.md) | The ID of the Scheduled Entity |
