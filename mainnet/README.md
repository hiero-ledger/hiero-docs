---
description: Join Hedera Mainnet
---

# Mainnet

## Overview

The Hedera Mainnet, short for main network, is where applications are running in production with real transactions and associated costs. Transactions can be submitted to the Hedera Mainnet by any application user and are timestamped and ordered automatically by the distributed ledger. All data associated with Hedera services and stored on ledger are accessible by any Hedera account. For each transaction, a small **transaction fee** will be charged (in tinybars). You can find more information about transaction fees [here](https://www.hedera.com/fees). If you are looking to test your application or just to experiment, please check out [Testnet Access,](../testnet/testnet-access.md) which allows you to prototype and practice in a real Hedera network without incurring those fees.

{% hint style="warning" %}
**Limited Support**\
Transactions are currently [throttled](https://docs.hedera.com/guides/mainnet#network-throttles) for Mainnet. You will receive a "BUSY" response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

| Network Services       | Availability |
| ---------------------- | ------------ |
| Cryptocurrency         | Limited      |
| Smart Contract Service | Limited      |
| File Service           | Limited      |
| Consensus Service      | Limited      |
| Token Service          | Limited      |

#### Network Throttles

| Network Request Types       | Throttle (tps)                                                                                                                                                                                            |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cryptocurrency Transactions | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: unlimited</p><p>TransferTransaction (inc. tokens): 10,000 tps<br>Other: 10,000 tps</p>                                                      |
| Consensus Transactions      | <p>TopicCreateTransaction: 5 tps</p><p>Other: 10,000 tps</p>                                                                                                                                              |
| Token Transactions          | <p>TokenMint:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p>TokenAssociateTransaction: 100 tps<br>TransferTransaction (inc. tokens): 10,000 tps</p><p>Other: 3,000 tps</p> |
| Schedule Transactions       | <p>ScheduleSignTransaction: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                                                             |
| File Transactions           | 10 tps                                                                                                                                                                                                    |
| Smart Contract Transactions | <p>ContractExecuteTransaction: 350 tps<br>ContractCreateTransaction: 350 tps</p>                                                                                                                          |
| Queries                     | <p>ContractGetInfo: 700 tps<br>ContractGetBytecode: 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo: 700 tps<br>FileGetContents: 700 tps<br><br>Other: 10,000 tps</p>                            |
| Receipts                    | unlimited (no throttle)                                                                                                                                                                                   |
