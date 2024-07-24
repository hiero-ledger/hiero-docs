# 煤气和费用

## 气体：

在执行智能合同时，EVM要求以气体支付大量工作。 “工作”包括计算、状态转换和存储。 气体是用来按EVM执行的代码收取费用的测量单位。 每个代码都有一个固定的气体成本。 气体是支付用于处理交易的计算资源所需的费用。

### **Weibar**

EVM 在 Weibar 中返回气体信息(引入于 [HIP-410](https://hips.hedera.com/hip/hip-410))。 一辆面包车是 10 ^-18 HBAR，它转换成1 tinybar is 10 ^10 weibars. 如HIP-410所述，这是为了最大限度地实现与第三方工具的兼容性，第三方工具预期ether 单位会分为10^18。 也称作Wei。

### **气体Schedule和费用**

为Hedera的EVM交易支付的天然气费用可包括三种不同的天然气费用：

- 内在气体
- EVM opcode Gas
- Hedera System Contract Gas

<table><thead><tr><th width="163">煤气费类型</th><th>描述</th></tr></thead><tbody><tr><td><strong>内在气体</strong></td><td>执行交易所需气体的最低数量。 这是一种独立于交易中进行的具体操作或计算的固定气体成本。<br><br>内在气体成本：21,000 Gazes</td></tr><tr><td><strong>EVM 操作代码</strong></td><td><p>为智能合同调用执行定义的操作代码所需的气体。</p><ul><li><strong>Opcode 固定执行成本</strong>: 每个opcode 在执行时都有固定的支付成本, 以气体计量。 这种费用对所有处决都是一样的，尽管这可能会在新的硬叉中改变。</li></ul><ul><li><strong>Opcode 动态执行成本</strong>: 一些说明比其他说明做更多的工作，取决于它们的参数。 因此，除了固定费用外，有些指示还有动态成本。 这些动态成本取决于几个因素（从硬叉到硬叉不等）。 </li></ul><p>请参阅 <a href="https://www.evm.codes/">参考</a> 以了解每个opcode 和 fork 的特定成本。</p></td></tr><tr><td><strong>Hedera System Contract</strong></td><td><p>The required gas that is associated with a Hedera-defined transaction like using the Hedera Token Service system contract that allows you to burn (<code>TokenBurnTransaction</code>) or mint (<code>TokenMintTransaction</code>) a token.</p><p></p><p>如果您没有使用映射到本地Hedera服务之一的系统合同，您就不需要使用这笔费用。</p><p></p><p>Hedera交易气体计算法： 美元/美元/美元+20%的交易成本</p><p>示例系统合同：</p><ul><li>Hedera 令牌服务</li><li>伪装随机号码生成器 (PRNG)</li><li>汇率</li></ul></td></tr></tbody></table>

### 煤气限制

气体限制是您愿意为操作支付的最大气体量。

当前的代码气费反映了[0.22Hedera Service release](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22)。

| 操作                                                                      | 取消费用 (Gas)                                                                                  | 当前赫德拉(Gas)                                                                                  |
| ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| 代码存款                                                                    | 200 \* bytes                                                                                                   | 200 \* bytes                                                                                                   |
| <p><code>BALANCE</code><br>(冷帐户)</p>                                    | 2600                                                                                                           | 2600                                                                                                           |
| <p><code>BALANCE</code><br>(温暖帐户)</p>                                   | 100                                                                                                            | 100                                                                                                            |
| `EXP`                                                                   | 10 + 50/字节                                                                                                     | 10 + 50/字节                                                                                                     |
| <p><code>EXTCODECOPY</code><br>(cold account)</p>                       | 2600 + Mem                                                                                                     | 2600 + Mem                                                                                                     |
| <p><code>EXTCODECOPY</code><br>(warm account)</p>                       | 100 + Mem                                                                                                      | 100 + Mem                                                                                                      |
| <p><code>EXTCODEHASH</code><br>(cold account)</p>                       | 2600                                                                                                           | 2600                                                                                                           |
| <p><code>EXTCODEHASH</code><br>(warm account)</p>                       | 100                                                                                                            | 100                                                                                                            |
| <p><code>EXTCODESIZE</code><br>(冷帐户)</p>                                | 2600                                                                                                           | 2600                                                                                                           |
| <p><code>EXTCODESIZE</code><br>(warm account)</p>                       | 100                                                                                                            | 100                                                                                                            |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>        | <p>375 + 375*主题<br>+ 数据 Mem</p>                                                                                | <p>375 + 375*主题<br>+ 数据 Mem</p>                                                                                |
| <p><code>SLOAD</code><br>(Cold slot)</p>                                | 2100                                                                                                           | 2100                                                                                                           |
| <p><code>减速</code><br>(温暖位置)</p>                                        | 100                                                                                                            | 100                                                                                                            |
| <p><code>SSTORE</code><br>(新位置)</p>                                     | 22,100                                                                                                         | 22,100                                                                                                         |
| <p><code>SSTORE</code><br>(现有位置，<br>冷却时间)</p>                           | 2,900                                                                                                          | 2,900                                                                                                          |
| <p><code>SSTORE</code><br>(现有的槽位，<br>温暖访问)</p>                          | 100                                                                                                            | 100                                                                                                            |
| <p><code>SSTORE</code><br>refund</p>                                    | EVM 指定的                                                                                                        | EVM 指定的                                                                                                        |
| <p><code>CallL</code> <em>et al</em>。<br>(冷收件人)</p>                     | 2,600                                                                                                          | 2,600                                                                                                          |
| <p><code>CallL</code> <em>et al</em>。<br>(温暖收件人)</p>                    | 100                                                                                                            | 100                                                                                                            |
| <p><code>CALL</code> <em>et al</em>。<br>Hbar/Eth Transfer Surcharge</p> | 9,000                                                                                                          | 9,000                                                                                                          |
| <p><code>SELFDESTRUCT</code><br>(cold beneficiary)</p>                  | 2600                                                                                                           | 2600                                                                                                           |
| <p><code>SELFDESTRUCT</code><br>(warm beneficiary)</p>                  | 0                                                                                                              | 0                                                                                                              |
| `TSTORE`                                                                | 100                                                                                                            | 100                                                                                                            |
| `TLOAD`                                                                 | 100                                                                                                            | 100                                                                                                            |
| `MCOPY`                                                                 | 3 + 3\*words\_copied + memory\_exten\_costal | 3 + 3\*words\_copied + memory\_exten\_costal |

上表中的“温暖”和“寒冷”两个术语与账户或储存栏位是否已经读取或写入当前智能合同交易中的内容相符。 即使在一个儿童呼叫框架内。

`CALL` _et al._' 包含有限制：`CALL`、`CALLCODE`、`DELEGATECALL`和`STATICCALL`

Reference: [HIP-206](https://hips.hedera.com/hip/hip-206), [HIP-865](https://hips.hedera.com/hip/hip-865)

### 每次排气量

大多数EVM兼容网络每个区块设有一个气体限值来管理资源分配。 这样做是为了限制区块验证花费的时间，以便矿工节点能够快速产生新的节点。 虽然Hedera 没有方块或矿工，但是在[Nakamoto共识](https://golden)的背景下。 om/wiki/Nakamoto\_consensus-AMB5VWM系统将使用它，我们受到时间物理学的限制，即我们可以处理多少块。

就智能合同交易而言，天然气比计算所有交易都更能衡量EVM交易的复杂性。 用这种方法来衡量天然气的限制可以更合理地限制资源的消耗。

允许我们接受的交易具有更大的灵活性，并反映Ethereum Mainnet 的行为， 交易限额将按每种气体计算智能合同调用(“ContractCreate`, `ContractCall`, `ContractCallLocalQuery\`”)。 这种双管齐下的办法可以更好地管理资源，为管理智能合同的执行提供细微细致的方法。&#x20

Hedera 网络已经在Hedera Service release [0.22](../../../networks/release-notes/services.md#v0.22)中安装了一个系统气管。&#x20

### 气体预订和未使用的气体退款

Hedera Throttle transactions before consultation and nodes limit the number of transactions can be submitted to the network. 然后，在协商一致的时候，如果超过交易的最大数目，则超出交易的超额交易不会被评估，并且在繁忙的状态下被取消。 按可变气体量进行排减量对该系统提出了一些挑战，因为该节点只提交其交易限额的一部分。

为了解决这个问题，将以两级气体测量系统为基础：事先达成共识和达成共识。 事先协商一致的节点将使用交易中指定的`gasLimit`字段。 协商一致后将使用交易所消耗的气体的实际估值量，以便对系统进行动态调整。 由于网络状态能够直接影响交易流量，因此无法知道已评估的气体预先协商一致意见。 这就是为什么协商一致之前使用了 `gasLimit` 字段，并将被称为**Gass reservation**。

合同查询请求是独特的，完全绕过了协商一致阶段。 这些请求只是在接收它们的本地节点上执行，并且只影响到指定节点的预选节点。 另一方面，标准合同交易既经过预选阶段又经过协商一致阶段，同时也受到两套管线限制。 预先检查和协商一致意见的温度限制可以设置为不同的数值。

为了确保交易能够正常执行，通常需要设置比执行时消耗更高的气体预订。 在Ethereum Mainnet上，整个预订将在执行前记入帐户，预订的未动用部分将退还。 然而，太太太空使用了内存池([mempool](../../../support-community/glosses. d#mempoor)并在区块生产时间订购交易，允许区块限制仅基于使用的天然气。

为了防止过度预留，Hedera将未使用的气体的数量限制在原先的气体预留量的20%。 这实际上意味着用户不论实际使用情况如何，至少将被收取80%的初始预订费用。 这一规则旨在鼓励用户作出更准确的气体估计。

例如，如果你最初为创建智能合同保留500万个天然气单位，但最终只能使用200万个天然气单位。 Hedera将退还您100万个天然气单位。 ，您最初预订的20%。 这一设置旨在平衡网络的资源管理，并鼓励用户在其气体估计中尽可能做到准确。

### 每笔交易最大煤气量

因为协商一致的执行时间现在受到实际使用的气体的限制，而不是基于交易计数， 提高每笔交易的气体限额是安全的。 在以气体为基础的计量之前，每笔交易都有可能消耗每笔交易的最大气体，而不考虑其他交易。 这种限制是基于这种最坏的情况。 既然所使用的是气体合计，我们就可以允许每笔交易消费大量的气体，而不必担心这种剧增。

当交易被提交到一个具有大于每笔交易的气体限额的`gasLimit`的节点时， 交易必须在预检查时被拒绝，响应代码为 `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`。 决不能以协商一致方式进行交易。

每次合同通话和合同产生了**每秒1 500万燃气**。

Reference: [HIP-185](https://hips.hedera.com/hip/hip-185)
