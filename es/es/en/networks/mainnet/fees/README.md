---
description: Hedera network fees
---

# Fees

The Hedera testnet fees tables found below offer a low-end estimate of transaction and query fees for all network services. The tables below contain USD, HBAR, and Tinybar (tℏ) values per each API call. All operation fees on the Hedera testnet are paid in test HBAR, which is freely available and only useful for development purposes.

Fee estimates are based on assumptions about the details of a specific API call. For instance, the fee for an HBAR cryptocurrency transfer (CryptoTransfer) assumes a single signature on the transaction and the fee for storing a file assumes a 48-byte sized file stored for 30 days. Transactions exceeding these base assumptions will be more expensive; we recommend increasing your maximum allowable fee to accommodate additional complexity.

### Mainnet Fees

Mainnet transaction and query fees can be estimated using the [Hedera Fee Estimator](https://hedera.com/fees). The Fee Estimator allows you to determine fees (in both USD and HBAR, using the current exchange rate live on the mainnet) for individual transactions & queries based on their characteristics, as well as projected costs based on expected volume for those transactions. The estimations may not be 100% accurate and the underlying prices are subject to change without prior notice.

## HBAR Denominations and Abbreviations

| Denominations | Abbreviations  | Amount of HBAR Cryptocurrency |
| ------------- | -------------- | ----------------------------- |
| gigabar       | 1 Gℏ           | = 1,000,000,000 ℏ             |
| megabar       | 1 Mℏ           | = 1,000,000 ℏ                 |
| kilobar       | 1 Kℏ           | = 1,000 ℏ                     |
| hbar          | 1 ℏ            | = 1 ℏ                         |
| millibar      | 1,000 mℏ       | = 1 ℏ                         |
| microbar      | 1,000,000 μℏ   | = 1 ℏ                         |
| tinybar       | 100,000,000 tℏ | = 1 ℏ                         |

## Transaction and Query Fees

All fees are subject to change. The fees below reflect a base price for the transaction or query. Transaction characteristics may increase the price from the base price shown below. Transaction characteristics include having more than one signature, a memo field, etc. Please reference the [Hedera fee estimator](https://hedera.com/fees) to estimate the transaction or query fee.

### Cryptocurrency Service

<table><thead><tr><th width="482">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>CryptoCreate</td><td>$0.05</td></tr><tr><td>CryptoAccountAutoRenew</td><td>$0.00022</td></tr><tr><td>CryptoDeleteAllowance</td><td>$0.05</td></tr><tr><td>CryptoApproveAllowance</td><td>$0.05</td></tr><tr><td>CryptoUpdate</td><td>$0.00022</td></tr><tr><td>CryptoTransfer</td><td>$0.0001</td></tr><tr><td>CryptoTransfer (custom fees)</td><td>$0.002</td></tr><tr><td>CryptoDelete</td><td>$0.005</td></tr><tr><td>CryptoGetAccountRecords</td><td>$0.0001</td></tr><tr><td>CryptoGetAccountBalance</td><td>$0.00</td></tr><tr><td>CryptoGetInfo</td><td>$0.0001</td></tr><tr><td>CryptoGetStakers</td><td>$0.0001</td></tr></tbody></table>

### Consensus Service

<table><thead><tr><th width="484">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>ConsensusCreateTopic</td><td>$0.01</td></tr><tr><td>ConsensusUpdateTopic</td><td>$0.00022</td></tr><tr><td>ConsensusDeleteTopic</td><td>$0.005</td></tr><tr><td>ConsensusSubmitMessage</td><td>$0.0001</td></tr><tr><td>ConsensusGetTopicInfo</td><td>$0.0001</td></tr></tbody></table>

### Token Service

<table><thead><tr><th width="486">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>TokenCreate</td><td>$1.00</td></tr><tr><td>TokenCreate (custom fees)</td><td>$2.00</td></tr><tr><td>TokenUpdate</td><td>$0.001</td></tr><tr><td>TokenFeeScheduleUpdate</td><td>$0.001</td></tr><tr><td>TokenDelete</td><td>$0.001</td></tr><tr><td>TokenAssociate</td><td>$0.05</td></tr><tr><td>TokenDissociate</td><td>$0.05</td></tr><tr><td>TokenMint (fungible)</td><td>$0.001</td></tr><tr><td>TokenMint (non-fungible)</td><td>$0.02</td></tr><tr><td>TokenMint (bulk mint 10k NFTs)</td><td>$200</td></tr><tr><td>TokenBurn</td><td>$0.001</td></tr><tr><td>TokenGrantKyc</td><td>$0.001</td></tr><tr><td>TokenRevokeKyc</td><td>$0.001</td></tr><tr><td>TokenFreeze</td><td>$0.001</td></tr><tr><td>TokenUnfreeze</td><td>$0.001</td></tr><tr><td>TokenPause</td><td>$0.001</td></tr><tr><td>TokenUnpause</td><td>$0.001</td></tr><tr><td>TokenWipe</td><td>$0.001</td></tr><tr><td>TokenGetInfo</td><td>$0.0001</td></tr><tr><td>TokenGetNftInfo</td><td>$0.0001</td></tr><tr><td>TokenGetNftInfos</td><td>$0.0001</td></tr><tr><td>TokenGetAccountNftInfos</td><td>$0.0001</td></tr><tr><td>TokenUpdateNfts (updates metadata of 1 NFT)</td><td>$0.001</td></tr><tr><td>TokenUpdateNfts (update multiple NFTs in a single call)</td><td>N * $0.001</td></tr></tbody></table>

### Schedule Transaction

<table><thead><tr><th width="491">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>ScheduleCreate</td><td>$0.01</td></tr><tr><td>ScheduleSign</td><td>$0.001</td></tr><tr><td>ScheduleDelete</td><td>$0.001</td></tr><tr><td>ScheduleGetInfo</td><td>$0.0001</td></tr></tbody></table>

### File Service

<table><thead><tr><th width="495">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>FileCreate</td><td>$0.05</td></tr><tr><td>FileUpdate</td><td>$0.05</td></tr><tr><td>FileDelete</td><td>$0.007</td></tr><tr><td>FileAppend</td><td>$0.05</td></tr><tr><td>FileGetContents</td><td>$0.0001</td></tr><tr><td>FileGetInfo</td><td>$0.0001</td></tr></tbody></table>

### Smart Contract Service

<table><thead><tr><th width="501">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>ContractCreate</td><td>$1.0</td></tr><tr><td>ContractUpdated</td><td>$0.026</td></tr><tr><td>ContractDelete</td><td>$0.007</td></tr><tr><td>ContractCall</td><td>$0.05</td></tr><tr><td>ContractCallLocal</td><td>$0.001</td></tr><tr><td>ContractGetByteCode</td><td>$0.05</td></tr><tr><td>GetBySolidityID</td><td>$0.0001</td></tr><tr><td>ContractGetInfo</td><td>$0.0001</td></tr><tr><td>ContractGetRecords</td><td>$0.0001</td></tr><tr><td>ContractAutoRenew</td><td>$0.026</td></tr></tbody></table>

### Miscellaneous

<table><thead><tr><th width="508">Operations</th><th>USD ($)</th></tr></thead><tbody><tr><td>EthereumTransaction</td><td>$0.0001</td></tr><tr><td>PrngTransaction</td><td>$0.001</td></tr><tr><td>GetVersion</td><td>$0.001</td></tr><tr><td>GetByKey</td><td>$0.0001</td></tr><tr><td>TransactionGetReceipt</td><td>$0.0000</td></tr><tr><td>TransactionGetRecord</td><td>$0.0001</td></tr></tbody></table>
