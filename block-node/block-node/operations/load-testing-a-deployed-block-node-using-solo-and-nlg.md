# Load Testing a Deployed Block Node Using Solo and NLG

## Overview

After deploying a Block Node, you should verify it can sustain production-level traffic before
connecting it to the live network. This guide uses
[Solo](https://solo.hiero.org/docs) to spin up a temporary network of three Consensus Nodes
(CNs) that stream blocks directly to your Block Node, then drives high-volume transaction load
through those CNs using the Network Load Generator (NLG).

For a lightweight connectivity check instead of a full load test, see
[Testing a Deployed Block Node Using the Simulator](./testing-a-deployed-block-node-using-the-simulator.md).

## Prerequisites

Before you begin, ensure you have:

- A running, reachable Block Node deployed via one of:
  - [Bare Metal Single Node Kubernetes Deployment](./single-node-k8s-deployment.md)
  - [Virtual Machine Single Node Kubernetes Deployment](./solo-weaver-single-node-k8s-deployment.md)
- The Block Node's external IP address or hostname and gRPC port. The default gRPC port is
  `40840` (see `server.port` in [configuration.md](../configuration.md)).
  Retrieve the external IP from your cluster:

  ```bash
  kubectl get svc -n block-node
  ```

  Use the `EXTERNAL-IP` value on the Block Node service row.

- All Solo tool dependencies (Docker, Kind, kubectl, Helm, Node.js) satisfied. See the
  [Solo System Readiness](https://solo.hiero.org/docs/simple-solo-setup/system-readiness/)
  guide. If you install Solo via Homebrew, Node.js, kubectl, and Helm are installed
  automatically as Homebrew dependencies; you still need Docker and Kind separately.

> **Network access:** The Solo test cluster must be able to reach your Block Node over the
> network. If the Block Node is on a cloud VM, confirm that its firewall allows inbound TCP on
> the gRPC port from the machine running Solo. See
> [Network Ports and Protocols](./network-ports-and-protocols.md) for the full port list.

## Step 1: Install the Solo CLI

Homebrew (recommended for macOS/Linux/WSL2):

```bash
brew install hiero-ledger/tools/solo
```

npm (alternative installation method):

```bash
npm install -g @hiero-ledger/solo@latest
```

Verify the installation:

```bash
solo --version
```

For full installation options and troubleshooting, see the
[Solo Quickstart](https://solo.hiero.org/docs/simple-solo-setup/quickstart/).

## Step 2: Set Up a Solo Test Network and Connect to Your Block Node

The commands in this step create a temporary Kubernetes cluster, deploy a three-node Hiero
network inside it, and point those CNs at your Block Node — all before the CNs start. This
ensures the Block Node receives every block from block 0 onwards. All resources are local and
isolated from the live network. For background on what each command does, see the
[Manual Deployment](https://solo.hiero.org/docs/advanced-solo-setup/network-deployments/manual-deployment/)
documentation.

Set shell variables for the values you will reuse across commands:

```bash
export SOLO_CLUSTER_NAME="solo"
export SOLO_NAMESPACE="solo-deployment"
export SOLO_CLUSTER_SETUP_NAMESPACE="solo-cluster"
export SOLO_DEPLOYMENT="solo-deployment"
export CONSENSUS_NODE_VERSION="v0.73.0"
```

> **Consensus Node version:** `v0.73.0` is the default used by Solo 0.77.0. Set this to the
> version that matches your Block Node's peer consensus node version. To see the version your
> Block Node is connected to, check the `block-node-sources` config in your Block Node namespace.

1. **Create a local Kind cluster**:

   ```bash
   kind create cluster -n "${SOLO_CLUSTER_NAME}"
   ```
2. **Connect Solo to the cluster and create a deployment**:

   ```bash
   # Connect to the Kind cluster
   solo cluster-ref config connect \
     --cluster-ref kind-${SOLO_CLUSTER_NAME} \
     --context kind-${SOLO_CLUSTER_NAME}

   # Create a new deployment
   solo deployment config create \
     -n "${SOLO_NAMESPACE}" \
     --deployment "${SOLO_DEPLOYMENT}"
   ```
3. **Add Cluster to Deployment with three Consensus Nodes**:

   ```bash
   solo deployment cluster attach \
     --deployment "${SOLO_DEPLOYMENT}" \
     --cluster-ref kind-${SOLO_CLUSTER_NAME} \
     --num-consensus-nodes 3
   ```
4. **Generate gossip and TLS keys**:

   ```bash
   solo keys consensus generate \
     --gossip-keys \
     --tls-keys \
     --deployment "${SOLO_DEPLOYMENT}"
   ```
5. **Set up shared cluster components** (MinIO, Prometheus CRDs):

   ```bash
   solo cluster-ref config setup \
     --cluster-setup-namespace "${SOLO_CLUSTER_SETUP_NAMESPACE}"
   ```
6. **Deploy the Hiero network**:

   ```bash
   solo consensus network deploy --deployment "${SOLO_DEPLOYMENT}"
   ```
7. **Set up the Consensus Nodes**:

   ```bash
   solo consensus node setup \
     --deployment "${SOLO_DEPLOYMENT}" \
     --release-tag "${CONSENSUS_NODE_VERSION}"
   ```
8. **Point the Consensus Nodes at your Block Node** (before starting them):

   Replace `<BN_IP>` with the external IP or hostname of your Block Node and `<PORT>` with the
   gRPC port (default `40840`):

   ```bash
   solo block node add-external \
     --deployment "${SOLO_DEPLOYMENT}" \
     --address <BN_IP>:<PORT>
   ```
9. **Start the Consensus Nodes**:

   ```bash
   solo consensus node start --deployment "${SOLO_DEPLOYMENT}"
   ```

Once the CNs are running, run a quick sanity check to confirm the Block Node is receiving
blocks:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' <BN_IP>:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

The `firstAvailableBlock` and `lastAvailableBlock` fields should start advancing from their
initial sentinel value (`18446744073709551615`) once blocks arrive. For instructions on
downloading the protobuf bundle into `~/bn-proto`, see
[Testing a Deployed Block Node Using the Simulator](./testing-a-deployed-block-node-using-the-simulator.md#step-5-test-block-node-accessibility-with-grpcurl)
in the prerequisite grpcurl setup section.

## Step 3: Run Load Tests with NLG

`rapid-fire load start` deploys the Network Load Generator (NLG) Helm chart into the Solo
cluster and runs a named test class against the CNs. NLG transactions flow through the CNs,
which produce blocks that are streamed to your Block Node.

For a comprehensive reference on NLG configuration options, available test classes, and
examples, see
[Using Network Load Generator with Solo](https://solo.hiero.org/docs/using-solo/using-network-load-generator-with-solo/).

The NLG arguments (`--args`) must be wrapped in two layers of quotes: the outer single quotes
are for the shell, the inner double quotes are for the NLG parser.

**Run a Crypto Transfer load test** (a good baseline test):

```bash
solo rapid-fire load start \
  --deployment "${SOLO_DEPLOYMENT}" \
  --test CryptoTransferLoadTest \
  --args '"-c 3 -a 10 -t 60"'
```

Key NLG arguments:

| Argument |                   Meaning                    |
|----------|----------------------------------------------|
| `-c`     | Number of concurrent clients                 |
| `-a`     | Number of accounts to create before the test |
| `-t`     | Duration of the test in seconds              |

**Available test classes:**

|          Class           |                 What it tests                  |
|--------------------------|------------------------------------------------|
| `CryptoTransferLoadTest` | HBAR transfers (good general-purpose baseline) |
| `TokenTransferLoadTest`  | Fungible token transfers                       |
| `NftTransferLoadTest`    | NFT transfers                                  |
| `HCSLoadTest`            | Hedera Consensus Service message submissions   |
| `SmartContractLoadTest`  | Smart contract calls                           |
| `LongevityLoadTest`      | Extended endurance run                         |

The command prints a summary when the test finishes, for example:

```text
com.hedera.benchmark.CryptoTransferLoadTest: TPS 3010 (146191 transactions in 48 sec)
```

A `zero-tps` result means NLG could not submit transactions — check the Troubleshooting section
below.

To stop a running test early:

```bash
solo rapid-fire load stop \
  --deployment "${SOLO_DEPLOYMENT}" \
  --test CryptoTransferLoadTest
```

## Step 4: Monitor Block Node Performance

While the load test runs, watch the Block Node to verify it is keeping up with the incoming
stream.

**Check ingestion and verification via Prometheus metrics** (primary check):

|             Metric             |                   What to watch for                    |
|--------------------------------|--------------------------------------------------------|
| `verification_blocks_verified` | Count of blocks verified and persisted; should grow    |
| `verification_blocks_failed`   | Verification failures — investigate immediately if > 0 |
| `verification_blocks_error`    | Internal errors during verification — should stay at 0 |
| Block ingest rate              | Should track CN output rate; drops indicate lag        |
| Subscriber backlog             | High values indicate the BN is falling behind          |
| Storage write latency          | Spikes may indicate I/O saturation                     |

Because the Block Node only persists blocks that pass verification, an advancing
`lastAvailableBlock` confirms both ingestion and verification are working correctly.

For the full metrics reference, see [configuration.md](../configuration.md#metrics).

**Check ingestion via `serverStatus`** (quick spot-check from outside the cluster):

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' <BN_IP>:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

Poll this every few seconds. `lastAvailableBlock` should increase steadily during the test.

**Tail Block Node logs** if metrics are unavailable:

```bash
BN_POD=$(kubectl get pods -n block-node -o name | head -1)
kubectl logs -n block-node $BN_POD -c block-node-server --follow \
  | grep -E "ERROR|WARN"
```

See [Troubleshooting](../troubleshooting.md) for guidance on common error patterns.

## Step 5: Tear Down the Test Cluster

Once you are done testing, remove the NLG and the Solo test cluster. **This does not affect
your Block Node** — it only removes the temporary CN infrastructure.

> **Firewall cleanup:** If you opened inbound firewall rules to allow the Solo test cluster to
> reach your Block Node, close them now that the test is complete.

1. **Remove NLG resources**:

   ```bash
   solo rapid-fire destroy all \
     --deployment "${SOLO_DEPLOYMENT}" \
     --quiet-mode
   ```
2. **Destroy the Solo network** (stops and removes all CN pods):

   ```bash
   solo consensus network destroy \
     --deployment "${SOLO_DEPLOYMENT}" \
     --force \
     --quiet-mode
   ```
3. **Delete the Kind cluster**:

   ```bash
   kind delete cluster -n "${SOLO_CLUSTER_NAME}"
   ```

## Step 6: Reset the Block Node

After the test, the Block Node contains test data from the Solo CNs. Before connecting to the
live network (or a production BN stream for Tier 2 nodes), reset the block store to start
clean.

Follow the reset procedure in
[Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md). For a
Solo Provisioner-managed deployment, the standard path is
[`block node reset`](./resetting-and-upgrading-the-block-node.md#reset-the-block-node-data).
For a Taskfile-managed deployment, use
[`task reset`](./resetting-and-upgrading-the-block-node.md#path-b-manual-taskfile-managed-deployments).

## Troubleshooting

**Solo CNs fail to start**

Check that your machine has enough resources for a three-node network. Each CN requires
approximately 2 CPU cores and 4 GB of RAM. If you are running on a machine with limited
resources, reduce to a single CN by changing `--num-consensus-nodes 1` in Step 2.

**`solo block node add-external` fails with a connection error**

Verify that `<BN_IP>:<PORT>` is reachable from the machine running Solo:

```bash
nc -zv <BN_IP> 40840
```

If the connection is refused, check that:

- The Block Node pod is running (`kubectl get pods -n block-node`).
- The Block Node service is exposing the gRPC port (`kubectl get svc -n block-node`).
- Your cloud firewall allows inbound TCP on port `40840`. See
  [Network Ports and Protocols](./network-ports-and-protocols.md).

**NLG reports `zero-tps`**

This usually means NLG could not submit transactions to the CNs. Check:

- CN pod logs: `kubectl logs -n $SOLO_NAMESPACE <cn-pod> --follow`
- That the CNs are in `Running` status: `kubectl get pods -n $SOLO_NAMESPACE`

**Block Node `lastAvailableBlock` is not advancing**

The BN may not be receiving blocks, or blocks are failing verification. Check the BN logs for
storage or verification errors:

```bash
kubectl logs -n block-node $BN_POD -c block-node-server \
  | grep -E "ERROR|storage|verification"
```

Also confirm the BN has sufficient disk space — the
[Block Node Hardware Specifications](./block-node-hardware-specifications.md) document the
minimum storage requirements for each tier.
