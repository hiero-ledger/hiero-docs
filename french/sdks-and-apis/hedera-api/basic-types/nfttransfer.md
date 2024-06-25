# NftTransfer

A sender account, a receiver account, and the serial number of an NFT of a Token with `NON_FUNGIBLE_UNIQUE` type.

| Field               | Type                      | Description                                                                                                                                            |
| ------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `senderAccountID`   | [AccountID](accountid.md) | The accountID of the sender                                                                                                                            |
| `receiverAccountID` | [AccountID](accountid.md) | The accountID of the receiver                                                                                                                          |
| `serialNumber`      | int64                     | The serial number of the NFT                                                                                                                           |
| `is_approval`       | bool                      | If true then the transfer is expected to be an approved allowance and the senderAccountID is expected to be the owner. The default is false (omitted). |
