# Hedera Schedule Service

## Overview

The [Hedera Schedule Service (HSS) system contract](hedera-schedule-service.md) exposes functions that enable smart contracts to interact with Hedera's native schedule service. The schedule service allows transactions to be scheduled for future execution, simplifies multi-sig coordination, and automates execution once all required signatures are collected. This eliminates the need for off-ledger signature coordination and reduces the complexity of multi-sig workflows in decentralized applications (dApps).

Additionally, the HSS includes expiration handling, where scheduled transactions that fail to collect and verify all required signatures within the specified expiration window are automatically removed from the network.&#x20;

The `IHederaScheduleService` interface, introduced in [HIP-755](https://hips.hedera.com/hip/hip-755), allows accounts to interact with the schedule transaction service via smart contracts. Following system contract conventions, HSS will be callable at the reserved `0x16b` address and will expose the following functions callable within smart contracts.

<table><thead><tr><th width="198">Function Name</th><th width="102" align="center">Consensus Node Release Version</th><th width="67" align="center">HIP</th><th>Function Interface</th></tr></thead><tbody><tr><td><code>signSchedule</code></td><td align="center"><a href="https://docs.hedera.com/hedera/networks/release-notes/services#release-v0.57">0.57</a></td><td align="center"><a href="https://hips.hedera.com/hip/hip-755">HIP 755</a></td><td><code>signSchedule() external returns (int64 responseCode)</code></td></tr><tr><td><code>authroizeSchedule</code></td><td align="center"><a href="https://docs.hedera.com/hedera/networks/release-notes/services#release-v0.58">0.58</a></td><td align="center"><a href="https://hips.hedera.com/hip/hip-755">HIP 755</a></td><td><code>authorizeSchedule(address) external returns (int64 responseCode)</code></td></tr></tbody></table>

#### **signSchedule()**

* Callable only by an externally owned account (EOA) or wallet, not callable from a smart contract.
* Signs the specified schedule transaction using the public key of the caller.&#x20;
* Executed through a system proxy contract to support safe and easy first calls by an EOA and ensure compliance with Hedera's security model.&#x20;
* Returns a `responseCode` indicating the success or failure of the signature addition attempt.

#### **authorizeSchedule(address)**

* Signs the schedule transaction identified by the pass-in parameter (`address`) with a `ContractKey` using the format `0.0.<ContractId of the calling contract>`.
* Allows contracts to sign schedule transactions using their own contract key.
* The `address` parameter is the unique identifier of the schedule transaction that specifies the transaction to be signed.
* Returns a `responseCode` indicating the success or failure of the authorization attempt.&#x20;

## Behavior and Costs

#### Behavior

* Scheduled transactions must collect all required signatures before they can be executed. These signatures can be added asynchronously using the `signSchedule` function.
* If all required signatures are not received within the specified expiration window, the transaction expires and is removed from the network.
* The execution of a schedule transaction occurs automatically when the final required signature is submitted.

#### Costs

* Schedule transaction fees are the same as a HAPI sign schedule transaction, with a 20% markup for using system contracts.&#x20;
* Fees include gas costs for EVM execution, storage costs, and network fees for consensus.
* Expired transactions cost no additional fees beyond the initial scheduling and signature costs.

{% embed url="https://docs.hedera.com/hedera/core-concepts/smart-contracts/gas-and-fees#gas-schedule-and-fee-calculation" %}
[Gas fee schedule and calculation](https://docs.hedera.com/hedera/core-concepts/smart-contracts/gas-and-fees#gas-schedule-and-fee-calculation)
{% endembed %}

**Reference**: [HIP-755](https://hips.hedera.com/hip/hip-755)
