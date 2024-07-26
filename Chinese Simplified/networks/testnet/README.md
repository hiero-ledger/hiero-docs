---
description: 加入Hedera Testnet
---

# Testnets

## 概览

Hedera 测试网络为开发者提供了免费测试环境的机会。 测试网模拟了您所期望的主网的确切开发环境。 这包括交易费、节流、可用服务等。 若要创建 Hedera Testnet 或 Previewnet 帐户，您可以访问 [Hedera 开发者门户](https://portal.hedera.com/login)。

一旦您的应用程序在这个测试环境中建立并测试完毕， 你可以指望将你分散的应用程序 (dApp) 迁移到主网而不做任何更改。

<table><thead><tr><th width="324">测试网络</th><th>描述</th></tr></thead><tbody><tr><td><strong>Testnet</strong></td><td>Testnet运行与Hedera Mainnet相同的代码，旨在为即将移动到主网的开发者提供一个生产前的环境。 You can find compatible SDKs <a href="../../sdks-and-apis/sdks/#hedera-supported-sdks">here</a>.</td></tr><tr><td><strong>预览网</strong></td><td><p>Hedera 团队正在开发的代码，很可能用于即将发布的版本，其目的是让开发者早日暴露进来的功能。 网络经常更新。 不能保证SDK会随时支持即将到来的功能。</p><p><strong>注意：</strong> 此网络的更新是由新版本触发的并且是频繁的。 这些更新将不会反映在状态页面上。</p></td></tr></tbody></table>

<table><thead><tr><th width="325">网络服务</th><th>可用性</th></tr></thead><tbody><tr><td><strong>Cryptocurrency</strong></td><td>限量的</td></tr><tr><td><strong>Consensus Service</strong></td><td>限量的</td></tr><tr><td><strong>文件服务</strong></td><td>限量的</td></tr><tr><td><strong>智能合同服务</strong></td><td>限量的</td></tr><tr><td><strong>令牌服务</strong></td><td>限量的</td></tr></tbody></table>

### 测试网络重置项

镜像节点和协商一致的节点测试网络计划每季度重置一次。 当测试网重置发生时，所有帐户、令牌、合同、主题、计划和文件数据都被擦除。

开发者将不再能够从测试网络协商一致节点访问状态数据。 例如，您将无法在重置之前的帐户执行交易或查询。&#x20

测试网镜像节点在重置日期两周后完全移除访问之前可供开发者存储任何数据。 如果可用，您将能查询两周期间的旧测试网信息。

**你应该做什么：**

- 注意即将到来的重置日期。
- 拥有为您的应用程序重新创建测试数据的能力，以尽量减少中断。
- 重置后，您需要访问 [Hedera 开发者门户](https://portal.hedera.com/register) 来获取您的新测试网帐号ID。
  - 重置后公钥和私钥对将保持不变。
- 订阅[Hedera 状态页面](https://status.hedera.com/) 接收重置通知。
- 镜像节点操作员可以参考指示 [here](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) 来设置您的镜像节点
  - GCP GCS 和 AWS 3 桶： `hedera-testnet-streams-2023-01`

如果您有任何问题或问题，请通过 [Discord](https://hedera.com/discord)与我们联系。

**重置日期:**

**2024**

- 2024年2月1日——完成
- 2024 年 4 月 25 日
- 7月25日，2024
- 2024 年 10 月 31 日

**2023**

- 1月26日，2023 - 完成 &#x20
- 2023年4月27日-跳过 &#x20
- 2023年7月27日―― 完成
- 10月26日，2023 - 跳过

### 测试网络阈值

{% hint style="warning" %}
**Limited Support**
交易目前被用于测试网。 如果提交到网络的交易数量超过阈值，您将收到\*\*`BUSY`\*\* 的回复。
{% endhint %}

<table><thead><tr><th width="322">网络请求类型</th><th>Throtle (tps)</th></tr></thead><tbody><tr><td><strong>加密货币交易</strong></td><td><p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>AccountBalanceQuery</code>: 无限<br><code>TransferTransaction</code> (inct. tokens): 10,000 tps</p><p><code>其它</code>: 100tps</p></td></tr><tr><td><strong>共识交易</strong></td><td><p><code>主题创建交易</code>: 5 tps</p><p><code>其它</code>: 100tps</p></td></tr><tr><td><strong>代币交易</strong></td><td><p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS 用于可互换的心情</li><li>NFT mint 50 TPS</li></ul><p><code>TokenAssociateTransactions</code>: 100 tps<br><code>TransferTransaction</code> (inct. tokens): 100,000 tps</p><p><code>其它</code>: 3,000吨点</p></td></tr><tr><td><strong>交易计划</strong></td><td><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</td></tr><tr><td><strong>文件交易</strong></td><td>10 tps</td></tr><tr><td><strong>智能合同交易</strong></td><td><code>合同执行交易</code>: 350 tps<br><code>合同创建交易</code>: 350 tps</td></tr><tr><td><strong>查询</strong></td><td><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetcontent</code>: 700 tps<br><br><code>Other</code>: 10, 00 tps</td></tr><tr><td><strong>收款</strong></td><td>无限制(无节点)</td></tr></tbody></table>
