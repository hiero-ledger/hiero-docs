# Hedera

## Hedera

Hedera is a public distributed ledger for building and deploying decentralized applications and microservices. You can use Hedera’s network services– Consensus, Tokens, Smart Contracts, and File Service–atop the [hashgraph consensus algorithm](core-concepts/hashgraph-consensus-algorithms/), to build applications with high throughput, fair ordering, and low-latency consensus finality in seconds without relying on centralized infrastructure.

The network is made up of permissioned nodes run by the [Hedera Governing Council](https://hedera.com/council), a group of term-limited enterprises that lead the network's direction. Over time the network will [move to a permissionless model](https://www.youtube.com/watch?v=QTNNYeSks-s).

{% hint style="info" %}
Hedera Token Service is now live on our networks. Learn more in the blog series, [Get started with the Hedera Token Service](https://hedera.com/blog/get-started-with-the-hedera-token-service-part-1). Hedera Token Service API information can be found in the SDK section [here](docs/hedera-api/token-service/).&#x20;
{% endhint %}

{% hint style="info" %}
Ready to submit your first transaction to a Hedera network? Visit our [Getting Started ](getting-started/introduction.md)section to learn the basics of how to create an account and transfer hbars :boom: .&#x20;
{% endhint %}

#### What is hashgraph?

Hashgraph is a distributed consensus algorithm and data structure that is fast, fair, and secure. This indirectly creates a trusted community, even when members do not necessarily trust each other. Hedera is the only authorized public network to use hashgraph. You can learn more about the consensus algorithm [here](core-concepts/hashgraph-consensus-algorithms/).

#### Under Development

The following features of the Hedera mainnet are still under development and therefore not yet available:

* State Proofs
  * An alpha version of the state proof is available [here](docs/mirror-node-api/rest-api.md#state-proof-alpha)
* Live Hashes
* Staking

### Have Feedback?

We're actively working on expanding documentation, examples, and constantly improving our SDK. We'd love to hear from you if a particular subject is confusing, or you have suggestions for an improvement, please let us know by suggesting an [edit](https://github.com/hashgraph/hedera-docs) or in the [discord chat](https://hedera.com/discord).

## Contributing Guide

Welcome to Hedera docs contribution guide! Check out ways you can contribute below :arrow\_down:.

All of the markdown files and images are stored in the hashgraph/hedera-docs repository.

### Overview

* **.gitbook/assets**
  * Contains the image files
* **/core-concepts**
  * Concepts someone new to Hedera should know about
* **/docs**
  * Technical documentation for the following:
    * Hedera API
    * Mirror Node API
    * SDKs
      * Java
      * Javascript
* **/mainnet**
  * Provides Hedera mainnet information including:
    * Mainnet nodes
    * Mirror nodes
    * Network services and support
* **/mirrornet**
  * Provides Hedera mirror node information including:
    * Community mirror nodes
    * Hedera ETL
    * Hedera Mirror Node
    * One-Click Mirror Node Deployment
    * Run Your Own Beta Mirror Node
* **/support-and-community**
  * Resources our developer community might be looking for
* **/testnet**
  * Testnet nodes
  * Mirror nodes
  * Network services and support
* **/resources**
  * Material to help you get started with one of our network services in more depth
* /**getting-started**
  * Examples that help you from your environment set-up to submitting your first transaction

### Ways You Can Contribute

If you find something that needs to be fixed, updated, or would like to publish additional content to the docs site you may do so by the following methods:

* Log an issue
  * You may submit an issue directly on `hedera-docs` repository&#x20;
* Create a pull request&#x20;
  * Fork the repository and submit a pull request that includes the suggested updates&#x20;

Issues and pull requests will be reviewed by the Hedera team.&#x20;
{% hint style="info" %}
Have a question?
[Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)&#x20;
{% endhint %}
