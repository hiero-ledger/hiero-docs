# CryptoDeleteAllowance

Deletes one or more non-fungible approved allowances from an owner's account. This operation will remove the allowances granted to one or more specific non-fungible token serial numbers. Each owner account listed as wiping an allowance must sign the transaction. Hbar and fungible token allowances can be removed by setting the amount to zero in CryptoApproveAllowance.

| Field           | Type                                                                       | Description                                                      |
| --------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| `nftAllowances` | repeated [NFTRemoveAllowance](cryptodeleteallowance.md#nftremoveallowance) | List of non-fungible token allowances to remove. |

### NFTRemoveAllowance

Nft allowances to be removed on an owner account.

| Field            | Type           | Description                                                                                                              |
| ---------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `token_id`       | TokenID        | The token that the allowance pertains to.                                                                |
| `owner`          | AccountID      | The account ID of the token owner (ie. the grantor of the allowance). |
| `serial_numbers` | repeated int64 | The list of serial numbers to remove allowances from.                                                    |
