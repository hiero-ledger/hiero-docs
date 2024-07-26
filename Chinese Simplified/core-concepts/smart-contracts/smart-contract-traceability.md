# 智能合同可追踪性

在合同部署完毕后，你可能想要进一步调查智能合同函数调用的执行情况。 跟踪可以全面了解操作的顺序及其效果，从而可以对智能合同行为进行分析、调试和审计。 这两种有用的痕迹：

**:right_箭头:** [**通话跟踪**](智能合同-可追踪性.md#call-trace)

**:right_箭头:** [**State Trace**](智能合同-可追踪性.md#state-trace)

***

## 通话跟踪

合同**调用跟踪**信息捕获了在交易中执行的所有嵌套智能合同功能的输入、输出和气体细节。 在以太， 它们偶尔被称为内部交易，但它们只是在处理所有相关函数的每个深度的智能合同执行时捕获电文框架的快照。

<table data-header-hidden><thead><tr><th width="173"></th><th></th></tr></thead><tbody><tr><td><strong>Input Data</strong></td><td>它记录在智能合约中调用某个函数时提供的输入数据或参数。 此输入数据基本上是函数签名及其参数的编码形式。</td></tr><tr><td><strong>输出数据</strong></td><td>在执行函数后，追踪信息包括该函数返回的输出数据。 这可能是函数计算的结果，也可能是函数生成的任何数据的结果。</td></tr><tr><td><strong>气体详细信息</strong></td><td>记录每个函数调用所消耗的气体的信息。 一个功能内的每个操作都会消耗一定数量的气体，这一信息被跟踪以计算交易总成本。</td></tr></tbody></table>

此信息可以使用交易 ID 或 Etherum 交易哈希查询。

i 通话跟踪的详细信息可以在 Hedera [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_action.proto) 中查找，包括：

<table><thead><tr><th width="178">通话跟踪数据</th><th>描述</th></tr></thead><tbody><tr><td><strong>调用操作类型</strong></td><td><p>在执行智能合同或EVM交易期间进行的特定类型的操作。 示例：“CALL”是指当交易援引智能合同中的函数时使用的操作类型。 它执行该功能，可以修改合同状态。</p><pre><code>OP_UNKNOWN = 0;
OP_CALL = 1;
OP_CALLCODE = 2;
OP_DELEGATECALL = 3;
OP_STATICCALL = 4;
OP_CREATE = 5;
OP_CREATE2 = 6;
</code></pre></td></tr><tr><td><strong>结果数据</strong></td><td>结果数据是通过执行智能合同函数或操作生成的输出或返回值。 When a function call is executed, it may produce data as a result, such as computed values, status indicators, contract revert reason if any and the error if the transaction itself failed without an explicit <code>REVERT</code></td></tr><tr><td><strong>结果数据类型</strong></td><td>“结果数据类型”是指函数或方法返回值的数据类型。 例如，如果你有一个函数 <code>添加(a, b)</code> 增加两个数字并返回结果。 结果数据类型可能是一个整数，如果返回数字的总和。</td></tr><tr><td><strong>通话深度</strong></td><td>调用堆栈中当前函数调用的级别或深度。 它提供了关于函数调用的嵌套性质的信息，并有助于跟踪在执行智能合约期间调用函数的顺序和等级。 <br><br>调用深度表示在调用堆栈中当前函数之前调用了多少函数。 对于初始函数调用和随后每次函数调用，它将从0开始递增。 <br><br>例如，父交易将表示为调用深度1，第一个子交易将处于通话深度1。 儿童交易2将在通话深度1.2。 深度1.2的儿童交易有双亲。</td></tr><tr><td><strong>呼叫者</strong></td><td>打电话者可以是调用合同的帐户ID或调用合同的另一个智能合同的ID。 <br><br>树中的第一个动作只能来自一个帐户。 呼叫树中的其余行动来自合同。 <br><br>当智能合同函数被外部帐户或另一个合同调用时。 调用地址记录在跟踪中，以识别函数调用的来源。 调用地址可以有助于理解执行的背景和确定触发函数调用的交易或消息的来源。</td></tr><tr><td><strong>收件人</strong></td><td>智能合同或接收特定通话或交易的账户地址。 它代表了EVM内部互动的目的地或目标。 合同操作可以指向以下之一： <br><br>• 账户：如果收件人是一个账户，收件人的账户ID。 只有HBAR将被转移。 <br>• 合同：如果收件人是智能合同 <br>• EVM 地址：如果合同操作被导向到无效的固态地址， 该地址是</td></tr><tr><td><strong>来自</strong></td><td>Hedera合同称下一份合同。</td></tr><tr><td><strong>到</strong></td><td>接收电话或正在创建的合同。</td></tr><tr><td><strong>Value/Amount</strong></td><td>此通话中传输的 hbars 数量。</td></tr><tr><td><strong>气体限制</strong></td><td>该气体被定义为合同调用的上限气体。</td></tr><tr><td><strong>Gas Used</strong></td><td>用于合同电话的气体数量。</td></tr><tr><td><strong>Input</strong></td><td>作为输入数据传递到此合同调用中</td></tr></tbody></table>

**示例**:

<figure><img src="../../.gitbook/assets/smart-contracts-core-concepts-call-trace.png" alt=""><figcaption><p>在HashScan</p></figcaption> 上调用轨迹示例</figure>

***

## 状态跟踪

当智能合同交易改变合同状态时，现在将对智能合同状态的变化进行跟踪。 这将使开发人员能够从合同创建之时起对合同的状态变化有一个纸质痕迹。 将跟踪的状态变化包括每次读取或写入智能合约时的值。 存储槽代表智能合同状态读取或写入的顺序。

所读价值反映了执行智能合同交易之前的储存价值。 所写的价值，如果存在，代表智能合同通话完成后存储槽的最后更新值。 从合同开始到结束之间的过渡状态不存储在交易记录中。

i 关于状态跟踪的详细信息可以在 [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_state\_change.proto)中找到，其中包括：&#x20

<table><thead><tr><th width="205">状态跟踪数据</th><th>描述</th></tr></thead><tbody><tr><td>地址</td><td><p>智能合同 EVM 地址。</p><p>Ex: <code>0000000000000000000000000000000000001f41</code></p></td></tr><tr><td>合同 ID</td><td><p>智能合同 ID。</p><p>Ex: <code>0.0.1234</code></p></td></tr><tr><td>栏位</td><td>指在合同状态内存储数据的存储地点。 它也可被视为具有特定价值的变量或储存单位。</td></tr><tr><td>值已读</td><td>进行修改之前的变量或数据结构的当前值。 这些值可以用于验证条件，进行计算或触发合同代码中的特定动作。</td></tr><tr><td>值已写入</td><td>修改后的书面或更改变量或数据结构。</td></tr></tbody></table>

### 共识节点

协商一致的节点储存名为`ContractStateChanges`的边防记录。 每次智能合同状态更改时，都会产生新的记录，纪念合同发生的状态变化。

### 镜像节点

Hedera [镜像节点](../../support-community/glossary.md#miror-nodes) 支持两个剩余的 API，返回有关智能合同状态变化的信息。 其中包括：

- `/api/v1/contracts/{id}/results/{timestamp}`
- `/api/v1/contracts/results/{transactionIdOrHash}`

**示例：**

```
    "state_changes": [
      {
        "address": "0000000000000000000000000000000000001f41",
        "contract_id": "0.1.2",
        "slot": "0x00000000000000000000000000000000000000000000000000000000000000fa",
        "value_read": "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
        "value_written": "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
      }
    ]
```

### Hedera 镜像节点浏览器

可以在支持的 Hedera 网络探索器上查看国家轨迹。

<figure><img src="../../.gitbook/assets/smart-contracts-network-explorer-example.png" alt=""><figcaption><p>State trace example on HashScan</p></figcaption></figure>
