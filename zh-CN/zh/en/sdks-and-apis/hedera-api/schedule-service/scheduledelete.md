# ScheduleDelete

Marks a schedule in the network's action queue as deleted. Must be signed by the admin key of the target schedule. A deleted schedule cannot receive any additional signing keys, nor will it be executed.

Other notable response codes include, INVALID\_SCHEDULE\_ID, SCHEDULE\_WAS\_DELETED, SCHEDULE\_WAS\_EXECUTED, SCHEDULE\_IS\_IMMUTABLE. For more information please see the section of this documentation on the ResponseCode enum.

## ScheduleDeleteTransactionBody

| Field        | Type                                       | Description                    |
| ------------ | ------------------------------------------ | ------------------------------ |
| `scheduleID` | [ScheduleID](../basic-types/scheduleid.md) | The ID of the Scheduled Entity |
