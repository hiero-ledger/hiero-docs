# For EVM Developers Migrating to Hedera

## **Overview**

Transitioning to Hedera’s EVM implementation offers exciting opportunities but also introduces unique challenges. As an [EVM (Ethereum Virtual Machine)](../../../../support-and-community/glossary.md#ethereum-virtual-machine-evm) developer, you’ll find familiar Solidity smart contracts and EVM logic, but Hedera’s architecture, key management using [ED25519](../../../../support-and-community/glossary.md#ed25519), and enhanced capabilities such as system contracts introduced in [HIP-632](https://hips.hedera.com/hip/hip-632) necessitate adjustments to your workflows. This guide provides the insights and tools you need to migrate successfully.

### **What You’ll Learn**

This guide helps EVM developers understand key concepts and differences that impact how they deploy and manage contracts on Hedera:

<table><thead><tr><th width="177">Topic</th><th>Description</th></tr></thead><tbody><tr><td><a href="accounts-signature-verification-and-keys-ecdsa-vs.-ed25519.md#understanding-account-models-and-aliases"><strong>Hedera Account Model &#x26; Aliases</strong></a></td><td>Understand how Hedera’s account structure differs from the EVM, including ED25519 vs. ECDSA keys, dynamic key rotation, and how aliases enable compatibility.</td></tr><tr><td><a href="decimal-handling-8-vs.-18-decimals.md"><strong>Decimal Handling</strong></a></td><td>Learn how to handle differences between the EVM standard 18 decimals and Hedera’s 8 decimals, ensuring accurate token calculations and conversions.</td></tr><tr><td><a href="accounts-signature-verification-and-keys-ecdsa-vs.-ed25519.md#key-rotation-adapting-to-hederas-dynamic-model"><strong>Key Rotation</strong></a></td><td>Discover strategies for designing secure smart contracts that accommodate key updates, enhancing account flexibility and security.</td></tr><tr><td><a href="handling-hbar-transfers-in-contracts.md"><strong>HBAR Transfers</strong></a></td><td>Explore explicit handling of HBAR transactions in Solidity contracts, enabling you to manage native token flows on Hedera.</td></tr><tr><td><a href="json-rpc-relay-and-evm-tooling.md"><strong>JSON-RPC Relay</strong></a></td><td>Understand how Hedera’s JSON-RPC relay differs from the EVM RPC APIs, ensuring you can use familiar tooling with Hedera’s network.</td></tr></tbody></table>

***

### Additional Resources

* [**Getting Started for EVM Developers**](https://docs.hedera.com/hedera/getting-started)
* [**JSON-RPC Documentation**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay)
* [**Mirror Node API Documentation**](https://docs.hedera.com/hedera/sdks-and-apis/rest-api)
* [**Java SDK**](https://github.com/hashgraph/hedera-sdk-java)**,** [**Go SDK**](https://github.com/hashgraph/hedera-sdk-go)**,** [**JavaScript SDK**](https://github.com/hashgraph/hedera-sdk-js)
