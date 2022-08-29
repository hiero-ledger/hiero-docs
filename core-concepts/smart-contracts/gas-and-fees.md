# Gas and Fees

Gas is used to pay for Hedera Smart Contract Service transactions like creating a contract, calling a smart contract function, or returning contract values. Gas reflects the cost necessary to pay for the computational resources used to process transactions.

### Gas Fees

Gas fees include the intrinsic gas cost and the cost of the EVM operation from the London gas schedule for all non-Hedera Service transactions. The changes from the London gas schedule are noted [here](hyperledger-besu-evm.md#gas-schedule). The intrinsic gas cost is 21,000 per transaction plus the cost of input data (16 gas per non-zero byte and 4 gas per zero byte). If you are calling a Hedera Service transaction in your contract then an additional Hedera Service transaction gas fee will be assessed along with the intrinsic gas cost and EVM operation cost.

**Total Gas (non-Hedera Service transaction) = Intrinsic Gas + EVM Operation Gas**

**Total Gas (Hedera Service transaction) = Intrinsic Gas + EVM Operation Gas + Hedera Service Gas**

The Hedera Service transaction gas fee is calculated using the [USD](../../mainnet/fees/#transaction-and-query-fees) price of the native Hedera Service transaction multiplied by the gas/USD conversion rate with an additional 20% charge. For example, a native token burn transaction costs $0.001 USD. To convert that to gas you would use the gas/USD conversion rate 1 gas = $0.000\_000\_0569 USD. Then you would add an additional 20% of gas to get the total gas cost.

To calculate the price of gas in USD you can take the gas amount and multiply it by the USD/gas conversion rate of $0.000\_000\_0569 USD/1 gas. For example, the price of 2 million gas in USD is $0.1138 (2,000,000 gas\*($0.000\_000\_0569 USD/1 gas). \
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
Gas throttle per contract call and contract create: **8 million gas per second**.
{% endhint %}

Reference [HIP-185](https://hips.hedera.com/hip/hip-185)

### Smart Contract Rent

{% hint style="danger" %}
Smart contract entity auto renewal and expiry will be enabled in a future release. Please check out [HIP-16](https://hips.hedera.com/hip/hip-16) for more information.
{% endhint %}

[Smart contract rent](https://hedera.com/blog/smart-contract-rent-on-hedera-is-coming-what-you-need-to-know) on Hedera will start once a total of **100 million key-value pairs** are stored cumulatively across the network. The expectation is that Hedera Coin Economics Committee will set this rent rate of _**$0.02 per key-value pair per year**_. This applies to all contracts on Hedera, regardless of the contract being created before or after the rent payments go live.

Once rent payments are enabled on Hedera:

* Each contract has **100 free key-value pairs** of storage available
* Once a contract exceeds the first 100 free key-value pairs, it must pay rent. Note that valid renewal windows are between \~30 and \~92 days (see [HIP-372](https://hips.hedera.com/hip/hip-372))

If a high enough utilization threshold is reached, **congestion pricing applies**

* In this circumstance, prices charged are inversely proportional to the remaining system capacity of the network (lower remaining capacity means higher pricing)
* This applies to all transactions
