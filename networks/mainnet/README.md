---
description: Join Hedera Mainnet
---

# Mainnet

## Overview

The Hedera mainnet (short for main network) is where applications are run in production, with transaction fees paid in [HBAR](https://www.hedera.com/hbar). Any application or retail user can submit transactions to the Hedera mainnet; they're automatically consensus-timestamped and fairly ordered.&#x20;

Any Hedera account can query data associated with Hedera's services and stored on-chain. Every transaction requires payment as a **transaction fee** denominated in **tinybars** (100,000,000 tℏ = 1 ℏ). You can learn more about transaction fees and estimate your application costs [here](https://www.hedera.com/fees).&#x20;

If you're looking to test your application (or just experiment), please visit [Testnet Access](../testnet/testnet-access.md). The Hedera testnet enables developers to prototype and test applications in a simulated mainnet environment that uses test _HBAR_ for paying transaction fees.

{% hint style="warning" %}
**Transaction Throttles**\
Transactions on the Hedera Mainnet are currently throttled. You will receive a `"BUSY"` response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

## Main Network Throttles

<table><thead><tr><th width="267">Network Request Types</th><th>Throttle (tps)</th></tr></thead><tbody><tr><td><strong>Cryptocurrency Transactions</strong></td><td><p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>TransferTransaction</code> (inc. tokens): 10,000 tps<br><code>Other</code>: 10,000 tps</p></td></tr><tr><td><strong>Consensus Transactions</strong></td><td><p><code>TopicCreateTransaction</code>: 5 tps</p><p><code>Other</code>: 10,000 tps</p></td></tr><tr><td><strong>Token Transactions</strong></td><td><p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p><code>TokenAssociateTransaction</code>: 100 tps<br><code>TransferTransaction</code> (inc. tokens): 10,000 tps</p><p><code>Other</code>: 3,000 tps</p></td></tr><tr><td><strong>Schedule Transactions</strong></td><td><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</td></tr><tr><td><strong>File Transactions</strong></td><td>10 tps</td></tr><tr><td><strong>Smart Contract Transactions</strong></td><td><code>ContractExecuteTransaction</code>: 15 million gas per second<br><code>ContractCreateTransaction</code>: 15 million gas per second</td></tr><tr><td><strong>Queries (per node)</strong></td><td><p><code>AccountBalanceQuery</code>: 1000 tps</p><p></p><p><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetContents</code>: 700 tps<br><br><code>Other</code>: 10,000 tps</p></td></tr><tr><td><strong>Receipts</strong></td><td>unlimited (no throttle)</td></tr></tbody></table>
