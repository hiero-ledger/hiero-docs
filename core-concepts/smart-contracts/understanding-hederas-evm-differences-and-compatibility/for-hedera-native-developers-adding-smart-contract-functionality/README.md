# For Hedera-Native Developers Adding Smart Contract Functionality

## **Introduction**

As a Hedera-native developer, you are already familiar with Hedera’s features, such as ED25519-based key management, the Hedera Token Service (HTS), the Hedera Consensus Service (HCS), and workflows enabled by Hedera's SDKs. Integrating smart contracts into your existing workflows by leveraging Hedera’s EVM implementation allows you to embed on-chain logic directly into Hedera-native applications. This guide outlines the key considerations for adding EVM-compatible smart contract functionality without losing the performance and security benefits of Hedera’s architecture.

***

### **What You'll Learn**

<table><thead><tr><th width="273">Topic</th><th>Description</th></tr></thead><tbody><tr><td><strong>Cross-Chain Compatibility</strong></td><td>Manage ECDSA and ED25519 key types to enable interoperability with EVM-based ecosystems.</td></tr><tr><td><strong>State Management</strong></td><td>Adapt to Hedera’s off-chain state model, leveraging mirror nodes and event logs for querying and validation.</td></tr><tr><td><strong>Token Management</strong></td><td>Extend the Hedera Token Service with custom on-chain logic for minting, burning, and transferring tokens.</td></tr><tr><td><strong>Signature Verification</strong></td><td>Implement robust authorization mechanisms using <code>isAuthorized</code> and <code>isAuthorizedRaw</code> system contract functions.</td></tr></tbody></table>

### **Why Add Smart Contract Functionality?**

Adding Hedera's EVM-compatible smart contract functionality  allows developers to:

* Build custom logic directly into your applications without relying solely on external SDKs.
* Connect and interact with other EVM-compatible chains and tools.&#x20;
* Combine Solidity’s flexibility with Hedera’s predictable cost model, high throughput, and finality guarantees.

***

## Additional Resources

* [**JSON-RPC Relay Guide**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay)
* [**Mirror Node API Reference**](https://docs.hedera.com/hedera/sdks-and-apis/rest-api)
* [**Hedera Token Service Documentation**](https://docs.hedera.com/hedera/core-concepts/tokens)
