# Block Stream Simulator

## Table of Contents

1. [Overview](#overview)
2. [Project Design Structure](#project-design-structure)
3. [Configuration](#configuration)
4. [Quickstart](#quickstart)

## Overview

The Block Stream Simulator is designed to simulate block streaming for Hiero Hashgraph.
It uses various configuration sources and dependency injection to manage its components.

## Project Design Structure

Uses Dagger2 for dependency injection, the project has a modular structure and divides the Dagger dependencies into modules, but all modules used can be found at the root Injection Module:

```plaintext
src/java/com/hedera/block/simulator/BlockStreamSimulatorInjectionModule.java
```

Entry point for the project is `BlockStreamSimulator.java`, in wich the main method is located and has 2 functions:
1. Create/Load the Application Configuration, it does this using Hiero Platform Configuration API.
1. Create a DaggerComponent and instantiate the BlockStreamSimulatorApp class using the DaggerComponent and it registered dependencies.
1. Start the BlockStreamSimulatorApp, contains the orchestration of the different parts of the simulation using generic interfaces and handles the rate of streaming and the exit conditions.

The BlockStreamSimulatorApp consumes other services that are injected using DaggerComponent, these are:
1. **generator:** responsible for generating blocks, exposes a single interface `BlockStreamManager` and several implementations
1. BlockAsFileLargeDataSets: designed to work with GB folders with thousands of big blocks (since it has a high size block and volume of blocks, is useful for performace, load and stress testing)
1. **grpc:** responsible for the communication with the Block-Node, currently only has 1 interface `PublishStreamGrpcClient` and 1 Implementation, however also exposes a `PublishStreamObserver'

## Configuration

Refer to the [Configuration](configuration.md) for configuration options.

## Quickstart

Refer to the [Quickstart](quickstart.md) for a quick guide on how to get started with the application.
