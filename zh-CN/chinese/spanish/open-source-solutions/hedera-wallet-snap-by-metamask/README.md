# Hedera Wallet Snap By MetaMask

## Overview

MetaMask is a popular Ethereum wallet and browser extension that developers can integrate into a variety of third-party applications. MetaMask Snaps is an open-source solution to enhance MetaMask's functionalities beyond its native capabilities. The [Hedera Wallet Snap](https://snaps.metamask.io/snap/npm/hashgraph/hedera-wallet-snap/), developed by [Tuum Tech](https://www.tuum.tech/) and managed by [Swirlds Labs](https://swirldslabs.com/), enables users to interact directly with the Hedera network without relying on [Hedera JSON-RPC Relay](https://github.com/hashgraph/hedera-json-rpc-relay), offering Hedera native functionalities like sending HBAR to different accounts and retrieving account information.

## What is a Snap?

MetaMask Snaps is an open-source framework allowing secure extensions to MetaMask, thus enhancing web3 user experiences. It empowers the addition of new API methods, supports various blockchain protocols, and tweaks existing functionalities via the Snaps JSON-RPC API

Snaps enable users to interact with new blockchains, protocols, and decentralized applications (dApps) beyond what is natively supported by MetaMask. The goal of the MetaMask Snaps system is to create a more open, customizable, and extensible wallet experience for users while fostering innovation and collaboration within the blockchain and decentralized application ecosystem.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p><strong>➡</strong> <a href="https://docs.tuum.tech/hedera-wallet-snap/basics/introduction"><strong>Hedera Wallet Snap Documentation</strong></a></p><p><a href="https://docs.tuum.tech/hedera-wallet-snap/basics/introduction"><strong>by Tuum Tech</strong></a></p></td><td><a href="https://docs.tuum.tech/hedera-wallet-snap/basics/introduction">https://docs.tuum.tech/hedera-wallet-snap/basics/introduction</a></td></tr><tr><td align="center"><strong>➡</strong> <a href="https://docs.hedera.com/hedera/tutorials/smart-contracts/metamask-hedera-wallet-snap-tutorial"><strong>Hedera Wallet Snap Tutorial</strong></a></td><td><a href="https://docs.hedera.com/hedera/tutorials/smart-contracts/metamask-hedera-wallet-snap-tutorial">https://docs.hedera.com/hedera/tutorials/smart-contracts/metamask-hedera-wallet-snap-tutorial</a></td></tr></tbody></table>

## FAQs

<details>

<summary><strong>What are the limitations for connecting MetaMask to the RPC vs the Snap?</strong></summary>

The Hedera JSON RPC Relay supports only the methods defined at [Hedera JSON RPC Relay Methods](https://playground.open-rpc.org/?schemaUrl=https%3A%2F%2Fraw.githubusercontent.com%2Fhashgraph%2Fhedera-json-rpc-relay%2Fmain%2Fdocs%2Fopenrpc.json), which are limited to Hedera Smart Contract Services. In contrast, the Hedera Wallet Snap uses the Hedera SDK to interact natively with the ledger, allowing the future support of a wider range of Hedera services like Hedera Token Service, Hedera Consensus Service, and Hedera File Service, beyond just smart contracts.

</details>

<details>

<summary><strong>How can I deploy a smart contract on Hedera using MetaMask?</strong></summary>

To deploy a smart contract on Hedera using MetaMask, you will need to use the [Hedera JSON RPC relay](https://docs.hedera.com/hedera/core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay). You can deploy using tools compatible with EVM-based chains. For detailed steps, refer to [Deploying Smart Contracts on Hedera](../../tutorials/smart-contracts/deploy-your-first-smart-contract.md).

</details>

<details>

<summary><strong>Can a signer created via ED25519 account be used for Ethereum-based transactions?</strong></summary>

No, you cannot use a signer created via ED25519 for Ethereum-based transactions due to the difference in cryptographic algorithms and key formats. EVM uses ECDSA with the secp256k1 curve, which is different from ED25519. For interacting directly with smart contracts on Hedera, only ECDSA-based accounts can be used.

</details>

<details>

<summary><strong>How can I delegate the signing process to MetaMask or WalletConnect when using Hedera SDK?</strong></summary>

Currently, there is no direct way to delegate the signing process to MetaMask or WalletConnect for transactions composed by the Hedera SDK, as they do not provide private keys of users.

</details>

<details>

<summary><strong>Can I use JSON RPC relay for transactions against Hedera native services?</strong></summary>

The Hedera JSON RPC relay exposes specific methods, as detailed in [Hedera JSON RPC Relay Methods](https://playground.open-rpc.org/?schemaUrl=https%3A%2F%2Fraw.githubusercontent.com%2Fhashgraph%2Fhedera-json-rpc-relay%2Fmain%2Fdocs%2Fopenrpc.json). You can use these methods for transactions with Hedera’s smart contracts. The Hedera Wallet Snap, using the Hedera SDK, can perform all Hedera transactions and will eventually support interactions with smart contracts as well.

</details>

<details>

<summary><strong>Why should I use the Snap instead of adding the Hashio RPC info to MetaMask?</strong></summary>

While Hashio RPC and other RPCs are limited to methods exposed by the Hedera JSON RPC relay, the Hedera Wallet Snap, using the Hedera SDK natively, offers access to all Hedera native features, including Hedera Token Service, Hedera File Service, and Hedera Consensus Service, enabling a broader range of interactions beyond smart contracts.

</details>

<details>

<summary><strong>Is the Snap more suited for custom Hedera functionalities like HCS and HFS, which can’t be done solely with MetaMask?</strong></summary>

Yes, that’s correct. The Hedera Wallet Snap is ideal for custom Hedera functionalities. It uses the Hedera SDK for all operations, allowing for native interactions with the full spectrum of Hedera’s offerings.

</details>
