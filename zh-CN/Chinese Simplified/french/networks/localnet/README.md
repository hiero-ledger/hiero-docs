# Localnet

## Introduction

The Hedera Localnet provides developers with a comprehensive framework for locally testing and refining Hedera-based applications. By operating outside the public networks, Localnet is crucial in the software development lifecycle, eliminating network I/O bottlenecks, minimizing shared resource conflicts, and offering complete control over network configurations. This local network comprises of two primary product offerings, [Local Node](https://github.com/hashgraph/hedera-local-node) and [Solo](https://github.com/hashgraph/solo), each serving distinct purposes in the development and testing process. For developers just getting started with Local Node, here is the recommended path for testing:

<figure><img src="../../.gitbook/assets/localnet-dev-testing-path.png" alt="" width="375"><figcaption><p>Hedera Local Node Testing Workflow</p></figcaption></figure>

1. **Local Node (single & multinode mode)**: Begin by testing your initial prototype on a local node. This step allows for quick iterations and debugging in a controlled environment. If your app needs to handle more complex scenarios, run the _Multinode configuration_.
2. **Solo**: Then move to _Solo_ for advanced testing under realistic network conditions.
3. **Previewnet**: Then test on Hedera Previewnet for latest/bleeding edge/upcoming code verification.
4. **Testnet**: Lastly, testing on the test network for stable code verification before deploying it on Hedera Mainnet.

***

## Local Node

### Overview

The Local Node replicates a Hedera network comprised of a single node (or few if configured) on a developer's local machine, offering a controlled environment for developing, testing, and experimenting with [decentralized applications (dApps)](../../support-and-community/glossary.md#decentralized-application-dapp). This local setup utilizes [Docker Compose](https://docs.docker.com/compose/) to create multiple containers, each with a specific role within the network, including but not limited to:

- **Consensus Node**: Simulates the behavior of Hedera’s consensus mechanism, processing/ordering transactions and participating in the network’s consensus algorithm.
- [**Mirror Node**](../../support-and-community/glossary.md#mirror-nodes)**:** Provides access to the historical data, transaction records, and the current state of the network without participating in consensus. This is useful for querying and analytics.
- [**JSON-RPC Relay**](../../support-and-community/glossary.md#json-rpc-relay): Offers a local JSON-RPC implementation of the [Ethereum](../../support-and-community/glossary.md#ethereum) JSON-RPC APIs for Hedera to enable interactions with smart contracts and accounts. This is particularly useful for developers familiar with Ethereum tooling and ecosystem.
- [**Mirror Node Explorer**](../../support-and-community/glossary.md#network-explorer): A web-based interface that allows developers to audit transactions, accounts, and other network activities visually.

### Setup and Configuration

Single-node configuration simulates the network's functions on a smaller scale (on a single node), ideal for debugging, testing, and prototype development. Multi-node configuration distributes multiple instances of the Hedera network nodes across a single machine using Docker containers, intended for advanced testing and network emulation.

➡ [**Single Node Configuration**](single-node-configuration.md)

➡ [**Multinode Configuration**](multinode-configuration.md)

### Operational Modes

Local Node offers two modes depending on a developer’s needs:

<details>

<summary><strong>Full mode</strong></summary>

Full mode is activated with the `--full` flag, and the system is designed to capture and store comprehensive data. Here's how it works:

- **Data Upload**: Each node within the network generates record stream files during operation. Record stream files are a sequence of transaction records grouped together over a specific interval. The Hedera network periodically consolidates these transaction records into stream files, which are then made available to the network nodes and mirror nodes. In full mode, these files are systematically uploaded to their own directory within the `minio` bucket. MinIo is an object storage platform that provides dedicated tools for storing, retrieving, and searching blobs. This process is managed by specific uploader containers assigned to each node, namely:
  - `record-streams-uploader-N`(contains record streams)
  - `account-balances-uploader-N` (contains account balances files)
  - `record-sidecar-uploader-N` (contains a list of `TransactionSidecarRecords` that were all created over a specific interval and related to the same `RecordStreamFile`.

</details>

<details>

<summary><strong>Turbo mode</strong></summary>

Turbo mode is the default setting when running the local node. This mode prioritizes efficiency and speed, with the following key characteristics:

- **Local Data Access**: Instead of uploading data to the cloud, record stream files are read directly from their corresponding local directories on each node. This method significantly reduces latency and resource consumption, making it ideal for scenarios where immediate data access and high performance are prioritized over long-term storage and external accessibility.

</details>

With these two options, users can tailor the local node's operation to suit their needs best, whether ensuring comprehensive data capture and backup or optimizing for performance and speed.

***

## Solo

Solo offers an advanced private network testing solution and adopts a Kubernetes-first strategy to create a network that fully mimics a production environment. Explore the Solo repository [here](https://github.com/hashgraph/solo).

_**More info coming soon...**_

***

## Additional Resources

- [**Hedera Local Node Repo**](https://github.com/hashgraph/hedera-local-node)
- [**Docker Compose Documentation**](https://docs.docker.com/compose/intro/features-uses/)
- [**Run a Local Node in Gitpod**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/gitpod.md) **\[tutorial]**
- [**Run a Local Node in Codespaces**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/codespaces.md) **\[tutorial]**
