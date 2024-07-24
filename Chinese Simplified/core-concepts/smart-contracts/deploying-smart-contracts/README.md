# 部署智能合同

编译您的智能合约后，您可以将它部署到Hedera网络。 构造商的"_init code_" 包括合同的整个字节代码。 部署时，EVM 将同时获得智能合同 [bytecode](../../…)。 支持和社区/glossary.md#bytecode)和执行和部署合同所需的气体。 部署后，构造函数被移除，只有`runtime_bytecode`将来的合同交互。

**:right_箭头:** [**超时账号别斯u EVM**](./#超时账号-besu-evm-on-hedera)

**:right_箭头:** [**Cancun Hard Fork**](./#cancun-hardfork)

**:right_箭头:** [**Solidity Variables and Opcodes**](./#solidity-variables-andopcodes)

***

## 以太机虚拟机 (EVM)

[太空虚拟机(EVM)](../../../support-community/glossary.md#eyasum-virtal-modal-evm)是一个运行环境，用于执行用EVM本地语言编写的智能合同，例如Solid。 源代码必须编译为 EVM 的字节代码才能执行给定的智能合同。

在喜代拉，用户可以几种方式与EVM兼容的环境进行互动。 他们可以直接提交`ContractCreate`, `EtherumTransaction`, 或使用 `eth_sendRawTransaction` RPC 调用合同字节代码。 这些不同的路径使开发人员能够有效地部署和管理智能合同。

当EVM 收到字节代码时，它将被进一步细分为操作代码([opcodes](../../../supportand-community/glossary.md#opcodes))。 EVM opcode代表了它可以执行的具体指令。 每个opcode 是一个字节，有自己的气体成本。 Cancerum Cancun硬叉的每个opcode 的成本可找到 [here](https://www.evm.codes/?fork=cancun)。

#### 智能合同Opcode 示例

```solidity
PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x40 MLOAD PUSH2 0x558 CODESIZE SUB DUP1 PUSH2 0x558 DUP4 CODECOPY DUP2 DUP2 ADD PUSH1 0x40 MSTORE PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x33 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 MLOAD PUSH1 0x40 MLOAD SWAP4 SWAP3 SWAP2 SWAP1 DUP5 PUSH5 0x100000000 DUP3 GT ISZERO PUSH2 0x53 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP4 DUP3 ADD SWAP2 POP PUSH1 0x20 DUP3 ADD DUP6 DUP2 GT ISZERO PUSH2 0x69 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 MLOAD DUP7 PUSH1 0x1 DUP3 MUL DUP4 ADD GT PUSH5 0x100000000 DUP3 GT OR ISZERO PUSH2 0x86 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 DUP4 MSTORE PUSH1 0x20 DUP4 ADD SWAP3 POP POP POP SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0xBA JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x9F JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xE7 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP PUSH1 0x40 MSTORE POP POP POP CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x144 SWAP3 SWAP2 SWAP1 PUSH2 0x14B JUMP JUMPDEST POP POP PUSH2 0x1E8 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x18C JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x1BA JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x1BA JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x1B9 JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x19E JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x1C7 SWAP2 SWAP1 PUSH2 0x1CB JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x1E4 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x1CC JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST PUSH2 0x361 DUP1 PUSH2 0x1F7 PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER
```

参考：[https://ethervm.io/](https://ethervm.io/)

***

## Hedera 上的超音速贝苏EVM

Hedera 网络节点使用 [HyperLedger Besu EVM ](../../../supportand-community/glossary.md#hyhledger-besu-evm)客户端作为Etherum类型交易的执行层。 代码库是当前Ethereum Mainnet 硬叉的最新版本。 Besu EVM客户端图书馆的使用没有钩子，用于Etherum的共识、网络和存储功能。 相反，Hedera 钩子进入了自己的哈希图共识，Gossip 通信和[Virtual Merkle Trees](../../../supportand-community/glossary.md#virtual al-merkle-tree)组件以获得更大的故障容忍、终结性和可扩展性。&#x20

Hedera Mainnet 版本[`0.50.0`](../../../networks/release-notes/services.md#v0)。 Besu EVM 客户端被配置为支持Ethereum Mainnet的Cancun硬叉，但有一些修改。

### **Cancun 硬叉**

智能合同平台已升级，以支持 [Cancun](https://github.com/etherum/exection-spections/blob/master/net-updes/mainnet-updes/cancun.md) 硬叉中可见的 EVM 更改。 This includes adding new opcodes for transient storage and memory copy, semantic updates for opcodes introduced certain operations introduced in the [Shanghai](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/shanghai.md), [London](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md), [Istanbul](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md), and [Berlin](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md) hard forks, except those with changes in block production, data serialization, and the double fee market.&#x20

截至Hedera Services [0.22](../../../networks/release-notes/services.md#v0.22)的版本，气体和输入数据成本都被收费。 固有气体的消耗量是在执行任何代码之前发生的一种固定费用。 天然气成本为21,000加元。 输入数据的相关成本为：非零数据每个字节16种气体；零数据每个字节4种气体。 所消耗的固有气体数量与合同调用外部合同功能参数时提供的数据有关。 气体表和收费表可在本文件的气体部分找到。

<figure><img src="../../../.gitbook/assets/cancun-blob-graphic-onchaintimes.jpeg" alt=""><figcaption><p><strong>EIP-4844 Unveiled: Paving the Way for Proto-Danksharding in Ethereum</strong></p></figcaption></figure>

#### Proto-Danksharding

作为在坎昆硬叉中提出的全面隔离的临时解决办法， 原始数据库提供了一些好处，可以通过降低复杂性和改变基础设施来避开，这些都是令人畏惧的实施工作的一部分。 这反过来又打开了增加数据“漏洞”的门，将其附在方块上，以进一步增加数据可用性，并提高处理效率。

Blobs 是块中的大数据对象。 这些可以用来存储滚动(第二层解决方案)和需要高效率存储大型数据对象的不同类型的应用。 这是验证器的离链数据，需要最小化的处理。 它减少了网络的计算负荷，从而降低了交易气费。

#### ❌ Hedera上支持Blobs？

Hedera 不提供 [EIP-4844]下的蓝图(https://eips.efum.org/EIPS/eip-4844)。 [HIP-866](https://hips.hedera.com/hip/hip-866) 定义了Hedera 如何在没有博客支持的情况下行事。 为了保持兼容性和未来的设计空间，Hedera将发挥类似于不添加蓝色的作用。 这将允许依赖于博客行为的现有合同在没有博客的情况下运行。 禁止“3类型”交易，从而阻止Blobs进入系统。 这将使人们摆脱EVM的关切，而不会影响到关于Hedera的其他可取的互动。

### 团结变量和Opcode：

下表确定了团结变量和操作代码到赫德拉的映射。 Cancun硬叉所支持的 Opcode 完整列表可以找到 [here](https://www.evm.codes/).&#x20

<table><thead><tr><th width="245" align="center">Solidity</th><th width="170" align="center">Opcode</th><th>Hedera</th></tr></thead><tbody><tr><td align="center"><code>地址</code></td><td align="center"></td><td>地址是 shard.realm.number (0.0.10) 映射到一个20字节团结地址。 地址可以是 Hedera 帐户 ID 或合约ID 的团结格式。</td></tr><tr><td align="center"><code>block.fee</code></td><td align="center"><code>BASEFEE</code></td><td><code>BASEFEE</code> opcode 将返回零。 Hedera 并不使用设计来支持的Fee Market机制。</td></tr><tr><td align="center"><code>block.chainId</code></td><td align="center"><code>CHAINID</code></td><td><code>CHAINID</code> opcode 将为主返回295(hex <code>0x0127</code>) 296(hex <code>0x0128</code>) 用于测试网， 297(hex <code>0x0129</code>) 用于预览网，298(<code>0x12A</code>)用于发展网络。</td></tr><tr><td align="center"><code>block.coinbase</code></td><td align="center"><code>COINBASE</code></td><td><code>COINBASE</code> 操作将返回融资账户 (Hedera 交易费用收集账户 <code>0.0.98</code>)。</td></tr><tr><td align="center"><code>block.number</code></td><td align="center"></td><td>记录文件的索引(不推荐使用 <code>block.timestamp</code>)。</td></tr><tr><td align="center"><code>block.timestamp</code></td><td align="center"></td><td>交易协商一致的时间戳。</td></tr><tr><td align="center"><code>block.difficult</code></td><td align="center"></td><td>总是零。</td></tr><tr><td align="center"><code>block.gaslimit</code></td><td align="center"><code>GASLIMIT</code></td><td>The <code>GASLIMIT</code> operation will return the <code>gasLimit</code> of the transaction. The transaction <code>gasLimit</code> will be the lowest of the gas limit requested in the transaction or a global upper gas limit configured for all smart contracts.</td></tr><tr><td align="center"><code>msg.sender</code></td><td align="center"></td><td>Hedera合同ID或账户ID的地址，其格式是团结，称作本合同。 对于根层或传递到根层的委托链，它是支付交易费用的帐户 ID。</td></tr><tr><td align="center"><code>msg.value</code></td><td align="center"></td><td>交易关联到Tinybar的值。</td></tr><tr><td align="center"><code>tx.origin</code></td><td align="center"></td><td>支付交易费用的帐户ID，无论深度如何。</td></tr><tr><td align="center"><code>tx.gasprice</code></td><td align="center"></td><td>固定(因全局收费表和汇率不同)。</td></tr><tr><td align="center"><p><code>selfdestruct</code></p><p><code>(地址应付款收款人)</code></p></td><td align="center"><code>SELFDESTRUCT</code></td><td>由于Hedera账户编号政策，地址将无法被复用。 On <code>SELFDESTRUCT</code> the contracts HBAR and HTS tokens are transferred to the recipients. 如果收件人不存在或没有任何HTS 令牌的允许，此opcode 将失败。 </td></tr><tr><td align="center"><code><地址>.code</code></td><td align="center"></td><td>预编译合同地址将不报告代码，包括HTS系统合同。</td></tr><tr><td align="center"><code><地址>.codehash</code></td><td align="center"></td><td>预编译合同地址将报告空代码哈希。</td></tr><tr><td align="center"></td><td align="center"><code>PRNGSEED</code></td><td>此opcode 返回一个基于运行哈希的 n-3记录的随机数字。</td></tr><tr><td align="center"><code>delegateCall</code></td><td align="center"></td><td>合同不能再使用 <code>delegateCall()</code> 来调用系统合同。 合同应该使用 <code>call()</code> 方法。</td></tr><tr><td align="center"><code>blobVersionedHashesAtIndex</code></td><td align="center"><code>BLOBHASH</code></td><td><code>BLOBHASH</code> 操作将随时返回所有零。</td></tr><tr><td align="center"><code>blobBasefee</code></td><td align="center"><code>BLOBBASEFEE</code></td><td><p><code>BLOBBASEFEE</code> 操作将返回</p><p><code>1</code> 随时发生。</p></td></tr></tbody></table>

Reference: [HIP-866](https://hips.hedera.com/hip/hip-866), [HIP-868](https://hips.hedera.com/hip/hip-868)

***

### Limitation on `fallback()` / `receive()` Functions in Hedera Smart Contracts <a href="#limitation-on-fallback-receive-functions-in-hedera-smart-contracts" id="limitation-on-fallback-receive-functions-in-hedera-smart-contracts"></a>

在Hedera开发智能合同时， 必须明白，`fallback()` 和 `receive()` 两个函数**当合同通过加密传输接收HBAR 时**不\*\* 会被触发。

在以太，当合同收到Ether时，这些功能作为“包罗一切”的机制。 然而，在赫德拉，合同余额可能会通过本机HAPI操作而改变，而不受EVM消息调用的影响， 不可能只使用 `fallback()` 或 `receive()` 方法来保持与平衡相关的变量。

#### 影响变量

- **`msg.sender`:** 发起合同呼叫的地址。
- **`msg.value`:** 与通话一起发送的 HBAR 金额。

#### 关键点

- 开发者应该实现处理HBAR 传输的明确功能。
- 要完全禁用本地操作，请考虑提交一份[Hedera Improvement Propos(HIP)](https://hips.hedera.com/)。

了解这些差异对于在赫代拉开发聪明合约的任何人，特别是熟悉埃斯特雷斯的人，都是至关重要的。

***

## 部署您的智能合同

**SDK**

你可以使用 [Hedera SDK](../../../sdks-and/sdks/) 将你的智能合约字节代码部署到网络。 这种方法不需要使用任何EVM工具，例如Hardhat或Hedera JSON-RPC 继电器的实例。

{% content-ref url="../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md" %}
[deploy-your-first-smart-contract.md](../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md)
{% endcontent-ref %}

**Hardhat**

通过指向社区托管的[JSON-RPC Relay](json-rpc-reliy.md]，硬帽可用来部署您的智能合同。 然而，EVM 工具不支持Hedera 智能合同的原生功能，如：

- 管理员密钥
- 合同备忘录
- 自动令牌关联
- 自动更新帐户 ID
- 预订节点ID或帐户 ID
- 拒绝预订奖励

如果您需要为您的合同设置任何上述属性， 您必须使用 [Hedera SDK 之一来调用 `ContractCreateTransaction` API。 (../../../sdks-andapis/sdks/)

{% content-ref url="../../../tutorials/smart-contracts/damy-a-smart-contract-using-hardhat-hedera-json-rpc-rey.md" %}
[deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md](../../../tutorials/smart-contracts/damy-a-smart-contract-using-hardhardhat-hedera-json-rpc-rey.md)
{% endcontent-ref %}

***

## 常见问题

<details>

<summary><strong>我可以直接使用Hedera EVM的团结功能吗？</strong></summary>

是的，您可以直接使用 Hedera EVM 使用团结功能。 然而，请参阅[Solidity Variables and Opcodes](./#solidity-variables-andopcodes)表格，以了解对opcode 描述的任何修改，这些修改能够更好地反映它们在Hedera网络上的行为。

</details>

<details>

<summary><strong>如果我的合同依赖于与蓝色相关的操作码，我应该做些什么？</strong></summary>

如果你的合同依赖于在坎昆硬叉中引入的蓝色代码，你仍然可以在黑德拉部署它。 与蓝色相关的opcode**将**不\*\*失败。 他们会返回[EVM指定的]默认值(https://www.evm.codes/?fork=cancun).&#x20

</details>

<details>

<summary><strong>在Hedera上使用更新的 EVM 代码是否有任何特殊考虑？</strong></summary>

是，Hedera EVM支持从坎昆硬叉中更新的代码。 您应该知道特有的天然气成本和输入数据电荷（Hedera）。 欲了解更多信息，请参阅[气体表和费用]（gas and-fees.md）。

</details>
