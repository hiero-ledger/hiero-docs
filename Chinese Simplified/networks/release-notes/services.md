---
description: Hedera Services release information
---

# Hedera Services

Please visit the [Hedera status page](https://status.hedera.com/) for the latest versions supported on each network.

## [v0.51](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)

{% hint style="info" %}
**MAINNET UPDATE SCHEDULED: JULY 17, 2024**
{% endhint %}

### 发布高亮

#### [HIP 206](https://hips.hedera.com/hip/hip-206)

**功能：**

- 定义Hedera Token 服务合同的新功能，允许HBAR、可互换代币和不可互换代币的原子转让。
  - 函数 cryptoTransfer(TransferList transferList,TokenTransferList\[tokenTransfer)
- 通过智能合约公开现有的 HAPI 通话。
- 转让方面给予的津贴。 &#x20

**福利：**

- Enables native royalty support on the EVM since native $hbar can now be transferred using spending allowances
- 与 HBAR 和 HTS 令牌的直接互动
- 取消令牌包装的需要。
- 提高效率，减少复杂性。
- 通过移除中间步骤来减少成本，例如将资产包装起来以便与它们互动。
- 启用对EVM的本地使用费支持，因为本机HBAR现在可以使用支出补贴来转移。

#### [HIP 906](https://hips.hedera.com/hip/hip-906)

**功能：**

- 引入新的Hedera账户服务合同。
- 启用通过智能合约代码查询和批准HBAR
  - hbarAlde, hbarApprove
- 开发者不必从智能合同代码中切换

**福利：**

- 为HBAR津贴引入新账户代理合同
- 在智能合约中启用赠予、检索和管理 HBAR
  - 开发者不必从智能合约代码中切换
- 简化工作流程和加强安全
- 扩展潜在的使用场景，尤其是 Defi 和令牌市场

### [0.51.5](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)

{% hint style="success" %}
**TESTNET Update: July 2, 2024**
{% endhint %}

#### What's Changed

- features (reconnect): introduction ReconnectMapStats interface by [@anthony-swirldslabs](https://github.com/anthony-swirldslabs) in [#13027](https://github.com/hashgraph/hedera-services/pull/13027)
- chore: returning removement of CLI report tool by [@lpetitrovic05](https://github.com/lpetrovic05) in [#13002](https://github.com/hashgraph/hedera-services/pull/13002)
- 文档：添加HIP-904令牌拒绝操作的设计文档，由 [@MiroslavGatsanooga](https://github.com/MiroslavGatsanoga) 在 [#12786](https://github.com/hashgraph/hedera-services/pull/1276)
- feate: gossip facade by [@cody-littley](https://github.com/cody-littley) in [#12897](https://github.com/hashgraph/hedera-services/pull/12897)
- 功能：添加能力以在 [#13083] (https://github.com/hashgraph/hedera-services/pull/13083) 禁用运行中的事件hasher
- 修复：忽略在 `TokenDissociate` 中的 tokenDissociate\` 里的 [@tinker-michaelj](https://github.com/tinker-michaelj) 在 [#13104](https://github.com/hashgraph/hedera-services/pull/13104)
- 特性：添加 javadoc 和图表，删除由[@tinker-michaelj](https://github.com/tinker-michaelj) 在 [#13070](https://github.com/hashgraph/hedera-services/pull/13070)
- 修复：在[#13020](https://github.com/hashgraph/hedera-services/pull/13020)
- 修复： 12853: MerkleDbDataSource.copyStatisticsFrom() 由 [@artemananiev](https://github.com/artemaniev) 在 [#13097](https://github.com/hashgraph/hedera-services/pull/13097)
- 功能：更新套期服务代码以支持 DAB 原始更改。 由 [@iwsimon](https://github.com/iwsimon) 在 [#13090](https://github.com/hashgraph/hedera-services/pull/13090)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)\*\* \*\*

## [v0.50](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)

{% hint style="success" %}
**MAINNET UPDATE: JUNE 20, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE: JUNE 5, 2024**
{% endhint %}

### [0.50.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.1)

#### What's Changed

- chore: Cherry pick 13648 into release 0.50 branch by [@lukelee-sl](https://github.com/lukelee-sl) in [#13662](https://github.com/hashgraph/hedera-services/pull/13662)
- fix(ci): cherry pick milestone assignee checks rel 50 by [@rbarkerSL](https://github.com/rbarkerSL) in [#13712](https://github.com/hashgraph/hedera-services/pull/13712)
- fix: (cherry-pick) Use restart method to all token schemas by [@Neeharika-Sompalli](https://github.com/Neeharika-Sompalli) in [#13676](https://github.com/hashgraph/hedera-services/pull/13676)
- fix: Enable tokens.balancesInQueries.enabled by [@netopyr](https://github.com/netopyr) in [#13716](https://github.com/hashgraph/hedera-services/pull/13716)
- chore: Enable tokens.balancesInQueries in code by [@netopyr](https://github.com/netopyr) in [#13769](https://github.com/hashgraph/hedera-services/pull/13769)

**➡ See the full list of changes** [**here**](https://github.com/hashgraph/hedera-services/compare/v0.50.0...v0.50.1)**.**

### [0.50.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)

#### 改变了什么

- feat: reorganize ISS wiring by [@alittley](https://github.com/alittley) in [#11685](https://github.com/hashgraph/hedera-services/pull/11685)
- feat(diff-testing): Script (python) to pull intervals - up to a day - from GCP by [@david-bakin-sl](https://github.com/david-bakin-sl) in [#11409](https://github.com/hashgraph/hedera-services/pull/11409)
- fix: 11750 Fixed synchronization in `BreakableDataSource.saveRecords` by [@imalygin](https://github.com/imalygin) in [#11756](https://github.com/hashgraph/hedera-services/pull/11756)
- 功能: 差异测试: 增强帐户存储转储器以处理[@vtronkov](https://github.com/vtronkov) [#11489](https://github.com/hashgraph/hedera-services/pull/11489)
- test: 添加 [@anastasiya-kovaliova](https://github.com/anastasiya-kovaliova) 代币关联的安全v2 模型测试[#11327](https://github.com/hashgraph/hedera-services/pull/11327)
- 修复：停止通过[@cody-littley](https://github.com/cody-littley)在[#11769](https://github.com/hashgraph/hedera-services/pull/11769) 检查最低生育周期(https://github.com/hedera-services/pull/11769)
- 特性：通过[@cody-littley](https://github.com/cody-littley)在 [#11780](https://github.com/hashgraph/hedera-services/pull/11780)
- 修复：[@mxtartaglia-sl](https://github.com/mxtartaglia-sl) [#11754](https://github.com/hashgraph/hedera-services/pull/11754)
- 特性：通过[@cody-littley](https://github.com/cody-littley)在 [#11801](https://github.com/hashgraph/hedera-services/pull/11801)
- 修复：冻结交易需要等待更长时间[@JeffreyDallas](https://github.com/JeffreyDallas)在 [#11790](https://github.com/hashgraph/hedera-services/pull/11790)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)\*\* \*\*

### 性能结果

<figure><img src="../../.gitbook/assets/0.50_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.49](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)

{% hint style="success" %}
**MAINNET更新：22, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：14, 2024**
{% endhint %}

### [0.49.7](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.7)

#### What's Changed

- 修复：支持系统合同`tokenCreate()`中的加密管理键，由 [@tinker-michaelj](https://github.com/tinker-michaelj) 在 [#13148](https://github.com/hashgraph/hedera-services/pull/13148)
- 修复：从状态记录中删除余额调整限制，使用`0`作为初始气体快照的 [@tinker-michaelj](https://github.com/tinker-michaelj) [#13185](https://github.com/hashgraph/hedera-services/pull/13185)

### [0.49.6](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.6)

#### What's Changed

- 修复：重启时cherry-candt 午夜速率管理 [#13071](https://github.com/hashgraph/hedera-services/pull/13071)) 由 [@povolev15](https://github.com/povolev15) 于 [#13091](https://github.com/hashgraph/hedera-services/pull/13091)
- 功能： 自动重新提交操作并修改 [#12811](https://github.com/hashgraph/hedera-services/pull/12811)) 的 [@Neeharika-Sompalli](https://github.com/Neeharika-Somparli) [#13088](https://github.com/hashgraph/hedera-services/pull/13088)
- 修复：忽略在 `TokenDissociate` 中的 tokenDissociate\` 中的 [@tinker-michaelj](https://github.com/tinker-michaelj) 在 [#13106](https://github.com/hashgraph/hedera-services/pull/13106)
- 修复：当由[@tinker-michaelj](https://github.com/tinker-michaelj)从基因迁移时(non-prod)状态在 [#13123](https://github.com/hashgraph/hedera-services/pull/13123)

### [0.49.5](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.5)

#### 改变了什么

- 修复：存储链接管理由 [@tinker-michaelj](https://github.com/tinker-michaelj) 在 [#13056](https://github.com/hashgraph/hedera-services/pull/13056)

### [0.49.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.1)

#### What's Changed

- 修复：通过 [@tinker-michaelj] (https://github.com/tinker-michaelj) 重新启动时管理`StakingInfos` [#12911](https://github.com/hashgraph/hedera-services/pull/1291)

### [0.49.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)

#### 改变了 &#x20

- 功能：在 ExtCodeHash 操作中解决冷读问题，由 [@lukelee-sl](https://github.com/lukelee-sl) 在 [#11323](https://github.com/hashgraph/hedera-services/pull/1123)
- 修复：11348：11231的修复不包括ParsedBucket [@artemananiev](https://github.com/artemaniev)[#11349](https://github.com/hashgraph/hedera-services/pull/11349)
- chore: 创建 ISS 检测器组件由 [@lpetrovic05](https://github.com/lpetrovic05) 在 [#11075](https://github.com/hashgraph/hedera-services/pull/11075)
- chore: 在 [#11330](https://github.com/hashgraph/hedera-services/pull/1130) 添加 `orderedSolderTo` 方法到 OutputWire
- chore: move hashgraph demoby [@lpetrovic05](https://github.com/lpetrovic05) in [#11352](https://github.com/hashgraph/hedera-services/pull/11352)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)\*\* \*\*

### **性能结果**

<figure><img src="../../.gitbook/assets/0.49_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.48](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.0)

{% hint style="success" %}
**MAINNET更新: APRIL 25, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 18，2024**
{% endhint %}

### [0.48.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.1)

#### What's Changed

- 修复：删除调整限制 [@tinker-michaelj](https://github.com/tinker-michaelj) [#12826](https://github.com/hashgraph/hedera-services/pull/12826)

### [0.48.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.0)

{% hint style="success" %}
**TESTNET更新: APRIL 11, 2024**
{% endhint %}

#### 改变了什么

- 功能：在同步前检查平台状态 [#11429](https://github.com/hashgraph/hedera-services/pull/11429)) 由 [@alittley](https://github.com/alittley) 在 [#12679](https://github.com/hashgraph/hedera-services/pull/12679)

### 性能结果

<figure><img src="../../.gitbook/assets/0.48_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.47](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)

{% hint style="success" %}
**MAINNET更新：APRIL 4, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: 二十八, 2024**
{% endhint %}

### [0.47.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.4)

#### What's Changed

- chore: cherry-select unique CryptoCryptoCreate throtle reclamation [#12339](https://github.com/hashgraph/hedera-services/pull/123339))。

### [0.47.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.3)

{% hint style="success" %}
**TESTNET Update：MARCH 2024**
{% endhint %}

#### What's Changed

- chore: 配置 `maxAggregateRels` 到 1 500万(所有编码) ([#12053](https://github.com/hashgraph/hedera-services/pull/12053))。

### [0.47.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.2)

#### What's Changed

- 修复：在 `MerkleDbConfig` 中更新配置 `hashesRamToDiskThreshold` 到 0
- 修复：虚拟地图刷新的补丁。

### [0.47.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.1)

{% hint style="success" %}
**TESTNET Update： FEBRUARY 29, 2024**
{% endhint %}

#### What's Changed

- 修复：只比较[@alittley](https://github.com/alittley)创建的子时间和[#11673](https://github.com/hashgraph/hedera-services/pull/11673)
- chore: 添加 [@cody-littley](https://github.com/cody-littley) 为输入添加旧风格的队列线程[#11671](https://github.com/hashgraph/hedera-services/pull/11671)
- 修复：11746：[#11304](https://github.com/hashgraph/hedera-services/issues/11304)返回本端，通过[@artemaniev](https://github.com/artemaniev)发布0.47 [#11747](https://github.com/hashgraph/hedera-services/pull/1147)

### [0.47.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)

#### What's Changed

- 修复：当节点被[@cody-littley](https://github.com/cody-littley)移除时出现错误[#10687](https://github.com/hashgraph/hedera-services/pull/10687)
- 修复：[@Jivkokelchev](https://github.com/JivkokKelchev)在 [#10185](https://github.com/hashgraph/hedera-services/pull/10185)
- 修复：记录缓存以提交添加的条目，并正确实现了由[@povolev15](https://github.com/povolev15)在[#10523](https://github.com/hashgraph/hedera-services/pull/10523) 从队列中移除的元素
- 修复：修复并启用所有 [@povolev15] (https://github.com/povolev15) 在 [#10551](https://github.com/hashgraph/hedera-services/pull/10551)
- 修复：通过 [@Jivkokelchev](https://github.com/Jivkokelchev) 在 [#9815](https://github.com/hashgraph/hedera-services/pull/9815) 实现sidecars
- 功能：添加出生回合古代阈值的设置，由 [@cody-littley](https://github.com/cody-littley) 在 [#10660](https://github.com/hashgraph/hedera-services/pull/10660)
- chore: dropchatter by [@cody-littley](https://github.com/cody-littley) in [#10670](https://github.com/hashgraph/hedera-services/pull/10670)
- chore: move state info by [@cody-littley](https://github.com/cody-littley) in [#10685](https://github.com/hashgraph/hedera-services/pull/10685)
- chore: 重命名合同导致服务回归，由 [@stoqnkpL] (https://github.com/stoqnkpL) 在 [#10700](https://github.com/hashgraph/hedera-services/pull/10700)
- 修复：状态泄露由 [@cody-littley](https://github.com/cody-littley) 在 [#10690](https://github.com/hashgraph/hedera-services/pull/10690)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)\*\* \*\*

### 性能结果

<figure><img src="../../.gitbook/assets/0.47_Performance Measurement Results_Extract.png" alt=""><figcaption></figcaption></figure>

## [v0.46](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)

{% hint style="success" %}
**MAINNET UPDATE: FEBRUARY 21, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: FEBRUARY 6, 2024**
{% endhint %}

### [**0.46.3**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.3)

#### What's Changed

- chore: bump HAPI proto version by [@tinker-michaelj](https://github.com/tinker-michaelj) in [#11232](https://github.com/hashgraph/hedera-services/pull/11232)

### [**0.46.2**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.2)

{% hint style="success" %}
**TESTNET Update: JANUARY 30, 2024**
{% endhint %}

#### What's Changed

- 修复：确保待定的创建自定义程序适用于[@lukelee-sl](https://github.com/lukelee-sl) [#11213](https://github.com/hashgraph/hedera-services/pull/11213) 正在创建的地址

### [**0.46.1**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.1)

#### What's Changed

- chore: bump HAPI proto version by [@tinker-michaelj](https://github.com/tinker-michaelj) in [#11232](https://github.com/hashgraph/hedera-services/pull/11232)

### [0.46.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)

{% hint style="success" %}
**TESTNET更新：JANUARY 23，2024**
{% endhint %}

#### What's Changed

- 功能: 线路图改进由 [@cody-littley](https://github.com/cody-littley) 在 [#10233](https://github.com/hashgraph/hedera-services/pull/10233)
- chore: 在 [@Neeharika-Sompalli](https://github.com/Neeharika-Sompalli) 在自定义费用评估中将 `HashMap` 更改为 `LinkedHashMap` (https://github.com/hashgraph/hedera-services/pull/10240)
- 功能：在线索设施中添加实现，以处理 N-Of-Unscale 类型的线索，由 [@MiroslavGatsanoga](https://github.com/MiroslavGatsanga) 在 [#10142](https://github.com/hashgraph/hedera-services/pull/10142)
- build: 不发布[@jjohannes](https://github.com/jjohannes)在 [#10147](https://github.com/hashgraph/hedera-services/pull/10147)
- build: 补丁我们用来作为真正的 Java 模块的 [@jjohannes](https://github.com/jjohannes) [#10056](https://github.com/hashgraph/hedera-services/pull/10056)
- chore!: 更常见的测试已移动到正确的模块, 由 [@hendrikebbers](https://github.com/hendrikebbers) 在 [#10133](https://github.com/hashgraph/hedera-services/pull/10133)
- 功能： [@hendrikebbers](https://github.com/hendrikebbers) 创建和使用的配置常量[#10117](https://github.com/hashgraph/hedera-services/pull/10117)
- 功能：清除构建文件的脚本 [@cody-littley](https://github.com/cody-littley) in [#10190](https://github.com/hashgraph/hedera-services/pull/10190)
- 修复：在启动时通过[@cody-littley](https://github.com/cody-littley)压缩最后的 PCES文件[#10257](https://github.com/hashgraph/hedera-services/pull/10257)
- feate: sync++- by [@cody-littley](https://github.com/cody-littley) in [#10260](https://github.com/hashgraph/hedera-services/pull/10260)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)\*\* \*\*

### 性能结果

<figure><img src="../../.gitbook/assets/0.46_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.45](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.0)

{% hint style="success" %}
**MAINNET更新：JANUARY 9, 2024**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: 2023年12月28日**
{% endhint %}

### [0.45.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.2)

#### What's Changed

- 修复：在`getAccountInfo`、`getAccountBalance`和`getContractInfo`中默认启用禁用tokenBalances 和 tokenRelationship。 [#10639](https://github.com/hashgraph/hedera-services/pull/10639)

### [0.45.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.0)

- 在 [#9453](https://github.com/hashgraph/hedera-services/pull/9453) 生成失败后的 evm 函数。
- 禁用压缩。 由 [@cody-littley](https://github.com/cody-littley) 在 [#9554](https://github.com/hashgraph/hedera-services/pull/9554)
- 通过 [@mhess-swl](https://github.com/mhess-swl) 修复独特令牌管理速度测试[#9537](https://github.com/hashgraph/hedera-services/pull/9537)
- 在[#9557](https://github.com/hashgraph/hedera-services/pull/9557)
- 启用 [@Ivo-Yankov](https://github.com/Ivo-Yankov) 从 CannotDeleteSystemesSuite 测试[@Ivo-Yankov](https://github.com/hashgraph/hedera-services/pull/9440)
- 修复[@agadzhalov](https://github.com/agadzhalov)在 [#9572](https://github.com/hashgraph/hedera-services/pull/9572)
- 调整依赖范围由 [@jjohannes](https://github.com/jjohannes) 在 [#8455](https://github.com/hashgraph/hedera-services/pull/8455)
- [@hendrikebbers](https://github.com/hendrikebbers) 移除不需要的swirlds-common in [#9003](https://github.com/hashgraph/hedera-services/pull/9003)
- 修复[@iwsimon](https://github.com/iwsimon)CryptoRecordsSanityCheckSuite) [#9551](https://github.com/hashgraph/hedera-services/pull/9551)
- 启用 AssociatePrecompileSuite 由 [@mustafauzunn](https://github.com/mustafauzunn) 在 [#9571](https://github.com/hashgraph/hedera-services/pull/9571) 进行测试

### 性能结果

<figure><img src="../../.gitbook/assets/0.45_Performance Measurement Results_Extract.png" alt=""><figcaption></figcaption></figure>

## [v0.44](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)

{% hint style="success" %}
**MAINNET Update: DecEMBER 19, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：2023年12月12日**
{% endhint %}

### [0.44.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.3)

#### What's Changed

- 在 [e69d0a9](https://github.com/hashgraph/hedera-services/commit/e69d0a917c1c0a9417a3f3125129a74ac3004b7c9)

### [0.44.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.2)

#### What's Changed

- 在 PCES 文件复制过程中捕获未选定的IOException ([#10083](https://github.com/hashgraph/hedera-services/pull/10083)) 由 [@cody-littley](https://github.com/cody-littley) 在 [#10087](https://github.com/hashgraph/hedera-services/pull/10087)

### [0.44.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.1)

#### Bug 修复

- 修复 PCES 复制bug。 ([#10057](https://github.com/hashgraph/hedera-services/pull/10057)) [#10062](https://github.com/hashgraph/hedera-services/pull/10062)

### [0.44.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)

#### 功能

- 重新添加 bootstrap.properties 文件来保持下游进程并增加帐户.maxNumber=20\_000[#8915](https://github.com/hashgraph/hedera-services/pull/8915)
- 8815：刷新[#8981](https://github.com/hashgraph/hedera-services/pull/8981)
- 添加设置以禁用关键法定人数。 [#8961](https://github.com/hashgraph/hedera-services/pull/8961)
- 为所有系统实体编号[#8993](https://github.com/hashgraph/hedera-services/pull/8993)
- 08566 - 在不同网络加载状态时验证 PCES 事件 [#8568](https://github.com/hashgraph/hedera-services/pull/8568)
- 差异测试分析引擎：状态文件转储现在转储特殊文件[#8991](https://github.com/hashgraph/hedera-services/pull/8991)
- 添加了改进的 ASCII art. [#9028](https://github.com/hashgraph/hedera-services/pull/9028)
- 为经典的 HTS 调用[#9053](https://github.com/hashgraph/hedera-services/pull/9053) 定性无效的 id 故障模式
- 将普通添加到状态图表中，并更新 javadocs [#9108](https://github.com/hashgraph/hedera-services/pull/9108)
- 5552: 创建一个Grafana 数据仪表盘来查看所有现有相关数据计量[#8845](https://github.com/hashgraph/hedera-services/pull/8845)
- 更新 Besu 到版本 23.10.0 [#9168](https://github.com/hashgraph/hedera-services/pull/9168)

**➡️ 查看全部更改列表** [**here**](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)\*\* \*\*

### 性能结果

<figure><img src="../../.gitbook/assets/0.44_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.43](https://github.com/hashgraph/hedera-services/releases/tag/v0.43.0)

{% hint style="success" %}
**MAINNET 更新: NOVEMBER 27, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: NOVEMBER 2, 2023**
{% endhint %}

服务 v0.43.0 增加了以下功能：

- HIP-786 ([#8620](https://github.com/hashgraph/hedera-services/pull/8620))

#### 改进

服务 v0.43.0 增加了以下增强功能：

- 更新 Besu 到 23.10.0 - cherry 选择 ([#9199](https://github.com/hashgraph/hedera-services/pull/9199))
- 将Besu EVM 库更新为23.7.2 ([#8472](https://github.com/hashgraph/hedera-services/pull/8472))
- “生产化”合同在最后拆卸([#8563](https://github.com/hashgraph/hedera-services/pull/8563))
- 自动校验([#8404](https://github.com/hashgraph/hedera-services/pull/8404))
- 使用 CLI 服务创建脂肪jar 以便它可以独立运行 [#8519](https://github.com/hashgraph/hedera-services/pull/8519))

### 性能结果

<figure><img src="../../.gitbook/assets/0.43_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.42](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.0)

{% hint style="success" %}
**MAINNET Update: OCTOBER 24, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: SEPTEMBER 26, 2023**
{% endhint %}

### [0.42.6](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.6)

这个版本更新了 `0.42.0` 平台SDK 版本到 `0.42.6`，从设置文件中删除 `reconnect.asyncStreamTimeout` 。 这样做会确保此属性将默认代码指定的值 (300 秒)。

#### 更改

- 升级平台 SDK ([#9224](https://github.com/hashgraph/hedera-services/pull/9224))

### [0.42.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.2)

#### 更改

- 0.42 账户余额测试 ([#8866](https://github.com/hashgraph/hedera-services/pull/8866))
- 重新添加 bootstrap.properties 文件，并增加 `accounts.maxNumber=20_000_000` ([#8928](https://github.com/hashgraph/hedera-services/pull/8928))

### [0.42.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.1)

#### 更改

- Chore: normalize 配置文件 (release/0.42) ([#8668](https://github.com/hashgraph/hedera-services/pull/8668))
- 8751: 没有帐户、NFTs或令牌的数据源指标 ([#8798](https://github.com/hashgraph/hedera-services/pull/8798))

### [0.42.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.0)

- 将 EIP 2930 支持添加到 EthTXData [#7696](https://github.com/hashgraph/hedera-services/pull/7696)
- 提供实体和节点仪表板 ([#7774](https://github.com/hashgraph/hedera-services/pull/7774))
- 07748 Post-consultative signature gathering [#7776](https://github.com/hashgraph/hedera-services/pull/7776))
- 默认启用 EIP-2930 交易 [#7786](https://github.com/hashgraph/hedera-services/pull/7786))
- 7570: 删除 JasperDB ([#7803](https://github.com/hashgraph/hedera-services/pull/7803))
- 删除对旧版同步gosip的支持。 ([#8059](https://github.com/hashgraph/hedera-services/pull/8059))
- 禁用账户余额导出 [#8272](https://github.com/hashgraph/hedera-services/pull/8272))
- 默认情况下修改配置以支持磁盘状态 [#8510](https://github.com/hashgraph/hedera-services/pull/8510))

### 性能结果

<figure><img src="../../.gitbook/assets/0.42_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.41](https://github.com/hashgraph/hedera-services/releases/tag/v0.41.0)

{% hint style="success" %}
**MAINNET更新: SEPTEMBER 20, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: AUGUST 22, 2023**
{% endhint %}

- Ethereum 交易类型支持扩展到第一类交易([#7670](https://github.com/hashgraph/hedera-services/issues/7670))，这类交易遵循EIP 2930 RLP 编码。 这增加了本机的EVM工具的数量，并增加了Hedera智能合同服务支持的场景。
- NFT 迷你定价更改为线性早期，基于枚举的序列数。 此外，收藏中的单个NFT将从0.05美元改为0.02美元。 [#7769](https://github.com/hashgraph/hedera-services/issues/7769)

### 性能结果

<figure><img src="../../.gitbook/assets/0.41_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.40](https://github.com/hashgraph/hedera-services/releases/tag/v0.40.0)

{% hint style="success" %}
**MAINNET更新: AUGUST 15, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 8, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: 7月19, 2023**
{% endhint %}

Hedera Services 0.40版本提供[HIP-729 \~"Contract Accounts Nonce Externalization"](https://hips.hedera.com/hip/hip-729)。 智能合同开发者使用 Hedera 公共镜像节点现在可以跟踪它们会在Etherum等上出现的合同异常。 使用案例可能包括故障排除合同调用或基于`CREATE1`地址验证交易订单的单位测试(一旦默认设置为 0)。 页: 1

由于[@jjohannes](https://github.com/jjohannes)的专家触摸，项目的开放源码贡献者将注意到Gradle建筑的重大改进。

### 性能结果

<figure><img src="../../.gitbook/assets/0.40_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.39](https://github.com/hashgraph/hedera-services/tags)

{% hint style="success" %}
**MAINNET更新：7月11, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 21, 2023**
{% endhint %}

服务 v0.39.0 增加了以下功能：

- VirtualRootNode 构造器创建一个缓存对象，不会被重新使用 [#6321](https://github.com/hashgraph/hedera-services/pull/6321)
- 实现EVM 地址块列表 [#5799](https://github.com/hashgraph/hedera-services/pull/5799)
- 优化虚拟节点缓存刷新策略[#5568](https://github.com/hashgraph/hedera-services/pull/5568)
- HIP-721： 06026 - 添加软件版本到事件 [#6236](https://github.com/hashgraph/hedera-services/pull/6236)
- 实现 CryptoCreate 句柄方法 [#6112](https://github.com/hashgraph/hedera-services/pull/6112)
- UtilPrng 句柄实现 [#6310](https://github.com/hashgraph/hedera-services/pull/6310)
- 添加 PCLI 子命令来签署服务流文件[#6309](https://github.com/hashgraph/hedera-services/pull/6309)
- 实现令牌冻结处理 [#6467](https://github.com/hashgraph/hedera-services/pull/6467)
- 实现 token 解冻手柄() [#6502](https://github.com/hashgraph/hedera-services/pull/6502)
- 合并管理员和网络模块 [#6511](https://github.com/hashgraph/hedera-services/pull/6511)
- 实现模块化预处理流程[#6291](https://github.com/hashgraph/hedera-services/pull/6291)
- 在 VirtualMap [#5825](https://github.com/hashgraph/hedera-services/pull/5825) 中将哈希移出叶节点
- TokenFeeScheduleUpdle() implementation [#6582](https://github.com/hashgraph/hedera-services/pull/6582)
- 基本文件服务实现[#6522](https://github.com/hashgraph/hedera-services/pull/6522)
- 实现代币关联到帐户 [#6609](https://github.com/hashgraph/hedera-services/pull/6609)
- 安装工作流程 [#6476](https://github.com/hashgraph/hedera-services/pull/6476)
- 实现模块化记录缓存 [#6754](https://github.com/hashgraph/hedera-services/pull/6754)
- Cryptodelete 句柄实现 [#6694](https://github.com/hashgraph/hedera-services/pull/6694)

### 性能结果

<figure><img src="../../.gitbook/assets/0.39_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.38](https://github.com/hashgraph/hedera-services/releases/tag/v0.38.0)

{% hint style="success" %}
**MAINNET更新：JUNE 8, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 1, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE: MAY 18, 2023**
{% endhint %}

- 升级EVM到上海[#5964](https://github.com/hashgraph/hedera-services/pull/5964)
- EVM 版本更新和优化[#5962](https://github.com/hashgraph/hedera-services/pull/5962)
- 在 [#6212](https://github.com/hashgraph/hedera-services/pull/6212) 中打开 EVM 上海版本
- 将 hedera-protobufs-java 版本更新到 0.38.10 [#6579](https://github.com/hashgraph/hedera-services/pull/6579)
- 添加 PCLI 命令以签署账户余额文件 [#6264](https://github.com/hashgraph/hedera-services/pull/6264)

### 性能结果

<figure><img src="../../.gitbook/assets/0.38_Performance Measurement Results_Extract.001.jpeg" alt=""><figcaption></figcaption></figure>

## [v0.37](https://github.com/hashgraph/hedera-services/releases/tag/v0.37.0)

{% hint style="success" %}
**MAINNET Update：maY 17, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 24, 2023**
{% endhint %}

### 功能

- 实现主题删除预处理([#5033](https://github.com/hashgraph/hedera-services/pull/5033))
- 启用并添加 Workflow 端口 [#5032](https://github.com/hashgraph/hedera-services/pull/5032))
- 预处理改进([#5056](https://github.com/hashgraph/hedera-services/pull/5056))
- 支持在套装中按类型分类的自动调度操作 ([#5054](https://github.com/hashgraph/hedera-services/pull/5054))
- 添加 SPI 和 App 组件支持已调制的 HCS TransactionDispatcher [#5062](https://github.com/hashgraph/hedera-services/pull/5062))
- 将缺少的功能添加到 FileSignTool ([#5100](https://github.com/hashgraph/hedera-services/pull/5100))
- 共识消息提交 Prechand([#5059](https://github.com/hashgraph/hedera-services/pull/5059))
- 添加 IngestChecker 单个适配器 ([#5098](https://github.com/hashgraph/hedera-services/pull/5098))
- \[HIP-583] Finalize hollow accounts via any required signature in a txn ([#4990](https://github.com/hashgraph/hedera-services/pull/4990))
- 删除CryptoCreate 能力创建空账户[#4998](https://github.com/hashgraph/hedera-services/pull/4998))
- 在 CryptoTransaction 中填写EVM 地址 ([#5010](https://github.com/hashgraph/hedera-services/pull/5010))
- 使所有 EVM E2E 套件能够与以太空调用一起运行 [#4375](https://github.com/hashgraph/hedera-services/pull/4375)

### 性能结果

<figure><img src="../../.gitbook/assets/0_37Performance Measurement Results_Extract.001.jpeg" alt=""><figcaption></figcaption></figure>

## [v0.36](https://github.com/hashgraph/hedera-services/releases/tag/v0.36.0)

{% hint style="success" %}
**MAINNET更新: APRIL 20, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: APRIL 13, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 4, 2023**
{% endhint %}

#### 功能

服务v0.36.0 增加以下功能：

- 添加对空账户完成的属性更改的跟踪([#4647](https://github.com/hashgraph/hedera-services/pull/4647))
- 添加支持重定向令牌调用 fro evm-module ([#4880](https://github.com/hashgraph/hedera-services/pull/4880))
- 更新文件签名工具 ([#4988](https://github.com/hashgraph/hedera-services/pull/4988))
- 添加块编号工具 [#4997](https://github.com/hashgraph/hedera-services/pull/4997))
- 添加 client.workflow.operations 与 workflow([#5053](https://github.com/hashgraph/hedera-services/pull/5053))
- 更新 hedera 服务以使用 FSTS CLI 而不是系统属性
- 6166：将 VirtualMap 数据从 JasperDB 迁移到 MerkleDb 数据源
- 在新的模块化应用程序结构中实施当前网络功能：共识操作、查询工作流程和各种预处理实现

### 安全更新：Hedera Smart Contract Service Security Model 更改

从服务v0.35.2改为v.36.0。

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

### 性能结果

<figure><img src="../../.gitbook/assets/0.36_Performance Measurement Results_Extract.001 (1).png" alt=""><figcaption></figcaption></figure>

## [v0.35](https://github.com/hashgraph/hedera-services/releases)

{% hint style="success" %}
**MAINNET更新：MARCH 31, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：16, 2023**
{% endhint %}

### [0.35.2 Hedera Smart Contract Service Security Model Changes](https://github.com/hashgraph/hedera-services/releases/tag/v0.35.2)

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

#### 功能

- [HIP-583](https://hips.hedera.com/hip/hip-583) 以扩展CryptoCreate & CryptoTransfer Transactions中的别名支持。

这包括：

- CryptoTransfer to non-existing EVM address 别名导致创建空账户。
- 在传入交易中使用付款人签名完成一个空账户

使用在这个版本中工作的HIP-583案例：

1. 作为一个从另一个链上以ECDS为基础的帐户的用户，我可以使用我的 evm 地址别名创建一个新的 Hedera 帐户。
2. 作为开发者，我可以通过 CryptoTransfer 交易，使用一个恶地址别名创建一个新帐户。
3. 作为开发者，我可以使用他们的 evm 地址别名将HBAR 或 Token 传输给Hedera 帐户。
4. 作为Hedera的用户，我可以通过只分享我的 evm 地址别名，在我的帐户中接收HBAR 或令牌。
5. 作为拥有赫德拉本土钱包的赫德拉用户，我可以只使用收件人的 evm-address 别名将HBAR 或 token 转移到另一个帐户。

#### 配置更改

```
自动创建.启用=true
lazyCreation.enabled=true
cryptoCreateWidAliasAndEvmAddress.enabled=falsal
contracts.evm.version=v0.34
```

### 性能结果

<figure><img src="../../.gitbook/assets/0.35_results.001.jpeg" alt=""><figcaption></figcaption></figure>

## [**v0.34**](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.0)

{% hint style="success" %}
**MAINNET更新：FEBRUARY 9, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: JANUARY 24, 2023**
{% endhint %}

### [0.34.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.3)

使用 `v0.34.3` SDK。

### [0.34.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.0)

服务 `v0.34.0` 完成了 [HIP-583]的实现(https://hips.hedera.com/hip/hip-583)。

为了确保这个错综复杂的功能的全面测试覆盖面，它将首先启用 **只在预览网上** 。

这个版本将不会启用智能合同租金。

### 性能结果

<figure><img src="../../.gitbook/assets/0.34.1.001.png" alt=""><figcaption></figcaption></figure>

## [v0.33](https://github.com/hashgraph/hedera-services/releases/tag/v0.3.30)

{% hint style="success" %}
**MAINNET更新：JANUARY 12, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET 更新：12月22日，2022**
{% endhint %}

服务 v0.33.0 增加了以下功能：

- 超文本贝苏EVM已更新到22.10.x版本
- '账户发送' 子命令已添加到 yahcli 以支持发送 HTS 令牌单位
- 开发者文档更新

<figure><img src="../../.gitbook/assets/Performance Measurement Results_033.001.png" alt=""><figcaption></figcaption></figure>

## [v0.31](https://github.com/hashgraph/hedera-services/releases/tag/v0.31.0)

{% hint style="success" %}
**MAINNET更新：DEEMBER 9, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: DecEMBER 1, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: NOVEMBER 11, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: OCTOBER 27, 2022**
{% endhint %}

0.31 服务完成了下列功能：

- [HIP-542 路线图](https://hips.hedera.com/hip/hip-542)，用于使`CryptoTransfer`的付款人担保`自动创建`。 它还允许除了Hbar 传输之外自动创建令牌传输。
- [HIP-564 路线图](https://hips.hedera.com/hip/hip-564) 允许零单位可换代币传输
- [HIP-573 路线图](https://hips.hedera.com/hip/hip-573)用于启用令牌创建者可以免除其令牌的费用收集者的自定义费用。

7. 除了上述特点外，还包括以下内容：

- 在 [HIP-514 romap](https://hips.hedera.com/hip/hip-514)中添加对 ERC20/721 `transferFrom` 方法的支持。
- 启用智能合同追踪。
- 添加与稳定性改善相关的一些更改。

<figure><img src="../../.gitbook/assets/0.31_results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.30](https://github.com/hashgraph/hedera-services/releases/tag/v0.30.0)

{% hint style="success" %}
**MAINNET Update: OCTOBER 21, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: OCTOBER 19, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：OCTOBER 6, 2022**
{% endhint %}

0.30服务完成了[HIP-514路线图](https://hips.hedera.com/hip/hip-514)，以便通过智能合约使Hedera本机令牌可以管理。 有五个新的系统合同：`getTokenExpiryInfo(address)`、`updateTokenExpiryInfo(address)、`isToken(address token)`、`getTokenType(address token)`和`updateTokenInfo(address)、HederaToken)\`。

`updateTokenInfo(地址，HederaToken)` 调用特别强大。 如果代币管理员密钥签名交易，调用合约， 该合同现在可以使自己成为代币的金库，拥有放置或烧毁单位或NFT的权力。

⚠️ Contract 作者 应该知道这次发行会启动赫德拉的合同[到期和租金模型](https://hedera.com/blog/smart-contract-rent-on-hedera-is-coming-what-you-need-know)。 0.30 升级后立即会有两个可见效果：

- 所有未删除的合同将在升级日期后至少延长90天。
- 已删除的合约将从州开始被清除；所以一个 `getContractInfo` 查询之前的一个
  返回 `CONTRACT_DELETED` 现在可以报告 \`INVALID_CONTRACT_ID'。

大约在0.30升级后90天内，一些合同将开始过期。 该网络将尝试向合同到期的自动续订账户自动收取续订费(约`0.026` ，为期90天)。 如果自动续订账户余额为零，网络将尝试收取合同本身的费用。

无力支付更新费的合同将输入一个为期一周的“宽限期”，在这段时间内无法使用。 除非它的到期期通过 `ContractUpdate` 延长或接收hbar。 在这个宽限期之后，合同将从邦上撤销。

我们**强烈** 鼓励所有合同作者为他们的合同设置一个自动续订账户。 这使得合同逻辑与租金的存在脱钩。

这个版本还带来两项附带改进：

1. 可以安排一个 `CryptoApprouveallevance` 交易。
2. 镜像节点操作员将能够使用每日`NodeStakeUpdate` 导出来跟踪[几个关键标记属性](https://github.com/hashgraph/hedera-protobufs/blob/main/services/node\stake\_update.proto#L45)的当前值。 请查看链接的原始评论以了解这些属性的更多详情。

<figure><img src="../../.gitbook/assets/0.30_results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.29](https://github.com/hashgraph/hedera-services/releases/tag/v0.29.0)

{% hint style="success" %}
**MAINNET更新: SEPTEMBER 27, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 7, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 30，2022**
{% endhint %}

### 合同管理令牌 :coin：

在 0.29 服务中，我们遵循了[HIP-514 路线图](https://hips.hedera.com/hip/hip-514)，给合同作者许多新的方式来检查和管理HTS token。

HIP列举了这些方法； 例子包括一个撤销帐户的 KYC 代币的合同，或删除它拥有管理权限的代币， 或者甚至根据一个 NFT 中的元数据更改令牌的供应密钥！

注意四个HIP-514功能将成为0.30版本的一部分，详情如下：`getTokenExpiryInfo(address)`、`updateTokenExpiryInfo(address)、`updateTokenInfo(address)、`isToken(address token)`和`getTokenType(address token)`。

[HIP-435 Recording Stream v6](https://hips.hedera.com/hip/hip-435)将在此版本中启用测试网和主网。

### 废弃的

请注意这[重要的废弃](https://github.com/hashgraph/hedera-protobufs/blob/main/services/crypto\_get\_info.proto#L141)将会改变客户在今年11月发布后如何获取令牌关联和平衡。 届时，镜像节点将成为令牌关联元数据的唯一来源。 这是因为 [HIP-367](https://hips.hedera. om/hip/hip-367使令牌关联无限，因此从长远来看，协商一致的节点服务于这种信息将不会有效。

<figure><img src="../../.gitbook/assets/0.29.2.png" alt=""><figcaption></figcaption></figure>

## [v0.28](https://github.com/hashgraph/hedera-services/releases/tag/v0.28.0)

{% hint style="success" %}
**MAINNET更新: AUGUST 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：29, 2022**
{% endhint %}

0.28服务给Hedera 在[HIP-351 (Pseudorandom Numbers)](https://hips.hedera.com/hip/hip-351)中开发一个新的 dApp 块。 HAPI 有一个新的 [`UtilService`](https://hashgraph.github.io/hedera-protobufs/#proto)。 为 "prng" 交易生成一个带有伪随机48字节种子的记录，或在请求范围内生成一个整数。

智能合同也可以通过在地址`0x169`调用一个新的系统合同来获得伪装，使用接口 [here](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contracts/PrngSystemContract/IPrngSystemContracts.sol#L4) 就像在[这个例子](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/PrngSystemContract/PrngSystemContracts.sol)。 应用可能包括NFT微型合同、彩票等等。

📝 HIP-351 文本尚未反映名称从`RandomGenerate`到`prng`, 或系统合同规格的变化。 它确实详细解释了`prng`是如何从网络生成的交易记录的运行哈希中产生它的缠绕的。

此版本还包括一些错误修复和较小的改进；特别是：

1. 将[`ContractCallLocal` 支持](https://github.com/hashgraph/hedera-services/issues/3632)扩展到ERC-20和ERC-721 函数`allowance`、`getApproved`和\`isApprovedForAll'。
2. 允许对订约账目有利害关系。

![](../../.gitbook/assets/0.28.0\_results.001.jpeg)

## [v0.27](https://github.com/hashgraph/hedera-services/releases)

### v0.27.7

{% hint style="success" %}
**MAINNET更新：AUGUST 9, 2022**
{% endhint %}

将增长到数十亿实体的任何分类账都必须有一个有效的办法来清除过期实体。 在Hedera 网络中，这意味着保留账户拥有的 NFT 列表。 这样，当一个账户到期时，我们可以将其NFT退回到其各自的财务账户。

在 0.27 发布中的某些条件下，维护这些列表的逻辑中的错误可能会导致NFT 转账失败，而无需退款。

我们赞赏赫德拉社区在这个问题上与我们合作。 我们邀请任何受此错误影响的用户通过support@hedera.com联系支持。

### v0.27.0

{% hint style="success" %}
**MAINNET Update: July 21, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: July 1, 2022**
{% endhint %}

Hedera Services的0.27版启动了[HIP-406(Staking)](https://hips.hedera.com/hip/hip-406)的第一阶段。 我们非常感谢社区对这个重要功能的反馈！

随着钱包和交易所推出客户端支持，用户现在可以选择将他们的hbar 置于一个节点上。 随着各节点从个人和组织中积累利害关系，它们将有资格向其利害关系方支付奖励。 此时此刻，一旦`0.0.800`账户余额跨越了由理事会硬币委员会设定的门槛值，奖励将被永久激活。

这将为第二阶段的利害关系做好准备，在第二阶段中，节点对共识的贡献成为其直接作用的一个函数， 而且与之有充分利害关系的社区节点可以开始参与协商一致意见。 请注意这一进程的分散性质，很难准确预测每个里程碑和阶段何时能够实现。 0.27释放的直接可见后果将很简单，

1. 协商一致的节点处理`CryptoCreate`和`CryptoUpdate`的交易——即使不是所有钱包和交易都被更新以进行这些选举。

观察读者可能会记得，早些时候的服务 0.27 [Alpha release](https://github.com/hashgraph/hedera-services/releases/tag/v0.27.0-alpha.5)启用了[HIP-423 (Long Term Scheduled Transactions)](https://hips.hedera.com/hip/hip-423)。 这是一个复杂的特点，具有一些深刻的影响，我们已决定在开始生产之前推迟一次释放。

![](<../../.gitbook/assets/0.27.4\_results copy.001.jpeg>)

## [v0.26](https://github.com/hashgraph/hedera-services/releases)

{% hint style="success" %}
**MAINNET更新：JUNE 9, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：25, 2022**
{% endhint %}

在这个版本中，我们很高兴部署对 [HIP-410 (在Hedera Transaction中的 Etherum Transaction)](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-410.ms)的支持。 和 [HIP-415 (Blocks导言)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-415.md)。

HIP-410 添加了一个 HAPI `EtherumTransaction` ，通过它一个账户是 [auto-created](https://hips.hedera. om/hip/hip-32使用[ECDSA(secp256k1)键](https://hips.hedera.com/hip/hip-222)可以通过签署其ECDSA 键将Etherum交易提交给Hedera。 (适用发送者`nonce`的 tandard 以太空为单位的限制。) 详情请参阅HIP-410, 其中包括`EtherumTransaction` 增强功能的一些非常有说服力的使用案例的概述。 “我想要使用 MetaMask 创建一个交易来将HBAR 转到另一个帐户”。

HIP-415还通过将Hedera“区块”的概念标准化来预测这种使用情况。 这对于充分实现[Ethereum JSON-RPC API](https://eth)很重要。 ki/json-rpc/API。 定义很简单：一个_block_ 是记录流文件中的所有交易。 _block 哈希_ 是文件末尾运行散列的交易的32字节前缀。 而_block数字是记录文件在整个流历史中的索引，第一个文件有索引\`0'。

Hedera Services 0.26 实现 [HIP-376](https://hips.hedera.com/hip/hip-376)，允许智能合同开发者使用熟悉的 [EIP-20](https://eips)。 因此，EIPS/eip-20和 [EIP-721](https://eips.efum.org/EIPS/eip-721) "操作员批准"，带有可互换和不可互换的HTS 令牌。

经批准的经营者可以代表他们管理所有权人的代币；这对于许多与第三方经纪人/钱包/拍卖人的货运使用案例来说是必要的。

通过 `approve()` 或 `setApprovalForAll()` 授予的任何权限都具有等效的 HAPI `cryptoApprouveAll()` 或 `cryptoDeleteAllitance` 表达式，此表达式是作为记录流中的 HAPI `TransactionBody` 外部化的。 也就是说，HIP-376系统合同只能在EVM内揭示本地HAPI操作的子集。

![](<../../.gitbook/assets/image (2).png>)

## [v0.25](https://github.com/hashgraph/hedera-services/releases/tag/v0.25..0)

{% hint style="success" %}
**MAINNET更新：19, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 26，2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: APRIL 21, 202**
{% endhint %}

Hedera 服务 0。 5 发行会给管理大量代币类型的 HTS 用户带来好消息，因为它传送了[HIP-367(无限制的代币关联每个帐户)](https://hips。 edera.com/hip/hip-367。 特别是，单个账户现在可以充当任何数量令牌类型的金库. (请注意`CryptoService`HAPI查询仍然只返回一个帐户最近关联的 1000 个验证码的信息； 镜像节点仍然是完整历史的最佳来源。)

我们也非常高兴地宣布支持[HIP-358 (允许 `TokenCreate` 透过Hedera Token Service Precompiled Contract)](https://hips.hedera.com/hip/hip-358)。 这种HIP超额费用合约集成，使智能合约能够创建一个新的 HTS token--互换性或不可互换性，不论是否有关税。 (感兴趣的团结开发者可以查阅[本合同](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/solidity/FeHelper.sol)中的示例。)

在[更多即将推出的 HTS 预编支持](https://hips.hedera.com/hip/hip-376)的预订器中，此版本也将启用 [HIP-336 (Tokens)的审批和津贴API](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-336.md)。 令牌所有者现在可以批准其他帐户来管理他们的 HTS 令牌或NFT, 直接类比于ERC-20和ERC-721样式令牌中的`核准()`和`transferFrom()`机制。

### 改进

- HIP-336 implementation [#2814](https://github.com/hashgraph/hedera-services/issues/2814)
- HI-358 implementation [#3015](https://github.com/hashgraph/hedera-services/issues/3015)
- HIP-367 implementation [#2917](https://github.com/hashgraph/hedera-services/issues/2917)

### 修复

- ERC `view` 函数现可在 `ContractCallLocalQuery` [#3061](https://github.com/hashgraph/hedera-services/issues/3061) 中使用

![](<../../.gitbook/assets/image (11).png>)

## [v0.24](https://github.com/hashgraph/hedera-services/releases/tag/v0.24.0)

{% hint style="success" %}
**MAINNET更新：APRIL 15，2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 7, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 31, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 24, 2022**
{% endhint %}

在0中。 4 发行Hedera Services 我们很高兴通过[HIP-218 (Smart Contract interactions with Hedera Token Accounts)](https://hips,让智能合同开发者与本机的 Hedera Token Service (HTS) tokens)获得新的互操作性。 edera.com/hip/hip-218。 Hedera EVM 现在暴露了每个HTS 可替换令牌，作为一个 ERC-20 令牌在令牌的 '0' 地址上。 .X\`实体 id；与此相类似，每个不可替代的 HTS 代币都是一个 ERC-721 代币。 这意味着智能合约可以寻找可替换的 HTS 代币的平衡； 或者根据特定HTS NFT的所有者改变其行为。 详情请查看链接的HIP。

这种升级还创建了两个新的系统账户0.0.800和0.0.801，这两个账户将持有奖金。

对Hedera API 的一个更改是，我们现在有足够的证据来完成实验性的 `getAccountNftInfos` 和 `getTokenNftInfos` 查询没有一个有利的成本/效益比， 这些查询现在[永久禁用](https://hashgraph)。 iob.io/hedera-protobufs/#proto.TokenService。

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (4).jpeg>)

## [v0.23](https://github.com/hashgraph/hedera-services/releases/tag/v0.23.0)

{% hint style="success" %}
**MAINNET更新：MARCH 10, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: FEBRUARY 17, 2022**
{% endhint %}

Hedera Services 0.23 flesh通过执行[HIP-329 (Support `CREATE2` opcode)](https://hips.hedera.com/hip/hip-329)提供我们的智能合同服务。 智能合同开发者现在可以免费使用 `CREATE2` EVM opcode。 典型的使用案例是一种分布式交易，它希望其对应合同有基于对应代币的确定地址。

请注意这个版本中修复的两个问题。 [First](https://github.com/hashgraph/hedera-services/issues/2841) 在版本0.22中，节点返回了 [HIP-33](https://hips.hedera.com/hip/hip-33)规定的 `bytes ledger_id` 作为十六进制字符串的 UTF-8 编码。 返回的字节现在是分类账数字的大端代号。 [Second](https://github)。 发布前, om/hashgraph/hedera-services/issues/2857)。 删除令牌的 `dissociatateToken` 记录没有列出被丢弃账户的余额，如果代币库丢失的话。 这个问题现在已经修复。

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (2).jpeg>)

## [v0.22](https://github.com/hashgraph/hedera-services/releases/tag/v0.22.1)

{% hint style="success" %}
**MAINNET更新：FEBRUARY 3, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JANUARY 2022**
{% endhint %}

0.22版是Hedera Services的典型转变，因为我们在智能合同2中完成了下一个重要步骤。 蛋白质 [HIP-25]的强度路线图(https://hips.hedera)。 om/hip/hip-25), 一个技术基础，用于将我们分类账的世界状态扩大到数十亿实体——没有这样做——牺牲了通过校准图协商一致算法启用的高TPS。

这次释放的重点包括：

- EVM网络容量增加到每秒15M的“气体”。 (Please see [HIP-185](https://hips.hedera.com/hip/hip-185) for details.)
- 升至4M的“ContractCreate”或“ContractCall”气体限值。
- 合同储存容量增加到10MB。
- 与 HTS 令牌的团结整合。 (Please see [HIP-206](https://hips.hedera.com/hip/hip-206) for details.)

我们期待着在这些方面在今后的释放方面取得更多进展。 注意HTS集成中的气体使用仍在演变中；按照[此问题](https://github)。 为了跟踪最终确定的气体费用以实现主网释放的情况，请参阅Oom/hashgraph/hedera-services/issues/2786。

这份版本中还有另外两份与智能合同服务无关的HIP。 首先， [HIP-33](https://hips.hedera.com/hip/hip-33) 增强了像`CryptoGetInfo` 这样的查询，并且有一个 _ledger id_ 标记Hedera 网络回答了查询。 第二， [HIP-31](https://hips.hedera.com/hip/hip-31) 允许客户端在`CryptoTransfer`中包含预期的小数标记。 这意味着硬件钱包可以保证其代币交易将具有用户在设备显示时所看到的精度。

虽然我们在智能合同路线图方面正在获得势头，但我们也坚定地致力于改进开发者的体验。 并欢迎我们[GitHub 仓库](https://github) 中的问题和想法。 om/hashgraph/hedera-services) 和 [Discord](https://hedera.com/discord)！

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (1).jpeg>)

## [v0.21.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.21.0-rc.1)

{% hint style="success" %}
**MAINNET更新：JANUARY 13, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新: DecEMBER 21, 2021**
{% endhint %}

在 Hedera Services 0.21 中，我们高兴地宣布支持[ECDSA(secp256k1) keys](https://hips.hedera.com/hip/hip-222) and [auto-account creatation](https://hips.hedera.com/hip/hip-32)。

以太坊网络使用secp256k1曲线大量使用ECDS加密法， 通过支持这些密钥，我们可以方便开发者将dApp 迁移到Hedera的体验。 任何地方都可以在 Hedera API中使用Ed25519键，现在可以替换一个 ECDSA(Secp256k1) 键。

自动帐户创建允许新用户通过`CryptoTransfer`_without_已经在网络上创建了 `0.0.X` ID来接收配额。 新用户只需要提供他们的公钥，当一个赞助者帐户通过一个新的 [`AccountID '将他们的密钥发送到'时。 lias`字段](https://hashgraph.github.io/hedera-protobufs/#proto.AccountID)，网络自动用他们的密钥创建帐户。 自动创建账户的其他转账也可能使用其别名而不是账户ID。

别名也可以用来获取帐户余额和帐户信息。 (注意：有一个[已知问题](https://github.com/hashgraph/hedera-services/issues/2653)会导致`getAccountInfo`的查询响应以回音账户别名而不是其`0。 。.<num>` id；这将在下次发布时被固定下来。 请使用免费的 `getAccountBalance` 查询来检查 \`0.0。<num>id 与别名相对应) 您将能够在未来发布的所有其他交易和查询中使用别名。

同时，我们的团队将继续对智能合同 2.0尽职尽责... 🚀

![](<../../.gitbook/assets/performance Measurements.jpeg>)

## [v0.20.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.20.0.0)

{% hint style="success" %}
**MAINNET更新：十二月 2,2021**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: NOVEMBER 18, 2021**
{% endhint %}

Hedera 服务 0。 0 主要是一个零件发布，因为我们的团队正在前头工作，提供大规模的新规模和性能的智能合同服务； 此外还有智能合同集成使用Hedera Token 服务创建的原生令牌。 这种复活的范围很广，我们认为这将是非常值得等待的。

本版本中的主要可达到目标是改进节点操作员在软件升级中的自动化； 和少量小错误修复，包括[<mark style="color:purple;">#242</mark>](https://github)。 om/hashgraph/hedera-services/issues/2432。

还请注意Hedera API 样本中的以下偏离：

- [<mark style="color:purple;">`ContractUpdateTransactionBody.fileID`</mark> <mark style="color:purple;">field</mark>](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L82)，由于[<mark style="color:purple;">`ContractGetBytecode`</mark> <mark style="color:purple;">quer</mark>y](https://github.com/hashgraph/hedera-protobufs/blob/main/services/smart\_contract\_contract\_service.proto#L63)，它是多余的。
- [<mark style="color:purple;">`ContractCallLocalQuery.maxResultSize`</mark> <mark style="color:purple;">字段</mark>](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_call\_local.proto#L136)，因为这个限制现在只是给定的气体限制的副作用。

![](../../.gitbook/assets/Performance%20Measurement%20Results\_Extract.001%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\).jpeg)

## [v0.19.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.19.4)

{% hint style="success" %}
**MAINNET 更新：NOVEMBER 4,2021**
{% endhint %}

{% hint style="success" %}
**TESTNET Update: OCTOBER 28, 2021**
{% endhint %}

## [v0.19.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.19.1)

{% hint style="success" %}
**TESTNET Update: OCTOBER 21, 2021**
{% endhint %}

In Hedera Services 0. 9. 我们很想宣布Hedera智能合同服务迁移到超音速贝苏EVM，就像 [HIP-26](https://github)中所规定的那样。 om/hashgraph/hedera-reform proposal/blob/master/HIP/hip-26.md. 这使我们能够支持最新的v0.8.9团结合同，并使我们的天然气时间表与“伦敦”硬叉的时间安排相协调。 别斯的迁移也为在赫德拉的智能合同性能发生一步改变奠定了基础。

另外两个针对Hedera Token 服务的HIP在这个版本中运行。 首先， [HIP-23](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23)。 d) 功能集现已启用，任何使用非零的 `maxAutoAssociations` 配置的帐户都可以获得空降（i）。 ，没有明确关联的代币类型的单位或NFTs)。 第二，我们也执行了 [HIP-24](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-24.md)，它为使用 "puseKey" 创建的令牌类型提供了安全措施。 如果使用此密钥签名提交了一个 `TokenPause` ，则令牌上的所有操作都将被暂停，直到其后的 `TokenUnpuse` 。

## [v0.18.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.18.1)

{% hint style="success" %}
**MAINNET更新：OCTOBER 7, 2021**
{% endhint %}

在Hedera Services 0.18.1中，我们有一个新的Hedera Token Service (HTS)NFTs 的可扩展性简介。 现在可能有多达5,000万(5,000M)的国家联络点，每个联络点都有100字节的元数据。 当然，我们的 `CryptoTransfer` 和 `ConsensusSubmitMessage` 两种操作即使在这个比额表下也仍然在10k TPS中得到支持。

在这次发布中，我们还启用了自动重新连接。 当某个网络分区导致某个节点在协商一致的协议中“落后”时，此功能将会起作用。 启用重新连接后，节点可以使用特殊形式的 gossip 到“迎接”并在没有人干预的情况下恢复参与网络。 即使该节点错过了数百万笔交易，世界状态也与上次活跃时大不相同。

我们还高兴地宣布，账户可以自定义，以利用即将到来的 [HIP-23 (Opt-in Token Associations)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md) 特性集。 也就是说，账户所有者现在可以通过[`CryptoCreate`](https://hashgraph.github.io/hedera-protobufs/#proto.CryptoCreateTransactionBody)或[`CryptoUpdate`](https://hashgraph.io/hedera-probufs/#proto.CryptoCreateTransactionBody)对代币关联进行“预付”。 ithub.io/hedera-protobufs/#proto.CryptoUpdateTransactionBody_without_预先知道他们将使用哪种特定的令牌类型。

HIP-23一旦完全启用，发布0。 9. 当他们的帐户通过 `CryptoTransfer` 接收一个新的代币类型的单位或 NFT， 网络将自动创建所需关联--- 不明确`TokenAssociate` 交易所需。 这支持几个有趣的使用案例。请查看链接的HIP-23了解更多详情。

这次释放还有另外三个令人感兴趣的问题。

首先，我们取消了上次公布中提到的HIP-18限制。 `tokenFeeScheduleUpdate`交易已被重新启用，现在可以为不可替代的代币类型收取多笔使用费。

第二，系统文件`0.0.101`和`0.0.102`中的地址簿现在将会进入他们的`ServiceEndpoint`字段'。 (然而，已废弃的`ipAddress`、`portno`和`memo`字段将在下次发布后不再填写)

第三，请注意`TokenService` `getTokenNftInfos` 和 `getAccountNftInfos` 查询现在**过时** 并将在未来版本中删除。 这种查询的最佳答案要求只有镜像节点的历史背景，因此这些和相关查询将移动到镜像REST API。

开发者可能会欣赏另外两种版本的0.18.1项。 首先，我们已迁移到 [Dagger2](https://dagger.dev/) 以获取依赖注入。 第二，[`NetworkService`](https://hashgraph.github.io/hedera-protobufs/#proto.NetworkService)中有一个新的`getExecutionTime`查询，支持开发环境中的颗粒性能测试。

![](<../../.gitbook/assets/image (3).png>)

## v0.18.0

{% hint style="success" %}
**TESTNET更新: SEPTEMBER 23, 2021**
{% endhint %}

在 Hedera Services 0.18.0中，我们高兴地宣布支持[HIP-23 (Opt-in Token Associations)](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-23.md)。 此功能允许Hedera 帐户所有者通过[`CryptoCreate`](https://hashgraph.github.io/hedera-protobufs/#proto)为代币关联“预付”。 ryptoCreateTransactionBody或[`CryptoUpdate`](https://hashgraph.github.io/hedera-protobufs/#proto.CryptoUpdateTransactionBody) 交易, _without_预先知道他们将使用哪些特定类型的令牌。

然后当他们的帐户通过 `CryptoTransfer` 接收一个新的代币类型的单位或 NFT， 网络自动创建所需关联--- 不明确`TokenAssociate` 交易所需。 这支持几个有趣的使用案例。请查看链接的HIP-23了解更多详情。

这次释放还有另外三个令人感兴趣的问题。

首先，我们取消了上次公布中提到的HIP-18限制。 `tokenFeeScheduleUpdate`交易已被重新启用，现在可以为不可替代的代币类型收取多笔使用费。

第二，系统文件`0.0.101`和`0.0.102`中的地址簿现在将会进入他们的`ServiceEndpoint`字段'。 (然而，已废弃的`ipAddress`、`portno`和`memo`字段将在下次发布后不再填写。)

第三，请注意`TokenService` `getTokenNftInfos` 和 `getAccountNftInfos` 查询现在**过时** 并将在未来版本中删除。 这种查询的最佳答案要求只有镜像节点的历史背景，因此这些和相关查询将移动到镜像REST API。

开发者可能会欣赏另外两种版本的0.18.0。 首先，我们已迁移到 [Dagger2](https://dagger.dev/) 以获取依赖注入。 第二，[`NetworkService`](https://hashgraph.github.io/hedera-protobufs/#proto.NetworkService)中有一个新的`getExecutionTime`查询，支持开发环境中的颗粒性能测试。

**性能测量结果：**

![](<../../.gitbook/assets/Performance Measurement Results.jpeg>)

## [v0.17.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.3)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 2, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 30，2021**
{% endhint %}

在 Hedera Services 0.17.2中，我们很高兴宣布支持[HIP-17 (不可互换令牌)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md)，并补充到 [HIP-18 (Custom Hedera Token Service Fees)](https://github)。 om/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-18.md, 允许NFT 创建者在交换其创作的可互换价值时设置使用费。

独特的令牌类型和心智的 NFT 对许多情况来说比可替代的令牌类型更加自然。 Hedera Token 服务现在支持这两项服务。 这样一个单一的 `CryptoTransfer` 可以任意进行原子交换，将可互换、不可互换和二次传输结合起来。 (请注意，在发布0.18.0之前，"paged" `getAccountNftInfos` 和 `getTokenNftInfos` 查询将被禁用。)

在这个版本中，我们已经能够在它所附着的令牌的单位中命名固定费用 (假定此令牌的类型是\`FUNGIBLE_COMMON')。 自定义分块费用现在也可以设置为“净转移”。 在这种情况下，汇款清单中的收款人将收到申报的金额，并向汇款人收取分摊费用。

最后有几个问题需要更加专门化。 首先，预定交易设施的用户现在也可以计划 `TokenBurn` 和 `TokenMint` 交易。 第二，网络管理员发布一个 `CryptoUpdate` 以更改金库账户的密钥现在必须用新的金库密钥签字。 第三，支持的TLS密码套装已更新到以下列表：

1. `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384` (TLS v1.2)
2. `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384` (TLS v1.2)
3. `TLS_AES_256_GCM_SHA384` (TLS v1.3)

⚠️ 在这次发布中有两种临时限制。 首先，`tokenFeeScheduleUpdate`交易目前不可用。 第二，对不可替代的代币类型只收取一种特许权使用费。 第0.18.0号版本中将删除这两项限制。

#### 性能测量结果：

![](../../.gitbook/assets/0.17.png)

## [v0.17.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.3-rc.1)

{% hint style="success" %}
**TESTNET更新：AUGUST 24, 2021**
{% endhint %}

请见0.17.4版本说明。

## [v0.17.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.2)

{% hint style="success" %}
**TESTNET更新: AUGUST 19, 2021**
{% endhint %}

请见0.17.4版本说明。

## [v0.16.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.16.1)

{% hint style="success" %}
**MAINNET更新：AUGUST 5, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：22, 2021**
{% endhint %}

在 Hedera Services 0.16.0中，我们很高兴宣布支持[HIP-18 (Custom Hedera Token Service Fees)](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-18.md)。

Hedera 令牌现在可以创建一个多达10个自定义费用的计划。 它或是在每个单位或另一个令牌中的_固定； 或 _fractional_ 并以拥有令牌的单位计算。 分类账自动向账户收取自定义费用，因为它们发送了一个可互换令牌的单位(或是NFT的所有权)。 请通过一个 `CryptoTransfer` 查看。

当无法收取自定义费用时，`CryptoTransfer`将会失灵。除Hedera网络费用外，没有任何余额变化。

[本文档](https://github.com/hashgraph/hedera-services/blob/master/docs/fees/custom-fees-charitization.md)中的五个案例研究显示了自定义费用是如何收取的以及它们是如何出现在记录中的。 请注意，最多允许两个“层次”自定义的HTTS收费，而自定义收费不能要求更改超过20个账户余额。

⚠️ 自定义费用有一个变化, 需要在这个版本中找到一个工作围绕. 具体而言，如果应该在它所属时间表的"父"令牌的单位中收取固定费用，然后在单元0.16中收取。 必须使用[这个问题](https://github.com/hashgraph/hedera-services/issues/1925)中描述的`FractionalFee` 来完成这个操作。 在 0.17.0 版本中，更自然的 `FixedFee` 配置将可用。

在这个版本中，我们还启用了预览网支持[HIP-17(不可互换令牌)](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-17.md)。 独特的令牌类型和心智的 NFT 对许多情况来说比可替代的令牌类型更加自然。 Hedera Token Service 很快将在当地支持这两项服务。 这样一个单一的 `CryptoTransfer` 可以任意进行原子交换，将可互换、不可互换和二次传输结合起来。

我们非常感谢赫德拉用户社区提供这些有趣和强大的新功能集。

#### 性能测量结果：

![](../../.gitbook/assets/0.16.1.png)

## [v0.15.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.15.1)

{% hint style="success" %}
**MAINNET Upeted: July 1, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 17, 2021**
{% endhint %}

在Hedera Services 0.15.1中，我们提高了性能并与最新的平台SDK集成，以便能够充分支持网络重新连接。

这些业绩改进使我们能够通过在协商一致时间的3分钟内处理所有交易的记录来扩大赫代拉世界的状态。 即使每秒处理10 000笔交易。 HAPI `GetAccountRecords`查询现在从状态返回所有此种记录，而所查询的帐户是付款人帐户。

我们还最后确定了不可互换令牌（NFT）支持的设计，将添加到0.16.0版本的 Hedera Token Service (HTS)。 新的 HAPI 操作的原始数据可在 [ hedera-protobufs](https://github.com/hashgraph/hedera-protobufs) GitHub 仓库的0.15.0标签中获得。简化费用计算， 对于任何生命周期不是 \_业已\_ 受最大自动更新期约束的实体，现在有一个世纪的最大实体寿命。 HAPI 交易试图设定一个从当前共识时间开始超过一个世纪的过期，它将解决为 \`INVALID_EXPIRATION_TIME'。

## [v0.14.0](https://github.com/hashgraph/hedera-services/releases/tag/0.14.0)

{% hint style="success" %}
**MAINNET更新：JUNE 3, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：2021年 5 月 20 日**
{% endhint %}

在 Hedera Services 0.14.0 中，我们已经按照 [HIP-16]的规格(https://github.com/hashgraph/hedera-reform-proposal)实现了账户自动更新。 在确保普遍认识到这一功能对用户社区的影响之后，该功能才能启用。

这个版本包括值得注意的基础设施工作，以便能够使用平台的重新连接功能。 重新连接可以让一个已经在协商一致的gossip 中落后的节点能够动态地进行备份。

Hedera API 的一个小的改进是，GetVersionInfo 查询现在包含可选的预发布版本，并从语义版本化速度(如果适用的话)生成元数据字段。

为了简化正在更新系统账户密钥的系统管理员的生命，我们现在放弃了账户新密钥的签名要求。

## [v0.13.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.13.2)

{% hint style="success" %}
**MAINNET更新：至少6, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: APRIL 29, 2021 \[v0.13.2]**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: APRIL 22, 2021 \[v0.13.0]**
{% endhint %}

在 Hedera Services v0.13.0 中，我们有 [redesigned](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md) schedule 交易。 新设计给协作节点一个定义明确的工作流程，如果它们正好安排相同的交易时间。 甚至如果他们正在使用不同的 gRPC 客户端库(例如，Go 和 JavaScript)。 新设计还减少了在许多常用情况下提交有效的`ScheduleSign`交易所需的签名数量。 用户可以在此发布中安排`CryptoTransfer` 和 `ConsensusSubmitMessage` 交易。 其他交易类型将在未来版本中引入。

{% hint style="warning" %}
**注意：** 此版本将不启用schedule交易功能； 预计它会在随后的VNA 上启用测试网。 3.2 4月29日的最新情况。 此功能已在预览网上启用。
{% endhint %}

此版本在 [protobuf](https://hashgraph.github.io/hedera-protobufs/#proto.NodeAddress) 中废弃了系统文件 '0.0.101' 和 '0.0.102' 中的三个字段。 这三个废弃的字段是`ipAddress`、`portno`和`memo`。 当我们依靠这些字段时，我们不能用多个IP地址简洁地表示节点。 例如，采用主节点0(帐户`0.0.3`)，到编写本文件时该节点有代理IP地址`13.82.40.153`, `34.239.82.6`和`35.237.200.180`。 Mainnet `0.0.101`文件必须包含每个代理的`NodeAddress`条目，这意味着复制像`nodeCertHash`这样的字段。

新的原始图避免这种重复，让我们在原生素中代表节点0：

```
{
    "nodeId" : 0,
    "certHash" : "337390d8fea144afc12e81254a28dac6ea82893836ac072effd85e0a7748580ef28096648c5a7f8dbb4ce81476815137",
    "nodeAccount" : "0.0.3",
    "serviceEndpoints" : [ {
      "ipAddressV4" : "13.82.40.153",
      "port" : 50211
    }, {
      "ipAddressV4" : "34.239.82.6",
      "port" : 50211
    }, {
      "ipAddressV4" : "35.237.200.180",
      "port" : 50211
    } ]
}
```

但是，服务部门将继续在重复输入的栏目中填写6个月，以便给所有档案`0'的消费者。 101`和`0.0.102`的时间来准备专用新格式。 六个月后，我们将消除重复现象，`ipAddress`、`portno`和`memo`等字段将留空。 (字段永远不会被删除，以确保仍然可以解析这些系统文件的早期版本。)

在一个小点中，服务现在拒绝任何UTF-8编码包含零字节字符的原始`string`字段。也就是说，Unicode 代码点0，\`NUL'。 数据库(例如，PostgreSQL) 通常将此字符保留为内部格式的分隔符， 让它发生在实体字段会使镜像节点操作员更难生活。

为了简化网络管理员的任务，我们还简化了系统账户更新的签名要求。 并引入基于 Docker 的工具，称为"yahcli"，用于系统文件更新等管理操作。

## [v0.12.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.12.0-rc.2)

{% hint style="success" %}
**MAINNET更新：MARCH 12, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: FEBRUARY 26, 2021**
{% endhint %}

在 Hedera Services v0.12.0, 我们完成了Hedera scheduled Transaction Service (HSTS) 的MVP 安装，详情见 [this](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) 设计文件。 这个服务将会从 _when_ 执行账号，为用户提供新的灵活性和编程性。 请注意，HSTS操作已在预览网上启用，但此时Testnet和Mainnet上仍然禁用。

我们已经让Hedera Token Service (HTS) 的用户更多地控制他们的令牌关联的生命周期。 在 v0.11.0中，已删除的代币立即与所有帐户脱钩。 这种自动分离不再发生。 如果帐户 `X` 与 tok`Y`关联，那么即使标记`Y` 被标记为要删除， 一个 `X` 的 `getAccountInfo` 查询将继续显示与 `Y` \_until\_it 的关联通过一个 `tokenDissociateFromAccount` 交易被明确删除。 请注意，为方便起见，返回代币余额的查询现在也返回相关代币的 `十进制' 值。 这允许用户将`balanc=10050`解释为`100.50`给定的`十进制=2\` 。

在Hedera API 的最后更改中，我们已经将合同和主题实体上的`memo`字段扩展到账户。 file, token和预定的交易实体。 (请注意，这个“回忆录”不同于短寿命的“回忆录”，而短寿命的“回忆录”可能包含在`交易记录'中。 所有这些对 HAPI 的更改现在都更容易通过 GitHub 页面 [here](https://hashgraph.github) 浏览。 o/hedera-protobufs/); 新的 [`hashgraph/hedera-protobufs\` 仓库](https://github.com/hashgraph/hedera-protobufs) 现在是定义HAPI 的 protobuf 文件的权威来源。

除了HAPI的这些增强之外，还有其他的增强措施。 镜像节点操作员消耗的"串流"现在包括一个 Alpha 版本的原始文件，其中包含与 `_Balances 相同的信息。 sv`文件。 此文件的类型是 [`AllAccountBalance`](https://hashgraph.github.io/hedera-protobufs/#proto.AllAccountBalances)。

## [v0.11.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.11.0)

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 4, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JANUARY 26，2021**
{% endhint %}

在 Hedera Services v0.11.0，我们将记录流格式从v2升级到v5，事件流格式从v3升级到v5。 这些更改详见"Record and Event Stream File Formats" [article](https://docs.hedera.com/guides/docs/record-event-stream-file-forms)。

我们还更新了启动代码，使开发和生产前网络中的系统账户数量与主机上的系统账户数量相匹配， [creating](https://github)。 如果不存在，启动时的帐号为`900-1000` 。

## [v0.10.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.10.0.0)

{% hint style="success" %}
**MAINNET更新：JANUARY 7, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 17、20**
{% endhint %}

Hedera Services v0.10。 ，我们提高了Hedera Token Service (HTS)的可用性，并在`TokenMint`和`TokenBurn`交易的收据中使用`newTotalSupply`字段。 没有这个字段 客户端必须跟随代币供应变化的整个记录流才能在收到时确定其供应量。 (请注意，HTS操作现在已经在预览网和测试网上启用，但目前在Mainnet上仍然禁用。 请访问 [SDK 文档](https://docs.hedera.com/452335315445331/token-service)

对于HTS，我们还添加了一个 `fees.tokenTransferUsageMultiplier` 的属性，它能够对分配给一个 `CryptoTransfer` 的资源使用量进行调整，从而改变令牌余额。 预计此缩放因子将被设定，以便更改两个代币余额的`CryptoTransfer`的成本大约是`CryptoTransfer`的成本的10倍，它只能改变两个小时的余额。

除了HTS以外，此版本取消了对付款人账户可以用于目标系统账户的 `CryptoUpdate` 交易的限制。 (也就是说，数字不大于 `hedera.numReservedSystemties` 的帐户。) 在较早的版本中，只接受三个付款人： 目标账户本身、系统管理账户或金库账户。 其他付款者则取得了“AUTHORIZATION_FAILED”的地位。 这个全部限制已被删除，还有一个例外——财政部必须支付针对国库的 `CryptoUpdate` 。

除了这些功能变化外，我们还修复了对加密平衡CSV文件命名的无意变更， 并提高客户端在 _test-clients/_ 下测试重新连接场景的效用。

## [v0.9.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0-rc.1)

{% hint style="success" %}
**MAINNET更新：DecEMBER 3, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 19, 2020**
{% endhint %}

在 Hedera Services v0.9.0, 我们完成了Hedera Token Service (HTS) Alpha 的实现。 请注意，所有HTS操作都已在预览网上启用，但是Testnet和Mainnet上仍然禁用。 HTS 语法请访问 [SDK 文档](https://docs.hedera.com//452335315445331/token-service)。

我们对HAPI原理作了几处修改。 首先，我们删除了已废弃的`签名列表`消息类型。 第二，我们在 `Transaction` 信息中添加了一个顶级的 `signedTransactionBytes` 字段，以确保不同客户端库之间的确定性交易哈希； 顶层的 `bodyBytes` 和 `sigMap` 字段现已废弃，已经被删除了 `body` 字段。 第三，我们不赞成与非付款人记录相关的所有字段，包括帐户发送和接收阈值。 这是由于在v0.8.1中有效删除了无付款人记录。

出于同样的原因，`CryptoGetRecords`和`ContractGetRecords`的语义也有所改变。 唯一可查询的记录现在是授予交易的有效支付者的记录，该交易是在网络属性“ledger.keepRecordsInState=true”时处理的。 这些记录的过期时间为180秒。 必须指出，由于合同帐户永远不能成为交易的有效支付者。 任意`ContractGetRecords`查询总是返回一个空记录列表，并且我们已经不推荐查询。

## [v0.8.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.8.1-rc1)

{% hint style="success" %}
**MAINNET Update Competed: OCTOBER 22, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：OCTOBER 7, 2020**
{% endhint %}

主网版本包含0.8.0版本更新。

## [v0.8.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.8.0-rc1)

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 17, 2020**
{% endhint %}

在Hedera Services v0.8.0中，我们做了几处小修补和改进。 这个标签还包括新建Hedera Token Service (HTS) 的几个操作的预发布实现。

**注意：** HTS 操作将在一段时间内在非开发环境中被禁用。 这些操作正在积极开发中。请访问 `master` 以了解最新的语义。

### 改进

- 已废弃的 HAPI 原始记录相关字段[#506](https://github.com/hashgraph/hedera-services/issues/506)
- 更新收据探测来将每个状态与NodeID配对-只有在最新的(重复)交易过期时才会删除收据。 `getTxRecord` API 将继续返回所有记录与交易 ID。
- `tokenCreate`, `tokenUpdate`, `tokenDelete`, `tokenTransfer`, `tokenFreeze`, `tokenUnfreeze`, `tokenGrantKyc`, `tokenRevokeYc`, `tokenWipe`, `tokenWipe`和`getTokenInfo` HAPI 操作的第一稿`tokenCreate`, `tokenUpe`, `tokenTransfer`, `tokenFreeze`, `tokenUnfreeze`, `tokenGrantKyc`, `tokenEvokeYc`, `tokenWipe`, `tokenWipe`, \`get [#505](https://github.com/hashgraph/hedera-services/pull/505)和[#522](https://github.com/hashgraph/hedera-services/pull/522)

## [v0.7.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.7.0-alpha1)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 8, 202020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 20、2020**
{% endhint %}

在 Hedera Services v0.7.0, 我们已经移动到 Swirlds SDK release `0.7.3` , 它使零利益节点能够成为网络的一部分而不影响共识。 Hedera Services v0.7.0 迁移到此版本的 Swirlds SDK 提供的新接口和方法。 运行哈希值的HCS主题现在被计算为包括付款人帐户id。 释放包括其他小修补和改进。

**增强**

- 迁移到 Swirlds SDK release `0.7.3` ，带有适当设置和日志配置[#347](https://github.com/hashgraph/hedera-services/issues/347)，[#427](https://github.com/hashgraph/hedera-services/issues/427)
- 更新运行散列的 HCS 主题以包含付款人帐户 [#88](https://github.com/hashgraph/hedera-services/issues/88)
- 添加零利害关系节点功能[#274](https://github.com/hashgraph/hedera-services/issues/274)
- 添加新的数据以计算已处理的 HCS 提交消息交易的平均大小和计算未创建的平台交易数 [#316](https://github)。 om/hashgraph/hedera-services/issues/316), [#334](https://github.com/hashgraph/hedera-services/issues/334)
- 更改 gRPC CipherSuite为 CNSA兼容[#215](https://github.com/hashgraph/hedera-services/issues/215)
- 让记录日志周期动态，默认为2秒[#315](https://github.com/hashgraph/hedera-services/issues/315)
- 处理交易[#348](https://github.com/hashgraph/hedera-services/issues/348)
- 启动开源的增强功能[#378](https://github.com/hashgraph/hedera-services/issues/378)，[#379](https://github.com/hashgraph/hedera-services/issues/379)

**文档更改**

- 澄清对应码`UNKNOWN`和`PLATFORM_TRANSACTION_NOT_CREATED`[#314](https://github.com/hashgraph/hedera-services/issues/314) [#394](https://github.com/hashgraph/hedera-services/issues/394)

**Bug 修复**

- 防止`CryptoCreate` 和 `CryptoUpdate` 交易给账户一个空密钥 [#58](https://github.com/hashgraph/hedera-services/issues/58), [#60](https://github.com/hashgraph/hedera-services/issues/60)
- 修复不正确提交的智能合同交易计数 [#371](https://github.com/hashgraph/hedera-services/issues/371)
- 在启动服务前验证总分类账余额 [#258](https://github.com/hashgraph/hedera-services/issues/258)
- 添加一个新的滚动文件来记录所有受控最大速率的查询 [#59](https://github.com/hashgraph/hedera-services/issues/59)
- 其它小bug[#373](https://github.com/hashgraph/hedera-services/issues/373)

## [v0.6.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.6.0)

{% hint style="success" %}
**MAINNET 升级：AUGUST 6, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：16，2020**
{% endhint %}

在 Hedera Services v0.6.0中，我们支持[HCS Topic Fragmentation](https://github.com/hashgraph/hedera-services/issues/53)来加强Hedera Consensus。 我们在“共识提交MessageTransactionBody”中添加了当前区块信息的可选字段。 对于每个区块，作为`initialTransactionID`一部分的付款人帐户必须匹配此交易的付款人帐户。 整个`initialTransactionID` 应该匹配第一个块的 `transactionID` 但除区块号为1外，Hedera不检查或强制执行。

**增强**

- 添加对 HCS 主题片断的支持

**文档更改**

- 使用 HAPI doc 更新的原始数据v0.6.0 支持HCS 主题片段

## [**v0.5.8**](https://github.com/hashgraph/hedera-services/releases/tag/oa-release-r5-rc8)

{% hint style="success" %}
**MAINNET 升级：JUNE 18, 2020**

v0.5.8 包含了在 [v0.5.0](services.md#v-0-5-0)
{% endhint %} 中发现的所有更新

{% hint style="success" %}
**TESTNET 升级：JUNE 8, 2020**
{% endhint %}

0.5.8版本包括一个补丁，用于处理同行网络在哈希图共识平台上的复原力。

## **v0.5.0**

{% hint style="success" %}
**TESTNET 升级：至少5, 2020**
{% endhint %}

在 Hedera 服务 v0.5.0中，我们已经添加了TLS 用于与Hedera 网络上的节点进行信任的通信。 为了更好的安全性，只允许使用 TLS \_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384 和 TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 密码套装。

我们已经在 Hedera NodeAddressBook中添加了新的元数据，可以在系统文件0.0.101。 节点软件和gRPC Hedera API 的版本现在可以通过 GetVersionInfo 查询，用于节点和网络范围的操作。

对于Hedera Consensus Service, 我们已经更新了运行散列计算的主题，以使用已提交消息的SHA-384哈希，而不是消息本身。 这减少了验证主题散列所需的存储要求。 使用新的散列方案的 ConsensusSubmitMessage 交易记录将在收到时有一个新的主题RunningHashversion 字段。 字段的值将是2。

Hedera 文件服务也有几处笔记。 首先，我们启用了不可变的文件。 第二，我们放宽了 FileDelete 交易的签名要求，以符合撤销服务的语义。 第三，我们确定了一个费用计算错误，它过高地收取了某些文件更新交易。

对于Hedera Smart Contracting Service 我们通过将创建的 id 放入交易记录来提高交易的可见性，从而使用新的关键字创建子合同； 并且我们现在传播父合同元数据给创建的孩子。

最后，如果您在系统文件0.0中使用节点属性。 21为了估计网络性能限制，您也会对这些属性的新的标准化格式感兴趣。 下面的列表包含这些和其他次要更新、错误修正和文档更改。

**增强**

- 为 TLS 添加支持
- 展开地址簿元数据
- 返回所有创建的合同ID
- 传播创建者合约元数据
- 介绍 GetVersionInfo 查询
- 标准化温度配置
- 启动时强制执行文件。编码=utf-8
- 使持续时间属性包含可读性

**Bug 修复**

- 在运行散列时使用消息 SHA-384 哈希值
- 启用不可变文件
- 放松文件删除签名要求
- 修复文件更新中的 sbh 计算
- 返回删除文件的元数据
- 在合同执行期间强制执行接收人签名要求
- 拒绝无效的 CryptoGetInfo
- 拒绝使用空密钥创建加密
- 返回Not\_SUPPORTED 以获取状态验证查询
- 0.0.57次更新 0.0.111 的等候费用
- 等待签名要求更新0.0.55 0.0.121/0.0122
- 等待所有手续费0.0.2
- 不要对系统帐号进行温度刻度

**文档更改**

- 酌情用“livehash”取代“claim”
- 标准化并澄清HAPI doc

## v0.4.1

- 软件更新包括Hedera能够在网络交易类型上动态设置节点。
- 以下线程将更新到：每秒1000次提交消息，每秒5个话题创建。
- 重新分配安理会新成员节点

## v0.4.0

- 向赫德拉共识服务局说你好！ 这种发布是第一次包括了特殊安全标准，允许可核查的时间戳和申请信息的排序。
- 网络定价已经更新，以包括HCS交易和查询
- 用于提交消息的HCS网络节点已设置为1000个，其他HCS操作的网络节点则设置为100个。
- 改进结束测试。
- 常规代码清理和重置。
- ContractCall - Contractionrecipt response to ContractCall不再包括称为合同ID的合同 ID
- CryptoUpdate - 交易收据响应CryptoUpdate 不再包含已更新的帐户 ID
- CryptoTransfer — — CryptoTransfer transactions resulting in inSUFFICIENT\_ACCOUNT\_BALANCE 错误不再在交易记录转移列表中列出未被应用的传输

### 其他事项

### SDK

- Java SDK 已更新，以支持Hedera共识服务
- JavaScript/Typescript SDK 已经达到版本 1.0.0.0，支持所有四个主网服务
- JavaScript/Typescript SDK 支持在浏览器(使用特使代理)和节点中运行。
- 去SDK现在支持所有四个主网服务。

**手续费**

- 交易记录中的转账列表现在只显示每个账户中的一个或多个净金额，反映了转账和已支付的任何费用。
- 修复了收费计划中的错误，导致ContractCallLocal, ContractGetBytecode和getVersion 查询被低收费的 \~33%
- You may get more information regarding transaction record fees [here](https://docs.hedera.com/guides/mainnet/fees/transaction-records).

### SDK 扩展组件

- Hedera SDK 扩展组件 (SXC) 是一套开放源码的预构件，其目的是在HCS以上提供额外功能，使开发应用程序更容易和更快。 特别是如果它们需要参加者之间进行安全的通信。
- 组件使用Hedera Java SDK与Hedera共识服务进行沟通。
- 了解更多关于Hedera SXC [here](https://github.com/hashgraph/hedera-hcs-sxc)。
