---
description: 存储历史记录和具有成本效益的查询数据
---

# 镜像节点

镜像节点提供了一个存储和成本效益高的查询公共分类账历史数据的方法，同时尽量减少Hedera网络资源的使用。 镜像节点支持目前可用的Hedera网络服务，可用来检索以下信息：

- 交易和记录
- 事件文件
- 平衡文件

## 了解镜像节点

Hedera 镜像节点接收来自Hedera 网络协商一致节点的信息，不管是主网还是测试网，并且提供了更有效的操作方式：

- 查询
- 分析
- 审计支助
- 监测

虽然镜像节点从协商一致的节点获得信息，但它们本身并无助于协商一致。 赫代拉的信任产生于协商一致节点达成的共识。 这种信任使用签名、哈希链和状态证明转移到镜像节点。

为了使初步部署更加容易， 镜像节点通过创建包含已处理信息(例如帐户余额或交易记录)的定期文件并完全信任Hedera协商一致节点来消除运行完整Hedera节点的负担。 镜像节点软件通过接收来自网络的预构建文件来减轻处理负担，验证那些生成的文件，并提供REST API。

![](../../.gitbook/assets/betaimirornode-overview.jpg)

通过验证与记录相关的签名文件, 平衡, 镜像节点工作。 和来自共识节点的事件文件已从网络上传到云存储解决方案。

当交易就Hedera Net或测试网达成共识时， Hedera 协商一致节点将交易及其相关记录添加到记录文件。 记录文件包含一系列订单交易及其相关记录。 在一段时间后，记录文件已关闭，新文件已创建。 当网络继续接收交易时，这个过程重复了。

一旦一个记录文件被关闭，协商一致的节点会生成一个签名文件。 签名文件包含相应记录文件哈希的签名。 除了共识节点的签名文件外，记录文件还包含上一个记录文件的哈希值。 先前的记录文件现在可以通过匹配上一个记录文件的哈希进行验证。

Hedera 协商一致的节点将新记录文件和签名文件推送到云存储提供商——目前支持AWS S3和谷歌文件存储。 镜像节点下载这些文件，根据它们的哈希来验证它们的签名，然后才让它们可以处理。

### 智能合同合成日志

从 [v0.79]起(../../networks/release-notes/miror-node.md#v0)。 另见Hedera Mirror Node 发布的第9段。 Hedera Token Service (HTS) 令牌交易的合成事件日志已被引入，以模仿智能合同令牌的行为。 为以下交易生成了合成事件：

- `CryptoTransfer`
- `CryptoApprouvealledance`
- `CryptoDeleteAllowance`
- `TokenMint`
- `TokenWipe`
- `TokenBurn`

此功能使开发人员能够有效地监视HTS 令牌活动，就好像它们是智能合约令牌一样。 示例代码实现使用 ethers.js 聆听合成事件可以找到 [here](https://github.com/ed-marquez/hedera-example-hts-synthetic-events-sdk-ethers).&#x20

### 来自 Hedera 的REST API

Hedera 提供REST API，便于查询由Hedera主持的镜像节点，从而消除了运行您自己的复杂程度。 查看下面的镜像节点 REST API 文档。

{% content-ref url="../../sdks-and/apis/rest-api.md" %}
[rest-api.md](../../sdks-and-apis/rest-api.md)
{% endcontent-ref %}

### 运行镜像节点

任何人都可以通过下载和配置计算机上的软件来运行Hedera 镜像节点。 通过运行镜像节点，您可以连接到适当的云存储和存储帐户平衡文件， 记录文件和上述事件文件。 请查看下面的链接如何开始操作。

{% content-ref url="run-your own-beta-miror-node/" %}
[run-your-own-beta-mirror-node](run-your-own-beta-miror-node/)
{% endcontent-ref %}

{% content-ref url="one-click-mirror-node-deployment.md" %}
[one-click-mirror-node-deployment.md](单击镜像-节点部署.md)
{% endcontent-ref %}

## 常见问题

<details>

<summary>数据如何存储在赫德拉镜像节点？ 这是一个特定类型的数据库，还是使用一个独特的数据结构？</summary>

Hedera Mirror Nodes use [PostgreSQL](../../support-and-community/glossary.md#postgresql) databases to store the transaction and event data organized in a structure that mirrors the Hedera Network. 镜像节点收到Hedera共识节点的记录文件后，数据将被验证并加载到数据库。&#x20

</details>

<details>

<summary>如何运行我自己的Hedera 镜像节点？ 什么是硬件和软件需要？</summary>

设置Hedera 镜像节点涉及硬件和软件组件。 要求可以找到 [here](运行您自己的镜像节点/)。

要运行您的镜像节点，请按照"[运行您自己的镜像节点](运行您自己的镜像节点/)"指南中的步骤操作。

</details>

<details>

<summary>Are there costs associated with running a mirror node?</summary>

否，Hedera 不会因为运行镜像节点而收费。 然而，购买硬件、互联网连接和可能的云服务费用都有相关的费用。 硬件和软件要求可以找到 [here](运行您自己的乙型镜像节点/)。

</details>

<details>

<summary>How do I configure a mirror node and query data?</summary>

你可以按照"[如何配置镜像节点和查询数据](.)中提供的分步指令来配置你自己的 Hedera 镜像节点。 /../tutorials/more-tutorials/how to configure-a-miror-node-and-query-specify-data.md)”指南。 指南提供关于前提、节点设置、配置和查询节点的说明。 此外，您可以在指南中找到更多关于保留和交易以及实体过滤的详细信息。

</details>

<details>

<summary>How can I provide feedback or create an issue to log errors?</summary>

若要提供反馈或日志错误，请参阅[贡献指南](../../supportand-community/contributing-guide.md)，并在Hedera Docs[GitHub 仓库](https://github.com/hashgraph/hedera-json-rpc-remedy/issues)。

</details>
