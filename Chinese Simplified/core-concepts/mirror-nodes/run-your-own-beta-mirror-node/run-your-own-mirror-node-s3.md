# Run Your Mirror Node with Amazon Web Services S3 (AWS)

## 必备条件

- An [Amazon Web Services](https://aws.amazon.com/free/?trk=ps\_a131L0000085DvcQAE\\&trkCampaign=acq\_paid\_search\_brand\\&sc\_channel=ps\\&sc\_campaign=acquisition\_US\\&sc\_publisher=google\\&sc\_category=core\\&sc\_country=US\\&sc\_geo=NAMER\\&sc\_outcome=acq\\&sc\_detail=aws%20account\\&sc\_content=Account\_e\\&sc\_segment=432339156165\\&sc\_medium=ACQ-P|PS-GO|Brand|Desktop|SU|AWS|Core|US|EN|Text\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&ef\_id=Cj0KCQjw8IaGBhCHARIsAGIRRYrLfWc3ykRf\_hAUeVvf4nNEYvacHwk\_w1jAuSj6hQZ8\_muh0T5p3acaAkZDEALw\_wcB:G:s\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&all-free-tier.sort-by=item.additionalFields.SortRank\\&all-free-tier.sort-order=asc\\&awsf.Free%20Tier%20Types=*all\\&awsf.Free%20Tier%20Categories=*all) account.
- 对Hedera Mirror Nodes的基本理解。
- [Docker](https://www.docker.com/) (`>= v20.10.x)` installed and opened on your machine. 在您的终端运行`docker -v`来检查您已安装的版本。
- [Java](https://www.java.com/en/) (openjdk@17: Java version 17), [Gradle](https://gradle.org/install/) (the latest version), and [PostgreSQL](https://www.postgresql.org/) (the latest version) are installed on your machine.

## 1. Create an IAM user

This step will teach you how to create a new IAM (_Identity and Access Management)_ user and generate new access keys in your AWS account. The **access key,** **secret** and **project ID** will be used to access S3 from the Hedera Mirror Node.

- Create an [IAM (Identity and Access Management) user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-set-up.html#create-an-admin) with either an administrator or custom policy. If you're unfamiliar with using AWS, go with the administrator policy:

{% tabs %}
{% tab title="Administrator Policy" %}

- Refer to AWS documentation to create an IAM user with an administrator policy [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
  - This sets up an IAM user with Administrator Access Policy
  - This user has full access and can delegate permissions to every service and resource in AWS.
- Once that is complete, select **Users** from the left IAM navigation bar
- Select the **Administrator** from the **User name** column
- Select the **Security credentials** tab
- Select **Create access key**
- Copy or download your **Access key ID** and **Secret access key**
  {% endtab %}

{% tab title="Custom Policy" %}

- Enable access to billing data
  - 跟随步骤 2 [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
- 从 IAM 左侧导航栏选择 **Polices**
- 点击 **创建策略**
  - 服务
    - 输入 **S3** 作为您的服务
  - 行动
    - 访问级别
    - 选择 **List** 和 **Read**
- 资源
  - 选择 **为GetBucketLocation指定bucket 资源 ARN**
  - 添加ARN
    - Hedera-mainnet-流
  - 添加ARN
    - Hedera-mainnet-streams/\*
- 点击 **下一步：标签**
- 点击 **下一步：评论**
  - 输入策略名称
- 点击 **创建策略**
- 从 IAM控制台左侧导航栏选择 **用户** **组**
- 点击 **创建群组**
- 输入用户组名称
- 选择上一步创建的策略
- 点击 **创建组**
- 从 IAM控制台左侧导航栏点击 **用户**
- 点击**添加用户**
- 输入用户名
- 选择 **程序访问权限类型**
- 点击 **下一步：权限**
- 选择上一步创建的组
- 点击**下一步：标签**
- 点击 **下一步：评论**
- 点击 **创建用户**
- 复制或下载您的 **Access key ID** 和 **绝密访问密钥**
  {% endtab %}
  {% endtabs %}

## 2. 克隆镜像节点仓库

- 打开您的终端并运行下面的命令来克隆镜像节点 [repository](https://github.com/hashgraph/hedera-mirror-node)，然后将`cd`进入`hedera-mirror-node`文件夹：

<pre class="language-bash"><code class="lang-bash"><strong>git 克隆https://github.com/hashgraph/hedera-miror-node.git
</strong>cd hedera-miror-节点
</code></pre>

## 3. 配置镜像节点

`application.yml`文件是Hedera 镜像节点的主要配置文件。 在这一步中，我们将使用您的特定设置更新配置文件。 例如您的 AWS 访问密钥、密钥和您想要镜像的 Hedera 网络。

- 在根目录中打开`application.yml`文件，由您选择的文本编辑器来选择。
- 从您的终端或 IDE 的 `hedera-mirror-node` 目录中的`cd`。
- 找到以下字段并用您的实际AWS访问密钥、密钥、工程ID和您想要镜像的网络替换占位符：

| 项目            | 描述            |
| ------------- | ------------- |
| **accessKey** | AWS 访问密钥      |
| **云提供商**      | s3            |
| **密钥**        | AWS 密钥        |
| **网络**        | 输入一个网络来运行镜像节点 |

{% code title="application.yml" %}

```yaml
hedera:
  镜像:
    导入器: 
      下载器:
        访问密钥: ENTER AccessS Key HERE
        云源: "s3"
        secretKey: ENTER SECRET Key HERE
      网络: PREVIEWNET/TESTNET/MAINNET #选择一个网络
```

{% endcode %}

## 4. 运行您的镜像节点

使用 Docker启动并运行Hedera 镜像节点。 Docker 软件包开发工具在一个自足的环境中，叫做一个 _container_，其中可以包括库、代码、运行时间等等。

- 从镜像节点目录运行以下命令：

```bash
Docker compose up -d db && docker logs hedera-miror-node-db-1 --following
```

## 5. 访问您的镜像节点数据

镜像节点运行成功后，它会从 Hedera 网络下载数据并在 PostgreSQL 数据库中存储。 要访问镜像节点数据，请输入数据库容器并使用 Docker 和 `psql` 命令行工具连接它。

- 打开一个新终端并运行以下命令来查看容器列表：

```bash
停靠栏
```

<figure><img src="../../../.gitbook/assets/docker ps (1).png" alt=""><figcaption></figcaption></figure>

- 运行以下命令来输入 `hedera-miror-node-db-1` 容器：

```bash
停靠exec - it hedera-miror-node-db-1 bash
```

- 输入以下命令来访问和查询数据库：

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_passport=5432"
```

- 输入以下命令以查看所有数据库表的完整列表：

```bash
\dt
```

<figure><img src="../../../.gitbook/assets/list of relations s3 mirror.png" alt=""><figcaption></figcaption></figure>

- 要退出 `psql` 数据库控制台，请运行退出命令：

```bash
\q
```

- 最后，运行以下命令来停止 Docker并移除创建的容器：

```bash
停靠栏下方组成
```

#### 恭喜！ 您已成功运行和部署了一个带有Amazon Web Services S3 (AWS) :routh:
