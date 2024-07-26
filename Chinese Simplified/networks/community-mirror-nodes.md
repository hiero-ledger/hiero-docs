---
description: 社区托管的网络探险者
cover: ../.gitbook/assets/Hero-Desktop NetworkExplorers_2022-12-07-020704_ehza (1).webp
coverY: -209.48275862068965
---

# 网络探索者和工具

Hedera网络探索器是跟踪Hedera网络活动的工具。 镜像节点提供有关交易的实时数据，而网络探索者则提供一个方便用户的界面，用于查看和搜索这些交易。

查看下面列出的一些社区托管网络浏览器服务。 每个社区托管网络探索者都可能有自己独特的特点和经验。

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden></th><th data-hidden></th></tr></thead><tbody><tr><td align="center"><a href="https://explorer.arkhia.io/#/mainnet/dashboard"><mark style="color:purple;"><strong>NETWORK EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/4 (1).png">4 (1).png</a></td><td><a href="https://explorer.arkhia.io/">https://explorer.arkhia.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://app.dragonglass.me/hedera/home"><mark style="color:purple;"><strong>NETWORK EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/3 (1).png">3 (1).png</a></td><td><a href="https://app.dragonglass.me/hedera/home">https://app.dragonglass.me/hedera/home</a></td><td></td><td></td></tr><tr><td align="center"><mark style="color:purple;"><strong>NETWORK EXPLORER</strong></mark></td><td></td><td></td><td><a href="../.gitbook/assets/22.png">22.png</a></td><td><a href="https://hashscan.io/">https://hashscan.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://hederaexplorer.io/"><mark style="color:purple;"><strong>NETWORK EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/23.png">23.png</a></td><td><a href="https://hederaexplorer.io/">https://hederaexplorer.io/</a></td><td></td><td></td></tr><tr><td align="center"><p><a href="https://explore.lworks.io/"><mark style="color:purple;"><strong>NETWORK EXPLORER</strong></mark></a></p><p><a href="https://www.lworks.io/"><mark style="color:purple;"><strong>EXPLORER TOOL</strong></mark></a></p></td><td></td><td></td><td><a href="../.gitbook/assets/7.png">7.png</a></td><td><a href="https://explore.lworks.io/">https://explore.lworks.io/</a></td><td></td><td></td></tr></tbody></table>

## 常见问题

<details>

<summary><mark style="color:blue;"><strong>How do I search for a transaction?</strong></mark></summary>

要搜索特定交易，您可以使用唯一的交易ID。

交易 ID应该像这样: `0.0.48750443@1671560120.085845879`

</details>

<details>

<summary><mark style="color:blue;"><strong>如何获取交易 ID？</strong></mark></summary>

事务ID可以由SDK自动生成，手动创建并与交易相关联。 或在交易处理完毕后从收到或记录中获得的资料。 它是交易的独特标识符，可用来搜索和查看其细节。

</details>

<details>

<summary><mark style="color:blue;"><strong>How do I search for an entity (account, topic, tokens, smart contracts)?</strong></mark></summary>

您可以通过您寻找的实体的唯一ID进行搜索。 实体 ID 格式是 `0.0.entityNumber` 。

例如，`0.0.2`是一个帐户 ID，您可以使用该帐号搜索该帐号。

</details>

<details>

<summary><mark style="color:blue;"><strong>How do I get the entity ID?</strong></mark></summary>

实体ID在收到创建它们的交易时被退回。 实体包括帐户、主题、智能合同、时间表和代币。例如，如果您在 SDK 中使用 `AccountCreateTransaction` 创建一个新账户 您可以从交易收据中获取新账户ID。

</details>

<details>

<summary><mark style="color:blue;"><strong>Can I host my own Hedera network explorer?</strong></mark></summary>

是的，你可以！ 您可以使用 [镜像节点REST API] (../sdks-andapis/rest-api)创建您自己的自定义 Hedera 网络浏览器。 d) 或查看[Hedera 镜像节点探索者](https://github.com/hashgraph/hedera-mirror-node-explorer) 开源项目。

</details>

<details>

<summary><mark style="color:blue;"><strong>How can I add a network explorer to this page?</strong></mark></summary>

To add a network explorer to this page, refer to the [contributing guide](../support-and-community/contributing-guide.md) and open an issue in the `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs). 请在该问题中列入下列资料：

- 网络浏览器名称
- 链接到网络浏览器
- 高分辨率标志

</details>
