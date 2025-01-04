# Gas and Fees

## Gas

When executing smart contracts, the EVM requires the amount of work paid in gas. The “work” includes computation, state transitions, and storage. Gas is the unit of measurement used to charge a fee per opcode executed by the EVM. Each opcode code has a defined gas cost. Gas reflects the cost necessary to pay for the computational resources used to process transactions.

### **Weibar**

The EVM returns gas information in Weibar (introduced in [HIP-410](https://hips.hedera.com/hip/hip-410)). One weibar is `10^-18th` HBAR, which translates to 1 tinybar is `10^10` weibars. As noted in HIP-410, this maximizes compatibility with third-party tools that expect ether units to be operated on in fractions of `10^18`, also known as a Wei.

## **Gas Schedule and Fee Calculation**

Gas is used to charge fees to pay for work performed by the network when a smart contract transaction is submitted. Specifically, transactions of type, `ContractCall`, `ContractCreate` and `EthereumTransactions` have fees charged denominated in gas. Other smart contracts-related transactions `ContractDelete`, `ContractGetInfo` etc., are only accessed by the normal Hedera-related network, node, and service fees denominated in HBAR. Gas fees paid for EVM transactions on Hedera can be composed of three different kinds of gas costs:

* **Intrinsic Gas**: The minimum amount of gas required to execute a transaction.
* **EVM opcode Gas**: The gas required to execute the defined [opcodes](../../support-and-community/glossary.md#opcodes) for the smart contract call.
* **Hedera System Contract Gas**: The required gas that is associated with a Hedera-defined transaction, like using the Hedera Token Service system contract that allows you to burn (`TokenBurnTransaction`) or mint (`TokenMintTransaction`) a token.

### Intrinsic Gas

A transaction submitted to the smart contract service must be sent with enough gas to cover intrinsic gas. With the Cancun fork of the EVM update, intrinsic gas is calculated as:

```bash
21000 + 4 * (number of zeros bytes) + 16 * (number of non-zeros bytes)= intrinsic gas
```

* **21,000**: The base gas cost for any transaction.
* **4 \*** (number of zero bytes): The cost of each zero byte in the transaction payload.
* **16 \*** (number of non-zero bytes): The cost for each non-zero byte in the transaction payload.

If insufficient gas is submitted, the transaction will fail during precheck and no record will be created.

### **EVM Opcode Gas**

Execution costs in the EVM include both fixed and dynamic components. The _fixed execution cost_ is the base cost applied each time the opcode is executed, while the _dynamic execution cost_ varies based on parameters, such as whether the storage slot is "cold" (accessed for the first time in the transaction) or "warm" (already accessed).

**Example**: For the `SLOAD` opcode, which loads data from storage:

* **Fixed Cost**: 100 gas units (base cost per execution)
* **Dynamic Cost (Cold Access)**: 2,100 gas units (first-time access to the storage slot)
* **Dynamic Cost (Warm Access)**: 100 gas units (subsequent access within the transaction)

If `SLOAD` accesses a storage slot twice within the same transaction, the **total gas cost** would be calculated as follows:

1. **First Access (Cold)** = 100 + 2,100 = 2,200 gas
2. **Second Access (Warm)** =  100 + 100 = 200 gas
3. **Final Gas Cost Total = 2,200 + 200 = 2,400 gas**

Here's an interactive opcode [reference](https://www.evm.codes/) supported in the Cancun fork.&#x20;

### **Hedera System Contract Gas**

Hedera system contract gas fees apply only when using a native Hedera service. They are calculated by converting the transaction cost in USD to gas using a set conversion rate. After calculating the base gas cost, a 20% surcharge was added for overhead and variations in gas usage.

**Example**: For a $0.10 transaction with a conversion rate of 1,000,000 gas per USD:

* **Base Gas Cost** = 0.10 × 1,000,000 = <mark style="color:blue;">100,000</mark> gas
* **Total Gas Cost** = <mark style="color:blue;">100,000</mark> × 1.2 = **120,000 gas**

**Final gas cost total** =  **120,000 gas**

### System Contract View Functions

The gas requirements for HTS view functions can be calculated in a slightly modified manner. The transaction type of `getTokenInfo` can be used and a nominal price need not be calculated. This implies that converting the fee into HBAR is not necessary as the canonical price ($0.0001) can be directly converted into gas by using the conversion factor of 852 tinycents. Add 20% markup. Thus gas cost is:

* **Base gas cost** = (1000000 + 852000 - 1) \* 1000 / 852000 = <mark style="color:blue;">2173</mark> gas
* **Total Gas Cost** =  <mark style="color:blue;">2173</mark> x 1.2 = **2607** **gas**

**Final gas cost total** =  **2607** **gas**&#x20;

{% hint style="info" %}
**Example System Contracts:**

* [Hedera Token Service](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/HederaTokenService.sol)
* [Pseudo Random Number Generator (PRNG)](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/pseudo-random-number-generator/PrngSystemContract.sol)
* [Exchange Rate](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/exchange-rate/ExchangeRateSystemContract.sol)

**Learn More:** Our detailed gas calculation [reference](https://github.com/hashgraph/hedera-services/blob/develop/hedera-node/docs/design/services/smart-contract-service/system-contract-gas-calc.md#system-contracts) explains the precise steps for calculating gas fees on Hedera.&#x20;
{% endhint %}

### Gas Limit

The gas limit is the maximum amount of gas you are willing to pay for an operation.

The current opcode gas fees are reflective as of the [0.22 Hedera Service release](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22).

| Operation                                                               | Cancun Cost (Gas)                              | Current Hedera (Gas)                           |
| ----------------------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------- |
| Code deposit                                                            | 200 \* bytes                                   | 200 \* bytes                                   |
| <p><code>BALANCE</code><br>(cold account)</p>                           | 2600                                           | 2600                                           |
| <p><code>BALANCE</code><br>(warm account)</p>                           | 100                                            | 100                                            |
| `EXP`                                                                   | 10 + 50/byte                                   | 10 + 50/byte                                   |
| <p><code>EXTCODECOPY</code><br>(cold account)</p>                       | 2600 + Mem                                     | 2600 + Mem                                     |
| <p><code>EXTCODECOPY</code><br>(warm account)</p>                       | 100 + Mem                                      | 100 + Mem                                      |
| <p><code>EXTCODEHASH</code><br>(cold account)</p>                       | 2600                                           | 2600                                           |
| <p><code>EXTCODEHASH</code><br>(warm account)</p>                       | 100                                            | 100                                            |
| <p><code>EXTCODESIZE</code><br>(cold account)</p>                       | 2600                                           | 2600                                           |
| <p><code>EXTCODESIZE</code><br>(warm account)</p>                       | 100                                            | 100                                            |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>        | <p>375 + 375*topics<br>+ data Mem</p>          | <p>375 + 375*topics<br>+ data Mem</p>          |
| <p><code>SLOAD</code><br>(cold slot)</p>                                | 2100                                           | 2100                                           |
| <p><code>SLOAD</code><br>(warm slot)</p>                                | 100                                            | 100                                            |
| <p><code>SSTORE</code><br>(new slot)</p>                                | 22,100                                         | 22,100                                         |
| <p><code>SSTORE</code><br>(existing slot,<br>cold access)</p>           | 2,900                                          | 2,900                                          |
| <p><code>SSTORE</code><br>(existing slot,<br>warm access)</p>           | 100                                            | 100                                            |
| <p><code>SSTORE</code><br>refund</p>                                    | As specified by the EVM                        | As specified by the EVM                        |
| <p><code>CALL</code> <em>et al</em>.<br>(cold recipient)</p>            | 2,600                                          | 2,600                                          |
| <p><code>CALL</code> <em>et al</em>.<br>(warm recipient)</p>            | 100                                            | 100                                            |
| <p><code>CALL</code> <em>et al</em>.<br>HBAR/ETH Transfer Surcharge</p> | 9,000                                          | 9,000                                          |
| <p><code>SELFDESTRUCT</code><br>(cold beneficiary)</p>                  | 2600                                           | 2600                                           |
| <p><code>SELFDESTRUCT</code><br>(warm beneficiary)</p>                  | 0                                              | 0                                              |
| `TSTORE`                                                                | 100                                            | 100                                            |
| `TLOAD`                                                                 | 100                                            | 100                                            |
| `MCOPY`                                                                 | 3 + 3\*words\_copied + memory\_expansion\_cost | 3 + 3\*words\_copied + memory\_expansion\_cost |

The terms 'warm' and 'cold' in the above table correspond with whether the account or storage slot has been read or written to within the current smart contract transaction, even within a child call frame.

'`CALL` _et al._' includes with limitation: `CALL`, `CALLCODE`, `DELEGATECALL`, and `STATICCALL`

Reference: [HIP-206](https://hips.hedera.com/hip/hip-206), [HIP-865](https://hips.hedera.com/hip/hip-865)

### Gas Per Second Throttling

Most EVM-compatible networks use a per-block gas limit to control resource allocation and limit block validation time, enabling miner nodes to produce new blocks quickly. While Hedera lacks blocks and miners, it must still manage resource use over time.

For smart contract transactions, gas is a more effective measure of transaction complexity than transaction count. To balance flexibility and resource management, Hedera mirrors Ethereum's approach by setting transaction limits based on gas consumption (for `ContractCreate`, `ContractCall`, and `ContractCallLocalQuery`), alongside per-transaction limits. This dual method enables precise regulation of smart contract executions.

The Hedera network has implemented a system gas throttle of **15 million gas per second** in the Hedera Service release [0.22](../../networks/release-notes/services.md#v0.22).&#x20;

### Gas Reservation and Unused Gas Refund

Hedera throttles transactions before consensus, and nodes limit the number of transactions they can submit to the network. Then, at consensus time, if the maximum number of transactions is exceeded, the excess transactions are not evaluated and are canceled with a busy state. Throttling by variable gas amounts provides challenges to this system, where the nodes only submit a share of their transaction limit.

To address this, throttling will be based on a two-tiered gas measuring system: pre-consensus and post-consensus. Pre-consensus throttling will use the `gasLimit` field specified in the transaction. Post-consensus will use the actual evaluated amount of gas the transaction consumes, allowing for dynamic adjustments in the system. It is impossible to know the _actual_ evaluated gas pre-consensus because the network state can directly impact the flow of the transaction, which is why pre-consensus uses the `gasLimit` field and will be referred to as the **gas reservation**.

Contract query requests are unique and bypass the consensus stage altogether. These requests are executed solely on the local node that receives them and only influence that specific node's precheck throttle. On the other hand, standard contract transactions go through both the precheck and consensus stages and are subject to both sets of throttle limits. The throttle limits for precheck and consensus may be set to different values.

In order to ensure that the transactions can execute properly, setting a higher gas reservation than will be consumed by execution is common. On Ethereum Mainnet, the entire reservation is charged to the account before execution, and the unused portion of the reservation is credited back. However, Ethereum utilizes a memory pool ([mempool](../../support-and-community/glossary.md#mempool)) and does transaction ordering at block production time, allowing the block limit to be based only on used and not reserved gas.

To help prevent over-reservation, Hedera restricts the amount of unused gas that can be refunded to a maximum of 20% of the original gas reservation. This effectively means users will be charged for at least 80% of their initial reservation, regardless of actual usage. This rule is designed to incentivize users to make more accurate gas estimates.

For example, if you initially reserve 5 million gas units for creating a smart contract but end up using only 2 million, Hedera will refund you 1 million gas units, or 20% of your initial reservation. This setup balances the network's resource management while incentivizing users to be as accurate as possible in their gas estimations.

### Maximum Gas Per Transaction

Each transaction on Hedera is capped by a per-transaction gas limit. If a transaction’s `gasLimit` exceeds this cap, it is rejected during precheck with the `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED` error and does not proceed to consensus. This gas metering approach ensures efficient resource use, preventing excessive consumption while allowing flexibility for larger, more complex smart contracts.

Gas throttle per contract call and contract create **15 million gas per second**.

Reference: [HIP-185](https://hips.hedera.com/hip/hip-185)
