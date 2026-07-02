# Quickstart of the Server

## Table of Contents

1. [Configuration](#configuration)
2. [Running locally](#running-locally)
   1. [Build the Server](#build-the-server)
   2. [Run the Server](#run-the-server)
   3. [Run the Server with Debug](#run-the-server-with-debug)
   4. [Stop the Server](#stop-the-server)

## Configuration

Refer to the [Configuration](../configuration.md) for configuration options.

## Running locally

- Server subproject qualifier: `:app`
- Assuming your working directory is the repo root

> **NOTE:** one may use the `-p` flag for `./gradlew` in order to avoid
> specifying the target subproject repeatedly on each task when running
> multiple tasks. When running only a single task, however, it is
> recommended to use the project qualifier (i.e. `:app:`) for
> both simplicity and clarity.

### Build the Server

> **NOTE:** if you have not done so already, it is
> generally recommended to build the entire repo first:
>
> ```bash
> ./gradlew clean build -x test
> ```

1. To quickly build the Server sources (without running tests), do the following:

   ```bash
   ./gradlew clean build -x test
   ```
2. Before building the server ensure your tests run successfully:

   ```bash
   ./gradlew clean qualityGate build runSuites
   ```
3. To build the Server docker image, do the following:

   ```bash
   ./gradlew :app:createDockerImage
   ```

### Run the Server

1. To start the Server, do the following:

   ```bash
   ./gradlew :app:startDockerContainer
   ```

### Run the Server with Debug

1. To start the Server with debug enabled, do the following:

   ```bash
   ./gradlew :app:startDockerDebugContainer
   ```
2. Attach your remote jvm debugger to port 5005.

### Stop the Server

1. To stop the Server do the following:

   ```bash
   ./gradlew :app:stopDockerContainer
   ```
