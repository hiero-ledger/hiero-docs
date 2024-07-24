# 使用 Google 云存储 (GCS) 运行您自己的镜像节点

## 必备条件

- [Google 云平台](https://cloud.google.com/)帐户
- 对Hedera Mirror Nodes的基本理解。
- [Docker](https://www.docker.com/) (`>= v20.10.x)` 已安装并在您的机器上打开。 在您的终端运行`docker -v`来检查您已安装的版本。
- [Java](https://www.java.com/en/)(openjdk@17：Java version 17)、 [Gradle](https://gradle.org/install/) (最新版本)和 [PostgreSQL](https://www.postgresql.org/) (最新版本) 安装在您的机器上。

## 1. 获取 Google Cloud Platform 请求者支付信息

在这个步骤中，您将生成您的谷歌云平台HMAC访问密钥。 需要这些密钥来验证您的机器和 Google 云存储之间的请求。 它们类似于用户名和密码。 按照这些步骤检索您的 **访问密钥、密钥**和 **project ID**：

- 创建新的 [project](https://cloud.google.com/resourcemaner/docs/creating-managing-projects) 并链接您的 [账单帐户](https://cloud.google.com/billing/docs/how-to/manage-billing-account)。
- 从左侧导航栏中选择 **云存储 > 设置。**
- 点击 **互操作性** 选项卡并向下滚动到 **用户帐户HMAC** 部分。
- 如果您尚未设置默认项目，请立即设置。
- 点击 **创建密钥** 以生成您账户的访问密钥。

<figure><img src="../../../.gitbook/assets/gcs mirror2.png" alt=""><figcaption></figcaption></figure>

- 您应该在访问密钥表中看到**访问密钥** 和 **秘密** 列。
- 您将使用这些键在稍后阶段配置\*\*`application.yml`\*\* 文件。

## 2. 克隆Hedera 镜像节点存储库

- 打开您的终端并运行下面的命令来克隆`hedera-miror-node` [repository](https://github.com/hashgraph/hedera-miror-node) 然后将`cd` 到 `hedera-mirror-node` 文件夹：

```bash
git 克隆https://github.com/hashgraph/hedera-mirror-节点
cd hedera-mirror-节点
```

## 3. 配置镜像节点

**`application.yml`** 文件是Hedera 镜像节点的主要配置文件。 我们将使用您的 GCP 密钥和您想要镜像的 Hedera 网络更新该文件。

- 在根目录中打开`application.yml`文件，由您选择的文本编辑器来选择。
- 查找下面的部分，并用您的实际GCP **访问密钥**，**secret密钥**，**project ID**和您想要镜像的网络替换占位符：

| 项目               | 描述             |
| ---------------- | -------------- |
| **accessKey**    | 您的 GCP 账户的访问密钥 |
| **云提供商**         | GCP            |
| **密钥**           | 您的 GCP 账户中的私钥  |
| **gcpProjectId** | 您的 GCP 项目 ID   |
| **网络**           | 输入您要运行镜像节点的网络  |

{% code title="application.yml" %}

```yaml
hedera:
  镜像:
    导入器:
      下载器:
        访问密钥: EnTER ACESS Key HERE
        云源: "GCP"
        secretKey: ENTER GCP Key HERE
        gcpProjectId: ENTER GCP GCP PROJECT ID HERE
      网络: PREVIEWNET/TESTNET / MAINNET #Pick 一个网络
```

{% endcode %}

- 保存更改并关闭文件。

## 4. 启动您的Hedera 镜像节点

现在让我们使用 Docker开始Hedera 镜像节点。 Docker允许您在一个叫做_container_的自足环境中运行应用程序。

- 从 `hedera-miror-node` 目录运行以下命令：

```bash
Docker compose up -d db && docker logs hedera-miror-node-db-1 --following
```

## 5. 访问您的Hedera 镜像节点数据

此步骤显示如何访问您Hedera 镜像节点正在收集的数据。 镜像节点将其数据存储在 PostgreSQL 数据库中，您正在使用 Docker 连接到该数据库。 要访问镜像节点数据，我们必须输入 **`hedera-miror-node-db-1`** 容器。

- 打开一个新终端并运行以下命令来查看容器列表：

```bash
停靠栏
```

- 输入以下命令来访问 Docker容器：

```bash
停靠exec - it hedera-miror-node-db-1 bash
```

- 输入以下命令来访问数据库：

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_passport=5432"
```

- 输入以下命令以查看数据库表的完整列表：

```bash
\dt
```

![](<../../../.gitbook/assets/image (4).png>)

- 要退出 `psql` 控制台，请运行退出命令：

```bash
\q
```

- 最后，运行以下命令来拦截和移除创建的容器：

```bash
停靠栏下方组成
```

#### 恭喜！ 您已成功地运行和部署了一个带有谷歌云存储 (GCS) :rossuth:
