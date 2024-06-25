# TokenRelationship

Token's information related to the given Account.

| Field                   | Type                                      | Description                                                                                                                                                                                             |
| ----------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tokenId`               | [TokenID](tokenid.md)                     | A unique token id                                                                                                                                                                                       |
| `symbol`                | string                                    | The Symbol of the token                                                                                                                                                                                 |
| `balance`               | uint64                                    | For token of type `FUNGIBLE_COMMON` - the balance that the Account holds in the smallest denomination. For token of type `NON_FUNGIBLE_UNIQUE` - the number of NFTs held by the account |
| `kycStatus`             | [TokenKycStatus](tokenkycstatus.md)       | The KYC status of the account (KycNotApplicable, Granted or Revoked). If the token does not have KYC key, KycNotApplicable is returned                               |
| `freezeStatus`          | [TokenFreezeStatus](tokenfreezestatus.md) | The Freeze status of the account (FreezeNotApplicable, Frozen or Unfrozen). If the token does not have Freeze key, FreezeNotApplicable is returned                   |
| `decimals`              | uint32                                    | Tokens divide into 10 decimal pieces                                                                                                                                                                    |
| `automatic_association` | bool                                      | <p>Specifies if the relationship is created implicitly.<br>False : explicitly associated,<br>True : implicitly associated.</p>                                                                          |
