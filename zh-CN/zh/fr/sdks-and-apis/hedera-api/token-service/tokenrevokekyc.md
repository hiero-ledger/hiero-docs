# TokenRevokeKyc

Revokes KYC to the account for the given token. Must be signed by the Token's kycKey.

If the provided account is not found, the transaction will resolve to INVALID\_ACCOUNT\_ID.

If the provided account has been deleted, the transaction will resolve to ACCOUNT\_DELETED.

If the provided token is not found, the transaction will resolve to INVALID\_TOKEN\_ID.

If the provided token has been deleted, the transaction will resolve to TOKEN\_WAS\_DELETED.

If an Association between the provided token and account is not found, the transaction will resolve to TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT.

If no KYC Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_KYC\_KEY.

Once executed the Account is marked as KYC Revoked

## TokenRevokeKycTransactionBody

| Field     | Type                                     | Description                                                                                                                                                                               |
| --------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`   | [TokenID](../basic-types/tokenid.md)     | The token for which this account will get his KYC revoked. If token does not exist, transaction results in INVALID\_TOKEN\_ID |
| `account` | [AccountID](../basic-types/accountid.md) | The account to be KYC Revoked                                                                                                                                                             |
