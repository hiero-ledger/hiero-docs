# 智能合同安全

[Hedera智能合同服务](../../support-community/词汇表)。 d#hedera-smart-contract services（Hedera）整合了Hedera第三代原生实体的功能——高功率和快尾性。 可预期和负担得起的费用和公平的交易订单——具有高度优化和业绩良好的第二代[以太坊虚拟机](……)。 /../support-community/glossary.md#eyasum-virtual-mye-evm)。 我们的目标是全面支持最初为与EVM兼容的其他链条订立的智能合同，并使其能够在黑德拉无缝地部署。

***

## EVM 等效性

我们努力确保开发人员能够方便地指向赫德拉支持的 RPC 端点，并使用相同的代码和类似工具进行智能合同执行和查询，以实现EVM 等值。 所有智能合同交易都使用[Besu EVM](../../support-community/glossary)执行。 d#hyledger-besu-evm以实现这项目标，由此产生的更改将存储在Hedera优化[虚拟默克树](../../support-community/glossary.md#virtual-merkle-tree)状态。 因此，保证用户在2-3秒内完成智能合同执行的确定终结（而不是概率），同时确保智能合同功能完全包含状态改变。 #x20

{% hint style="info" %}
🔔 Hedera's EVM 等效目标和异常的全面细分可以找到 [**here**](hederas-evm-equinence-goals-and-exceptions.md).&#x20;
{% endhint %}

***

## 安全模型

### 旧型号(v1) 边界

旧的安全模型(pre [0.35.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.35.2)) 支持交易时提供的账户密钥签名。 这种模式的一些主要特点包括：

- [Smart contracts](../../supportand-community/glossary.md#smart-contracts)只能更改他们自己的存储或他们[委托](https://docs.soliditylang.org/en/v0.8.19/introductionto-smart-contracts.html#delegatecall-libraries)。
- 系统智能合约可以调用来执行 [Hedera Token Service (HTS)](../../supportand-community/glosses. d#hedera-token-service-hs) 操作代表另一个帐户-外部拥有的帐户或合同帐户。
- 智能合同可以使用交易中的适当签名更改EOA的存储。
- 智能合同可以通过交易中的适当签名或先前添加到备用金审批清单来更改EOA的余额。

这大大提高了用户的体验，因为合同可以将交易合并在一起，试图解剖。 例如，合同可以代表用户与一个签名联系、转让和批准交易。 在注重可用性的同时， 这种办法没有处理坏行为者可以代表用户进行未经批准的交易的情况。 ., [https://hedera. om/blog/analysis-remediation-of precompile-attack-onlyhedera-network](https://hedera.com/blog/analysis-remediation-of precompile-attack-withhhedera-network)

为了解决这个问题，Hedera核心工程师彻底分析了智能合同服务和HTS系统合同。 旨在在智能合同执行期间保护用户和网络的状态和令牌资产。 这项工作的结果是[Hedera Services release v0.35.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.35.2)。

### 新建模型 (v2) 边界

在新的安全模式中，账户钥匙签名不能为合同行动提供授权。 它的主要特点包括：

- 智能合约只能更改他们自己的存储或他们[委托](https://docs.soliditylang.org/en/v0.8.19/introductionto-smart-contracts.html#delegatecall-libraries)。
- 系统智能合同可以**不**调用，但令牌代理/脸部流量除外，例如：[HIP 719](https://hips.hedera.com/hip/hip-719)。 在这种情况下，HTS 令牌作为智能合约（参见[HIP 218](https://hips.hedera.com/hip/hip-218)）表示为通用的ERC 方法。
- 智能合同只有在合同ID包含在EOA密钥中时才能更改EOA存储。
- 智能合同可以更改EOA持有的特定令牌允许的余额.

#### 边界比较表

<table data-full-width="false"><thead><tr><th width="191">边界速度</th><th width="238">v1 模型</th><th width="225">v2 模型</th><th align="center">更改</th></tr></thead><tbody><tr><td>存储更改</td><td>智能合约只能更改他们自己的存储或他们被调用的存储</td><td>智能合约只能更改他们自己的存储或他们被调用的存储</td><td align="center"><strong>N</strong></td></tr><tr><td>系统合同调用类型</td><td>可以调用系统智能合同，以便代表另一个帐户或合同执行Hedera Token Service 业务。</td><td>系统智能合同不能被授予，唯一的是令牌面流程，它提供高密度硬币作为通用ERC方法的智能合同。</td><td align="center"><strong>Y</strong></td></tr><tr><td>允许的帐户存储更改</td><td>智能合同可以使用交易中的适当签名更改EOA的存储。</td><td>如果合同ID包含在EOA密钥中，智能合同可以更改EOA存储。</td><td align="center"><strong>Y</strong></td></tr><tr><td>授权账户余额变化</td><td>智能合同可以使用交易中的适当签名或预先添加备用金审批清单来更改账户（EOA或合同）余额</td><td>智能合同如果已经批准了象征性的津贴，就可以更改EOA余额。</td><td align="center"><strong>Y</strong></td></tr></tbody></table>

8. 总之，安全和安保部采用三级安全办法：

1. **级别0 - EVM 安全模式：** 实体只能修改自己的状态和余额。
2. **Level 1 - ERC Account Value Security Models:** Transfer and access to account value will follows very web3 interface standards, e.g. ERC20, ERC721.
3. **Level 2 - Hedera Advanced Security Features：** 独特的Hedera 功能可以使用合同兼容的权限，例如ContractID密钥。

<figure><img src="../../.gitbook/assets/SecurityModel01a.png" alt="" width="188"><figcaption><p>Hedera的三级智能合同安全模型</p></figcaption></figure>

为了实现状态改变或价值转移，执行必须遵守每一级别的规则。 当发送者未被授权进行操作时，不符合适当授权的交易将在响应码中失败，如`INVALID_FULL_PREFIX_SIGNATURE_FOR_PRECOMPILE` 。 将在适用情况下返回更多针对具体操作的响应代码，例如`SPENDER_DOE_NOT_HAVE_ALLOWANCE`。

***

## 对开发者的影响

### 作为赫德拉的开发者，我应该怎么办？

强烈鼓励开发者用新的合同测试他们的应用，使用新的安全模型测试UX，以避免意外后果。

- 新的安全模式已经适用于从主机[0.35.2版](../../networks/release-notes/services.md#0.35.2-hedera-smart-contract-service-security-models)及其后产生的合同。
- 升级之前部署的现有合同将在有限的时间内继续使用以前的安全模式，以便能够进行应用/UX修改。
- 以前的安全模式只能维持大约三个月。 目前的目标是网络移除以前的安全模式，所有合同在2023年7月的主网发布之前沿用新的安全模式。
- 查看一个 [here]的安全更新的完整列表(security.md#0.35.2).&#x20

### 安全模式的变化对智能合同开发者意味着什么？

安全更新涉及修改状态时合同执行过程中实体权限的更改。 简而言之，系统合同调用 (智能合同呼叫到Hedera Token Service) 不再与所有较高呼叫权限一起执行。 即使授权用户提供签名。

了解外部拥有账户的合同执行过程以及在经常性呼吁和授权呼吁期间的合同执行过程至关重要。 这个过程涉及跟踪账户、状态(存储和价值余额)以及代码可能随着您在通话链中的进展而改变。

#### 前 (v1 模式)

在正常的通话环境中，当调用时，B的代码是在其自己的状态下执行的。 这只允许B修改自己的状态。 发送者的值在调用之间也不同，以强调EOA进行了第一次调用，而合同A则使第二次调用。

<figure><img src="../../.gitbook/assets/SecurityModel02rev - EOA.png" alt=""><figcaption></figcaption></figure>

#### 在 (v2 建制后)

另一方面，在代理通话的情况下，对 B 的调用在A 状态下执行了 B 的代码。 这允许B修改A的状态。 发件人和收件人的值不会被第一次调用保存，仿佛EOA启动了呼叫。

<figure><img src="../../.gitbook/assets/SecurityModel03b - EOA.png" alt=""><figcaption></figcaption></figure>

总之，一名代表在前一个帐户中执行电话合同的代码。 授予代码访问前一个帐户状态的权限，并且模糊了授权的状态管理线。

将此应用于安全模型的更改，以下表格概括了授权检查的更改。

<table><thead><tr><th width="296">场景</th><th width="227">授权检查</th><th width="107" align="center">旧模型</th><th align="center">新建模型</th></tr></thead><tbody><tr><td>智能合同 A 可以通过呼叫更改自己的状态</td><td>发送者=合同A</td><td align="center">Y</td><td align="center">Y</td></tr><tr><td>智能合同 A 可以通过通话更改EOA 状态</td><td>sender = EOA</td><td align="center">N</td><td align="center">N</td></tr><tr><td>智能合同B可以通过通话更改合同A的状态</td><td>发送者 = A</td><td align="center">N</td><td align="center">N</td></tr><tr><td>智能合同 A 可以通过交谈更改EOA 状态</td><td>sender = EOA</td><td align="center">Y</td><td align="center">Y</td></tr><tr><td>智能合同B可以通过交谈更改合同A的状态</td><td>发送者=合同A</td><td align="center">Y</td><td align="center">Y</td></tr><tr><td>系统智能合同可以通过调用更改其他帐户(EOA或contract A或contract B)</td><td>发件人 = 帐户</td><td align="center">N</td><td align="center">N</td></tr><tr><td>系统智能合同可以通过授权电话更改另一个帐户EOA或contract A或contract B状态</td><td>发件人 = 帐户</td><td align="center">N</td><td align="center">N</td></tr><tr><td><strong>System contracts can change an accounts (EOA or contact A or contract B) state via call with the appropriate signature</strong></td><td><strong>签名地图包含账户签名(分别为EOA或联系A或合同B)</strong></td><td align="center"><strong>Y</strong></td><td align="center"><strong>N</strong></td></tr><tr><td><strong>System smart contract can change another accounts (EOA or contact A or contract B) state via delegate call with the appropriate signature</strong></td><td><strong>signature map contains signature of accounts (EOA or contact A or contract B)</strong></td><td align="center"><strong>Y</strong></td><td align="center"><strong>N</strong></td></tr><tr><td>合同A或B可以通过通话调用系统合同</td><td>-</td><td align="center">Y</td><td align="center">Y</td></tr><tr><td><strong>Contract A or B can call a system contract via a delegate call</strong></td><td><strong>-</strong></td><td align="center"><strong>Y</strong></td><td align="center"><strong>N</strong></td></tr></tbody></table>

在更改时，[HTS system contract](https://github.com/hashgraph/hedera-smart-contracts/tree/release/0.3/contracts/hts-precompile)是唯一通过智能合同暴露Hedera API 功能的路径。 因此，在观察高频系统合同状态变化功能时，考虑事前和事后安全模型之间的差异是公平的。

#### HTS 系统现有合同影响摘要

<table data-full-width="false"><thead><tr><th width="212" align="center">IHederaTokenService 系统智能合同功能</th><th width="147" align="center">v1 模型授权要求</th><th width="139" align="center">v2 模型授权要求</th><th width="96" align="center">影响代码</th><th align="center">开发者解答</th></tr></thead><tbody><tr><td align="center"><strong>批准、核准NFT</strong></td><td align="center">签名地图包含账户管理员密钥签名</td><td align="center"><code>msg.sender</code> 必须是实体才能修改</td><td align="center">Y</td><td align="center"><p>升级合同</p><p></p><p>或</p><p></p><p>升级 DApp 以提供明确的用户批准</p><p></p><p><mark style="background-color:yellow;">*附加安全路径:</mark> <a href="https://hips.hedera.com/hip/hip-376"><mark style="background-color:yellow;">HIP 376</mark></a> <mark style="background-color:yellow;">IERC.approve()</mark></p></td></tr><tr><td align="center"><strong>associateToken</strong></td><td align="center">签名地图包含账户管理员密钥签名</td><td align="center"><code>msg.sender</code> 必须是实体才能修改</td><td align="center">Y</td><td align="center"><p>升级合同</p><p></p><p>或 </p><p></p><p>升级 DApp 以提供明确的用户关联</p><p></p><p><mark style="background-color:yellow;">*附加安全路径:</mark> <a href="https://hips.hedera.com/hip/hip-719"><mark style="background-color:yellow;">HIP 719</mark></a> <mark style="background-color:yellow;">IHRC.associate()</mark></p></td></tr><tr><td align="center"><strong>burnToken</strong></td><td align="center"><p>签名地图包含令牌烧录密钥签名</p><p></p><p>或</p><p></p><p>合同 Id 满足了 Token.supplyKey 的要求</p></td><td align="center">合同 Id 满足了 Token.supplyKey 的要求</td><td align="center">Y</td><td align="center">Token 管理员必须在供应密钥中设置所需的合同</td></tr><tr><td align="center"><strong>createFungibleToken, createFungibleTokenWithCustomFees, createNonFungibleToken, createNonFungibleTokenWithCustomFees</strong></td><td align="center"><p>签名地图包含受影响的账户管理员密钥签名(s) </p><p></p><p>或 </p><p></p><p>自动更新作业大小写</p></td><td align="center"><p><code>msg.sender</code> 必须是实体才能在金库中修改 </p><p></p><p>或 </p><p></p><p>自动更新作业大小写</p></td><td align="center">Y</td><td align="center">-</td></tr><tr><td align="center"><strong>加密传输</strong></td><td align="center"><p>签名地图包含发件人管理员密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足Entity.key 的要求</p></td><td align="center"><p><code>msg.sender</code> 必须是实体才能在金库中修改 </p><p></p><p>或 </p><p></p><p>自动更新作业大小写</p></td><td align="center">Y</td><td align="center">升级 DApp 以提供明确的用户批准。</td></tr><tr><td align="center"><strong>deleteToken</strong></td><td align="center"><p>签名地图包含管理员密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.adminKey 的要求</p></td><td align="center">合同 Id 满足了 Token.adminKey 的要求</td><td align="center">Y</td><td align="center">代币管理员必须在管理员密钥中设置所需的合同</td></tr><tr><td align="center"><strong>dissociateToken, dissociateTokens</strong></td><td align="center">签名地图包含管理员密钥签名</td><td align="center"><code>msg.sender</code> 必须是实体才能修改</td><td align="center">Y</td><td align="center"><p>升级合同</p><p></p><p>或 </p><p></p><p>升级 DApps 以提供明确的用户脱离关联</p><p></p><p><mark style="background-color:yellow;">*附加安全路径:</mark> <a href="https://hips.hedera.com/hip/hip-719"><mark style="background-color:yellow;">HIP 719</mark></a> <mark style="background-color:yellow;">IHRC.associate()</mark></p></td></tr><tr><td align="center"><strong>冻结令牌</strong></td><td align="center"><p>签名地图包含冻结密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.freezeKey 的要求</p></td><td align="center">合同 Id 满足了 Token.freezeKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在冻结密钥中设置所需的合同</td></tr><tr><td align="center"><strong>grantTokenKyc</strong></td><td align="center"><p>签名地图包含 kyc 键签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.freezeKey 的要求</p></td><td align="center">合同 Id 满足了 Token.kycKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在 kyc 键中设置所需的合同</td></tr><tr><td align="center"><strong>mintToken</strong></td><td align="center"><p>签名地图包含适当的签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.supplyKey 的要求</p></td><td align="center">合同 Id 满足了 Token.supplyKey 的要求</td><td align="center">Y</td><td align="center">Token 管理员必须在供应密钥中设置所需的合同</td></tr><tr><td align="center"><strong>pauseToken</strong></td><td align="center"><p>签名地图包含暂停密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足Token.pauseKey 的要求</p></td><td align="center">合同 Id 满足Token.pauseKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在暂停密钥中设置所需的合同</td></tr><tr><td align="center"><strong>revokeTokenKyc</strong></td><td align="center"><p>签名地图包含 kyc 键签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.freezeKey 的要求</p></td><td align="center">合同 Id 满足了 Token.kycKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在 kyc 键中设置所需的合同</td></tr><tr><td align="center"><strong>setApprovalForAll</strong></td><td align="center">签名地图包含管理员密钥签名</td><td align="center"><code>msg.sender</code> 必须是实体才能修改</td><td align="center">Y</td><td align="center"><p>升级合同</p><p></p><p>或</p><p></p><p>升级 DApp 以提供明确的用户关联</p><p></p><p><mark style="background-color:yellow;">*附加安全路径:</mark> <a href="https://hips.hedera.com/hip/hip-376"><mark style="background-color:yellow;">HIP 376</mark></a> <mark style="background-color:yellow;">IERC.setApprovalForAll()</mark></p></td></tr><tr><td align="center"><strong>transferFrom, transferfromNFT</strong></td><td align="center"><p>签名地图包含管理员密钥签名 </p><p></p><p>或 </p><p></p><p>Spender 必须已经预先批准了一个津贴</p></td><td align="center"><p><code>msg.sender</code> 必须是实体才能在金库中修改 </p><p></p><p>或 </p><p></p><p>自动更新作业大小写</p></td><td align="center">Y</td><td align="center">升级 DApp 以提供明确的用户批准。</td></tr><tr><td align="center"><strong>transferToken, transferToken, transferfact NFT, transfering NFTs</strong></td><td align="center"><p>签名地图包含管理员密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足Entity.key 的要求 </p><p></p><p>或 </p><p></p><p>合同已获核准，所有人可支用的津贴</p></td><td align="center"><p><code>msg.sender</code> 必须是平衡所有者。 </p><p></p><p>如果不是 1。 合同 Id 满足Entity.key 的要求 </p><p></p><p>或 </p><p></p><p>2. 合同已获核准，所有人可支用的津贴</p></td><td align="center">Y</td><td align="center">升级 DApp 以提供明确的用户批准。</td></tr><tr><td align="center"><strong>updateTokenInfo, updateTokenExpiryInfo, updateTokenKeys</strong></td><td align="center"><p>签名地图包含管理员密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.adminKey 的要求</p></td><td align="center">合同 Id 满足了 Token.adminKey 的要求</td><td align="center">Y</td><td align="center">代币管理员必须在管理员密钥中设置所需的合同</td></tr><tr><td align="center"><strong>wipeTokenAccount, wipeTokenAccountNFT</strong></td><td align="center"><p>签名地图包含令牌擦除密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.wipeKey 的要求</p></td><td align="center">合同 Id 满足了 Token.wipeKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在 Wipe 密钥中设置所需的合同</td></tr><tr><td align="center"><strong>unfreezeToken</strong></td><td align="center"><p>签名地图包含令牌冻结密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足了 Token.freezeKey 的要求</p></td><td align="center">合同 Id 满足了 Token.freezeKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在冻结密钥中设置所需的合同</td></tr><tr><td align="center"><strong>unpauseToken</strong></td><td align="center"><p>签名地图包含令牌暂停密钥签名 </p><p></p><p>或 </p><p></p><p>合同 Id 满足Token.pauseKey 的要求</p></td><td align="center">合同 Id 满足Token.pauseKey 的要求</td><td align="center">Y</td><td align="center">令牌管理员必须在暂停密钥中设置所需的合同</td></tr></tbody></table>

{% hint style="info" %}
**注意：** 虽然更改影响到用户体验，需要更明确的步骤，但它们不仅仅是相应地提高用户和网络的整体安全。 团队继续积极推动为社区提供安全和可缩放的 API 解决方案，使他们能够建立创造性的 dapp 并在分类账上将自己共享的世界。
{% endhint %}

***

## 安全升级

<details>

<summary>0.35.2</summary>

- 3月9日发生安全事件后，工程师对智能合同服务和Hedera Token服务系统合同进行了彻底分析。
- 作为这项工作的一部分， 我们没有发现可能导致我们在3月9日目睹的攻击的任何其他脆弱性。
- 该团队还寻找一个典型的智能合同开发者的期望与Hedera Token系统合同API的行为之间的差异。 这种行为差异可能会被恶意智能合同开发者以意料之外的方式使用。
- 为了消除这些行为差异今后被用作攻击媒介的可能性， 协商一致的节点软件将使Hedera Smart Contract Service token system contracting system contracting series with the those of EVM and 典型令牌 API，例如ERC 20 and ERC 721。
- 因此，在3月31日发布的0.35.2号主网时做出了下列修改：
  - 如果合同想要从账户余额中转移价值，EOA(外部拥有的账户)必须对合同提供明确的批准/准许。
  - `transferFrom`系统合同的行为与ERC 20和721 spec`transferFrom`功能的行为完全相同。
  - 对于HTTS 特定的令牌功能(例如) 暂停、冻结或赠送KYC， 只有当合同ID被列为令牌上的一个密钥时，合同才会被授权执行关联的令牌管理功能 (例如) 暂停密钥，冻结密钥，KYC密钥。
  - 如果调用者拥有正在传输的值，则`transferToken` 和 `transferNFT` API将在ERC20/721中以 `transfer`为单位， 否则，它将依靠令牌所有者批准Spender许可。
  - 在修改状态时，上述模型将要求实体(EA和合同)在合同执行过程中的权限。 合同将不再依靠Hedera交易签名，而是按照EVM、ERC和ContractId所指出的关键模型。
- 作为这次释放的一部分，该网络将在以前的合同中列入祖父的逻辑。
  - 从这种放行中产生的任何合同都将使用更严格的安全模式，因此不会考虑在交易中使用最高层次的签名来提供许可。
  - 在此升级之前部署的现有合同将自动移植到原来的位置，并且继续使用在发布之前已经存在的旧模式，在有限的时间内允许修改DApp/UX，以便与新的安全模式一起工作。
  - 祖父的逻辑将在释放后大约保持3个月。 在今后的2023年7月公布时，该网络将取消祖父的逻辑，所有合同都将遵循新的安全模式。
  - 鼓励开发者用新的合约测试他们的DApp 和 UX 使用新的安全模型避免意外后果。 如果任何DApp 开发者未能修改他们的应用程序或更新他们的合同（视情况而定）以遵守新的安全模型， 他们在应用中可能会遇到问题。

</details>
