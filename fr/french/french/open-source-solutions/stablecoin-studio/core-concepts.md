# Core Concepts

## Introduction

As the adoption of [cryptocurrencies](../../support-and-community/glossary.md#cryptocurrency) grows, a common obstacle deterring new adoption by users is price volatility, particularly when considering these assets for everyday transactions. Stablecoins offer a solution to this volatility by maintaining a consistent value, pegged to traditional assets like the US Dollar. Prominent examples include Tether (USDT), USD Coin (USDC), and Binance USD (BUSD). The stablecoin landscape also features asset-backed options such as MakerDAO's DAI, which is crypto-secured, and Paxos Gold (PAXG), backed by physical gold. These stable assets have not only transformed crypto trading by offering a secure place to store value temporarily but also have the potential to revolutionize e-commerce by providing a stable medium for online transactions.

> _**A**_ [_**stablecoin**_](../../support-and-community/glossary.md#stablecoin) _**is a specialized form of cryptocurrency engineered to maintain a stable value by pegging it to an external asset, such as a fiat currency like the US Dollar or a commodity like gold. By doing so, stablecoins seek to combine the programmability and ease of transfer inherent in cryptocurrencies with the price stability typically associated with traditional currencies. This low-volatility characteristic makes stablecoins particularly useful for transactions, cross-border payments, and as a stable asset within decentralized finance ecosystems.**_

Despite its many advantages, Hedera currently lacks a ready-to-use stablecoin framework. Stablecoin Studio aims to fill that void by offering a comprehensive stablecoin solution tailored to Hedera’s architecture. Developers will be equipped with a suite of resources, including essential tools, documentation, and sample code, allowing them to create applications that make use of stablecoins, such as digital wallets. The ultimate goal is to facilitate the seamless integration of stablecoins into a variety of platforms and applications, thereby boosting Hedera’s utility and adoption.

***

## Stablecoin Studio

Stablecoin Studio is an open-source solution that simplifies and enhances the granularity of access control and permission management when issuing [stablecoins](../../support-and-community/glossary.md#stablecoin) using Hedera network services. Utilizing a hybrid solution, the platform leverages both [Hedera Token Service (HTS)](../../support-and-community/glossary.md#hedera-token-service-hts) and [Hedera Smart Contract Service (HSCS)](../../support-and-community/glossary.md#hedera-smart-contract-service-hscs), offering [interoperability](../../support-and-community/glossary.md#interoperable) with [Solidity](../../support-and-community/glossary.md#solidity) Smart Contracts. This adds an extra layer of flexibility and capability for stablecoin issuers. As an all-in-one toolkit, this project enables stablecoin issuers to easily deploy applications and oversee operations via a comprehensive management toolkit, streamlining digital asset operations.

The toolkit offers [proof-of-reserve (PoR)](../../support-and-community/glossary.md#proof-of-reserves-por) functionality that utilizes existing systems or on-chain oracles to bolster the ability to provide transparency in disclosure while seamless custody provider integrations ease development and reduce time-to-market.

Complemented by advanced Hedera-native [Know Your Customer (KYC)](../../support-and-community/glossary.md#know-your-customer-kyc) / [Anti-Money Laundering (AML)](../../support-and-community/glossary.md#anti-money-laundering-aml) account flags and integrated service provider hooks, Stablecoin Studio gives issuers new ways to manage compliance and security.

***

## Core Components

The core components of Stablecoin Studio include these four components that help simplify stablecoin creation and management:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><a href="https://github.com/hashgraph/stablecoin-studio/blob/main/sdk/README.md"><strong>TypeScript SDK</strong></a></td><td>The SDK implements the features for creating, managing, and operating stablecoins. The SDK interacts with the Smart Contract and exposes an API to be used by client-facing applications.</td><td><a href="https://github.com/hashgraph/stablecoin-studio/blob/main/sdk/README.md">https://github.com/hashgraph/stablecoin-studio/blob/main/sdk/README.md</a></td><td><a href="../../.gitbook/assets/stablecoin-typescript-icon.png">stablecoin-typescript-icon.png</a></td></tr><tr><td><a href="web-ui-application.md"><strong>Web UI Application</strong></a></td><td>A visually rich platform that adds a layer of user-friendly interaction for creating. managing, and operating tokens. A dApp developed in React. Uses the SDK-exposed API.</td><td><a href="web-ui-application.md">web-ui-application.md</a></td><td><a href="../../.gitbook/assets/stablecoin-web-ui-Icon.png">stablecoin-web-ui-Icon.png</a></td></tr><tr><td><a href="cli-management.md"><strong>Command Line Interface (CLI)</strong></a></td><td>A command line interface (CLI) tool for creating, managing, and operating stablecoins. Uses the SDK-exposed API.</td><td><a href="cli-management.md">cli-management.md</a></td><td><a href="../../.gitbook/assets/stablecoin-command-line-tool.png">stablecoin-command-line-tool.png</a></td></tr><tr><td><a href="https://github.com/hashgraph/stablecoin-studio/blob/main/contracts/README.md"><strong>Smart Contracts</strong></a></td><td>A set of well-audited, open-source, smart contracts ready to be deployed for creating and managing stablecoins on Hedera network.</td><td><a href="https://github.com/hashgraph/stablecoin-studio/blob/main/contracts/README.md">https://github.com/hashgraph/stablecoin-studio/blob/main/contracts/README.md</a></td><td><a href="../../.gitbook/assets/smart-contracts-icon.png">smart-contracts-icon.png</a></td></tr></tbody></table>

***

## Robust Administration and Compliance Configurations

Stablecoin Studio includes Hedera-native token administration functionalities, enabling issuers to easily burn, mint (cash-in), freeze, wipe, and pause stablecoins. Account-based permissions, like native KYC account flags, provide compliance configurations when connecting Stablecoin Studio to custody providers and KYC/AML services.

#### Hedera Native Token Administration Functionalities

<table><thead><tr><th width="343">Functionality</th><th>Description</th></tr></thead><tbody><tr><td><strong>CashIn (Mint)</strong></td><td>Issuers can effortlessly create new tokens, increasing the total supply of the stablecoin. Minting is often subject to regulatory compliance and internal governance.</td></tr><tr><td><strong>Burn</strong></td><td>The platform allows issuers to reduce the overall token supply by 'burning' or destroying tokens, usually in a controlled and auditable manner.</td></tr><tr><td><strong>Freeze/Unfreeze</strong></td><td>For various compliance-related or operational reasons, Stablecoin Studio provides the option to freeze tokens, making them non-transferable until further actions are taken.</td></tr><tr><td><strong>Pause/Unpause</strong></td><td>The platform includes a <code>pause</code> functionality, enabling issuers to temporarily halt all token-related activities in emergencies or for scheduled maintenance.</td></tr><tr><td><strong>Wipe</strong></td><td>Stablecoin Studio allows issuers to <code>wipe</code> or delete tokens from specific accounts, mainly for regulatory or security reasons.</td></tr></tbody></table>

#### Account-Based Permissions and Compliance Features

<table><thead><tr><th width="343">Compliance Mechanisms</th><th>Description</th></tr></thead><tbody><tr><td><strong>Native KYC Account Flags</strong></td><td>Incorporate KYC checks directly into the token issuance and management process. These flags serve as compliance markers, indicating whether an account has been verified for KYC requirements.</td></tr><tr><td><strong>Integration with Custody Providers</strong></td><td>Stablecoin Studio is designed to connect seamlessly with third-party custody solutions, offering an extra layer of security for asset management.</td></tr><tr><td><strong>KYC/AML Service Connections</strong></td><td>Through native account flags and API integrations, Stablecoin Studio can be easily hooked into existing KYC/AML solutions, streamlining the compliance workflow.</td></tr></tbody></table>

***

## Ecosystem Integrations

The Stablecoin Studio SDK provides integrations into third-party providers who can fully support your stablecoin offering on the public Hedera network. These providers offer solutions for KYC/AML, custody, wallets, infrastructure management, smart contract monitoring, regulatory compliance, and more.

#### Custody Providers

<table data-view="cards"><thead><tr><th align="center"></th><th></th><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>🟠 Integration in progress</strong></td><td>Zodia specializes in institutional-grade digital asset custody, offering cutting-edge tech within a compliant framework. They primarily cater to corporate investors and institutions.</td><td align="center"><a href="https://zodia.io/"><strong>LEARN MORE</strong></a></td><td><a href="../../.gitbook/assets/stablecoin-zodia-logo.png">stablecoin-zodia-logo.png</a></td><td><a href="https://zodia.io/">https://zodia.io/</a></td></tr><tr><td align="center"><strong>🟠 Integration in progress</strong></td><td>DFNS offers a wallet-as-a-service infrastructure targeting crypto developers, allowing developers to focus on their applications by handling private key management.</td><td align="center"><a href="https://www.dfns.co/"><strong>LEARN MORE</strong></a></td><td><a href="../../.gitbook/assets/stablecoin-dfns-logo.png">stablecoin-dfns-logo.png</a></td><td><a href="https://www.dfns.co/">https://www.dfns.co/</a></td></tr><tr><td align="center"><strong>🟠 Integration in progress</strong></td><td>Hex Trust offers bank-grade digital asset custody. Led by experienced finance and tech experts, their Hex Safe™ platform provides an array of custody solutions.</td><td align="center"><a href="https://hextrust.com/"><strong>LEARN MORE</strong></a></td><td><a href="../../.gitbook/assets/hex-trust2.png">hex-trust2.png</a></td><td><a href="https://hextrust.com/">https://hextrust.com/</a></td></tr></tbody></table>

#### KYC/AML Service Providers

<table data-view="cards"><thead><tr><th align="center"></th><th></th><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Coming soon</strong></td><td>Elliptic offers a suite of compliance solutions designed to assist financial institutions. Services include: transaction monitoring for KYC/AML purposes, identity verification, and risk assessment.</td><td align="center"><a href="https://www.elliptic.co/"><strong>LEARN MORE</strong></a></td><td><a href="../../.gitbook/assets/stablecoin-elliptic-logo.png">stablecoin-elliptic-logo.png</a></td><td><a href="https://www.elliptic.co/">https://www.elliptic.co/</a></td></tr><tr><td align="center"><strong>Coming soon</strong></td><td>Merkle Science provides transaction monitoring and intelligence solutions for cryptoasset service providers, financial institutions, and government agencies to detect, investigate, and prevent money laundering.</td><td align="center"><a href="https://www.merklescience.com/"><strong>LEARN MORE</strong></a></td><td><a href="../../.gitbook/assets/stablecoin-merkle-science-logo.png">stablecoin-merkle-science-logo.png</a></td><td><a href="https://www.merklescience.com/">https://www.merklescience.com/</a></td></tr></tbody></table>

#### Oracle Provider

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th></th><th></th><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center">🟢 <strong>Available</strong></td><td></td><td>HashPort Pro offers an advanced middleware toolkit for enterprises deploying applications on Hedera, using its Validator Swarm for transaction verification. Axiom, a feature of HashPort Pro, is an on-chain oracle linking to banking and fintech for price and reserve data.</td><td align="center"><a href="https://www.hashport.network/contact/"><strong>CONTACT FOR INTEGRATION</strong></a></td><td><a href="../../.gitbook/assets/stablecoin-pro-axiom-logo.png">stablecoin-pro-axiom-logo.png</a></td><td><a href="https://www.hashport.network/contact/">https://www.hashport.network/contact/</a></td></tr></tbody></table>

***

## Proof-of-Reserve (PoR) for Treasury Management

To ensure the security and transparency of your stablecoin, Stablecoin Studio offers a proof-of-reserve (PoR) feature. This feature serves two key objectives: preventing under-collateralization and providing transparent asset backing for your stablecoin. The PoR system employs automated verification, where [off-chain ](../../support-and-community/glossary.md#off-chain)assets are continually monitored through [oracles](../../support-and-community/glossary.md#oracles) or similar mechanisms. These monitoring systems subsequently update a dedicated reserve contract to reflect changes in the asset value accurately.

Before you mint new tokens, a [smart contract](../../support-and-community/glossary.md#smart-contract) automatically checks this reserve contract. Minting is authorized only when sufficient reserve assets are available to back the new tokens. For instance, let's say you have enabled PoR with a reserve of 1,000 units. You can comfortably mint up to 900 tokens. However, if you attempt to mint an additional 100 tokens, the smart contract will halt the process due to insufficient reserves. You also have the flexibility to update these reserves either manually or programmatically as per your operational requirements.

Lastly, when using Stablecoin Studio, you can link an existing Oracle-fed reserve contract or let the platform automatically deploy a new one. Both options aim to guarantee the stability of your stablecoin and foster user trust.

<figure><img src="../../.gitbook/assets/stablecoin-concepts-proof-of-reserve.png" alt=""><figcaption><p>Proof of Reserve: How does it work?</p></figcaption></figure>

##

***

## Stablecoin Studio: How It All Works Together

At a functional level, Stablecoin Studio provides a comprehensive architecture that enables developers to create, manage, and operate stablecoins on the Hedera network using pre-built “factory smart contracts” [audited by CertiK.](https://files.hedera.com/stablecoin-studio-audit.pdf) The architecture consists of several components that work together to facilitate the deployment and management of stablecoins:

1. **SDK**: The Software Development Kit (SDK) provides a set of tools and libraries for developers to build and deploy stablecoin applications on the Hedera network. It simplifies the process of interacting with Stablecoin Studio’s “factory contracts” used to create and manage stablecoins.
2. **CLI**: The Command Line Interface (CLI) offers a user-friendly way for developers to interact with the SDK and manage stablecoins. It allows developers to perform various operations, such as creating, managing, and operating stablecoins using simple commands.
3. **Web UI**: The User Interface (UI) component provides a graphical interface for developers and users to interact with stablecoins on the Hedera network. It simplifies the process of managing and operating stablecoins for non-technical users.

When you initiate the creation process using the SDK, CLI, or Web UI, two smart contracts are deployed: The Hedera Token Manager Proxy and the Hedera Token Manager. The former oversees the ownership of the latter, which in turn is responsible for managing the permissions and functionalities of your stablecoin.

As a stablecoin admin/delegate, you’ll manage your stablecoin using the Hedera Token Manager smart contract, offering granular control and flexibility. End-users will not need to navigate the complexities of smart contracts — they can interact directly with your stablecoin using the Hedera Token Service (HTS), ensuring a more convenient, cost-effective, and scalable experience.

<figure><img src="../../.gitbook/assets/stablecoin-concepts-how-it-works.png" alt=""><figcaption><p>Stablecoin Studio Work Flow/Process</p></figcaption></figure>
