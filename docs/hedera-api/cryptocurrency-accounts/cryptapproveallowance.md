# CryptApproveAllowance

## CryptoApproveAllowanceTransactionBody

Creates one or more hbar/token approved allowances **relative to the payer account of thistransaction**. Each allowance grants a spender the right to transfer a pre-determined amount of the payer's hbar/token to any other account of the spender's choice. (So if account 0.0.X pays for this transaction and owner is not specified in the allowance, then at consensus each spender account will have new allowances to spend hbar or tokens from 0.0.X).

| Field             | Type                                                                                                                                                                  | Description                                                         |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `cryptoAllowance` | repeated [CryptoAllowance](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | List of hbar allowances approved by the account owner               |
| `nftAllowance`    | repeated [NftAllowance](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)    | List of non-fungible token allowances approved by the account owner |
| `tokenAllowance`  | repeated [TokenAllowance](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)  | List of fungible token allowances approved by the account owner.    |
