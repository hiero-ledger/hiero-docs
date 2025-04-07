# Getting Started with Hiero

Welcome to the Hiero Getting Started section where you'll find a collection of step-by-step guides designed to help you set up and explore a fully functional Hiero local node (network). The [**Hiero Local Node**](https://github.com/hashgraph/hedera-local-node) project empowers developers to deploy their own local network for development and testing. This network includes essential services such as the consensus node, mirror node, JSON-RPC relay, and more that can be deployed using multiple methods.

**➡** [**Deploy Your Hiero Local Node**](./#deploy-your-hiero-local-node)

**➡** [**Available Services and Dashboards**](./#available-services-and-dashboards)

***

## Deploy Your Hiero Local Node

Choose to deploy between Docker with the Hiero CLI, managing your node via the official NPM package, or leveraging Cloud Development Environments (CDEs) like Gitpod or GitHub Codespaces. These guides are designed to help you quickly and efficiently establish a testing environment. This flexibility enables you to work from any device without being tied down by a static local setup.

<table data-view="cards"><thead><tr><th align="center"></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="how-to-set-up-a-hedera-local-node.md"><strong>Set Up with Docker</strong></a></td><td>Use Docker and the Hiero CLI to spin up a complete local network on your machine. This includes a consensus node, mirror node, JSON-RPC relay, and other supporting services.</td><td><a href="../.gitbook/assets/hiero-docker-icon.png">hiero-docker-icon.png</a></td><td><a href="how-to-set-up-a-hedera-local-node.md">how-to-set-up-a-hedera-local-node.md</a></td></tr><tr><td align="center"><a href="setup-hedera-node-cli-npm.md"><strong>Use the NPM CLI Tool</strong></a></td><td>Use the CLI tool to install and run the Hiero local node using the official NPM package. This lets you start, stop, and generate accounts directly via CLI commands.</td><td><a href="../.gitbook/assets/hiero-cli-tool-icon.png">hiero-cli-tool-icon.png</a></td><td><a href="setup-hedera-node-cli-npm.md">setup-hedera-node-cli-npm.md</a></td></tr><tr><td align="center"><a href="how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/"><strong>Use Cloud Development Environments (CDEs)</strong></a></td><td>Use a Cloud Development Environment (CDE) like Gitpod or GitHub Codespaces to build a virtual dev environment with the preconfigured Hiero node.</td><td><a href="../.gitbook/assets/hiero-cloud-env-icon.png">hiero-cloud-env-icon.png</a></td><td><a href="how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/">how-to-run-hedera-local-node-in-a-cloud-development-environment-cde</a></td></tr></tbody></table>

***

## **Available Services and Dashboards**

The Hiero local node comes with various services, each serving different functions, and accessible locally. You can use these services on `localhost`.&#x20;

{% hint style="info" %}
In Gitpod and Codespaces, "localhost" refers to a virtual cloud server you're accessing via your browser. These platforms redirect local addresses to your cloud workspace, making it feel like you're working on a local setup.
{% endhint %}

These are the `localhost` endpoints for each service:

<table><thead><tr><th width="327.8828125">Type</th><th>Endpoint</th></tr></thead><tbody><tr><td>Consensus Node Endpoint</td><td><a href="http://localhost:50211/">http://localhost:50211/</a></td></tr><tr><td>Mirror Node GRPC Endpoint</td><td><a href="http://localhost:5600/">http://localhost:5600/</a></td></tr><tr><td>Mirror Node REST API Endpoint</td><td><a href="http://localhost:5551/">http://localhost:5551/</a></td></tr><tr><td>JSON RPC Relay Endpoint</td><td><a href="http://localhost:7546/">http://localhost:7546/</a></td></tr><tr><td>JSON RPC Relay Websocket Endpoint</td><td><a href="http://localhost:8546/">http://localhost:8546/</a></td></tr><tr><td>Mirror Node Explorer</td><td><a href="http://localhost:8080/devnet/dashboard">http://localhost:8080/devnet/dashboard</a></td></tr><tr><td>Grafana UI</td><td><a href="http://localhost:3000/">http://localhost:3000/</a></td></tr><tr><td>Prometheus UI</td><td><a href="http://localhost:9090/">http://localhost:9090/</a></td></tr></tbody></table>
