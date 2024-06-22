# TokenMint

Mints tokens to the Token's treasury Account. If no Supply Key is defined, the transaction will resolve to `TOKEN_HAS_NO_SUPPLY_KEY`.

The operation increases the Total Supply of the Token. The maximum total supply a token can have is `2^63-1`.

The amount provided must be in the lowest denomination possible.\
Example:Token A has 2 decimals. In order to mint `100` tokens, one must provide amount of 10000. In order to mint `100.55` tokens, one must provide amount of `10055`.

## TokenMintTransactionBody

| Field      | Type                                 | Description                                                                                                                                                                                                                                                                                                                                   |
| ---------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`    | [TokenID](../basic-types/tokenid.md) | The token for which to mint tokens. If token does not exist, transaction results in INVALID\_TOKEN\_ID                                                                                                                                                                            |
| `amount`   | uint64                               | Applicable to tokens of type `FUNGIBLE_COMMON`. The amount to mint to the Treasury Account. Amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than 2^63.                                            |
| `metadata` | repeated bytes                       | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. Maximum allowed size of each metadata is 100 bytes. The metadata can be arbitrary, but we recommend developers to follow [HIP-10](https://hips.hedera.com/hip/hip-10)standard. |
