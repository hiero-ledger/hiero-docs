# Multinode Configuration

## Using Multinode Configuration

Multinode configuration is an advanced feature designed for specific scenarios that require multiple consensus nodes. This configuration demands higher resources and involves more complexity, making it suitable primarily for testing and development environments. Before attempting to use the multinode setup, it's crucial to ensure that the local node operates correctly in the default single-node mode.

<details>

<summary><strong>Multinode Mode Requirements</strong></summary>

To run the multinode mode, ensure the following configurations are set at minimum in Docker **Settings** -> **Resources** and at least 14 GB of memory are available for Docker:

- **CPUs:** 6
- **Memory:** 14 GB
- **Swap:** 1 GB
- **Disk Image Size:** 64 GB

<img src="../../.gitbook/assets/localnode-multinode-requirements.png" alt="" data-size="original">

</details>

{% hint style="info" %}
_**📣 Note**: Creating a decentralized network where each node runs independently on its own machine is currently unsupported. Nonetheless, advanced networking and configuration capabilities are available, allowing nodes to communicate with each other similar to their interactions on the Hedera Mainnet._
{% endhint %}

#### **Starting Multinode Mode**

To start Hedera Local Node in multinode mode, append the `--multinode` flag to your [start command](single-node-configuration.md#npm). For example:

```bash
# npm command to start the local network in multinode mode
npm run start -- -d --multinode

# docker command to start the local network in multinode mode
docker compose up -d --multinode
```

Verify the successful launch of multinode mode by inspecting Docker output of `docker ps --format "table {{.Names}}" | grep network` or the Docker Desktop dashboard. You should identify four running nodes:

```bash
network-node
network-node-1
network-node-2
network-node-3
```

_📣 **Note**: In multinode mode, you need at least three healthy nodes for operational network._

#### **Starting and Stopping Nodes**

Individual nodes can be started or stopped to test consensus, synchronization, and node selection processes using `npm` or `docker` management commands:

<details>

<summary><strong>npm commands</strong></summary>

```bash
# npm command to start an individual node
npm run start network-node-3

# npm command to stop an individual node
npm run stop network-node-3

# npm command to restart an individual node
npm run restart network-node-3
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

Alternatively, run `docker compose down -v; git clean -xfd; git reset --hard` to stop the local node and reset it to its original state.

#### Multinode Mode Diagram

The following diagram illustrates the architecture and flow of data in multinode mode.

<figure><img src="../../.gitbook/assets/multinode-diagram.jpeg" alt="" width="535"><figcaption><p>Multinode mode diagram</p></figcaption></figure>
