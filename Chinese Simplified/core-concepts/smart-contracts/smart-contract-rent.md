# Smart Contract Rent

{% hint style="danger" %}
🚨 **HEDERA Council HAS未在SMART CONTRACTS YET上启用。 2. 偿还高级顾问使用的资源的费用 如同在本次会议上一样，希望能够在未来放入该区域的位置。 更多信息即将到来... 🚨**
{% endhint %}

智能合同租金是一种经常性的支付机制，旨在保持资源分配，合同仍然在网络中运作所需。 就合同而言，租金由两个主要部分组成：

**:right_箭头:** [**自动续订**](智能合同-租用.md#合同-自动续订)

**:right_箭头:** [**Storage Payments**](smart-contract-rent.md#storage-pay)

***

## 合同自动续期

自动续订是一个功能，它将未删除的智能合约的有效期至少延长90天。 鼓励合同作者为此目的建立一个自动续订帐户。&#x20

网络将尝试将**续订付款**自动向合同到期的自动续订账户收费。 如果自动续订账户余额为零，网络将尝试收取合约费用。&#x20

如果账户资金不足，合同期为一周。 在这段时间里，除非增拨资金，否则合同将无法履行，合同的到期期将延长（通过“合同更新”），或者收到六溴代二苯。 如果不续签，合同将被从该州清除。

***

## 存储付款

一旦**1亿对钥匙**累积储存在整个网络中，Hedera上的合同存储付款将会启动。 Hedera Coin Economics Committee 预计将确定每年每对钥匙价值为0.02美元的费率\*\*。 这适用于赫德拉的所有合同，不管租金支付之前或之后是订立的。

在Hedera启用存储付款后，每个合同都有**100个免费的密钥价值对**。 然后，一旦合同超过前100个免费的密钥对，它必须支付存储费。&#x20

> 如果合同自动续签，仓储费将是收取的租金支付的一部分。 有效的更新窗口介于 \~30 和 \~92 天之间(参见_ [_HIP-372_](https://hips.hedera.com/hip/hip-372))._

如果达到足够高的利用阈值，\*\*拥堵定价。 \* 在这种情况下，所收取的价格将与网络剩余的系统容量成反比（剩余的容量较低意味着价格较高）。 这适用于所有交易。

***

## 智能合同租金-常见问题 (FAQ)

<details>

<summary>Why do contracts have to pay rent on Hedera?</summary>

像赫德拉这样分布式网络拥有有限的计算资源。 当像智能合约这样的实体部署在分散的网络上时，这些资源的一部分将被消耗。 因此，利用有限资源维持无限数量的实体是不可行的。 解决这个问题是必要的，它是Leemon和 [others]讨论的一个关键话题(https://www.coindesk.com/markets/2018/03/27/vitalik-wants-youto-pay-to-lowetherums-growth/)。

合同租金是管理聪明合同实体和国家储存的一种经济和技术上可行的办法。

</details>

<details>

<summary>Do all entities on Hedera have to pay rent or just contracts?</summary>

所有其他网络实体(例如令牌、帐户、主题和文件)也将支付租金。 然而，租金的时限尚未确定。 在向其他实体提供租金之前，将向社区提供足够的时间和通知。

</details>

<details>

<summary>合同租金中包含哪些费用？</summary>

租金的定义是合同（以及最终所有其他黑德拉实体）继续活跃在网络上所需的经常性付款。 对于合同，租金由**自动更新** 和 **存储** 付款组成：

- **自动续订付款** 合同自动续订费为每90天026美元。
- **存储付款** 一旦总共有**1亿对钥匙价值** 累计储存在整个网络中就会开始。 这些储存费将是合同自动续签时收取的租金付款的一部分。 储存费为每年每对钥匙价值为0.02美元。

<img src="../../.gitbook/assets/smart-contracts-rent-storage-payments.png" alt="" data-size="original">

</details>

<details>

<summary>更新过程中的步骤是什么？ 如果合同不支付租金会发生什么情况？</summary>

Hedera 上的每个实体都有字段“到期时间”、“自动续订时间”和“自动续订账户”。

1. 当到达合同的`到期时间`时，网络将首先尝试向合同的`自动续订账户`收取租金'
   - 如果续订成功，合同仍然活跃在网络
   - 如果续订失败，合同将标记为 `过期`
2. 已过期的实体在从网络中移除之前获得一段宽限期。 在宽限期内，该实体(合同)处于非活动状态，所有涉及该实体的交易都将失败，除非更新交易以延长“到期时间”
   - 在宽限期内的合同可以通过发送一些HBAR 或通过合同更新交易手动延长它的“到期时间”立即“重新激活”
3. 在宽限期结束时，在下列情况下，合同将永久从分类账中删除：
   - 合同及其“自动续订账户”在宽限期结束时仍有零HBAR余额，
   - 在宽限期内不会手动延长合同

请注意，已移除的实体的 ID 号不会再被使用。 此外，如果一个实体被标记为 `deleted` ，那么它就不能延长它的 `expendationTime` 。 更新交易或自动更新都不能够延伸。

欲了解更多详情，请参阅下面的图和 [HIP-16](https://hips.hedera.com/hip/hip-16)。

<img src="../../.gitbook/assets/Untitled.png" alt="" data-size="original">

</details>

<details>

<summary>How long is the grace period for expired contracts?</summary>

实体到期与删除之间的宽限期为30天。

</details>

<details>

<summary>Who pays for the contract’s renewal and storage fees?</summary>

Hedera上的智能合同可以两种方式支付租金：外部资金或合同资金。

当到达合同的`到期时间`时，网络将首先尝试向合同的\`自动续订账户'收取租金：

- 如果`autoReform Account`有足够的 HBAR 来支付`autoReform Period`, 那么合同将成功续签。
- 如果`autoReform Account ` 有一些HBAR, 但不足以支付完整的 `autoReform Period` ， 然后将合同延长尽可能长的时间（例如，1周而不是90天）。 一旦扩展(1周)结束，如果`autoReforewe账户'没有被重新供资以支付`autoReforest Period\`, 然后合同账户本身将收取租金费用
- 如果`autoReform Account`有零HBAR 余额，那么合同本身就会被收取费用
- 如果`autoReform Account ` 和合同都在到期更新费时有零HBAR余额， 合同标记为 `过期`

</details>

<details>

<summary>What happens if I call a contract that is expired?</summary>

调用`过期'合同将解析为 `CONTRACT_EXPIRED_AND_AWAITING_REMOVAL'。

</details>

<details>

<summary>When a contract is expired and deleted from the network, what happens to its account and assets?</summary>

如果持有原生Hedera Token Service (HTS)的过期合同已到达删除阶段。 然后将该合同持有的资产归还其各自的财务账户。

如果删除的合同被用作HTS令牌的特定密钥， 然后，关键字段将指不再存在的合同。 只要在创建令牌时指定了管理员密钥，该密钥可以被更改。 如果令牌不可变(没有管理员密钥)，则无法更改特定的密钥。

属于HTS tokens金库的合同此时不会过期(可能会在未来发生改变)。

</details>

<details>

<summary>续订合同多长时间？</summary>

最短的更新时间是2,592,000秒(\~30天)，最长是8,000 001秒(\~92天)。

详见[HIP-372: Enty Auto-Reforewals and Expiry Window](https://hips.hedera.com/hip/hip-372)。

</details>

<details>

<summary>If I change the <code>autoRenewPeriod</code> of my contract from 30 to 90 days, what will the cost of my transaction be?</summary>

租金比额表的费用与延长期的长短相近。 因此，支付90天的续订将花费\~3倍于支付30天的续订。

</details>

<details>

<summary>Where can I seen when a contract will expire?</summary>

镜像节点提供合同的到期时间。 您可以使用镜像节点REST API 获取此信息(显示它为 `expendation_time` )，并使用网络探索器如HashScan (显示它为 `expires at`)。

</details>

<details>

<summary>自动更新交易出现在哪里？ 在网络探索器上能看到这些吗？</summary>

根据[HIP-16：Enty Auto-Renewal](https://hips.hedera.com/hip/hip-16)，自动更新费用的记录将在记录流中显示为“actions”，并将通过镜像节点提供。 此外，费用细目是由HashScan等网络探索者提供的，用于合同更新交易。 自动更新操作的收据或记录将无法通过 HAPI 查询。

[HIP-449](https://hips.hedera.com/hip/hip-449) 提供了关于到期合同的信息如何包含在记录流中的技术细节。

</details>

<details>

<summary>Can the <code>autoRenewAccount</code> for a contract be set to another contract ID?</summary>

是的，这对于合同来说是可能的。

</details>

<details>

<summary>What are the key-value pair thresholds that I should be aware of that impact the size of the storage payment?</summary>

- 只有在网络内累积达到**1亿个密钥对**时，合同的存储付款才会开始充电
- 在这之后，每项合同都有**100个免费的密钥对** 可供储存。 一旦合同超过前100个免费密钥对，它必须支付存储费

</details>

<details>

<summary>通过 <code>CREATE2</code>创建的智能合同 如何指定与租用相关的属性，例如<code>自动续展账户</code> 和 <code>自动续展期间</code>？</summary>

通过`CREATE2`创建的合同将继承`sender`地址的自动续订账户`和`autorrecondemerod\`。

例如，如果你调用了`0xab...cd`的合同，它拥有`0.0.X`和`autorreconperiod`45天'，并且这个合同部署了一个新的合同`0xcd'。 .ef`, 然后新的合同还将有 `autoreconseAccount` `0.0.X`和`autorreconperiod `45天'。

另外，还记得租金可以由合同的HBAR余额支付。 因此，开发者可以发送HBAR到合同或配置合同，在执行操作时向用户收取特定的 HBAR 金额。

</details>
