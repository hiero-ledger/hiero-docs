# TokenBalance

A number of transferable units of a certain token. The transferable unit of a token is its smallest denomination, as given by the token's decimals property---each minted token contains 10decimals transferable units. For example, we could think of the cent as the transferable unit of the US dollar (decimals=2); and the tinybar as the transferable unit of hbar (decimals=8). Transferable units are not directly comparable across different tokens.

| Field      | Type                  | Description                                                                                                                                                                                                          |
| ---------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tokenId`  | [TokenID](tokenid.md) | A unique token id                                                                                                                                                                                                    |
| `balance`  | uint64                | A number of transferable units of the identified token. For token of type `FUNGIBLE_COMMON` - balance in the smallest denomination. For token of type `NON_FUNGIBLE_UNIQUE` - the number of NFTs held by the account |
| `decimals` | uint32                | Tokens divide into 10 decimals pieces                                                                                                                                                                                |
