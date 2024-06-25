# TokenFeeScheduleUpdate

At consensus, updates a token type's fee schedule to the given list of custom fees. If the target token type has no fee\_schedule\_key, resolves to `TOKEN_HAS_NO_FEE_SCHEDULE_KEY.` \*\*\*\* Otherwise, this transaction must be signed to the fee\_schedule\_key, or the transaction will resolve to `INVALID_SIGNATURE`. If the custom\_fees list is empty, clears the fee schedule or resolves to `CUSTOM_SCHEDULE_ALREADY_HAS_NO_FEES` if the fee schedule was already empty.

| Field         | Type                                          | Description                                                                                   |
| ------------- | --------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `token_id`    | [TokenID](../basic-types/tokenid.md)          | The token whose fee schedule is to be updated                                                 |
| `custom_Fees` | repeated [CustomFee](customfees/customfee.md) | The new custom fees to be assessed during a CryptoTransfer that transfers units of this token |
