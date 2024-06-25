# Gas and Fees

## Gas

When executing smart contracts, the EVM requires the amount of work to be paid in gas. The “work” includes computation, state transitions, and storage. Gas is the unit of measurement used to charge a fee per opcode that is executed by the EVM. Each opcode code has a defined gas cost. Gas reflects the cost necessary to pay for the computational resources used to process transactions.

### **Weibar**

The EVM returns gas information in Weibar (introduced in [HIP-410](https://hips.hedera.com/hip/hip-410)). One weibar is 10^-18th HBAR, which translates to 1 tinybar is 10^10 weibars. As noted in HIP-410, this is to maximize compatibility with third-party tools that expect ether units to be operated on in fractions of 10^18, also known as a Wei.

### **Gas Schedule and Fees**

Gas fees paid for EVM transactions on Hedera can be composed of three different kinds of gas costs:

- Intrinsic Gas
- EVM opcode Gas
- Hedera System Contract Gas

<table><thead><tr><th width="163">Gas Fee Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>Intrinsic Gas</strong></td><td>The minimum amount of gas required to execute a transaction. It is a fixed gas cost that is independent of the specific operations or computations performed within the transaction.<br><br>Intrinsic gas cost: 21,000 gas</td></tr><tr><td><strong>EVM Operation Code</strong></td><td><p>The gas required to execute the defined operation code(s) for the smart contract call.</p><ul><li><strong>Opcode Fixed Execution Cost</strong>: Each opcode has a fixed cost to be paid upon execution, measured in gas. This cost is the same for all executions, though this is subject to change in new hard forks.</li></ul><ul><li><strong>Opcode Dynamic Execution Cost</strong>: Some instructions conduct more work than others, depending on their parameters. Because of this, on top of fixed costs, some instructions have dynamic costs. These dynamic costs are dependent on several factors (which vary from hard fork to hard fork).</li></ul><p>See the <a href="https://www.evm.codes/">reference</a> to learn about the specific costs per opcode and fork.</p></td></tr><tr><td><strong>Hedera System Contract Transaction</strong></td><td><p>The required gas that is associated with a Hedera-defined transaction like using the Hedera Token Service system contract that allows you to burn (<code>TokenBurnTransaction</code>) or mint (<code>TokenMintTransaction</code>) a token.</p><p>If you are not using a system contract that maps to one of the native Hedera services, you do not need to apply this fee.</p><p>The Hedera transaction gas calculation is: Cost of the transaction in USD x Gas Conversion gas/USD + 20%</p><p>Example System Contracts:</p><ul><li>Hedera Token Service</li><li>Pseudo Random Number Generator (PRNG)</li><li>Exchange Rate</li></ul></td></tr></tbody></table>

### Gas Limit

The gas limit is the maximum amount of gas you are willing to pay for an operation.

The current opcode gas fees are reflective of the [0.22 Hedera Service release](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22).

| Operation                                                               | Cancun Cost (Gas)                                                                             | Current Hedera (Gas)                                                                          |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Code deposit                                                            | 200 \* bytes                                                                                                     | 200 \* bytes                                                                                                     |
| <p><code>BALANCE</code><br>(cold account)</p>                           | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>BALANCE</code><br>(warm account)</p>                           | 100                                                                                                              | 100                                                                                                              |
| `EXP`                                                                   | 10 + 50/byte                                                                                                     | 10 + 50/byte                                                                                                     |
| <p><code>EXTCODECOPY</code><br>(cold account)</p>                       | 2600 + Mem                                                                                                       | 2600 + Mem                                                                                                       |
| <p><code>EXTCODECOPY</code><br>(warm account)</p>                       | 100 + Mem                                                                                                        | 100 + Mem                                                                                                        |
| <p><code>EXTCODEHASH</code><br>(cold account)</p>                       | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>EXTCODEHASH</code><br>(warm account)</p>                       | 100                                                                                                              | 100                                                                                                              |
| <p><code>EXTCODESIZE</code><br>(cold account)</p>                       | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>EXTCODESIZE</code><br>(warm account)</p>                       | 100                                                                                                              | 100                                                                                                              |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>        | <p>375 + 375*topics<br>+ data Mem</p>                                                                            | <p>375 + 375*topics<br>+ data Mem</p>                                                                            |
| <p><code>SLOAD</code><br>(cold slot)</p>                                | 2100                                                                                                             | 2100                                                                                                             |
| <p><code>SLOAD</code><br>(warm slot)</p>                                | 100                                                                                                              | 100                                                                                                              |
| <p><code>SSTORE</code><br>(new slot)</p>                                | 22,100                                                                                                           | 22,100                                                                                                           |
| <p><code>SSTORE</code><br>(existing slot,<br>cold access)</p>           | 2,900                                                                                                            | 2,900                                                                                                            |
| <p><code>SSTORE</code><br>(existing slot,<br>warm access)</p>           | 100                                                                                                              | 100                                                                                                              |
| <p><code>SSTORE</code><br>refund</p>                                    | As specified by the EVM                                                                                          | As specified by the EVM                                                                                          |
| <p><code>CALL</code> <em>et al</em>.<br>(cold recipient)</p>            | 2,600                                                                                                            | 2,600                                                                                                            |
| <p><code>CALL</code> <em>et al</em>.<br>(warm recipient)</p>            | 100                                                                                                              | 100                                                                                                              |
| <p><code>CALL</code> <em>et al</em>.<br>Hbar/Eth Transfer Surcharge</p> | 9,000                                                                                                            | 9,000                                                                                                            |
| <p><code>SELFDESTRUCT</code><br>(cold beneficiary)</p>                  | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>SELFDESTRUCT</code><br>(warm beneficiary)</p>                  | 0                                                                                                                | 0                                                                                                                |
| `TSTORE`                                                                | 100                                                                                                              | 100                                                                                                              |
| `TLOAD`                                                                 | 100                                                                                                              | 100                                                                                                              |
| `MCOPY`                                                                 | 3 + 3\*words\_copied + memory\_expansion\_cost | 3 + 3\*words\_copied + memory\_expansion\_cost |

The terms 'warm' and 'cold' in the above table correspond with whether the account or storage slot has been read or written to within the current smart contract transaction, even if within a child call frame.

'`CALL` _et al._' includes with limitation: `CALL`, `CALLCODE`, `DELEGATECALL`, and `STATICCALL`

Reference: [HIP-206](https://hips.hedera.com/hip/hip-206), [HIP-865](https://hips.hedera.com/hip/hip-865)

### Gas Per Second Throttling

Most EVM-compatible networks place a gas limit per block to manage resource allocation. This is done to place a limit on the amount of time spent in block validation so that the miner nodes can produce new nodes quickly. While Hedera does not have blocks or miners, in the context of how a [Nakamoto consensus](https://golden.com/wiki/Nakamoto\_consensus-AMB5VWM) system would use it, we are constrained by the physics of time as to how many blocks we can process.

For smart contract transactions, gas is a better measure of the complexity of the EVM transaction than counting all transactions the same, so metering the limits on gas provides a more reasonable limit on resource consumption.

To allow for more flexibility in what transactions we accept and to mirror Ethereum Mainnet behavior, the transaction limits will be calculated on a per-gas basis for smart contract calls (`ContractCreate`, `ContractCall`, `ContractCallLocalQuery`) in addition to a per-transaction limit. This dual approach allows for better resource management, providing a nuanced way to regulate smart contract executions.

The Hedera network has implemented a system gas throttle of **15 million gas per second** in the Hedera Service release [0.22](../../../networks/release-notes/services.md#v0.22).

### Gas Reservation and Unused Gas Refund

Hedera throttles transactions prior to consensus, and nodes limit the number of transactions they can submit to the network. Then, at consensus time, if the maximum number of transactions is exceeded, the excess transactions are not evaluated and are canceled with a busy state. Throttling by variable gas amounts provides some challenges to this system, where the nodes only submit a share of their transaction limit.

To address this, throttling will be based on a two-tiered gas measuring system: pre-consensus and post-consensus. Pre-consensus throttling will use the `gasLimit` field specified in the transaction. Post-consensus will use the actual evaluated amount of gas consumed by the transaction, allowing for dynamic adjustments in the system. It is impossible to know the _actual_ evaluated gas pre-consensus because the network state can directly impact the flow of the transaction, which is why pre-consensus uses the `gasLimit` field and will be referred to as the **gas reservation**.

Contract query requests are unique and bypass the consensus stage altogether. These requests are executed solely on the local node that receives them and only influences that specific node's precheck throttle. On the other hand, standard contract transactions go through both the precheck and consensus stages and are subject to both sets of throttle limits. The throttle limits for precheck and consensus may be set to different values.

In order to ensure that the transactions can execute properly, setting a higher gas reservation than will be consumed by execution is common. On Ethereum Mainnet, the entire reservation is charged to the account prior to execution, and the unused portion of the reservation is credited back. However, Ethereum utilizes a memory pool ([mempool](../../../support-and-community/glossary.md#mempool)) and does transaction ordering at block production time, allowing the block limit to be based only on used and not reserved gas.

To help prevent over-reservation, Hedera restricts the amount of unused gas that can be refunded to a maximum of 20% of the original gas reservation. This effectively means that users will be charged for at least 80% of their initial reservation, regardless of actual usage. This rule is designed to incentivize users to make more accurate gas estimates.

For example, if you initially reserve 5 million gas units for creating a smart contract but end up using only 2 million, Hedera will refund you 1 million gas units, i.e., 20% of your initial reservation. This setup aims to balance the network's resource management while incentivizing users to be as accurate as possible in their gas estimations.

### Maximum Gas Per Transaction

Because consensus time execution is now limited by actual gas used and not based on a transaction count, raising the gas limit available for each transaction is safe. Prior to gas-based metering, it would be possible for each transaction to consume the maximum gas per transaction without regard to the other transactions, so limits were based on this worst-case scenario. Now that throttling is the aggregate gas used, we can allow each transaction to consume large amounts of gas without concern for an extreme surge.

When a transaction is submitted to a node with a `gasLimit` that is greater than the per-transaction gas limit, the transaction must be rejected during precheck with a response code of `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`. The transaction must not be submitted to consensus.

Gas throttle per contract call and contract create **15 million gas per second**.

Reference: [HIP-185](https://hips.hedera.com/hip/hip-185)
