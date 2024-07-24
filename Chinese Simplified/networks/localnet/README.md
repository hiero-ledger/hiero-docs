# 本地网

## 一. 导言

Hedera Localnet为开发商提供了一个在当地测试和改进以Heder为基础的应用的全面框架。 通过在公共网络之外运作，当地网对软件开发生命周期至关重要。 消除网络 I/O 瓶颈，最大限度地减少共享资源冲突，并提供对网络配置的完全控制。 这个本地网络由两种主要产品产品提供组成，[本地节点](https://github.com/hashgraph/hedera-local-node)和 [Solo](https://github.com/hashgraph/solo)，这两种产品在开发和测试过程中都有不同的用途。 对于刚刚开始使用本地节点的开发人员来说，这里是推荐的测试路径：

<figure><img src="../../.gitbook/assets/localnet-dev-testing-path.png" alt="" width="375"><figcaption><p>Hedera 本地节点测试工作流程</p></figcaption></figure>

1. **本地节点(单个和multinode 模式)**：在本地节点上测试您的初始原型。 此步骤允许在受控环境中进行快速迭代和调试. 如果您的应用需要处理更复杂的场景，请运行 _Multinode 配置。&#x20
2. **Solo**: 然后移动到 _Solo_ 在现实的网络条件下进行高级测试。&#x20
3. **Previewnet**：然后在Hedera Previewnet上测试最新/出血的边缘/即将进行的代码验证。
4. **Testnet**：最后在测试网络上测试稳定的代码验证然后才部署到 Hedera Mainnet。

***

## 本地节点

### 概览

本地节点复制一个Hedera网络，由一个开发者本地机器上的单个节点组成(如果配置则是少数节点)， 为开发、测试和试验[分散的应用程序(dApp)]提供一个受控制的环境。 /../support-community/glossary.md#decentralized-app_dapp)。 此本地设置使用 [Docker Compose](https://docs.docker.com/compose/) 创建多个容器，每个容器在网络中具有特定的角色，包括但不限于：

- **共识节点**：模拟赫德拉共识机制的行为，处理/订购交易并参与网络的协商一致算法。
- [**镜像节点**](../../supportand-community/glossary.md#miror-nodes)**:** 提供访问历史数据、交易记录和网络当前状态但未达成共识。 这对查询和分析很有用。
- [**JSON-RPC Relay**](../../supportand-community/glossary.md#json-rpc-relation): 主动提供本地JSON-RPC 实现 [Ethereum](../../supportand-community/glossary.md#efum) JSON-RPC API以启用与智能合同和帐户的交互。 这对熟悉以太坊工具和生态系统的开发者尤其有用。
- [**镜像节点浏览器**](../../supportand-community/glossary.md#network-explorer): 一个网上接口，允许开发人员视觉审计交易、账户和其他网络活动。

### 设置和配置

单节点配置模拟网络在较小规模(单个节点)、用于调试、测试和原型开发的理想功能。 多节点配置将多个Hedera网络节点的实例分布在单个机器上，使用Docker容器进行高级测试和网络仿效。

➡️ [**单一节点配置**](单节点配置.md)

➡️ [**Multinode Configuration**](multinode-configuration.md)

### 操作模式

本地节点根据开发者的需要提供两种模式：

<details>

<summary><strong>完整模式</strong></summary>

全模式使用`--full` 标志激活，系统旨在捕获和存储全面数据。 下面是如何工作的：

- **数据上传**：网络中的每个节点在操作过程中生成记录流文件。 记录流文件是一个按特定间隔分组的一系列交易记录。 Hedera网络定期将这些交易记录合并为串流文件，然后提供给网络节点和镜像节点。 完整模式，这些文件被系统地上传到他们自己的目录在 `minio` 桶中。 MinIo 是一个天体存储平台，提供存储、检索和搜索博客的专用工具。 此过程由指定给每个节点的特定上传容器管理，即：
  - “记录流-上传-N”(含有记录流)
  - `Account-balance-uploader-N` (包含账户余额文件)
  - `recordecar-uploader-N` (包含一个 `TransactionSidecarRecords` 的列表，这些都是在特定间隔内创建的，并且与同一`RecordStreamFile` 相关联。

</details>

<details>

<summary><strong>Turbo模式</strong></summary>

运行本地节点时，Turbo模式是默认设置。 这种模式确定了效率和速度的优先次序，具有以下关键特性：

- **本地数据访问**：不要将数据上传到云端，而是直接从每个节点上相应的本地目录读取记录流文件。 这种方法大大降低延迟和资源消耗。 使之成为一种理想的假设：即时数据存取和高性能优先于长期储存和外部存取。

</details>

通过这两个选项，用户可以调整本地节点的操作来最适合他们的需要， 是否确保全面的数据捕获和备份或优化性能和速度。

***

## Solo

Solo提供一个先进的私人网络测试解决方案，并采用Kubernetes-第一策略来创建一个完全模仿生产环境的网络。 探索单人仓库 [here](https://github.com/hashgraph/solo)。

_**更多信息即将到来...**_

***

## 额外资源

- [**Hedera 本地节点仓库**](https://github.com/hashgraph/hedera-local-node)
- [**Docker Compose Documentation**](https://docs.docker.com/compose/intro/features-uses/)
- [**Run a Local Node in Gitpod**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/gitpod.md) **\[tutorial]**
- [**在 Codespaces**中运行本地节点](../../tutorials/local-node/how to run-hedera-local-node-in-a-cloud-development-environment-cde/codespace.md) **\[tutorial]**
