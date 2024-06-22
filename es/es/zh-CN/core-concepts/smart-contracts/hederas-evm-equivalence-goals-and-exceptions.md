# Hedera's EVM Equivalence Goals and Exceptions

Hedera aims to achieve [Ethereum Virtual Machine (EVM)](../../support-and-community/glossary.md#ethereum-virtual-machine-evm) equivalence to enhance the programmability and adoption of the network. This page outlines Hedera's EVM equivalence goals and highlights the key differences and exceptions between Hedera and [Ethereum](../../support-and-community/glossary.md#ethereum).

## Hedera's EVM Equivalence Goals

Hedera's EVM equivalence strategy is designed to empower developers by facilitating the execution of EVM smart contracts more rapidly and cost-effectively while ensuring seamless integration of Hedera’s native services. The key goals include:

#### Robust Application Programmability

Hedera aims to enable a wider range of application programmability without sacrificing its unique value. This means allowing developers to build a diverse range of applications on the Hedera network, from [decentralized finance (DeFi)](../../support-and-community/glossary.md#decentralized-finance-defi) applications to supply chain solutions, while still benefiting from the unique features of Hedera, such as its speed, security, and fairness.

#### Seamless Integration

Hedera plans to integrate its native services with the EVM seamlessly. This will allow developers to execute [smart contracts](../../support-and-community/glossary.md#smart-contract) more rapidly and cost-effectively. It will also make it easier for developers to use Hedera's services, as they can use the same tools and languages they are already familiar with from the Ethereum ecosystem.

#### Leverage Existing Expertise

Hedera's strategy involves leveraging the [HyperLedger Besu EVM](../../support-and-community/glossary.md#hyperledger-besu-evm) client, which allows developers to capitalize on their existing Solidity expertise. This means that developers already familiar with [Solidity](../../support-and-community/glossary.md#solidity) and the EVM can easily transition to developing on Hedera, taking advantage of its unique strengths such as faster transactions, low and fixed fees, fair transaction ordering, immediate finality, and heightened security.

#### Smooth Transition

Hedera aims to provide a seamless transition for developers already working with EVM-based platforms. This means making it easy for developers to move their existing applications, smart contracts, and other digital assets to the Hedera network. This goal aims to encourage the adoption of the Hedera network by reducing the barriers to entry for developers.

#### Support for EVM Tools

Hedera plans to support familiar [EVM tools](../../#evm-compatible-tools), libraries, and environments. This includes providing support for popular development environments like [Truffle](https://trufflesuite.com/) and [HardHat](https://hardhat.org/), as well as libraries like [Web3js](https://web3js.readthedocs.io/en/v1.10.0/) and [EthersJS](https://ethers.org/). The network also supports tools like [MetaMask](../../support-and-community/glossary.md#metamask), [The Graph](https://thegraph.com/), and [Remix](https://remix.ethereum.org/#lang=en\\\\&optimize=false\\\\&runs=200\\\\&evmVersion=null). This goal is to make it easier for developers to build on Hedera, as they can use the same tools they are already familiar with from the Ethereum ecosystem.

#### Community Engagement

Hedera encourages developers to contribute to its EVM development and join the community discussions in the official [Hedera Discord](https://www.hedera.com/discord). This goal aims to foster a vibrant and engaged developer community, which is crucial for the long-term success and sustainability of the Hedera network.

{% hint style="info" %}
🔔 Detailed [blog post](https://hedera.com/blog/evm-equivalence-unveiling-hederas-strategy-for-enhanced-programmability-and-network-adoption) of Hedera's EVM Equivalence strategy.
{% endhint %}

## Key Differences and Exceptions between Hedera and Ethereum

While Hedera strives for EVM equivalence, it's important to recognize certain unique aspects and fundamental differences in its network architecture and operations, such as the handling of state data structures, hashing algorithms, and the management of accounts and transactions. These distinctions in network behaviors are intentional design choices made to align with EVM standards, thereby achieving EVM compatibility. This approach ensures that while Hedera aligns closely with Ethereum, it also maintains its distinctive features and optimization.

### Network Differences

| Function                         | Hedera              | Ethereum             |
| -------------------------------- | ------------------- | -------------------- |
| **Network State Data Structure** | Virtual Merkle Tree | Merkle Patricia Trie |
| **Hashing Algorithm**            | SHA-384             | Keccak-256           |

### Accounts and Authorizations Differences

<table><thead><tr><th width="247.33333333333331">Function</th><th width="247">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Authorization Signatures</strong></td><td>Used for transaction authorization outside of smart contracts</td><td>Typically used within smart contracts</td></tr><tr><td><strong>Special System Accounts</strong></td><td>Available with unique properties</td><td>Not available</td></tr><tr><td><strong>Non-ECDSA Accounts</strong></td><td><p>Non-ECDSA accounts (such as ED or multi-key) are supported by Hedera and</p><p>ECDSA accounts are fully compatible</p></td><td>ECDSA accounts are supported by Ethereum and non-ECDSA accounts are not supported/compatible</td></tr><tr><td><strong>Account Deletion</strong></td><td>Possible</td><td>Not possible</td></tr></tbody></table>

### Contract Accounts Differences

<table><thead><tr><th width="244.33333333333331">Function</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Data Return on Static Calls</strong></td><td>Data retrieval must be done through the relay</td><td>Data returned directly</td></tr><tr><td><strong>Gas Fees</strong></td><td>Charges at least 80% of gas fees regardless of transaction outcome</td><td>Gas fees depend on transaction outcome but typically 100% of the gas fees are charged and the unused portion is credited back</td></tr><tr><td><strong>Contract Lifecycle</strong></td><td>Contract entities can expire, rent fees may apply</td><td>No expiration or rent fees</td></tr></tbody></table>

### Transactions and Queries Differences

<table><thead><tr><th width="244.33333333333331">Function</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Transaction Size Limit</strong></td><td>6kb</td><td>No limit</td></tr><tr><td><strong>Transaction Throttling</strong></td><td><a href="deploying-smart-contracts/#gas-limit">Transactions may be throttled by gas limits</a></td><td>Transactions pending until future submission</td></tr><tr><td><strong>Query Costs</strong></td><td>Not free, can use mirror node for free queries</td><td>Free read-only calls</td></tr><tr><td><strong>Mempools</strong></td><td>No <a href="../../support-and-community/glossary.md#mempool">mempools</a></td><td>Mempools available</td></tr></tbody></table>

### RPC Endpoint Differences

<table><thead><tr><th width="267">Function</th><th width="244">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>RPC Block Requests</strong> (e.g., <code>eth_getBlockByHash*</code> & <code>eth_getBlockByNumber</code> )</td><td>Return zero 32bytes hexadecimal value for the <code>stateRoot</code></td><td>Returns the <code>stateRoot</code> hexadecimal value of the final state trie of the block</td></tr></tbody></table>

{% hint style="info" %}
**Note**: Hedera Consensus and mirror nodes do not provide Ethereum RPC API endpoints.
{% endhint %}

### Tokens and Fee Differences

<table><thead><tr><th width="230.33333333333331">Function</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Native Tokens</strong></td><td>Supports native tokens in addition to <a href="tokens-managed-by-smart-contracts/">ERC-20 and ERC-721 token standards</a></td><td>All ERC token standards but primarily ERC-20 and ERC-721 tokens.</td></tr><tr><td><strong>Fee Structure</strong></td><td><a href="deploying-smart-contracts/#gas-schedule-and-fees">Complex with two different gas prices</a></td><td>Single gas price</td></tr><tr><td><strong>Token Association**</strong></td><td><a href="../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md">Concept of token association</a></td><td>No concept of token association</td></tr></tbody></table>

{% hint style="info" %}
**\*\*Note:** Token Association only applies to _native_ HTS tokens and does not affect ERC-20/721 tokens.
{% endhint %}

### Keys and Other Differences

<table><thead><tr><th width="234">Function</th><th width="274.3333333333333">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Keys for Token Functionality</strong></td><td>Keys control access to token functionality (<code>KYC</code>, <code>FREEZE</code>, <code>WIPE</code>, supply, fee, and <code>PAUSE</code>)</td><td>No equivalent <em>native</em> functionality</td></tr><tr><td><strong>Precheck Failures</strong></td><td><a href="../../sdks-and-apis/hedera-api/miscellaneous/responsecode.md">Multiple precheck failure reasons</a></td><td>Typically single failure reason</td></tr><tr><td><strong>Communication</strong></td><td>Requires communication with both consensus and mirror nodes</td><td>Direct communication with nodes</td></tr><tr><td><strong>HBAR Decimal Precision</strong></td><td>8 or 18<a href="../../sdks-and-apis/sdks/hbars.md#hbar-decimal-places"> (varies across Hedera APIs)</a></td><td>Consistent 18 point decimal precision</td></tr></tbody></table>
