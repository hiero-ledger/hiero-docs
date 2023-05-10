# RoyaltyFee

A fee to assess during a CryptoTransfer that changes ownership of an NFT. Defines the fraction of the fungible value exchanged for an NFT that the ledger should collect as a royalty. ("Fungible value" includes both ℏ and units of fungible HTS tokens.) When the NFT sender does not receive any fungible value, the ledger will assess the fallback fee, if present, to the new NFT owner. Royalty fees can only be added to tokens of type `NON_FUNGIBLE_UNIQUE`.

| Field                     | Type     | Description                                                                                                 |
| ------------------------- | -------- | ----------------------------------------------------------------------------------------------------------- |
| `exchange_value_fraction` | Fraction | The fraction of fungible value exchanged for an NFT to collect as royalty                                   |
| `fallback_fee`            | FixedFee | If present, the fixed fee to assess to the NFT receiver when no fungible value is exchanged with the sender |
