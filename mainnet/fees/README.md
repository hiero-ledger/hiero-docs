---
description: Hedera network fees
---

# Fees

The Hedera testnet fees tables found below offers a low-end estimate of transaction and query fees for all network services. The tables below contain USD, HBAR, and Tinybar (tℏ) values per each API call. All operation fees on the Hedera testnet are paid in test HBAR, which is freely available and only useful for development purposes.

Fee estimates are based on assumptions about the details of a specific API call. For instance, the fee for an HBAR cryptocurrency transfer (CryptoTransfer) assumes a single signature on the transaction and the fee for storing a file assumes a 48 byte sized file stored for 30 days. Transactions which exceed these base assumptions will be more expensive; we recommend increasing your maximum allowable fee to accommodate for additional complexity.

### Mainnet Fees

Mainnet transaction and query fees can be estimated using the [Hedera Fee Estimator](https://www.hedera.com/fees). The Fee Estimator allows you to determine fees (in both USD and HBAR, using the current exchange rate live on the mainnet) for individual transactions & queries based on their characteristics, as well as projected costs based on expected volume for those transactions. The estimations may not be 100% accurate and the underlying prices are subject to change without prior notice.

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

All fees are subject to change. The fees below reflect a base price for the transaction or query. Transaction characteristics may increase the price from the base price shown below. Transaction characteristics include having more than one signature, a memo field, etc. Please reference the [Hedera fee estimator](https://hedera.com/fees) to estimate the a transaction or query fee.

### Cryptocurrency Service

| Operations                   | USD ($)  |
| ---------------------------- | -------- |
| CryptoCreate                 | $0.05    |
| CryptoAccountAutoRenew       | $0.00022 |
| CryptoDeleteAllowance        | $0.05    |
| CryptoApproveAllowance       | $0.05    |
| CryptoUpdate                 | $0.00022 |
| CryptoTransfer               | $0.0001  |
| CryptoTransfer (custom fees) | $0.002   |
| CryptoDelete                 | $0.005   |
| CryptoGetAccountRecords      | $0.0001  |
| CryptoGetAccountBalance      | $0.00    |
| CryptoGetInfo                | $0.0001  |
| CryptoGetStakers             | $0.0001  |

### Consensus Service

| Operations             | USD ($)  |
| ---------------------- | -------- |
| ConsensusCreateTopic   | $0.01    |
| ConsensusUpdateTopic   | $0.00022 |
| ConsensusDeleteTopic   | $0.005   |
| ConsensusSubmitMessage | $0.0001  |
| ConsensusGetInfo       | $0.0001  |

### Token Service

| Operations                | USD ($) |
| ------------------------- | ------- |
| TokenCreate               | $1.00   |
| TokenCreate (custom fees) | $2.00   |
| TokenUpdate               | $0.001  |
| TokenFeeScheduleUpdate    | $0.001  |
| TokenDelete               | $0.001  |
| TokenAssociate            | $0.05   |
| TokenDissociate           | $0.05   |
| TokenMint (fungible)      | $0.001  |
| TokenMint (non-fungible)  | $0.05   |
| TokenBurn                 | $0.001  |
| TokenGrantKyc             | $0.001  |
| TokenRevokeKyc            | $0.001  |
| TokenFreeze               | $0.001  |
| TokenUnfreeze             | $0.001  |
| TokenPause                | $0.001  |
| TokenUnpause              | $0.001  |
| TokenWipe                 | $0.001  |
| TokenGetInfo              | $0.0001 |
| TokenGetNftInfo           | $0.0001 |
| TokenGetNftInfos          | $0.0001 |
| TokenGetAccountNftInfos   | $0.0001 |

### Schedule Transaction

| Operations      | USD ($) |
| --------------- | ------- |
| ScheduleCreate  | $0.01   |
| ScheduleSign    | $0.001  |
| ScheduleDelete  | $0.001  |
| ScheduleGetInfo | $0.0001 |

### File Service

| Operations      | USD ($) |
| --------------- | ------- |
| FileCreate      | $0.05   |
| FileUpdate      | $0.05   |
| FileDelete      | $0.007  |
| FileAppend      | $0.05   |
| FileGetContents | $0.0001 |
| FileGetInfo     | $0.0001 |

### Smart Contract Service

| Operations          | USD ($) |
| ------------------- | ------- |
| ContractCreate      | $1.0    |
| ContractUpdated     | $0.026  |
| ContractDelete      | $0.007  |
| ContractCall        | $0.05   |
| ContractCallLocal   | $0.001  |
| ContractGetByteCode | $0.05   |
| GetBySolidityID     | $0.0001 |
| ContractGetInfo     | $0.0001 |
| ContractGetRecords  | $0.0001 |
| ContractAutoRenew   | $0.026  |

### Miscellaneous

| Operations            | USD ($) |
| --------------------- | ------- |
| EthereumTransaction   | $0.0001 |
| PrngTransaction       | $0.001  |
| GetVersion            | $0.001  |
| GetByKey              | $0.0001 |
| TransactionGetReceipt | $0.0000 |
| TransactionGetRecord  | $0.0001 |
