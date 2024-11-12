---
description: Join Hedera Mainnet
---

# Mainnet

## Overview

The Hedera mainnet (short for main network) is where applications are run in production, with transaction fees paid in [HBAR](https://www.hedera.com/hbar). Any application or retail user can submit transactions to the Hedera mainnet; they're automatically consensus-timestamped and fairly ordered.&#x20;

Any Hedera account can query data associated with Hedera's services and stored on-chain. Every transaction requires payment as a **transaction fee** denominated in _**tinybars**_ (100,000,000 tℏ = 1 ℏ). You can learn more about transaction fees and estimate your application costs [here](https://www.hedera.com/fees).&#x20;

If you're looking to test your application (or just experiment), please visit [Testnet Access](../testnet/testnet-access.md). The Hedera testnet enables developers to prototype and test applications in a simulated mainnet environment that uses test _HBAR_ for paying transaction fees.

{% hint style="warning" %}
**Transaction Throttles**\
Transactions on the Hedera Mainnet are currently throttled. You will receive a `"BUSY"` response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

## Main Network Throttles

| Network Request Types           | Throttle (tps)                                                                                                                                                                                                                                                                                                       |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cryptocurrency Transactions** | <p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>TransferTransaction</code> (inc. tokens): 10,000 tps<br><code>Other</code>: 10,000 tps</p>                                                                                                                                                               |
| **Consensus Transactions**      | <p><code>TopicCreateTransaction</code>: 5 tps</p><p><code>Other</code>: 10,000 tps</p>                                                                                                                                                                                                                               |
| **Token Transactions**          | <p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p><code>TokenAssociateTransaction</code>: 100 tps<br><code>TransferTransaction</code> (inc. tokens): 10,000 tps</p><p><code>Other</code>: 3,000 tps</p>                                             |
| **Schedule Transactions**       | <p><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</p>                                                                                                                                                                                                              |
| **File Transactions**           | 10 tps                                                                                                                                                                                                                                                                                                               |
| **Smart Contract Transactions** | <p><code>ContractExecuteTransaction</code>: 350 tps<br><code>ContractCreateTransaction</code>: 350 tps</p>                                                                                                                                                                                                           |
| **Queries (per node)**          | <p><code>AccountBalanceQuery</code>: 1000 tps</p><p></p><p><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetContents</code>: 700 tps<br><br><code>Other</code>: 10,000 tps</p> |
| **Receipts**                    | unlimited (no throttle)                                                                                                                                                                                                                                                                                              |
