# Supra Oracles

## Overview

[Supra](https://supra.com/) is a novel, high-throughput Oracle & IntraLayer offering a vertically integrated toolkit of cross-chain solutions. These solutions include data oracles, asset bridges, and automation networks that aim to interlink all public and private blockchains. Supra provides decentralized Oracle price feeds to deliver real-world data to web3 ecosystems through various on-chain and off-chain use cases. Oracles ensure that the data from the real world is accurate, which is crucial for decentralized applications (dApps) that rely on real-world, real-time data. This is important for dApps that need real-time information, such as the prices of cryptocurrencies and various other assets. The Supra x Hedera integration aims to bring speed, security, and accuracy to real-time data feeds, enhancing the functionality and reliability of dApps on the Hedera network.

## **Developer Considerations**

Contract calls, such as `eth_call` and `eth_estmateGas`, go to the mirror node, which limits those to a small data payload size. Our engineering team has successfully upgraded the API call data payload capacity to 24 KB. This enhancement is designed to fetch a single price pair from the data feed efficiently, ensuring a more streamlined retrieval process.

{% hint style="info" %}
**Please Note:** While this update offers improved performance to pull single price pairs, attempting to pull more than one price pair at a time may surpass the new 24 KB data payload limit. Should this limit be exceeded, the API will return an error message. We recommend structuring your API calls accordingly to avoid any potential disruptions.
{% endhint %}

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Official Documentation</strong></td><td><a href="../../.gitbook/assets/supra-oracles7134.jpg">supra-oracles7134.jpg</a></td><td><a href="https://docs.supra.com/">https://docs.supra.com/</a></td></tr><tr><td align="center"><strong>🔮 HBAR Price Feed</strong></td><td><a href="../../.gitbook/assets/hedera-logo-black.png">hedera-logo-black.png</a></td><td><a href="https://supra.com/data/details?instrumentName=hbar_usd&#x26;providerName=supra">https://supra.com/data/details?instrumentName=hbar_usd&#x26;providerName=supra</a></td></tr></tbody></table>

## Supra Demo on Hedera

<details>

<summary><strong>Data Feeds - Pull Model</strong></summary>

### Pull Model

This example shows how to use Supra Oracles real-world data feeds (Pull model). It fetches and verifies price data from Supra's gRPC server and use it within a smart contract on the Hedera network. These are key files:

[**`main.js`**](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-pull/blob/main/client/main.js)

This `main` script interfaces with the Supra Oracle to request price data proofs for a specified pair. It demonstrates the initialization of a PullServiceClient, the request for data proofs, and the interaction with a smart contract deployed on Hedera to deliver the obtained price data.

The script enables switching between the Hedera mainnet and testnet environments. It uses Web3.js and includes functions to sign and send transactions to Hedera, estimate gas, and extract price data.

[**`MockOracleClient.sol`**](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-pull/blob/main/smartcontract/MockOracleClient.sol)

This Solidity smart contract acts as a mock client for consuming Oracle pull data. It defines a structure for the price data and a function to receive and process the verified Oracle proof bytes.

### Try this Example on Your Browser with GitPod

1. Go to [this link](https://gitpod.io/#https://github.com/hedera-dev/hedera-example-supra-oracle-contract)
2.  Run the following commands on the terminal:

    `cd client` `npm init --y` `npm install`
3.  Rename the file `.env.SAMPLE` to `.env` and enter you Hedera network credentials (testnet/mainnet):

    `cp .env.SAMPLE .env`
4.  Run the `main.js` script:

    `node main.js`

You should see a console output similar to:

&#x20;[![Console Output](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-pull/raw/main/images/console_output.png)](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-pull/blob/main/images/console_output.png)

</details>

<details>

<summary><strong>Data Feeds - Push Model</strong></summary>

### Push Model

This example shows how to use Supra Oracles real-world data feeds (Push model).

[**`main.js`**](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-push/blob/master/main.js)

With this script, you start by deploying the [`ConsumerContract.sol`](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-push/blob/master/contracts/ConsumerContract.sol) and passing to its constructor the Supra storage contract address (`storageContractAddress`). Get the right storage address from: [https://supra.com/docs/data-feeds/decentralized/networks/](https://supra.com/docs/data-feeds/decentralized/networks/)

Then call the `getPrice` and/or `getPriceForMultiplePair` functions using the desired price pair indices. Get the right price pair indices from: [https://supra.com/docs/data-feeds/data-feeds-index/](https://supra.com/docs/data-feeds/data-feeds-index/)

### Try this Example on Your Browser with GitPod

1. Go to [this link](https://gitpod.io/#https://github.com/hedera-dev/hedera-example-supra-oracle-contract-push)
2.  Run the following commands on the terminal:

    `npm install`
3.  Rename the file `.env.SAMPLE` to `.env` and enter you Hedera network credentials (testnet/mainnet):

    `cp .env.SAMPLE .env`
4.  Run the `main.js` script:

    `node main.js`

[![Console Output](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-push/raw/master/images/console_output.png)](https://github.com/hedera-dev/hedera-example-supra-oracle-contract-push/blob/master/images/console_output.png)

</details>
