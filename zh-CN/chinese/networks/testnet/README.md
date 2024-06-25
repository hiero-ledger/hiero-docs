---
description: Join a Hedera Testnet
---

# Testnets

## Overview

Hedera test networks provide developers access to a free testing environment for Hedera network services. Testnets simulate the exact development environment as you would expect for mainnet. This includes transaction fees, throttles, available services, etc. To create a Hedera Testnet or Previewnet account, you can visit the [Hedera Developer Portal](https://portal.hedera.com/login).

Once your application has been built and tested in this test environment, you can expect to migrate your decentralized application (dApp) to mainnet without any changes.

<table><thead><tr><th width="324">Test Networks</th><th>Description</th></tr></thead><tbody><tr><td><strong>Testnet</strong></td><td>Testnet runs the same code as the Hedera Mainnet, designed to provide a pre-production environment for developers about to move to mainnet. You can find compatible SDKs <a href="../../sdks-and-apis/sdks/#hedera-supported-sdks">here</a>.</td></tr><tr><td><strong>Previewnet</strong></td><td><p>Code that is under development by the Hedera team and likely to be used in an upcoming release designed to give developers early exposure to features coming down the pipe. Updates to the network are made frequently. There is no guarantee an SDK will readily support the up-and-coming features.</p><p><strong>Note:</strong> Updates to this network are triggered by a new release and are frequent. These updates will not be reflected on the status page.</p></td></tr></tbody></table>

<table><thead><tr><th width="325">Network Service</th><th>Availability</th></tr></thead><tbody><tr><td><strong>Cryptocurrency</strong></td><td>Limited</td></tr><tr><td><strong>Consensus Service</strong></td><td>Limited</td></tr><tr><td><strong>File Service</strong></td><td>Limited</td></tr><tr><td><strong>Smart Contract Service</strong></td><td>Limited</td></tr><tr><td><strong>Token Service</strong></td><td>Limited</td></tr></tbody></table>

### Test Network Resets

The mirror node and consensus node test network are scheduled to reset once a quarter. When a testnet reset occurs, all account, token, contract, topic, schedule, and file data are wiped.

Developers will no longer have access to the state data from test network consensus nodes. For example, you will not be able to perform transactions or queries on an account that existed before the reset.&#x20

The testnet mirror node will be available for developers to store any data before access is completely removed for two weeks after the date of the reset. You will be able to query old testnet information for the two-week period if it is available.

**What you should do:**

- Take note of the upcoming reset dates.
- Have the ability to recreate test data for your application to minimize interruptions.
- After the reset, you will need to visit the [Hedera Developer Portal](https://portal.hedera.com/register) to get your new testnet account ID.
  - The public and private key pair will remain the same after resets.
- Subscribe to the [Hedera status page](https://status.hedera.com/) to receive reset notifications.
- Mirror Node operators can reference the instructions [here](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) to set up your mirror node
  - GCP GCS and AWS S3 buckets: `hedera-testnet-streams-2023-01`

If you have any questions or concerns, please connect with us via [Discord](https://hedera.com/discord).

**Reset Dates:**

**2024**

- February 1, 2024 - Completed
- April 25, 2024
- July 25, 2024
- Oct 31, 2024

**2023**

- January 26, 2023 - Completed&#x20
- April 27, 2023 - Skipped&#x20
- July 27, 2023 - Completed
- October 26, 2023 - Skipped

### Test Network Throttles

{% hint style="warning" %}
**Limited Support**\
Transactions are currently throttled for testnets. You will receive a **`BUSY`** response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

<table><thead><tr><th width="322">Network Request Type</th><th>Throttle (tps)</th></tr></thead><tbody><tr><td><strong>Cryptocurrency Transactions</strong></td><td><p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>AccountBalanceQuery</code>: unlimited<br><code>TransferTransaction</code> (inc. tokens): 10,000 tps</p><p><code>Other</code>: 10,000 tps</p></td></tr><tr><td><strong>Consensus Transactions</strong></td><td><p><code>TopicCreateTransaction</code>: 5 tps</p><p><code>Other</code>: 10,000 tps</p></td></tr><tr><td><strong>Token Transactions</strong></td><td><p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p><code>TokenAssociateTransaction</code>: 100 tps<br><code>TransferTransaction</code> (inc. tokens): 10,000 tps</p><p><code>Other</code>: 3,000 tps</p></td></tr><tr><td><strong>Schedule Transactions</strong></td><td><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</td></tr><tr><td><strong>File Transactions</strong></td><td>10 tps</td></tr><tr><td><strong>Smart Contract Transactions</strong></td><td><code>ContractExecuteTransaction</code>: 350 tps<br><code>ContractCreateTransaction</code>: 350 tps</td></tr><tr><td><strong>Queries</strong></td><td><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetContents</code>: 700 tps<br><br><code>Other</code>: 10,000 tps</td></tr><tr><td><strong>Receipts</strong></td><td>unlimited (no throttle)</td></tr></tbody></table>
