# Multinode 配置

## 使用Multinode 配置

Multinode 配置是一个高级功能，用于需要多个共识节点的特定场景。 这种配置需要更多的资源，需要更加复杂，从而主要适合测试和发展环境。 在尝试使用 multinode 设置之前，必须确保本地节点在默认单节点模式下正常运行。

<details>

<summary><strong>多种模式要求</strong></summary>

要运行 multinode 模式， 确保在Docker **Settings** -> **Resources** 中至少设置了以下配置，Docker至少可以使用14GB 内存：

- **CPU:** 6
- **内存：** 14 GB
- **交换：** 1 GB
- **磁盘图像大小：** 64 GB

<img src="../../.gitbook/assets/localnode-multinode-requirements.png" alt="" data-size="original">

</details>

{% hint style="info" %}
_**📣 Note**: 创建一个分散的网络，在这个网络中，每个节点在自己的机器上独立运行目前不受支持。 尽管如此，高级的网络和配置功能已经可用，可以让节点在Hedera Mainnet上进行类似的相互交流。_
{% endhint %}

#### **启动Multinode 模式**

要在 multinode 模式中启动 Hedera 本地节点，请将 `--multinode` 标签附加到您的 [start command](sin-configuration.md#npm)。 例如：

```bash
# npm 命令在multinode 模式下启动本地网络
npm 运行开始 -- -d --multinode

# docker 命令在multinode 模式下启动本地网络
docker compose -d --multinode
```

Verify the successful launch of multinode mode by inspecting Docker output of `docker ps --format "table {{.Names}}" | grep network` or the Docker Desktop dashboard. 您应该识别四个正在运行的节点：

```bash
网络节点
网络节点1
网络节点2
网络节点3
```

_📣 **注意**: 在multinode 模式中，你至少需要三个健康的节点进行操作网络。

#### **启动和停止节点**

可以启动或停止单个节点来测试共识、同步和节点选择过程，使用 `npm` 或 `docker` 管理命令：&#x20

<details>

<summary><strong>npm commands</strong></summary>

```bash
# npm 命令启动单个节点
npm 运行启动网络节点 3

# npm 命令停止单个节点
npm 运行停止网络节点 3

# npm 命令重启单个节点
npm 运行重新启动网络节点 node-3
```

</details>

<details>

<summary><strong>docker commands</strong></summary>

```bash
# Docker command to start an individual node
docker compose start network-node-3

# Docker command to stop an individual node
docker compose stop network-node-3

# Docker command to restart an individual node
docker compose restart network-node-3

# Docker command to check logs of the individual node
docker compose logs network-node-3 -f

# Docker command to stop local network and remove containers
docker compose down
```

</details>

或者，运行 `docker compose -v; git clean -xfd; git reset --hard` 以停止本地节点并将其重置为原始状态。

#### 多码模式图

下面的图表显示了多式联运模式下的数据结构和流量。

<figure><img src="../../.gitbook/assets/multinode-diagram.jpeg" alt="" width="535"><figcaption><p>多种模式图</p></figcaption></figure>
