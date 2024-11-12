# Tokens Managed by Smart Contracts

A [smart contract](../../../support-and-community/glossary.md#smart-contract) is a programmable, self-executing agreement designed to create, manage, or enforce the conditions of digital assets, also known as tokens. Tokens managed by smart contracts serve as digital representations of various asset types, such as artwork, cryptocurrency, and carbon credits on the blockchain. These tokens allow assets to be securely transferred between users or contracts and interact with others, adding functionality and interoperability within the blockchain ecosystem.

The [ERC-20](../../../support-and-community/glossary.md#erc-20) and [ERC-721](../../../support-and-community/glossary.md#erc-721) standards provide common interfaces for token contracts to standardize how tokens function across platforms. These interfaces enable tokens to be easily recognized by wallets, exchanges, and decentralized applications (dApps) in the Ethereum ecosystem. By conforming to these standards, tokens gain a predictable structure, simplifying integration for developers and ensuring users experience consistent functionality across compatible smart contract platforms.

"ERC" stands for [Ethereum Request for Comments](../../../support-and-community/glossary.md#ethereum-request-for-comments-erc), a protocol developers can follow to propose improvements or introduce new guidelines to the Ethereum blockchain. Hedera smart contracts are compatible with several ERC standards, allowing developers to implement these standardized interfaces. This compatibility simplifies the token integration and provides a consistent user experience with token contracts across different platforms.

{% hint style="info" %}
With [HIP-218](https://hips.hedera.com/hip/hip-218) and [HIP-376](https://hips.hedera.com/hip/hip-376), Hedera provides the ability to treat native HTS tokens as if they were ERC-20 (if fungible)  or ERC-721 (if non-fungible) contracts. This ensures that developers have predictable functionality and minimal to not changes when bring their smart contracts to Hedera.
{% endhint %}

## Hedera-Compatible ERC Token Standards

Explore some of the token standards supported and compatible with Hedera:

**➡** [**ERC-20 (Fungible Tokens)**](erc-20-fungible-tokens.md)

**➡** [**ERC-721 Non-Fungible Tokens (NFTs)**](erc-721-non-fungible-tokens-nfts.md)

**➡** [**ERC-3643 Real World Assets (RWAs)** ](erc-3643-real-world-assets-rwa.md)

**➡** [**ERC-1363 Payable Tokens** ](erc-1363-payable-tokens.md)

***

## **Token Associations**

Before sending a token to a smart contract, you need to confirm whether you need to associate the token with the smart contract before transferring it. The transfer will fail if you transfer a token to a smart contract that was not associated with it first or does not have an open auto-association slot.&#x20;

You can associate a smart contract with a token in the following ways:

* Use the `TokenAssociationTransaction` in the supported Hedera SDKs
* Use the `associateToken()` or `associateTokens()` from [HIP-206](https://hips.hedera.com/hip/hip-206).

{% hint style="info" %}
**Note:** `Token association` is for HTS tokens only.
{% endhint %}

***

## Synthetic Events

Smart contract tokens like ERC-20 and ERC-721 emit events, creating contract logs that developers can query or subscribe to. Hedera Token Service (HTS) tokens are not inherently equipped with such event logs. As a solution to this limitation, Hedera Mirror Nodes now generates synthetic event logs for HTS tokens. Learn more [here](../../mirror-nodes/#synthetic-smart-contract-contract-logs).&#x20;

***

## FAQs

<details>

<summary>What should I consider when evaluating managing tokens via a smart contract (EVM)or natively on Hedera for my distributed application?</summary>

**Speed:** HTS transactions are native and offer faster execution time than a smart contract execution.

**Pricing:** Native services should be cheaper than the equivalent smart contract scenario.

</details>

<details>

<summary>Do I need to modify my existing contract on another Ethereum chain to use the Hedera token service system contract if my contract adheres to the ERC-720 or ERC-721 standard?</summary>

No, you do not need to modify your existing smart contract deployed to another EVM compatible chain.

</details>
