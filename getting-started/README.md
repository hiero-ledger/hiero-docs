# Getting Started with Hiero

This Getting Started guide introduces the essential tools and workflows to begin building on decentralized networks using Hiero SDKs. Hiero enables developers to run a complete local environment—including consensus, mirror, and JSON-RPC services—without needing access to a live network. This setup supports fast iteration, full-stack testing, and flexible development across multiple environments.

### What's Included

* ✅ Set up and run a local node using Docker and the Hiero CLI
* ✅ Generate local accounts (ECDSA, ED25519, Alias) with default balances
* ✅ Interact with consensus and mirror nodes in your development environment
* ✅ Access and monitor your local network with endpoints and dashboards

Each tutorial walks through the full transaction lifecycle—from creating, signing, and submitting transactions via the consensus node to verifying the results with a mirror node and viewing them in your local network explorer.

***

## Set Up a Local Node

Use Docker and the Hiero CLI to spin up a complete local network on your machine. This includes a consensus node, mirror node, JSON-RPC relay, and other supporting services.

**👉** [**Set up a local node with Docker**](how-to-set-up-a-hedera-local-node.md)

***

## Use the NPM CLI Tool

Prefer working from the command line? You can install and run the Hiero local node using the official NPM package. This lets you start, stop, and generate accounts directly via CLI commands.

**👉** [**Run the Hiero node using the NPM CLI**](setup-hedera-node-cli-npm.md)

***

## Cloud Development Environments (CDEs)

If you don’t want to run Docker locally or want to develop from any device, you can use a Cloud Development Environment (CDE) like Gitpod or GitHub Codespaces. These environments let you spin up a virtual dev environment with the Hiero node preconfigured.

**👉** [**Run the node in a cloud dev env**ironment](how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/)
