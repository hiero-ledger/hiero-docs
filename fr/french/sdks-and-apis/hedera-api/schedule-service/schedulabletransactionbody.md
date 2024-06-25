# SchedulableTransactionBody

A schedulable transaction. Note that the global/dynamic system property scheduling.whitelist controls which transaction types may be scheduled. In Hedera Services 0.13.0, it will include only CryptoTransfer and ConsensusSubmitMessage functions.

| Field                           | Type                                  | Description                                                                                                          |
| ------------------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `transactionFee`                | uint64                                | The maximum transaction fee the client is willing to pay                                                             |
| `memo`                          | string                                | A memo to include the execution record; the UTF-8 encoding may be up to 100 bytes and must not include the zero byte |
| **Oneof Data:** |                                       |                                                                                                                      |
| `contractCall`                  | ContractCallTransactionBody           | Calls a function of a contract instance                                                                              |
| `contractCreateInstance`        | ContractCreateTransactionBody         | Creates a contract instance                                                                                          |
| `contractUpdateInstance`        | ContractUpdateTransactionBody         | Updates a contract                                                                                                   |
| `contractDeleteInstance`        | ContractDeleteTransactionBody         | Delete contract and transfer remaining balance into specified account                                                |
| `cryptoApproveAllowance`        | CryptoApproveAllowanceTransactionBody | Adds one or more approved allowances for spenders to transfer the paying account's hbar or tokens.   |
| `cryptoDeleteAllowance`         | CryptoDeleteAllowanceTransactionBody  | Deletes one or more NFT allowances from an owner's account                                                           |
| `cryptoCreateAccount`           | CryptoCreateTransactionBody           | Create a new cryptocurrency account                                                                                  |
| `cryptoDelete`                  | CryptoDeleteTransactionBody           | Delete a cryptocurrency account (mark as deleted, and transfer hbars out)                         |
| `cryptoTransfer`                | CryptoTransferTransactionBody         | Transfer amount between accounts                                                                                     |
| `cryptoUpdateAccount`           | CryptoUpdateTransactionBody           | Modify information such as the expiration date for an account                                                        |
| `fileAppend`                    | FileAppendTransactionBody             | Add bytes to the end of the contents of a file                                                                       |
| `fileCreate`                    | FileCreateTransactionBody             | Create a new file                                                                                                    |
| `fileDelete`                    | FileDeleteTransactionBody             | Delete a file (remove contents and mark as deleted until it expires)                              |
| `fileUpdate`                    | FileUpdateTransactionBody             | Modify information such as the expiration date for a file                                                            |
| `systemDelete`                  | SystemDeleteTransactionBody           | Hedera administrative deletion of a file or smart contract                                                           |
| `systemUndelete`                | SystemUndeleteTransactionBody         | To undelete an entity deleted by SystemDelete                                                                        |
| `freeze`                        | FreezeTransactionBody                 | Freeze the nodes                                                                                                     |
| `consensusCreateTopic`          | ConsensusCreateTopicTransactionBody   | Creates a topic                                                                                                      |
| `consensusUpdateTopic`          | ConsensusUpdateTopicTransactionBody   | Updates a topic                                                                                                      |
| `consensusDeleteTopic`          | ConsensusDeleteTopicTransactionBody   | Deletes a topic                                                                                                      |
| `consensusSubmitMessage`        | ConsensusSubmitMessageTransactionBody | Submits message to a topic                                                                                           |
| `tokenCreation`                 | TokenCreateTransactionBody            | Creates a token instance                                                                                             |
| `tokenFreeze`                   | TokenFreezeAccountTransactionBody     | Freezes account not to be able to transact with a token                                                              |
| `tokenUnfreeze`                 | TokenUnfreezeAccountTransactionBody   | Unfreezes account for a token                                                                                        |
| `tokenGrantKyc`                 | TokenGrantKycTransactionBody          | Grants KYC to an account for a token                                                                                 |
| `tokenRevokeKyc`                | TokenRevokeKycTransactionBody         | Revokes KYC of an account for a token                                                                                |
| `tokenDeletion`                 | TokenDeleteTransactionBody            | Deletes a token instance                                                                                             |
| `tokenUpdate`                   | TokenUpdateTransactionBody            | Updates a token instance                                                                                             |
| `tokenMint`                     | TokenMintTransactionBody              | Mints new tokens to a token's treasury account                                                                       |
| `tokenBurn`                     | TokenBurnTransactionBody              | Burns tokens from a token's treasury account                                                                         |
| `tokenWipe`                     | TokenWipeAccountTransactionBody       | Wipes amount of tokens from an account                                                                               |
| `tokenAssociate`                | TokenAssociateTransactionBody         | Associate tokens to an account                                                                                       |
| `tokenDissociate`               | TokenDissociateTransactionBody        | Dissociate tokens from an account                                                                                    |
| `token_pause`                   | TokenPauseTransactionBody             | Pauses the Token                                                                                                     |
| `token_unpause`                 | TokenUnpauseTransactionBody           | Unpauses the Token                                                                                                   |
| `scheduleDelete`                | ScheduleDeleteTransactionBody         | Marks a schedule in the network's action queue as deleted, preventing it from executing                              |
| `token_fee_schedule_update`     | TokenFeeScheduleUpdateTransactionBody | Updates a token's custom fee schedule                                                                                |
| `cryptoAdjustAllowance`         | CryptoAdjustAllowanceTransactionBody  | Adjusts the approved allowance for a spender to transfer the paying account's hbar or tokens                         |
| `cryptoApproveAllowance`        | CryptoApproveAllowanceTransactionBody | Adds one or more approved allowances for spenders to transfer the paying account's hbar or tokens                    |
