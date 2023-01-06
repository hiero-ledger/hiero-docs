# Gas and Fees

Gas is used to pay for Hedera Smart Contract Service transactions like creating a contract, calling a smart contract function, or returning contract values. Gas reflects the cost necessary to pay for the computational resources used to process transactions.

### Gas Fees

Gas fees include the intrinsic gas cost and the cost of the EVM operation from the London gas schedule for all non-Hedera Service transactions. The changes from the London gas schedule are noted [here](hyperledger-besu-evm.md#gas-schedule). The intrinsic gas cost is 21,000 per transaction plus the cost of input data (16 gas per non-zero byte and 4 gas per zero byte). If you are calling a Hedera Service transaction in your contract then an additional Hedera Service transaction gas fee will be assessed along with the intrinsic gas cost and EVM operation cost.

**Total Gas (non-Hedera Service transaction) = Intrinsic Gas + EVM Operation Gas**

**Total Gas (Hedera Service transaction) = Intrinsic Gas + EVM Operation Gas + Hedera Service Gas**

The Hedera Service transaction gas fee is calculated using the [USD](../../mainnet/fees/#transaction-and-query-fees) price of the native Hedera Service transaction multiplied by the gas/USD conversion rate with an additional 20% charge. For example, a native token burn transaction costs $0.001 USD. To convert that to gas you would use the gas/USD conversion rate 1 gas = $0.000\_000\_0569 USD. Then you would add an additional 20% of gas to get the total gas cost.

To calculate the price of gas in USD you can take the gas amount and multiply it by the USD/gas conversion rate of $0.000\_000\_0569 USD/1 gas. For example, the price of 2 million gas in USD is $0.1138 (2,000,000 gas\*($0.000\_000\_0569 USD/1 gas).\
\
Contract call transaction USD/gas conversion rate is $0.000\_000\_0852. The HAPI fee is wrapped into the per gas unit cost for this transaction and is not additionally charged.

Please use a test network to validate the gas costs for execution.

### Gas Per Second Throttling

Most Ethereum based blockchains place a limit on the amount of gas per block that transactions can consume. This is done to place a limit on the amount of time spent in block validation so that the miner nodes can more quickly produce new nodes. While Hedera does not have blocks or miners, in the context of how a Nakamoto consensus system would use it, we are constrained by the physics of time as to how many blocks we can process.

For smart contract transactions, gas is a better measure of the complexity of the transaction than counting all transactions the same, so metering the limits on gas provides a more reasonable limit on resource consumption.

To allow for more flexibility in what transactions we accept and to mirror Ethereum Mainnet behavior the transactions limits will be calculated on a per-gas basis for smart contract calls in addition to a per-transaction limit.

{% hint style="info" %}
The system gas throttle introduced in the Hedera Service release 0.22 is **15 million gas per second.**
{% endhint %}

### Gas Reservation and Unused Gas Refund

Hedera throttles prior to consensus, and nodes limit the number of transactions they can submit to the network. Then at consensus time if the maximum number of transactions is exceeded the excess transactions are not evaluated and are canceled with a busy state. Throttling by variable gas amounts provides some challenges to this system, where the nodes only submit a share of their transaction limit.

To address this, throttling will be based on two different gas measures: prior to consensus and post-consensus. Pre-consensus throttling will use the `gasLimit` to measure the throttle. Post-consensus time throttling will use the actual evaluated transaction gas limit. It is impossible to know the actual evaluated gas pre-consensus because network state can directly impact the flow of the transaction, which is why pre-consensus uses the `gasLimit` field and will be referred to as the **gas reservation**.

Contract query requests are not submitted to consensus and are only executed on the node receiving the request. Hence, they will only count against the local node’s precheck throttle. Contract transactions are executed in consensus and count against both precheck throttle limits and consensus throttle limits. The throttle limits for precheck and consensus may be set to different values.

In order to ensure that the transactions can execute properly, it is common to set a higher gas reservation than will be consumed by execution. In Ethereum Mainnet the entire reservation is charged to the account prior to execution and then the unused portion of the reservation is credited back. Ethereum Mainnet, however, utilizes a memory pool and does transaction ordering at block production time, and that allows the block limit to be based only on used gas and not reserved gas.

In order to prevent over-reservation of the smart contract services, the gas credited back relative to the reservation will be limited to at most 20% of the reservation amount. From a different perspective, the amount of gas charged to a user based on the reservation will be a minimum of 80% of the reservation. This will incentivize transaction submitters to get within 25% of the actual gas used in order to not be charged for the unused gas reservation.

For example, you reserve 5 million gas to create a smart contract. The gas used to perform that transaction was 2 million. The gas that will be refunded to your account will be 20% of the 5 million gas that you reserved resulting in a 1 million gas refund even though a total of 3 million gas was not used for the transaction.

### Maximum Gas per Transaction

Because consensus time execution is now limited by actual gas used and not based on a transaction count, it is now safe to raise the limit of gas available to each transaction. Prior to gas based metering, it would be possible for each transaction to consume the maximum gas per transaction without regard to the other transactions, so limits were based on this worst case scenario. Now that throttling is the aggregate gas used, we can allow each transaction to consume large amounts of gas without concern for an extreme surge.

When a transaction is submitted to a node with a `gasLimit` that is greater than the per-transaction gas limit the transaction must be rejected during precheck with a response code of `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`. The transaction must not be submitted to consensus.

{% hint style="info" %}
Gas throttle per contract call and contract create: **15 million gas per second**.
{% endhint %}

Reference [HIP-185](https://hips.hedera.com/hip/hip-185)

### Smart Contract Rent: Auto Renewal and Storage

Rent is the recurring payment required for contracts to remain active on the network. Rent is comprised of **auto renewal** and **storage** payments.

{% hint style="warning" %}
Smart contract expiry and auto renewal, and storage payments are currently disabled. These features will be enabled on mainnet February 9, 2022.
{% endhint %}

#### Contract Auto Renewal

Smart contract expiry and auto renewal was introduced in the `0.30.4` Hedera Services release. All contract authors to are encouraged to set an auto-renew account for their contract.\
\
All non-deleted contracts will have their expiry extended to at least 90 days after the `0.30.4` upgrade date\
\
About 90 days after the `0.30.4` upgrade, some contracts will begin to expire. The network will try to automatically charge the **renewal payment** to the expired contract's auto-renew account. If an auto-renew account has zero balance, the network will then try to charge the contract itself.

A contract unable to pay renewal fees will enter a week-long "grace period" during which it is unusable, unless its expiry is extended via `ContractUpdate` or it receives hbar. After this grace period, the contract will be purged from state.

#### Storage Payment

[Contract **storage payments** on Hedera](https://hedera.com/blog/smart-contract-rent-on-hedera-is-coming-what-you-need-to-know) will start once a total of **100 million key-value pairs** are stored cumulatively across the network. The expectation is that Hedera Coin Economics Committee will set a rate of **$0.02 per key-value pair per year**. This applies to all contracts on Hedera, regardless of the contract being created before or after the rent payments go live.

Once storage payments are enabled on Hedera:

* Each contract has **100 free key-value pairs** of storage available
* Once a contract exceeds the first 100 free key-value pairs, it must pay storage fees. Note that valid renewal windows are between \~30 and \~92 days (see [HIP-372](https://hips.hedera.com/hip/hip-372))

{% hint style="info" %}
Storage fees will be part of the rent payment collected when a contract is auto-renewed.
{% endhint %}

If a high enough utilization threshold is reached, **congestion pricing applies**

* In this circumstance, prices charged are inversely proportional to the remaining system capacity of the network (lower remaining capacity means higher pricing)
* This applies to all transactions

### Smart Contract Rent - Frequently Asked Questions (FAQ)

<details>

<summary>Why do contracts have to pay rent on Hedera?</summary>

Distributed networks like Hedera have a finite amount of computational resources. When entities like smart contracts are deployed on a decentralized network, a portion of those resources are consumed. Thus, it is unfeasible to maintain an unlimited number of entities for an infinite amount of time on finite resources. Solving this problem is necessary, and it’s a key topic of discussion by Leemon and [others](https://www.coindesk.com/markets/2018/03/27/vitalik-wants-you-to-pay-to-slow-ethereums-growth/) in the layer 1 network space.&#x20;

Contract rent is an economically and technically viable approach to manage smart contract entities and state storage.

</details>

<details>

<summary>Do all entities on Hedera have to pay rent or just contracts?</summary>

Contracts are the first entity paying rent on Hedera starting in February 2023.

Eventually, all other network entities (e.g. Tokens, accounts, topics, and files) will pay rent as well. However, the timeline for the rent of other entities is not yet defined. Sufficient time and notice will be provided to the community before enabling rent for other entities.

</details>

<details>

<summary>What charges are included in contract rent?</summary>

Rent is defined as the recurring payment required for contracts (and, eventually, all other Hedera entities) to remain active on the network. For contracts, rent is comprised by **auto-renewal** and **storage** payments:

* **Auto-renewal payments** will be enabled on mainnet (for contracts only) with the Hedera Services release planned for February 2023. The auto-renewal fee for a contract is $0.026 USD per 90 days
* **Storage payments** will start once a total of **100 million key-value pairs** are stored cumulatively across the network. These storage fees will be part of the rent payment collected when a contract is auto renewed. The storage fee rate is $0.02 per key-value pair per year

<img src="../../.gitbook/assets/image (2).png" alt="" data-size="original">

</details>

<details>

<summary>What are the steps in the renewal process? And what happens if a contract doesn’t pay rent?</summary>

Every entity on Hedera has the fields `expirationTime`, `autorenewPeriod`, and `autorenewAccount`.

1. When the `expirationTime` for a contract is reached, the network will first try to charge rent to the contract’s `autoRenewAccount`
   * If renewal is successful, then the contract remains active on the network
   * If renewal fails, then the contract is marked as `expired`
2. An `expired` entity is given a grace period before it is removed from the network. During the grace period, the entity (contract) is inactive, and all transactions involving it will fail, except for an update transaction to extend the `expirationTime`
   * A contract in the grace period can be immediately "re-activated" by either sending it some hbar or manually extending its `expirationTime` via a contract update transaction
3. At the end of the grace period, the contract is permanently removed from the ledger if:
   * The contract and its `autoRenewAccount` still have a zero HBAR balance at the end of the grace period, OR
   * The contract is not manually extended during the grace period

Note that the ID number of a removed entity is not reused going forward. In addition, if an entity was marked as `deleted`, then it cannot have its `expirationTime` extended. Neither an update transaction nor an auto-renew will be able to extend it.

See the diagram below and [HIP-16](https://hips.hedera.com/hip/hip-16) for more details.

![](../../.gitbook/assets/Untitled.png)

</details>

<details>

<summary>Who pays for the contract’s renewal and storage fees?</summary>

Smart contracts on Hedera can pay for rent in two ways: external funds or contract funds.

When the `expirationTime` for a contract is reached, the network will first try to charge rent to the contract’s `autoRenewAccount`:&#x20;

* If the `autoRenewAccount` has sufficient HBAR to pay for the `autoRenewPeriod`, then the contract is successfully renewed
* If the `autoRenewAccount` has some HBAR but not enough to afford the full `autoRenewPeriod`, then the contract is extended for as long as possible (say, 1 week instead of 90 days). Once that extension (1 week) elapses, if the `autoRenewAccount` hasn't been re-funded to cover the `autoRenewPeriod`, then the contract account itself will be charged for rent
* If the `autoRenewAccount` has a zero HBAR balance, then the contract itself is charged
* If the `autoRenewAccount` and the contract both have a zero HBAR balance at the time that renewal fees are due, the contract is marked as `expired`

</details>

<details>

<summary>What happens if I call a contract that is expired?</summary>

Calling an `expired` contract will resolve to `CONTRACT_EXPIRED_AND_AWAITING_REMOVAL`.

</details>

<details>

<summary>For how long can I renew my contract?</summary>

The minimum renewal period possible is 2,592,000 seconds (\~30 days) and the maximum is 8,000,001 seconds (\~92 days).

See details in [HIP-372: Entity Auto-Renewals and Expiry Window](https://hips.hedera.com/hip/hip-372).

</details>

<details>

<summary>If I change the <code>autoRenewPeriod</code> of my contract from 30 to 90 days, what will the cost of my transaction be?</summary>

The cost of rent scales just about linearly with the length of the renewal period. So a renewal that pays for 90 days will cost \~3 times as much as a renewal that pays for 30 days.

</details>

<details>

<summary>What are the key-value pair thresholds that I should be aware of that impact the size of the storage payment?</summary>

* Storage payments for contracts will only start being charged once **100 million key-value pairs** are reached cumulatively across the network
* After than, each contract has **100 free key-value pairs** of storage available. Once a contract exceeds the first 100 free key-value pairs, it must pay storage fees

</details>

<details>

<summary>Where do the auto-renewal transactions appear? Can these be seen on network explorers like HashScan?</summary>

According to [HIP-16: Entity Auto-Renewal](https://hips.hedera.com/hip/hip-16), records of auto-renew charges will appear as `actions` in the record stream, and will be available via mirror nodes. No receipts or records for auto-renewal actions will be available via HAPI queries.

[HIP-449](https://hips.hedera.com/hip/hip-449) provides technical details on how information for expiring contracts is included in the record stream.

</details>
