# 智能合同地址

智能合约部署到赫德拉后，它与独特的智能合约地址相关联。 智能合同可以在系统中被引用，地址有两种类型：

**:right_箭头:** [**智能合同EVM 地址**](智能合同-地址.md#evm-address)

**:right_箭头:** [**智能合同ID**](智能合同-地址.md#contract-id)

***

### EVM 地址

标准智能合同的EVM地址是与 EVM 兼容的地址。 一旦合同得到部署，EVM合同地址将由系统退回。 这是Ethereum 生态系统中通常使用的地址格式。 您可以使用智能合约EVM地址在以太坊的生态系统工具中参考智能合约，如 [Hardhat](../../supportand-community/glossary.md#hardhat) 和 [MetaMask](../../supportand-community/glossary.md#metmask)。

例如EVM 地址编码合同ID: `0x00000000000000000000000000000000000000000000002cd37f`

{% hint style="info" %}
_**注意：** 使用 `ContractCreate` Hedera API 交易部署的合同将具有这种表单(例如，在 SDK中使用 ContractCreateTransaction)。 所有其他部署案例将在标准的EVM地址中，post_ [_HIP-729_](https://hips.hedera.com/hip/hip-729)_._
{% endhint %}

EVM 合同地址：[`0x86ecca95fecdb515d068975b75eac4357contractd6e86c5`](https://hashscan.io/mainnet/contract/0.0.2958097?p=1\\&k=1685819177.474035003)

***

### 合同 ID

在Hedera Network, 智能合同也可以通过智能合同ID来确认。 智能合同 ID 是Hedera 网络本机的合同标识符。 智能合同EVM地址和智能合同ID都是通过Hedera交易在Hedera与合同互动时被接受的智能合同标识符。

Example Contract ID: `0.0.123`

在某些情况下，EVM地址是合同编号的十六进制编码格式。

智能合同 ID 是**不兼容的** 地址格式，在Ethereum 生态系统中是接受或已知的。 例如，如果您使用Metamask，您将不会通过其合同ID指定合同，而是使用其EVM地址。

当查看合同信息时，你可能会看到Hedera Network Explorers中注意到的两种地址类型，如 [HashScan](https://hashscan.io/)。

<figure><img src="../../.gitbook/assets/contract ID.png" alt=""><figcaption><p>EVM 地址 & 合同编号示例在 HashScan</p></figcaption></figure>

***

### 智能合同帐户

类似于 [Ethereum](../../support-community/glossary.md#eferum)，智能合同实体也是一种类型的帐户。 在Hedera 上部署的智能合约可以持有 [HBAR](../../supportand-community/glossary.md#hbar)、 [fungible](../../supportand-community/glossary.md#forgible-token)和[不可互换令牌](../../supportand-community/glossary.md#non-fugible-token-nft)。

<table><thead><tr><th width="289">智能合同属性</th><th>示例</th></tr></thead><tbody><tr><td><strong>智能合同 ID</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0.0.2940467</a></td></tr><tr><td><strong>Smart Contract EVM Address</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0xde2b7414e2918a393b59fc130bceb75c3ee52493</a></td></tr><tr><td><strong>智能合同十六进制编码合同 ID</strong></td><td>0x0000000000000000000000000000000000002cff73<br>*<em>只有当合同不是通过EVM 工具而是通过Hedera SDK部署时才存在。</em></td></tr><tr><td><strong>智能合同账户 ID</strong></td><td><a href="https://hashscan.io/mainnet/account/0.0.2940467?app=false&#x26;ph=1&#x26;pt=1&#x26;p2=1&#x26;p1=1">0.0.2940467</a></td></tr></tbody></table>
