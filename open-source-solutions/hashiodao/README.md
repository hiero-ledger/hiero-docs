# HashioDAO

## **Introduction**

Distributed ledger technology (DLT) enables the shift from centralized control into the world of decentralization. Instead of having one central authority making all the decisions, decentralization gives the power and control back to the users. This enables new use cases in finance, governance, voting, and fundraising. In this documentation, you will learn about one use case: decentralized autonomous organizations (DAOs). By the end of this, you'll have a better understanding of what and how DAOs and HashioDAO function.

***

## What is a DAO?

A DAO is a collective where members can make decisions, like managing financial assets or controlling a smart contract. These communities operate without a central authority, so decision-making powers are distributed among members who collectively make decisions through proposals and voting processes within a decentralized governance model. All transactions and decisions are auditable and transparent as they are recorded on a public ledger.

The key components of a DAO include smart contracts for automating proposals and managing funds, governance tokens that represent voting rights, and decentralized governance, where members collectively make decisions through proposals and voting. These components work together to create a decentralized, transparent, and community-driven organization that operate autonomously on a public network, like Hedera.

***

## **What is HashioDAO?**

HashioDAO is a decentralized platform that offers a user-friendly interface for creating and managing DAOs. It provides customizable governance tokens, multisig capabilities, and treasury management tools. These features empower communities to define their governance model to best fit their preferences, simplifying voting and proposal management without the need for extensive technical knowledge. This opens up decentralized governance to a broader audience.

The platform offers three flexible governance models, so communities can choose the structure that fits their needs:

* **Governance Token**: This model gives decision-making powers to token holders, making it perfect for DAOs that have large communities. It may be more vulnerable to governance attacks if tokens are owned by a small group.
* **NFT**: This model gives decision-making powers to the holders of a specific NFT and allows more features than a regular token model like [soulbound tokens](../../support-and-community/glossary.md#soul-bound-token-sbt) or limiting the number of NFTs an account can own.
* **Multisig**: This model requires two or more members to authorize and sign transactions. This structure helps reduce the risk of governance attacks by sharing authority among select members, making it more of a centralized governance model.

The platform offers multiple proposal templates to get members involved in shaping the DAO's future and making decisions:

* **Text Proposal**: For presenting ideas or suggestions through written proposals.
* **Token** **Transfer Proposal**: For requesting treasury funds for projects and payments.
* **Token** **Associate Proposal**: For associating new tokens with the DAO's treasury.
* **Upgrade DAO Proposal:** For implementing smart contract upgrades or enhancing security features.

***

## HashioDAO: How It All Works Together

<figure><picture><source srcset="../../.gitbook/assets/hashiodao-contracts-system-dark-mode.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/hashiodao-contracts-system.png" alt=""></picture><figcaption></figcaption></figure>

HashioDAO provides a comprehensive architecture that utilize a system of smart contracts that work together to simplify DAO creation and management. The main components of the architecture and how it works:

1. **Factory Contracts**: The platform uses factory smart contracts ([FTDaoFactory](https://github.com/hashgraph/hedera-accelerator-defi-dex/blob/main/contracts/dao/FTDAOFactory.sol), [NFTDaoFactory](https://github.com/hashgraph/hedera-accelerator-defi-dex/blob/main/contracts/dao/NFTDAOFactory.sol), or [MultisigDaoFactory](https://github.com/hashgraph/hedera-accelerator-defi-dex/blob/main/contracts/dao/MultisigDAOFactory.sol)). A Factory smart contract is a design pattern used in decentralized applications (dApps) to allow efficient deployment of multiple smart contracts with similar functionalities. These factory contracts have been [audited by CertiK](https://skynet.certik.com/projects/swirlds-labs-dao-as-a-service).
2. **Treasury Contract:** When you call the `createDAO()` function, the factory contract creates the new DAO treasury smart contract based on the parameters you set for your token. This treasury contract holds and manages your DAO's funds, including the governance tokens and any other tokens you associate with it. It also ensures all predefined rules set by the governance model are enforced, like quorum requirements and executing proposals.
3. **Governor Contract:** Along with the treasury contract, the factory contract also creates a separate Governor contract for each DAO. This Governor contract handles everything related to proposals, like creating, voting, and executing them. When a proposal is created, the Governor contract stores all the details, like the description and voting options, on [InterPlanetary File System (IPFS)](../../support-and-community/glossary.md#interplanetary-file-system-ipfs) to keep things decentralized and secure.

So, once voting on a proposal ends, the Governor contract validates the results. If the proposal passes, the Governor contract tells the Treasury contract to execute the proposed actions, like transferring funds or associating new tokens. By using the factory pattern and IPFS, HashioDAO makes creating and managing DAOs efficient and decentralized.

***

## Getting Started with HashioDAO

To get started with using HashioDAO, complete these prerequisites:

* [Create](https://docs.hedera.com/hedera/getting-started/introduction#hedera-developer-portal-profile) a Hedera account.
* [Fund](https://docs.hedera.com/hedera/getting-started/introduction#hedera-faucet) the Hedera account.
* [Download](https://www.hashpack.app/download) the HashPack wallet extension.

{% hint style="info" %}
📣 **Note**: The cost of creating a DAO is 1 HBAR. It is recommended that you test out the features of [HashioDAO on Hedera Testnet](https://dao.web3nomad.org/) first. This is where you can experiment and familiarize yourself with the platform before jumping into [HashioDAO on Hedera Mainnet](https://www.hashgraph.com/hashiodao/).
{% endhint %}

Once you have completed the above, choose the governance model and follow the steps to create your first:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center">➡ <a href="governance-token-dao.md"><strong>Governance Token DAO</strong></a></td><td><a href="governance-token-dao.md">governance-token-dao.md</a></td></tr><tr><td align="center">➡ <a href="nft-dao.md"><strong>NFT DAO</strong></a></td><td><a href="nft-dao.md">nft-dao.md</a></td></tr><tr><td align="center">➡ <a href="multisig-dao.md"><strong>Multisig DAO</strong></a></td><td><a href="multisig-dao.md">multisig-dao.md</a></td></tr><tr><td align="center">➡ <a href="dao-proposals.md"><strong>DAO Proposals</strong></a></td><td><a href="dao-proposals.md">dao-proposals.md</a></td></tr></tbody></table>
