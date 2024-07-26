# 帐户属性

## 帐户 ID

帐户 ID 是Hedera 网络上账户实体的ID。 The account ID includes the **shard number**, **realm number**, and an **account** <mark style="color:purple;">`<shardNum>.<realmNum>.<account>`</mark>**.** The account ID is used to specify the account in all Hedera transactions and queries. 可能有多个帐户ID可以代表一个帐户。\

<details>

<summary>碎片编号</summary>

格式：**`shardNum`**`.realmNum.account`

碎片编号是帐户所在的碎片数量。 一个碎片是参与给定碎片的节点收到的数据的分区。  今天，赫德拉只在一家商店营业。 这个值将保持为零，直到赫德拉在一个以上的牢房中运行。 此值不是负值，是8字节。\默认值：`0`

</details>

<details>

<summary>领域编号</summary>

Format: `shardNum.`**`realmNum`**`.account`\
\
The realm number is the number of the realm the account exists within a given shard. 今天，赫德拉只在一个领域运作。 这个值将保持为零，直到赫德拉在一个以上的牢房中运行。 此值为非负值，为8字节。 帐户只能属于一个领域。 领域ID可以在其他棚子中重新使用。 \默认: `0`

</details>

<details>

<summary><strong></strong></summary>

格式: `shardNum.realmNum.`**`account`**

`account` 可以是以下内容之一：\
A format@@1
:black\_circle: [account Number](account-properties.md#account-number)
:black\_circle: [account 别名](account-properties.md#account-alias)

</details>

### **帐户编号**

每个Hedera帐户创建时都有一个系统提供 **帐户编号**。  帐户号码是非负数的 8 字节。 您可以使用帐号在所有Hedera交易和查询请求中指定帐户。 帐号是独一无二和不可变的。 新创建的帐户的帐户编号将在创建帐户的事务ID的交易收据或交易记录中退回。 帐号ID有以下格式  <mark style="color:purple;">"<shardNum><realmNum><accountNum></mark><mark style="color:blue;"></mark>

<table><thead><tr><th width="199">帐号编号</th><th width="523.3333333333333">描述</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>帐号10中的帐号ID格式。</td></tr></tbody></table>

#### 帐户别名

所有账户都可以有 \*\*账户号别名。 \* 账户号别名是账户号的十六进制编码形式，前置有20字节为零。 这是一个兼容的EVM地址，用于引用Hedera帐户。 账户号别名不包含碎片ID和领域 ID。&#x20

此帐户属性未存储在协商一致节点状态中。 当查询账户对象的共识节点并检查账户别名字段时，您将不会看到返回此值。镜像节点将从帐号中计算账户号别名。 只有当帐户没有现有帐户别名时，才能在帐户REST API中计算和返回帐户别名。 例如，如果帐户是通过[自动帐户创建](自动帐户创建.md)流程使用账户别名创建的，将不会使用帐户别名。 如果帐户通常被创建，那么帐户别名字段将存储帐户编号别名。

<table><thead><tr><th width="175">帐户 ID</th><th>账户号别名示例</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>10的十六进制编码值是 "0a."<br><br><code>000000000000000000000000000000000a</code></td></tr></tbody></table>

### 账户别名

一些Hedera 账户将有一个 **account 别名** 。 账户别名是帐户对象的指针，此外还有帐户编号的标识。 当帐户通过[自动帐户创建](./#自动帐户创建)流程创建时，帐户别名将被分配给帐户。 网络不生成账户别名；相反，用户在创建账户时指定账户别名。 如果通过正常帐户创建流程创建帐户，此属性将是无效的。 账户别名是独特和不可变的。 The account alias ID has the following format  <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark><mark style="color:blue;">.</mark>

此格式仅在`TransferTransaction`、`AccountInfoQuery`和`AccountBalanceQuery`中指定时才可接受。 如果此格式用于引用任何其他交易类型中的帐户，交易将不会成功。&#x20

别名可以是下列别名类型之一：

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>               公钥</td><td><a href="account-properties.md#public-key-account-alias">#公开密钥账户别名</a></td></tr><tr><td>             EVM 地址</td><td><a href="account-properties.md#public-key-account-alias">#公开密钥账户别名</a></td></tr></tbody></table>

#### 公钥账户别名

账户别名公钥是ECDSA secp256k1 或 ED25519 类型的公钥。 公钥格式 ID 是 <mark style="color:blue;">"<shardNum><realmNum><alias></mark> <mark style="color:blue;">\`别名'</mark> 是公钥。 此格式使用Hedera支持的一个简单的加密公钥字节创建。 公共密钥账户别名不需要匹配账户的 [公用钥匙](帐户属性)。 d#keys 用于确定账户交易签名所需的加密签名。

<details>

<summary>Example Public Key Alias Account ID</summary>

`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`

</details>

#### **EVM 地址账户别名**

EVM 地址账户别名是账户的 `ECDSA` 公共密钥的 32 字节的 `Keccak-256 ` 最右20个字节。 这种计算是按照[Ethereum Yellow Paper](https://eferum.github.io/yellowpaper/paper.pdf)所述的方式进行的。 请注意，回收id不是公钥的正式组成部分，不包括在哈希中。 这是在使用[自动帐户创建](自动帐户创建.md)流程中提供的 `ECDSA` 键的协商一致节点上计算的。  EVM 地址也通常称为公共地址。 EVM 地址 ID 格式是 <mark style="color:purple;">"<shardNum>。<realmNum>。<alias></mark> <mark style="color:purple;">`alias`</mark> 是EVM 地址。

EVM 地址账户和[帐户号别名](帐户-properties.md#account-number-alias)是20字节值。 他们可以被区分，因为账户号别名总是以12字节为前缀。 EVM 地址别名通常用于钱包和工具来代表账户地址。&#x20

<details>

<summary>EVM 地址账户别名账户ID 示例</summary>

碎片编号和范围编号设置为0，然后是EVM地址。&#x20

\
**Example**\
\
EVM Address: `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Encoded EVM Address: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
EVM Address Account Alias Account ID: \
\
`0.0.b794f5ea0ba39494ce839613fffba74279579268`

</details>

参考Hedera改进建议： [HIP-583](https://hips.hedera.com/hip/hip-583)

## 账户备注

一个备忘录就像一个简短的记事，这个记事生活在分类账状态中，并且可以在网络浏览器上查看。 此帐户备注限制为 100 个字符。 账户备注是可变的，可以随时更新或从账户中删除。 账户密钥必须签名交易，以便利此属性的任何更改。

{% hint style="warning" %}
不要在帐户备注字段中发布任何私密信息。 此字段对网络中的所有参与者都可见，&#x20;
{% endhint %}

## 帐号编号

Hedera 上的帐户可以在协商一致的节点上提交Etherum交易所处理的`EtherumTransaction`类型。 帐户上的nonce 表示一个帐户通过 `EtherumTransaction` 类型提交的交易的顺序递增计数。 默认账户nonce 值设置为零。

参考Hedera改进建议： [HIP-410](https://hips.hedera.com/hip/hip-410)

## 自动令牌关联

Hedera 帐户通常必须先批准自定义代币才能转移到接收帐户。 接收账户必须签名将关联令牌的交易，允许指定令牌存入他们的账户。 自动令牌关联功能允许账户在将其转移到账户之前手动关联自定义令牌。&#x20

账户可以自动批准最多5,000个代币，而无需手动预先授权每个自定义代币。 假定账户需要保持超过5000的自定义代币余额。 在这种情况下，账户必须手动批准每个额外的代币，使用交易关联代币。 帐户可以保留的代币总数量没有限制。 此属性是可变的，可以在设置后更改。

参考Hedera改进建议： [HIP-23](https://hips.hedera.com/hip/hip-23)

## 余额

创建新账户时，您可以为账户指定初始的 HBAR 余额。 代币的初始HBAR 余额将从支付创建新账户的账户中扣除。 创建初始余额是可选的。&#x20

Hedera账户可以保持HBAR 和自定义可互换和不可互换代币的余额。 账户余额可以在 [网络探索者] (../../networks/community miror-nodes.md) 上查看，并且从镜像节点REST API或共识节点查询。

| 令牌类型                                | 描述                                              | 令牌ID 示例                                                                                   |
| ----------------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **HBAR**                            | 本地Hedera 可互换令牌用于支付交易费和保护网络。                     | 无                                                                                         |
| **不可恢复令牌**                          | 在 Hedera 上创建了自定义可互换令牌。                          | <p>可替换令牌ID代表为 <code>0.0.tokenNum</code> <br><br>例如： <code>0.0.100</code></p>              |
| **不可改变令牌(NFTs)** | 在 Hedera 上创建的自定义不可互换代币(NFTs) | <p>NFT ID表示为 <code>0.0.tokenNum-serialNum</code><br></p><p>例如： <code>0.0.101-1</code></p> |

## 密钥

每个帐户必须在创建时至少有一个密钥。 如果在创建账户时没有提供密钥，网络将拒绝交易。 有权访问帐户私钥的个人可以授权将代币转入或转出帐户，并且必须签署修改帐户的交易。 修改帐户包括更改任何属性，如余额、密钥、备忘录等。

账户可以选择与它们关联多个密钥。 这些类型的帐户是多签名帐户，意味着您需要多个密钥才能在一个帐户或借记HBAR上更改属性。 多签名账户的签名要求取决于该账户选定的钥匙结构。 若要支持关键结构和密钥类型，请点击下面的链接。

{% content-ref url="../keys and-signatures.md" %}
[keys-and-signatures.md](../keys and-signatures.md)
{% endcontent-ref %}

{% hint style="danger" %}
警告：与账户相关联的私钥不会与任何人共享，因为它允许其他人代表您授权您的账户进行交易。 分享您的私钥就像分享您的银行账户密码。 请确保您的私钥存储在一个安全的钱包中。
{% endhint %}

## 需要收件人签名

账户可以选择要求账户签署任何将代币存入账户的交易。 此功能默认设置为 false。 如果此功能设置为 true，帐户将需要签名所有将代币存入帐户的交易。 此属性是可变的，可以在帐户创建后更新。

## 加载中

在赫德拉的参与正在考虑并将HBAR余额与网络中的一个节点联系起来。 自定义可互换或不可互换的代币平衡账户不会有助于在网络上挂起。 在网络上将帐户存入某个节点的目的是加强网络的安全。 为了促进网络安全，相关账户可以在HBAR中获得奖励。 请查看此 [guide](https://docs.hedera.com/hedera/core-concepts/staking) 了解更多关于该标记奖励程序的信息。 合约也可以为赚取报酬而与他们的账户发生关系。

{% hint style="info" %}
在任何时间只能与一个节点或一个账户相关联。
{% endhint %}

<details>

<summary>Staked Node ID</summary>

一个帐户可以选择在Hedera网络中将其HBAR 置于一个节点中。 所关联的节点 ID 是帐户可以关联的节点。 账户的全部余额被关联到节点。 不要混淆节点ID和节点帐户ID。 如果您与节点账户ID相关，您的账户将不会获得预订奖励。 \Staked 帐户余额在任何时候都是液态的。 这意味着您可以将HBAR 令牌转入和转出账户。 并且您的帐户将继续被隐藏到该节点而不受任何干扰。 \没有锁定时间。 这意味着您账户中的 HBAR 代币在您可以使用之前不会被持有一段时间。 \节点ID可以找到 [here](https://docs.hedera.com/hedera/nets/mainnet/mainnet-nodes) 或可以从 [nodes REST API](https://testnet) 查询。 irrornode.hedera.com/api/v1/docs/#/networkNodes).\示例：\Node ID: `1`\

</details>

<details>

<summary>相关账户 ID</summary>

一个帐户可以选择将其HBAR 置于Hedera 网络的另一个帐户中。 这被称为**间接刺伤**。 所关联的帐户 ID 是该账户的ID。 账户的全部余额被关联到指定的账户。 \没有锁定期，余额总是液态，就像对一个节点的跟踪一样。 \
\
Accounts that stake to another account do not earn the staking rewards. 例如，如果账户A被关联到账户B， 账户B需要被关联到一个节点才能有助于网络安全和赚取收藏奖励。 账户B在账户A+账户B中被关联到节点时将获得与账户A+账户B中HBAR余额有关的奖励。 账户A将不会因为标记获得奖励。 \示例：\
\
科目ID: `0.0.10`

</details>

<details>

<summary>Decline to Earn Staking Rewards</summary>

当帐户与某个节点或某个帐户相关时，帐户可以拒绝赚取股票奖励。 Staked 帐户仍然有助于该节点的分量， 但不获得奖励，也不作为向选择获得奖励的其他账户支付奖励的一部分来计算。 默认情况下，除非将这个布尔型标志设置为真，否则所有预订的帐户都将获得奖励。 此选择可以通过更新帐户属性来更改。 海德拉国库帐户使这个旗帜能够拒绝赚取收入的奖励。默认: `true` (所有帐户如果帐户被存档，都接受收益分期付款的奖励)

</details>

### 预订信息

该网络储存一个帐户和合同帐户的分期元数据。 此信息在账户信息查询请求中返回(`AccountInfoQuery` 或`ContractInfoQuery`)。 一个帐户的隐藏元数据包括以下信息：

- **decline\_remark:** 帐户是否拒绝获得staking 奖励
- **stak\_period \_start:** 此帐户的staking 设置或合同更改(例如开始staked\_node\_id)或最近的奖励期间, 以较晚者为准。 如果此帐户或合同目前未被关联到节点，则此字段未设置。 关键时间是24小时，午夜开始UTC
- **挂起\_奖励:** 在下次挂起奖励支付中将收到的 tinybars 金额
- **staked\_to\_me:** 此帐户中所有帐户的总微巴余额
- **staked\_id:** 该帐户或合同所涉的帐户或节点的 ID
  - **staked\_account\_id:** 该帐户或合同所涉及的帐户
  - **staked\_node\_id:** 此帐户或合约的节点的 ID

参考Hedera改进建议： [HIP-406](https://hips.hedera.com/hip/hip-406)

## 自动续订和 **过期**

{% hint style="warning" %}
自动续订和到期(出租)当前未启用。
{% endhint %}

像其他赫德拉实体一样，帐户占用网络存储。 为了支付储存账户的费用，将对网络上使用的储存收取延期费。 此功能今天未在网络上启用； 然而，在将来启用时，账户必须有足够的资金来支付续约费用。 #x20

每个预先确定的时期将以秒为单位收取延期费用。 账户收取费用的时间间隔是自动续延期。 该系统将自动收取账户更新费。 如果帐户没有HBAR 余额，它将在从分类账中删除之前被暂停一周。 您可以在暂停期间续延帐户。

<details>

<summary> 过期时间</summary>

在（及其后）该实体即将到期的有效共识时间戳。

</details>

<details>

<summary><strong>自动更新账户</strong></summary>

自动续约账户是用来支付自动续约费的账户。 如果没有指定的自动续订帐户，自动续订费用将记入帐户。

</details>

<details>

<summary><strong>Auto Renew Period</strong></summary>

该账户收取自动续约费的间隔。 帐户的最大自动更新期限制为3个月(8000001秒)。 最少自动更新期为30天。 自动更新期是可变的，可以随时更新。 如果资金不足，则应尽可能延长时间。 如果它过期时为空，它将被删除。

</details>

参考Hedera改进建议： [HIP-16](https://hips.hedera.com/hip/hip-16)
