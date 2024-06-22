# Tokens Managed by Smart Contracts

[Smart contracts](../../../support-and-community/glossary.md#smart-contract) can be used to create, manage, or serve as the description of tokens. A token is a digital representation of an asset that can include artwork, [cryptocurrency](../../../support-and-community/glossary.md#cryptocurrency), carbon credits, etc.

The [ERC-20](../../../support-and-community/glossary.md#erc-20) and [ERC-721](../../../support-and-community/glossary.md#erc-721) standards define a common interface for token contracts, enabling interoperability between wallets, exchanges, and standardized interaction between different smart contracts and [decentralized applications (dApps)](../../../support-and-community/glossary.md#decentralized-application-dapp) in the Ethereum ecosystem. ERC stands for Ethereum Request for Comments, where developers can propose improvements, new features, and protocols for the Ethereum blockchain.

Implementing these interfaces simplifies the process of integrating tokens into applications for developers and ensures consistent user interactions with token contracts. Hedera smart contracts support the following ERCs:

**➡** [**ERC-20 (Fungible Tokens)**](erc-20-fungible-tokens.md)

**➡** [**ERC-721: Non-Fungible Tokens (NFTs)**](erc-721-non-fungible-tokens-nfts.md)

## **Token Associations**

Before sending a token to a smart contract, you need to confirm whether you need to associate the token with the smart contract before transferring it. The transfer will fail if you transfer a token to a smart contract that was not associated with it first or does not have an open auto-association slot.\&#x20

You can associate a smart contract with a token in the following ways:

- Use the `TokenAssociationTransaction` in the supported Hedera SDKs
- Use the `associateToken()` or `associateTokens()` from [HIP-206](https://hips.hedera.com/hip/hip-206).

{% hint style="info" %}
**Note:** `Token association` is for HTS tokens only.
{% endhint %}

## Synthetic Events

Smart contract tokens like ERC-20 and ERC-721 emit events, creating contract logs that developers can query or subscribe to. Hedera Token Service (HTS) tokens are not inherently equipped with such event logs. As a solution to this limitation, Hedera Mirror Nodes now generates synthetic event logs for HTS tokens. Learn more [here](../../mirror-nodes/#synthetic-smart-contract-contract-logs).\&#x20

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
