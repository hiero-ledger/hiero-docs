---
description: Join Hedera Mainnet
---

# Mainnet

## Overview

The Hedera mainnet (short for main network) is where applications are run in production, with transaction fees paid in [HBAR](https://www.hedera.com/hbar). Transactions are submitted to the Hedera mainnet by any application or retail user; they're automatically consensus timestamped and fairly ordered.

Data associated with Hedera's services and stored on-chain can be queried by any Hedera account. Every transaction requires payment in the form of a **transaction fee** denominated in _**tinybars**_ (100,000,000 tℏ = 1 ℏ). You can learn more about transaction fees and estimate your application costs [here](https://www.hedera.com/fees).

If you're looking to test your application (or just experiment), please visit [Testnet Access](../testnet/testnet-access.md). The Hedera testnet enables developers to prototype and test applications in a simulated mainnet environment that uses test _HBAR_ for paying transaction fees.

{% hint style="warning" %}
**Transaction Throttles**\
Transactions on the Hedera Mainnet are currently throttled. You will receive a `"BUSY"` response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

#### Network Throttles

| Network Request Types       | Throttle (tps)                                                                                                                                                                         |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cryptocurrency Transactions | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: unlimited</p><p>TransferTransaction (inc. tokens): 10,000 tps<br>Other: 10,000 tps</p>                                                      |
| Consensus Transactions      | <p>TopicCreateTransaction: 5 tps</p><p>Other: 10,000 tps</p>                                                                                                                                              |
| Token Transactions          | <p>TokenMint:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p>TokenAssociateTransaction: 100 tps<br>TransferTransaction (inc. tokens): 10,000 tps</p><p>Other: 3,000 tps</p> |
| Schedule Transactions       | <p>ScheduleSignTransaction: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                                                             |
| File Transactions           | 10 tps                                                                                                                                                                                                    |
| Smart Contract Transactions | <p>ContractExecuteTransaction: 350 tps<br>ContractCreateTransaction: 350 tps</p>                                                                                                                          |
| Queries                     | <p>ContractGetInfo: 700 tps<br>ContractGetBytecode: 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo: 700 tps<br>FileGetContents: 700 tps<br><br>Other: 10,000 tps</p>                            |
| Receipts                    | unlimited (no throttle)                                                                                                                                                                |
