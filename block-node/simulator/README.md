# Block Stream Simulator

## Overview

The Block Stream Simulator lets you inject or consume a block stream from a Block Node without a
live Consensus Node. Use it to:

- Test a Block Node locally during development
- Validate a deployed Block Node by publishing blocks to it
- Subscribe to a Block Node and verify it is producing blocks

## Modes

|             Mode             |                                 Description                                  |
|------------------------------|------------------------------------------------------------------------------|
| `PUBLISHER_CLIENT` (default) | Connects to a Block Node as a gRPC client and publishes blocks to it         |
| `PUBLISHER_SERVER`           | Acts as a gRPC server that accepts incoming block stream publish connections |
| `CONSUMER`                   | Subscribes to a Block Node and consumes the block stream                     |

## Quickstart

The simulator connects to a running Block Node. If you don't have one yet, start one locally with
the [Docker Compose Quickstart](../block-node/docker-compose-quickstart.md) before proceeding.

Refer to the [Quickstart](quickstart.md) to run the simulator locally in minutes.

## Configuration

Refer to the [Configuration](configuration.md) for all configuration options including mode,
streaming rate, block generation, and gRPC settings.

## Internals

The simulator uses Dagger2 for dependency injection. The project has a modular structure with all
modules wired from the root injection component:

```plaintext
tools-and-tests/simulator/src/main/java/org/hiero/block/simulator/BlockStreamSimulatorInjectionComponent.java
```

The entry point is `org.hiero.block.simulator.BlockStreamSimulator`, which:

1. Creates and loads the application configuration using the Hiero Platform Configuration API.
2. Creates a Dagger component and instantiates `BlockStreamSimulatorApp` with its registered
   dependencies.
3. Starts `BlockStreamSimulatorApp`, which orchestrates the simulation - handling streaming rate
   and exit conditions via generic interfaces.

`BlockStreamSimulatorApp` consumes services injected by the Dagger component:

- **generator** - responsible for generating blocks; exposes the `BlockStreamManager` interface
- **grpc** - responsible for communication with the Block Node; exposes `PublishStreamGrpcClient`
  and `ConsumerStreamGrpcClient`
