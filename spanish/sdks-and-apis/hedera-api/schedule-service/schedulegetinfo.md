# ScheduleGetInfo

Gets information about a schedule in the network's action queue. Responds with INVALID\_SCHEDULE\_ID if the requested schedule doesn't exist.

## ScheduleGetInfoQuery

| Field        | Type                                             | Description                                                                                                                                         |
| ------------ | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | ​[QueryHeader](../miscellaneous/queryheader.md)​ | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `scheduleID` | ​[ScheduleID](../basic-types/scheduleid.md)      | The ID of the Scheduled Entity                                                                                                                      |

## ScheduleInfo

Information summarizing schedule state.

| Field                      | Type                                                        | Description                                                                                                                                                                          |
| -------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `scheduleID`               | [ScheduleID](../basic-types/scheduleid.md)                  | The id of the schedule                                                                                                                                                               |
| `creatorAccountID`         | [AccountID](../basic-types/accountid.md)                    | The id of the account responsible for the service fee of the scheduled transaction                                                                                                   |
| `payerAccountIDAccountID`  | [AccountID](../basic-types/accountid.md)                    | The account which is going to pay for the execution of the scheduled transaction                                                                                                     |
| `scheduledTransactionBody` | [SchedulableTransactionBody](schedulabletransactionbody.md) | The scheduled transaction                                                                                                                                                            |
| `signers`                  | [KeyList](../basic-types/keylist.md)                        | The Ed25519 keys the network deems to have signed the scheduled transaction                                                                                                          |
| `adminKey`                 | [Key](../basic-types/key.md)                                | The key used to delete the schedule from state                                                                                                                                       |
| `memo`                     | string                                                      | The publicly visible memo of the schedule                                                                                                                                            |
| `expirationTime`           | [TimeStamp](../miscellaneous/timestamp.md)                  | The epoch second at which the schedule will expire                                                                                                                                   |
| `scheduledTransactionID`   | [TransactionID](../basic-types/transactionid.md)            | The transaction id that will be used in the record of the scheduled transaction (if it executes)                                                                                     |
| `ledger_id`                | bytes                                                       | The ledger ID the response was returned from; please see [HIP-198](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-198.md) for the network-specific IDs |
| **`oneof data`** :         |                                                             |                                                                                                                                                                                      |
| `deletion_time`            | [TimeStamp](../miscellaneous/timestamp.md)                  | If the schedule has been deleted, the consensus time when this occurred                                                                                                              |
| `execution_time`           | [TimeStamp](../miscellaneous/timestamp.md)                  | If the schedule has been executed, the consensus time when this occurred                                                                                                             |

‌

## ScheduleGetInfoResponse <a href="#consensusgettopicinforesponse" id="consensusgettopicinforesponse"></a>

Response wrapper for the ScheduleInfo.

| Field          | Type                                                   | Description                                                                                                       |
| -------------- | ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------- |
| `header`       | ​[ResponseHeader](../miscellaneous/responseheader.md)​ | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither. |
| `scheduleInfo` | [ScheduleInfo](schedulegetinfo.md#scheduleinfo)        | The information requested about this schedule instance                                                            |
