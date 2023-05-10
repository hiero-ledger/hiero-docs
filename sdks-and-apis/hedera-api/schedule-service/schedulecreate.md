# ScheduleCreate

Create a new schedule entity (or simply, \<i>schedule\</i>) in the network's action queue. Upon SUCCESS, the receipt contains the \`ScheduleID\` of the created schedule. A schedule entity includes a scheduledTransactionBody to be executed when the schedule has collected enough signing Ed25519 keys to satisfy the scheduled transaction's signing requirements. Upon \`SUCCESS\`, the receipt also includes the scheduledTransactionID to use to query for the record of the scheduled transaction's execution (if it occurs).

The expiration time of a schedule is always 30 minutes; it remains in state and can be queried using GetScheduleInfo until expiration, no matter if the scheduled transaction has executed or marked deleted.

If the adminKey field is omitted, the resulting schedule is immutable. If the adminKey is set, the ScheduleDelete transaction can be used to mark it as deleted. The creator may also specify an optional memo whose UTF-8 encoding is at most 100 bytes and does not include the zero byte is also supported.

When a scheduled transaction whose schedule has collected enough signing keys is executed, the network only charges its payer the service fee, and not the node and network fees. If the optional payerAccountID is set, the network charges this account. Otherwise it charges the payer of the originating ScheduleCreate.

Two ScheduleCreate transactions are identical if they are equal in all their fields other than payerAccountID. (Here "equal" should be understood in the sense of gRPC object equality in the network software runtime. In particular, a gRPC object with [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) is not equal to a gRPC object without unknown fields, even if they agree on all known fields.)

A ScheduleCreate transaction that attempts to re-create an identical schedule already in state will receive a receipt with status

IDENTICAL\_SCHEDULE\_ALREADY\_CREATED; the receipt will include the ScheduleID of the extant schedule, which may be used in a subsequent ScheduleSign transaction. (The receipt will also include the TransactionID to use in querying or the receipt or record of the scheduled transaction.)

Other notable response codes include, INVALID\_ACCOUNT\_ID, UNSCHEDULABLE\_TRANSACTION, UNRESOLVABLE\_REQUIRED\_SIGNER, INVALID\_SIGNATURE.

## ScheduleCreateTransactionBody

| Field                        | Type                                     | Description                                                                                                                                                                                  |
| ---------------------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SchedulableTransactionBody` | scheduledTransactionBody                 | The scheduled transaction                                                                                                                                                                    |
| `adminKey`                   | [Key](../basic-types/key.md)             | An optional Hedera key which can be used to sign a ScheduleDelete and remove the schedule                                                                                                    |
| `payerAccountId`             | [AccountID](../basic-types/accountid.md) | An optional id of the account to be charged the service fee for the scheduled transaction at the consensus time that it executes (if ever); defaults to the ScheduleCreate payer if not give |
| `memo`                       | string                                   | An optional memo with a UTF-8 encoding of no more than 100 bytes which does not contain the zero byte                                                                                        |
