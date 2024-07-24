# 为EVM开发者了解Hedera

## Hedera vs Ethereum

Hedera 努力实现EVM 等效，但必须认识到其网络结构和运作中的某些独特方面和基本差异。 例如，处理国家数据结构、散列算法以及管理账户和交易。 网络行为中的这些区别是为了符合EVM标准而作出的有意的设计选择，从而实现EVM兼容性。 这种办法确保赫德拉与埃瑟姆密切结合，同时也保持其独特的特点和优化。

### 网络和安全差异

<table><thead><tr><th width="211">函数</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>网络状态数据结构</strong></td><td>虚拟默克尔树</td><td>Merkle Patrie</td></tr><tr><td><strong>散列算法</strong></td><td>SHA-384</td><td>Keccak-256</td></tr><tr><td><strong>安全</strong></td><td>使用 aBFT 高度安全</td><td>使用分散的 PoS 网络安全</td></tr></tbody></table>

### 帐户和授权差异

<table><thead><tr><th width="202.33333333333331">函数</th><th width="296">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>授权签名</strong></td><td>用于智能合同之外的交易授权</td><td>智能合约中通常使用</td></tr><tr><td><strong>特别系统帐户</strong></td><td>可用唯一属性</td><td>不可用</td></tr><tr><td><strong>Non-ECDSA Accounts</strong></td><td><p>非ECDSA 帐户(例如ED或多键) 由 Hedera 和</p><p>ECDSA 帐户完全兼容</p></td><td>ECDSA账户由 Ethereum 支持，非ECDSA 账户不支持/兼容</td></tr><tr><td><strong>帐户删除</strong></td><td>可能的</td><td>不可用</td></tr></tbody></table>

### 合同和天然气差异

<table><thead><tr><th width="210.33333333333331">函数</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Data Return on Static Calls</strong></td><td>数据检索必须通过继电器完成</td><td>直接返回数据</td></tr><tr><td><strong>煤气费</strong></td><td>不论交易结果如何，收取至少80%的煤气费</td><td>天然气费用取决于交易结果，但通常要收取100%的天然气费用，未使用的部分应计还贷。</td></tr><tr><td><strong>合同寿命</strong></td><td>合同实体可能过期，租金可能适用</td><td>无到期费或租费</td></tr></tbody></table>

### 交易和查询差异

<table><thead><tr><th width="212.33333333333331">函数</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>交易大小限制</strong></td><td>6kb</td><td>无限制</td></tr><tr><td><strong>Transaction Throttling</strong></td><td><a href="deploying-smart-contracts/#gas-limit">交易可能会被气体限制</a></td><td>待处理的交易直到将来提交</td></tr><tr><td><strong>查询成本</strong></td><td>不是免费的，可以使用镜像节点进行免费查询</td><td>免费只读电话</td></tr><tr><td><strong>Mempools</strong></td><td>No <a href="../../support-and-community/glossary.md#mempool">mempools</a></td><td>可用的备用内存</td></tr><tr><td><strong>成本</strong></td><td>B. 低和可预测的费用(1%的比率)</td><td>变量，常常是很高的煤气费</td></tr></tbody></table>

### RPC 端点和通信差异

<table><thead><tr><th width="259">函数</th><th width="244">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>RPC 块请求</strong> (例如 <code>eth_getBlockByHash*</code> & <code>eth_getBlockByNumber</code>)</td><td>Return zero 32bytes hexadecimal value for the <code>stateRoot</code></td><td>Returns the <code>stateRoot</code> hexadecimal value of the final state trie of the block</td></tr><tr><td><strong>通信</strong></td><td>需要与共识和镜像节点进行交流</td><td>与节点的直接联系</td></tr></tbody></table>

{% hint style="info" %}
**注意**: Hedera Consensus和镜像节点不提供Etherum RPC API 端点。
{% endhint %}

### 令牌和费用差异

<table><thead><tr><th width="232.33333333333331">函数</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td> <strong>原生令牌</strong></td><td>Supports native tokens in addition to <a href="tokens-managed-by-smart-contracts/">ERC-20 and ERC-721 token standards</a></td><td>所有ERC 令牌标准，但主要是ERC-20 和 ERC-721 代币。</td></tr><tr><td><strong>费用结构</strong></td><td><a href="deploying-smart-contracts/#gas-schedule-and-fees">复杂，有两种不同的煤气价格</a></td><td>单气价格</td></tr><tr><td><strong>Token Association**</strong></td><td><a href="../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md">令牌关联概念 </a></td><td>没有令牌关联的概念</td></tr><tr><td><strong>Keys for Token Functionality</strong></td><td>密钥控制对令牌功能的访问权(<code>KYC</code>, <code>免费</code>, <code>WIPE</code>, 供应、费用和 <code>PAUSE</code>)</td><td>没有相应的 <em>原生</em> 功能</td></tr></tbody></table>

{% hint style="info" %}
**\*\*注意：** 令牌协会仅适用于_native_HTS 令牌，不影响ERC-20/721 令牌。
{% endhint %}

### 其他差异

<table><thead><tr><th width="238">函数</th><th width="274.3333333333333">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Precheck Failures</strong></td><td><a href="../../sdks-and-apis/hedera-api/miscellaneous/responsecode.md">多重预检查失败原因</a></td><td>通常单个故障原因</td></tr><tr><td><strong>HBAR 十进制精度</strong></td><td>8 或 18<a href="../../sdks-and-apis/sdks/hbars.md#hbar-decimal-places"> (贯穿Hedera API)</a></td><td>协调18点小数精度</td></tr></tbody></table>
