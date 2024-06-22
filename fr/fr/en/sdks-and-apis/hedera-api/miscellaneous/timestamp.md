# TimeStamp

## TokenUnitBalance

| Field     | Description                                                                                                                                                                                                                                                         |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tokenId` | A unique token id                                                                                                                                                                                                                                                   |
| `balance` | Number of transferable units of the identified token. For token of type `FUNGIBLE_COMMON` - balance in the smallest denomination. For token of type `NON_FUNGIBLE_UNIQUE` - the number of NFTs held by the account. |

## Timestamp

An exact date and time. This is the same data structure as the protobuf Timestamp.proto (see the comments in https:github.com/google/protobuf/blob/master/src/google/protobuf/timestamp.proto)

| Field     | Description                                              |
| --------- | -------------------------------------------------------- |
| `seconds` | Number of complete seconds since the start of the epoch  |
| `nanos`   | Number of nanoseconds since the start of the last second |

## TimestampSeconds

An exact date and time, with a resolution of one second (no nanoseconds).

| Field     | Description                                             |
| --------- | ------------------------------------------------------- |
| `seconds` | Number of complete seconds since the start of the epoch |
