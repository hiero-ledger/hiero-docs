# HederaFunctionality

The functionality provided by Hedera.

| Enum Name                    | Description                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NONE`                       | UNSPECIFIED - Need to keep first value as unspecified because first element is ignored and not parsed (0 is ignored by parser) |
| `CryptoTransfer`             | Crypto transfer                                                                                                                                   |
| `CryptoUpdate`               | Crypto update account                                                                                                                             |
| `CryptoDelete`               | Crypto delete account                                                                                                                             |
| `CryptoAddLiveHash`          | Add a livehash to a crypto account (0.5.0)                                                     |
| `CryptoDeleteLiveHash`       | Delete a livehash from a crypto account (0.5.0)                                                |
| `CryptoAddClaim`             | Crypto add claim to the account                                                                                                                   |
| `CryptoDeleteClaim`          | Crypto delete claim to the account                                                                                                                |
| `ContractCall`               | Smart Contract Call                                                                                                                               |
| `ContractCreate`             | Smart Contract Create Contract                                                                                                                    |
| `ContractUpdate`             | Smart Contract update contract                                                                                                                    |
| `FileCreate`                 | File Operation create file                                                                                                                        |
| `FileAppend`                 | File Operation append file                                                                                                                        |
| `FileUpdate`                 | File Operation update file                                                                                                                        |
| `FileDelete`                 | File Operation delete file                                                                                                                        |
| `CryptoGetAccountBalance`    | Crypto get account balance                                                                                                                        |
| `CryptoGetAccountRecords`    | Crypto get account record                                                                                                                         |
| `CryptoGetInfo`              | Crypto get info                                                                                                                                   |
| `ContractCallLocal`          | Smart Contract Call                                                                                                                               |
| `ContractGetInfo`            | Smart Contract get info                                                                                                                           |
| `ContractGetBytecode`        | Smart Contract, get the byte code                                                                                                                 |
| `GetBySolidityID`            | Smart Contract, get by solidity ID                                                                                                                |
| `GetByKey`                   | Smart Contract, get by key                                                                                                                        |
| `CryptoGetClaim`             | Crypto get the claim                                                                                                                              |
| `CryptoGetLiveHash`          | Get a live hash from a crypto account                                                                                                             |
| `CryptoGetStakers`           | Crypto, get the stakers for the node                                                                                                              |
| `FileGetContents`            | File Operations get file contents                                                                                                                 |
| `FileGetInfo`                | File Operations get the info of the file                                                                                                          |
| `TransactionGetRecord`       | Crypto get the transaction records                                                                                                                |
| `ContractGetRecords`         | Contract get the transaction records                                                                                                              |
| `CryptoCreate`               | crypto create account                                                                                                                             |
| `SystemDelete`               | System delete file                                                                                                                                |
| `SystemUndelete`             | System undelete file                                                                                                                              |
| `ContractDelete`             | Delete contract                                                                                                                                   |
| `Freeze`                     | Freeze                                                                                                                                            |
| `CreateTransactionRecord`    | Create Tx Record                                                                                                                                  |
| `CryptoAccountAutoRenew`     | Crypto Auto Renew                                                                                                                                 |
| `ContractAutoRenew`          | Contract Auto Renew                                                                                                                               |
| `getVersion`                 | Get Version                                                                                                                                       |
| `TransactionGetReceipt`      | Transaction Get Receipt                                                                                                                           |
| `ConsensusCreateTopic`       | Create a topic                                                                                                                                    |
| `ConsensusUpdateTopic`       | Update a topic                                                                                                                                    |
| `ConsensusDeleteTopic`       | Delete a topic                                                                                                                                    |
| `ConsensusGetTopicInfo`      | Get topic info                                                                                                                                    |
| `ConsensusSubmitMessage`     | Submit a message to a topic                                                                                                                       |
| `TokenCreate`                | Create Token                                                                                                                                      |
| `TokenTransact`              | Transfer Tokens                                                                                                                                   |
| `TokenGetInfo`               | Transfer Tokens                                                                                                                                   |
| `TokenFreezeAccount`         | Freeze Account                                                                                                                                    |
| `TokenUnfreezeAccount`       | Unfreeze Account                                                                                                                                  |
| `TokenGrantKycToAccount`     | Grant KYC to Account                                                                                                                              |
| `TokenRevokeKycFromAccount`  | Revoke KYC from Account                                                                                                                           |
| `TokenDelete`                | Delete Token                                                                                                                                      |
| `TokenUpdate`                | Update Token                                                                                                                                      |
| `TokenMint`                  | Mint tokens to treasury                                                                                                                           |
| `TokenBurn`                  | Burn tokens from treasury                                                                                                                         |
| `TokenAccountWipe`           | Wipe token amount from Account holder                                                                                                             |
| `TokenAssociateToAccount`    | Wipe token amount from Account holder                                                                                                             |
| `TokenDissociateFromAccount` | Dissociate tokens from an account                                                                                                                 |
| `ScheduleCreate`             | Create Scheduled Transaction                                                                                                                      |
| `ScheduleDelete`             | Delete Scheduled Transaction                                                                                                                      |
| `ScheduleSign`               | Sign Scheduled Transaction                                                                                                                        |
| `ScheduleGetInfo`            | Get Scheduled Transaction Information                                                                                                             |
| `TokenGetAccountNftInfo`     | Get Token Account Nft Information                                                                                                                 |
| `TokenGetNftInfo`            | Get Token Nft Information                                                                                                                         |
| `TokenGetNftInfos`           | Get Token Nft List Information                                                                                                                    |
| `NetworkGetExecutionTime`    | Get execution time(s) by TransactionID, if available                                                                           |
| `TokenPause`                 | Pause the Token                                                                                                                                   |
| `TokenUnpause`               | Unpause the Token                                                                                                                                 |
| `CryptoApproveAllowance`     | Approve allowance for a spender relative to the payer account                                                                                     |
| `CryptoDeleteAllowance`      | Deletes granted NFT allowances on an owner account                                                                                                |
