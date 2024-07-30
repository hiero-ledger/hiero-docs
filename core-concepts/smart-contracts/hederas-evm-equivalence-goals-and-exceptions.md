# Understanding Hedera for EVM Developers

## Hedera vs Ethereum

While Hedera strives for EVM equivalence, it's important to recognize certain unique aspects and fundamental differences in its network architecture and operations, such as the handling of state data structures, hashing algorithms, and the management of accounts and transactions. These distinctions in network behaviors are intentional design choices made to align with EVM standards, thereby achieving EVM compatibility. This approach ensures that while Hedera aligns closely with Ethereum, it also maintains its distinctive features and optimization.

### Network and Security Differences

<table><thead><tr><th width="211">Function</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Network State Data Structure</strong></td><td>Virtual Merkle Tree</td><td>Merkle Patricia Trie</td></tr><tr><td><strong>Hashing Algorithm</strong></td><td>SHA-384</td><td>Keccak-256</td></tr><tr><td><strong>Security</strong></td><td>High security with aBFT</td><td>Secure with decentralized PoS network</td></tr></tbody></table>

### Account and Authorization Differences

<table><thead><tr><th width="202.33333333333331">Function</th><th width="296">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Authorization Signatures</strong></td><td>Used for transaction authorization outside of smart contracts</td><td>Typically used within smart contracts</td></tr><tr><td><strong>Special System Accounts</strong></td><td>Available with unique properties</td><td>Not available</td></tr><tr><td><strong>Non-ECDSA Accounts</strong></td><td><p>Non-ECDSA accounts (such as ED or multi-key) are supported by Hedera and</p><p>ECDSA accounts are fully compatible</p></td><td>ECDSA accounts are supported by Ethereum and non-ECDSA accounts are not supported/compatible</td></tr><tr><td><strong>Account Deletion</strong></td><td>Possible</td><td>Not possible</td></tr></tbody></table>

### Contract and Gas Differences

<table><thead><tr><th width="210.33333333333331">Function</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Data Return on Static Calls</strong></td><td>Data retrieval must be done through the relay</td><td>Data returned directly</td></tr><tr><td><strong>Gas Fees</strong></td><td>Charges at least 80% of gas fees regardless of transaction outcome</td><td>Gas fees depend on transaction outcome but typically 100% of the gas fees are charged and the unused portion is credited back</td></tr><tr><td><strong>Contract Lifecycle</strong></td><td>Contract entities can expire, rent fees may apply</td><td>No expiration or rent fees</td></tr></tbody></table>

### Transactions and Queries Differences

<table><thead><tr><th width="212.33333333333331">Function</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Transaction Size Limit</strong></td><td>6kb</td><td>No limit</td></tr><tr><td><strong>Transaction Throttling</strong></td><td><a href="deploying-smart-contracts/#gas-limit">Transactions may be throttled by gas limits</a></td><td>Transactions pending until future submission</td></tr><tr><td><strong>Query Costs</strong></td><td>Not free, can use mirror node for free queries</td><td>Free read-only calls</td></tr><tr><td><strong>Mempools</strong></td><td>No <a href="../../support-and-community/glossary.md#mempool">mempools</a></td><td>Mempools available</td></tr><tr><td><strong>Cost</strong></td><td>Low, predictable fees (fraction of a cent)</td><td>Variable, often high gas fees</td></tr></tbody></table>

### RPC Endpoint and Communication Differences

<table><thead><tr><th width="259">Function</th><th width="244">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>RPC Block Requests</strong> (e.g., <code>eth_getBlockByHash*</code> &#x26; <code>eth_getBlockByNumber</code> )</td><td>Return zero 32bytes hexadecimal value for the <code>stateRoot</code></td><td>Returns the <code>stateRoot</code> hexadecimal value of the final state trie of the block</td></tr><tr><td><strong>Communication</strong></td><td>Requires communication with both consensus and mirror nodes</td><td>Direct communication with nodes</td></tr></tbody></table>

{% hint style="info" %}
**Note**: Hedera Consensus and mirror nodes do not provide Ethereum RPC API endpoints.
{% endhint %}

### Token and Fee Differences

<table><thead><tr><th width="232.33333333333331">Function</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td> <strong>Native Tokens</strong></td><td>Supports native tokens in addition to <a href="tokens-managed-by-smart-contracts/">ERC-20 and ERC-721 token standards</a></td><td>All ERC token standards but primarily ERC-20 and ERC-721 tokens.</td></tr><tr><td><strong>Fee Structure</strong></td><td><a href="deploying-smart-contracts/#gas-schedule-and-fees">Complex with two different gas prices</a></td><td>Single gas price</td></tr><tr><td><strong>Token Association**</strong></td><td><a href="../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md">Concept of token association </a></td><td>No concept of token association</td></tr><tr><td><strong>Keys for Token Functionality</strong></td><td>Keys control access to token functionality (<code>KYC</code>, <code>FREEZE</code>, <code>WIPE</code>, supply, fee, and <code>PAUSE</code>)</td><td>No equivalent <em>native</em> functionality</td></tr></tbody></table>

{% hint style="info" %}
**\*\*Note:** Token Association only applies to _native_ HTS tokens and does not affect ERC-20/721 tokens.
{% endhint %}

### Other Differences

<table><thead><tr><th width="238">Function</th><th width="274.3333333333333">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Precheck Failures</strong></td><td><a href="../../sdks-and-apis/hedera-api/miscellaneous/responsecode.md">Multiple precheck failure reasons</a></td><td>Typically single failure reason</td></tr><tr><td><strong>HBAR Decimal Precision</strong></td><td>8 or 18<a href="../../sdks-and-apis/sdks/hbars.md#hbar-decimal-places"> (varies across Hedera APIs)</a></td><td>Consistent 18 point decimal precision</td></tr></tbody></table>
