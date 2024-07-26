# 单个节点配置

## 使用单节点配置

单个节点配置模拟网络在较小规模(单个节点)、用于调试、测试和原型开发的理想功能。

<details>

<summary><strong>单节点模式要求</strong></summary>

在停泊器设置中启用 **`VirtioFS`** 文件共享实现。

![](../../.gitbook/assets/docker-compose-settings\(1\).png)

请确保以下配置至少在 Docker **Settings** -> **Resources** 中设置为可用：

**CPU:** 6

**内存:** 8GB

**交换：** 1 GB

**磁盘图像大小：** 64 GB

![](../../.gitbook/assets/docker-settings.png)

确认 **`允许使用默认Docker 套接字(需要密码)`** 已在 Docker **设置 -> 高级** 中启用。

![](../../.gitbook/assets/docker-socket-setting.png)

**注意：** 如果您处于不同的版本，图像可能看起来不同。

</details>

#### **启动和停止节点**

在启动网络命令之前，请确认Docker已安装并在您的机器上打开。 要停止您的本地节点，请使用以下`npm`或`docker`命令。 在继续此操作之前，请确保备份工作目录中手动创建的文件。

<details>

<summary><strong>npm commands</strong></summary>

{% code overflow="wrap" %}

```bash
# npm 命令启动本地网络并在分离模式下生成账户
npm 运行开始 -- -d

# npm 命令停止
npm 运行停止

# npm 命令重启节点
npm 运行重启
```

{% endcode %}

</details>

<details>

<summary><strong>docker commands</strong></summary>

```bash
# 启动本地网络的 Docker 命令。 不生成账户
Docker compose up -d

# Docker 命令以停止服务
Docker compose 停止

# Docker 命令以重启本地网络
docker compose reading

# Docker 命令以停止本地网络并移除容器
docker composed
```

</details>

或者，运行 `docker compose -v; git clean -xfd; git reset --hard` 以停止本地节点并将其重置为原始状态。 可用命令的完整列表可以找到 [here](https://github.com/hashgraph/hedera-local-node?tab=readme-ov-file#commands)。

#### 单个节点模式图

以下图表显示单个节点模式下数据的结构和流量。

<figure><img src="../../.gitbook/assets/localnet-single-node-diagram.png" alt="" width="563"><figcaption><p>单节点模式图</p></figcaption></figure>
