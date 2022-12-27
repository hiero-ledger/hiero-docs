---
description: Join a Hedera Testnet
---

# Testnets

## Overview

Hedera testnets provide developers with access to a free testing environment for Hedera network services. Testnets simulate the exact same development environment as you would expect for mainnet. This includes transaction fees, throttles, available services, etc. To create a Hedera testnet or previewnet account, you can visit the [Hedera Developer Portal](https://portal.hedera.com/login).

Once your application has been built and tested in this test environment you can expect to migrate your decentralized application to mainnet without any changes.

**Test Networks:**

| **Name**             | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Test Network         | Runs the same code as the Hedera mainnet, designed to provide a pre-production environment for developers about to move to mainnet. You can find compatible SDKs [here](../../docs/sdks/#hedera-supported-sdks).                                                                                                                                                                                                                                                             |
| Preview Test Network | <p>Code that is under development by the Hedera team, and likely to be used in an upcoming release, designed to give developers early exposure to features coming down the pipe. Updates to the network are made frequently. There is no guarantee an SDK will readily support the up-and-coming features.</p><p><strong>Note:</strong> Updates to this network are triggered by a new release and are frequent. These updates will not be reflected on the status page.</p> |

| Network Service        | Availability |
| ---------------------- | ------------ |
| Cryptocurrency         | Limited      |
| Consensus Service      | Limited      |
| File Service           | Limited      |
| Smart Contract Service | Limited      |
| Token Service          | Limited      |

### Test Network Resets

The mirror node and consensus node test network are scheduled to reset once a quarter. When a testnet reset occurs all account, token, contract, topic, schedule, and file data are wiped.

Developers will no longer have access to the state data from test network consensus nodes. For example, you will not be able to perform transactions or queries on an account that existed before the reset.

The testnet mirror node will be available for developers for two weeks after the date of the reset to store any data before access is completely removed. You will be able to query old testnet information for the two week period is it available.

What you should do:

* Take note of the upcoming reset dates
* Have the ability to recreate test data for your application to minimize interruptions
* After the reset, you will need to visit the [Hedera Developer Portal](https://portal.hedera.com/register) to get your new testnet account ID
  * The public and private key pair will remain the same after resets
* Subscribe to the [Hedera status page](https://status.hedera.com/) to receive reset notifications

If you have any questions or concerns, please connect with us via [Discord](https://discord.com/invite/EC2GY8ueRk).

**Reset Dates:**\
\*\*\*\*\
**2022**

* Oct 27, 2022 \[Cancelled]

**2023**

* January 26, 2023
* April 27, 2023
* July 27, 2023
* October 26, 2023

### Test Network Throttles

{% hint style="warning" %}
**Limited Support**\
Transactions are currently throttled for testnets. You will receive a "BUSY" response if the number of transactions submitted to the network exceeds the threshold value.
{% endhint %}

| **Network Request Type**    | **Throttle (tps)**                                                                                                                                                                                                   |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cryptocurrency Transactions | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: unlimited<br>TransferTransaction (inc. tokens): 10,000 tps</p><p>Other: 10,000 tps</p>                                                                 |
| Consensus Transactions      | <p>TopicCreateTransaction: 5 tps</p><p>Other: 10,000 tps</p>                                                                                                                                                         |
| Token Transactions          | <p>TokenMintTransaction:</p><ul><li>125 TPS for fungible mint</li><li>50 TPS for NFT mint</li></ul><p>TokenAssociateTransaction: 100 tps<br>TransferTransaction (inc. tokens): 10,000 tps</p><p>Other: 3,000 tps</p> |
| Schedule Transactions       | <p>ScheduleSignTransaction: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                                                                        |
| File Transactions           | 10 tps                                                                                                                                                                                                               |
| Smart Contract Transactions | <p>ContractExecuteTransaction: 350 tps<br>ContractCreateTransaction: 350 tps<br><br></p>                                                                                                                             |
| Queries                     | <p>ContractGetInfo: 700 tps<br>ContractGetBytecode: 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo: 700 tps<br>FileGetContents: 700 tps<br><br>Other: 10,000 tps</p>                                       |
| Receipts                    | unlimited (no throttle)                                                                                                                                                                                              |
