# Docker Compose Local Quickstart

Run a Block Node locally using Docker Compose - no Kubernetes required. This is the fastest way
for developers, SDK integrators, and contributors to get a Block Node running on their workstation.

> **Note:** This setup uses the Block Node developer image and is intended for local testing only.
> It is not suitable for production or testnet/mainnet operation. For production deployment, see the
> [operations guides](operations/). The `hiero-local-node` tool that previously bundled a Block
> Node is being deprecated in September 2026 - use this quickstart instead.

## What this sets up

One `./gradlew` command starts the following Docker Compose stack:

|  Service   |            Port             |                                    Purpose                                    |
|------------|-----------------------------|-------------------------------------------------------------------------------|
| Block Node | `40840` / `16007` / `40983` | gRPC APIs on `40840`; Prometheus metrics on `16007`; health (HTTP) on `40983` |
| Prometheus | ephemeral                   | Scrapes Block Node metrics; no fixed host port - use Grafana at `3000`        |
| Grafana    | `3000`                      | Pre-provisioned dashboards                                                    |
| Loki       | `3100`                      | Log aggregation                                                               |
| Promtail   | -                           | Log shipping from Docker to Loki                                              |
| cAdvisor   | `8081`                      | Container resource metrics                                                    |

## Prerequisites

- **Docker 24+** with Docker Compose v2 - verify with `docker compose version`
  - You may also use **Podman 1.5+** _optionally_ with Podman Compose v1.6+ - verify with `podman compose version`
- **Java 25 or later** to run Gradle - verify with `java --version`. Gradle provisions JDK 25
  for compilation automatically via toolchains.
- **Git**
- Ports `40840`, `40983`, `16007`, `3000`, and `3100` must be free on your machine

Optional, for the verification steps in Step 3:

- **`grpcurl`** - see [Block Node gRPC API Quickstart](api-quickstart.md) for install instructions

## Step 1 - Clone the repository

```bash
git clone https://github.com/hiero-ledger/hiero-block-node.git
cd hiero-block-node
```

If you already have the repository cloned, `cd` into it and skip to Step 2.

## Step 2 - Build and start the stack

```bash
./gradlew :app:startDockerContainer
```

This compiles the Block Node from source, builds the Docker image, and starts the full stack.

**First run takes 5-15 minutes** while Gradle downloads dependencies and compiles the project.
Subsequent runs are faster because compiled classes and the Docker image are cached.

Confirm the stack is up:

```bash
docker compose -p block-node ps
```

All services should show `Up` status within about 30 seconds of the build completing.

Once the stack is up, connect your gRPC client to `localhost:40840` with plaintext (no TLS).

## Step 3 - Verify the Block Node is running

Check the metrics endpoint (no extra tools required):

```bash
curl -s http://localhost:16007/metrics | grep blocknode_publisher_block_items_received_total
```

A healthy response:

```
blocknode_publisher_block_items_received_total 0
```

The counter is `0` because no blocks have been published yet - that is expected. If the command
returns no output, the metrics endpoint is not ready yet - wait 30 seconds and retry.

For a status check using `grpcurl` (requires the `~/bn-proto` bundle - see
[Block Node gRPC API Quickstart](api-quickstart.md) for setup):

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  localhost:40840 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

On a fresh start, `firstAvailableBlock` and `lastAvailableBlock` show `18446744073709551615`
(uint64 max) - this means no blocks are stored yet, not an error.

## Step 4 - Open Grafana

Navigate to [http://localhost:3000](http://localhost:3000) in your browser - no login required.
Open the **Block-Node: Full-History Metrics** dashboard to see node status, ingestion rate,
storage, and verification metrics.

All panels show zero until blocks are published. Continue to Step 5 to send test blocks.

## Step 5 - Send test blocks with the simulator (optional)

The Block Stream Simulator publishes synthetic blocks to the Block Node at one block per second.
It is pre-configured to reach the Block Node started in Step 2 - no additional configuration needed.

In a second terminal, from the repository root:

```bash
./gradlew :simulator:startDockerContainerPublisher
```

**First run takes 5-10 minutes** to build the simulator Docker image. Subsequent runs are faster.
Once the build completes, verify blocks are flowing:

```bash
curl -s http://localhost:16007/metrics | grep blocknode_publisher_block_items_received_total
```

The counter should be non-zero and increasing. Run the command twice, 10 seconds apart, to
confirm it is incrementing.

Refresh the **Block-Node: Full-History Metrics** dashboard at [http://localhost:3000](http://localhost:3000)
to see ingestion rate, storage growth, and verification activity.

To stop the simulator:

```bash
./gradlew :simulator:stopDockerContainer
```

## Step 6 - Stop the Block Node stack

```bash
./gradlew :app:stopDockerContainer
```

Or directly with Docker Compose:

```bash
docker compose -p block-node stop
```

`stop` halts the containers without removing them - block data is retained in the container
filesystem and the next `startDockerContainer` resumes from where you left off.

To remove all containers and start completely fresh:

```bash
docker compose -p block-node down
```

## Limitations

This local setup intentionally omits several things that production deployments require:

- **No Consensus Node** - blocks arrive only if you run the simulator (Step 5) or configure a
  real CN to point at `localhost:40840`.
- **Developer image** - the container includes debug tools and a remote debugger on port `5005`.
  It is not the production image.
- **No TLS** - all gRPC traffic is plaintext.
- **No on-chain registration** - the Block Node is not registered on the Hiero network. Use this
  setup for local integration testing only.
- **Single-port gRPC** - all gRPC services share port `40840`. Production Kubernetes deployments
  use per-service ports.

## Related documentation

- [Block Node gRPC API Quickstart](api-quickstart.md)
- [Configuration Reference](configuration.md)
- [Block Node Overview](block-node-overview.md)
- [Operations guides](operations/)
- [Troubleshooting](troubleshooting.md)
