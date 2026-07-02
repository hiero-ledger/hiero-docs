# Testing a Deployed Block Node Using the Simulator

## Overview

After deploying a Block Node using either manual Kubernetes configuration or Solo Provisioner, you must verify that the node is functioning correctly by testing its ability to receive and process streamed blocks.

This guide walks you through testing your Block Node deployment using a simulator Docker container that publishes test blocks to the node.

The testing process uses a Docker Compose file to create a simulator publisher container.
This container streams blocks to your deployed Block Node, verifying connectivity and block processing functionality.

For production-scale load testing using real Consensus Nodes and the Network Load Generator, see
[Load Testing a Deployed Block Node Using Solo and NLG](./load-testing-a-deployed-block-node-using-solo-and-nlg.md).

## Prerequisites

Before you begin, ensure you have:

- A running Block Node deployment on your system using one of the following methods:
  - [**Bare Metal Single Node Kubernetes Deployment**](./single-node-k8s-deployment.md)
  - [**Virtual Machine Single Node Kubernetes Deployment**](./solo-weaver-single-node-k8s-deployment.md)
- **[Docker](https://docs.docker.com/get-started/get-docker/) and Docker Compose** installed and available on your machine where you will run the simulator.
- The **gRPC** service address and port for your Block Node:
  - For Local deployment: `localhost:40840`
  - For cloud deployment server address:

    ```bash
    kubectl get svc -n block-node
    ```
- A gRPC client tool for verification (optional but recommended):
  - **Postman** (with gRPC support), or
  - **grpcurl** command-line tool

## Step 1: Create the Simulator Docker Compose File

Create `docker-compose-publisher.yaml` in the directory where you plan to run the test, with the following content:

```yaml
services:
  simulator-publisher:
    container_name: simulator-publisher
    image: ghcr.io/hiero-ledger/hiero-block-node/simulator-image:<BLOCK_NODE_VERSION_TAG>
    environment:
      - BLOCK_STREAM_SIMULATOR_MODE=PUBLISHER_CLIENT
      - GRPC_SERVER_ADDRESS=<BLOCK_NODE_HOST>
      - GRPC_PORT=40840
      - GENERATOR_START_BLOCK_NUMBER=0
      - GENERATOR_END_BLOCK_NUMBER=100
```

Two placeholders need real values:

- `<BLOCK_NODE_HOST>` — the Block Node address. See [Choose the Block Node address](#choose-the-block-node-address).
- `<BLOCK_NODE_VERSION_TAG>` — the simulator image tag matching your Block Node version. See [Pick a simulator image tag](#pick-a-simulator-image-tag).

The remaining values typically don't need changing for an initial test:

- `BLOCK_STREAM_SIMULATOR_MODE=PUBLISHER_CLIENT` makes the simulator act as a publisher and stream blocks into the Block Node.
- `GRPC_PORT=40840` is the Block Node's default gRPC port. If your deployment uses a non-default port (see `server.port` in [configuration.md](../configuration.md)), set it here.
- `GENERATOR_START_BLOCK_NUMBER` and `GENERATOR_END_BLOCK_NUMBER` define the inclusive block-number range the simulator generates (here, blocks 0 through 100).

### Choose the Block Node address

The `<BLOCK_NODE_HOST>` value depends on where the simulator runs:

- **From the same VM as the Block Node** (the simulator runs in Docker on the same host as the cluster): use the Kubernetes service IP from `kubectl get svc -n block-node` (the `CLUSTER-IP` or `EXTERNAL-IP` column on the `block-node` service), or the VM's internal IP from `hostname -I`. `localhost` does not work on a typical cloud VM, because the Block Node listens on the cluster service network, not the host loopback. `host.docker.internal` is also unreliable on cloud Linux — use the service or internal IP directly.
- **From a different machine or network**: use the VM's external IP. Cloud firewalls (including GCP's default) only open SSH, so you must add an inbound rule for the gRPC port before this works.

### Pick a simulator image tag

The simulator image tag must match your running Block Node version. Read the version from the StatefulSet:

```bash
kubectl get statefulset block-node-block-node-server -n block-node \
  -o jsonpath='{.spec.template.spec.containers[0].image}'
```

> The StatefulSet name `block-node-block-node-server` and the namespace `block-node` assume the default Helm release name `block-node` used by both deployment guides. If you chose a different release name or namespace, substitute them here and in the `kubectl` commands later in this guide.

This returns the full image reference, for example:

```text
ghcr.io/hiero-ledger/hiero-block-node/block-node-server:0.35.0
```

The portion after the final `:` (here, `0.35.0`) is the simulator tag to use.

Simulator images are published to the [package registry](https://github.com/hiero-ledger/hiero-block-node/pkgs/container/hiero-block-node%2Fsimulator-image). Not every Block Node release has a matching simulator GA tag - some GA versions only ship `-SNAPSHOT` or `-rc*` tags. If a `:X.Y.Z` tag returns a `not found` error from Docker, use the closest available tag from the registry (commonly `X.Y.Z-rc2` or `X.Y.Z-SNAPSHOT`).

## Step 2: Run the Docker Compose File

From the directory containing `docker-compose-publisher.yaml`, run:

```bash
docker compose -f docker-compose-publisher.yaml up
```

The simulator streams blocks to the Block Node and stops automatically when it reaches the `GENERATOR_END_BLOCK_NUMBER` you set. Step 3 explains what to look for in the output.

## Step 3: Verify Block Streaming

Once the Docker Compose container is running, verify that blocks are being streamed and accepted.

1. **Check simulator logs**:

   In the terminal where `docker compose up` is running, you should see the simulator go through three phases:

   ```text
   [+] Running 1/1
    ✔ Container simulator-publisher  Recreated                                                                                                                                  0.1s
   Attaching to simulator-publisher
   simulator-publisher  | 2026-06-04 10:47:00.123+0000 INFO    BlockStreamSimulatorApp#start            Block Stream Simulator started initializing components...
   simulator-publisher  | 2026-06-04 10:47:00.234+0000 INFO    SimulatorConfigurationLogger#log         blockStream.simulatorMode=PUBLISHER_CLIENT
   simulator-publisher  | 2026-06-04 10:47:00.345+0000 INFO    SimulatorConfigurationLogger#log         blockStream.millisecondsPerBlock=1000
   ...
   simulator-publisher  | 2026-06-04 10:48:18.776+0000 INFO    PublishStreamObserver#onNext             Received Response: acknowledgement {
   simulator-publisher  |           block_number: 44
   simulator-publisher  |         }
   simulator-publisher  | 2026-06-04 10:48:19.774+0000 INFO    PublishStreamObserver#onNext             Received Response: acknowledgement {
   simulator-publisher  |           block_number: 45
   simulator-publisher  |         }
   ...
   simulator-publisher  | 2026-06-04 10:50:17.720+0000 INFO    PublisherClientModeHandler#millisPerBlockStreaming Block Stream Simulator has stopped
   simulator-publisher  | 2026-06-04 10:50:17.720+0000 INFO    PublisherClientModeHandler#millisPerBlockStreaming Number of BlockItems sent by the Block Stream Simulator: 1574
   simulator-publisher  | 2026-06-04 10:50:17.721+0000 INFO    PublisherClientModeHandler#millisPerBlockStreaming Number of Blocks sent by the Block Stream Simulator: 31
   ```

   - **Startup**: Docker Compose attaches to the simulator container, which prints its configuration (`PUBLISHER_CLIENT` mode, block range, pacing) and begins streaming.
   - **Streaming**: Each `acknowledgement { block_number: N }` line confirms the Block Node received and accepted block `N`.
   - **Summary**: When the simulator finishes streaming the configured range, it prints the totals and exits. `Number of Blocks sent` should match `GENERATOR_END_BLOCK_NUMBER` − `GENERATOR_START_BLOCK_NUMBER` + 1; the `1574` and `31` shown are from an example run and will differ for yours.
2. **Verify on the Block Node side**:

   Confirm the Block Node is processing the incoming blocks by tailing its logs and looking for verification-session entries:

   ```bash
   kubectl logs -n block-node block-node-block-node-server-0 -c block-node-server \
     | grep ExtendedMerkleTreeSession
   ```

   Healthy output shows one `Created ExtendedMerkleTreeSession for block N` line per block received:

   ```text
   2026-06-04 10:47:34.942+0000 INFO    [org.hiero.block.node.verification.session.impl.ExtendedMerkleTreeSession <init>] Created ExtendedMerkleTreeSession for block 0
   2026-06-04 10:47:35.641+0000 INFO    [org.hiero.block.node.verification.session.impl.ExtendedMerkleTreeSession <init>] Created ExtendedMerkleTreeSession for block 1
   2026-06-04 10:47:36.642+0000 INFO    [org.hiero.block.node.verification.session.impl.ExtendedMerkleTreeSession <init>] Created ExtendedMerkleTreeSession for block 2
   ```

   > **About `Defaulted container ...` messages:** if you omit `-c block-node-server`, `kubectl` prints which container it picked (the pod has multiple). It's informational; the command still runs correctly. Pass `-c block-node-server` explicitly to suppress it.

## Step 4: Clean Up After Testing

The Docker Compose container stops automatically when it reaches the last block in the configured range. To stop early or reset the Block Node before another test:

1. If the simulator is still running, press **Ctrl+C** in the terminal where `docker compose up` is running.

2. Stop and remove the simulator publisher container:

   ```bash
   docker compose -f docker-compose-publisher.yaml down
   ```
3. **(Optional) Reset the Block Node data**, either to run a new test from a clean state or to prepare the deployment to receive real block streams.

   > **Caution:** These commands permanently delete all current block data from the Block Node. Only run them in non-production environments, or when you explicitly intend to clear test data.

   **For single-node Kubernetes deployments**, delete the live and historic data directories inside the Block Node pod, then restart the pod:

   ```bash
   kubectl -n ${NAMESPACE} exec ${POD} -c block-node-server \
     -- sh -c 'rm -rf /opt/hiero/block-node/data/live/* /opt/hiero/block-node/data/historic/*'
   kubectl -n ${NAMESPACE} delete pod ${POD}
   ```

   Replace `${NAMESPACE}` and `${POD}` with values from your deployment: `${NAMESPACE}` is the Kubernetes namespace (for example, `block-node`), and `${POD}` is the Block Node pod name from `kubectl get pods -n ${NAMESPACE}` (for example, `block-node-0`).

   **For Docker-based Block Node deployments**, run the equivalent inside the Block Node container:

   ```bash
   docker exec <BLOCK_NODE_CONTAINER_NAME> sh -c 'rm -rf /opt/hiero/block-node/data/live/* /opt/hiero/block-node/data/historic/*'
   docker restart <BLOCK_NODE_CONTAINER_NAME>
   ```

   After the pod or container restarts, the Block Node comes up with empty live and historic data directories, ready for a fresh simulator run or to begin consuming real block streams.
