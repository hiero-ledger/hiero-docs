# Understanding Hedera's EVM Differences and Compatibility

## **Introduction to Hedera's EVM Implementation**

Hedera’s EVM-compatible environment enables developers to deploy Solidity smart contracts while benefiting from Hedera’s advanced distributed ledger technology (DLT). Built on hashgraph consensus, Hedera offers unparalleled throughput, deterministic finality, and predictable transaction fees.&#x20;

However, transitioning to Hedera from Ethereum's EVM requires understanding key architectural differences, tokenomics, and tooling. These differences present both challenges and opportunities, which this guide will help you navigate.

This guide is for:

* **EVM developers migrating to Hedera:** If you are experienced with EVM smart contracts, this guide helps you understand key differences in Hedera’s architecture, tokenomics, and tooling.
* **Hedera-native developers adding smart contract functionality:** If you are familiar with Hedera services and now want to integrate smart contract functionality into your [dApp](../../../support-and-community/glossary.md#decentralized-application-dapp), this guide shows you how these contracts interact with Hedera’s native ecosystem.

## **High-Level Differences: Hedera vs. Ethereum**

The following table highlights foundational differences that may affect your smart contract development workflow:

<table><thead><tr><th width="149">Feature</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Consensus Mechanism</strong></td><td>Asynchronous Byzantine Fault Tolerance (aBFT), Proof of Stake (PoS)</td><td>Byzantine Fault Tolerance (BFT), Proof of Stake (PoS)</td></tr><tr><td><strong>Transaction Fees</strong></td><td>Low and predictable <a href="../../../networks/mainnet/fees/">fees</a></td><td>Variable gas fees; can be high during network congestion</td></tr><tr><td><strong>Governance Model</strong></td><td>Governed by the Hedera Governing Council, comprising leading global organizations</td><td>Decentralized; governed by the Ethereum community</td></tr><tr><td><strong>Native Token</strong></td><td>HBAR</td><td>ETH</td></tr><tr><td><strong>Token Standard</strong></td><td>Supports ERC-20 and ERC-721 standards, with Hedera Token Service (HTS) for native token issuance and management without smart contracts</td><td>ERC-20 and ERC-721 standards for fungible and non-fungible tokens</td></tr><tr><td><strong>Network State Data Structure</strong></td><td>Virtual Merkle Tree</td><td>Merkle Patricia Trie</td></tr><tr><td><strong>Historical Data</strong></td><td>Off-chain mirror nodes provide access to historical data and state queries</td><td>On-chain <code>stateRoot</code>; historical data can be accessed through the blockchain</td></tr><tr><td><strong>Key Management</strong></td><td>Supports <a href="../../../support-and-community/glossary.md#ed25519">ED25519</a> (Hedera-native accounts) <a href="../../../support-and-community/glossary.md#ecdsa-secp256k1">ECDSA  (secp256k1)</a>, and complex keys (keylist and threshold).</td><td>Accounts are managed using ECDSA (secp256k1) keys</td></tr><tr><td><strong>Network Upgrades</strong> </td><td>Upgrades are proposed through Hedera Improvement proposals (HIPs) and governed by the Hedera Governing Council. Upgrades are backward compatible, not forks.</td><td>Upgrades are proposed and implemented through Ethereum Improvement Proposals (EIPs)</td></tr></tbody></table>

***

## Next

Choose your tailored path below:

* [**EVM Developers Migrating to Hedera**](for-evm-developers-migrating-to-hedera/)
* [**Hedera-Native Developers Adding Smart Contracts**](for-hedera-native-developers-adding-smart-contract-functionality/)
