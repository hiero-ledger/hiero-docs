# Access HTS Tokens Through the EVM

Hedera provides a native API called the **Hedera API (HAPI)**, which offers comprehensive access to its services, including account management, token transactions, consensus, and more. For further information, refer to the [HAPI documentation](../../sdks-and-apis/hedera-api/). The [Hedera SDKs](../../sdks-and-apis/sdks/) provide easy access to these APIs to perform actions like token transfers, contract calls, and consensus messaging.

To make it easier for Ethereum developers to bring their contracts to Hedera, we have implemented a [JSON-RPC relay](../smart-contracts/deploying-smart-contracts/json-rpc-relay.md) that provides a familiar JSON-RPC interface. These developers can use standard Ethereum libraries and tools like [Hardhat](../../support-and-community/glossary.md#hardhat) and [Web3.js](https://web3js.readthedocs.io/en/v1.10.0/) to interact with Smart Contracts on Hedera.

The diagram below shows how users interact with various Hedera services.

<figure><img src="../../.gitbook/assets/SmartContractsAndHTS.drawio.png" alt=""><figcaption></figcaption></figure>

Hedera’s EVM is compatible with the Ethereum EVM, which, therefore, allows the creation of any smart contract—including contracts like DeFi contracts and popular tokenization contracts such as [ERC-20 ](../smart-contracts/tokens-managed-by-smart-contracts/erc-20-fungible-tokens.md)and [ERC-721](../smart-contracts/tokens-managed-by-smart-contracts/erc-721-non-fungible-tokens-nfts.md) tokens. Typically, users access these tokens using the JSON-RPC interface provided by the relay using standard Ethereum tooling.

In addition, Hedera also provides the [Hedera Token Service (HTS)](../../support-and-community/glossary.md#hedera-token-service-hts) that provides native support for [fungible](../../support-and-community/glossary.md#fungible-token) and [non-fungible tokens](../../support-and-community/glossary.md#non-fungible-token-nft). This service natively implements tokenization that allows developers to create and manage tokens without deploying smart contracts, simplifying the tokenization process and reducing development overhead. HTS is designed for high-speed transactions, supporting up to 10,000 [transactions per second (TPS)](../../support-and-community/glossary.md#transactions-per-second-tps) for token transfers. This makes it ideal for applications that require rapid, secure token operations. HTS also provides additional features such as atomic transfers with other tokens and/or HBAR, as well as the built-in ability to specify and collect custom fees and control through KYC, Pause, and Freeze keys.

The overlap between HTS tokens and tokenization in Smart Contracts allows a few interesting possibilities that we explore below:

1. **Exposing HTS tokens as if they were ERC-20/ERC-721 tokens**: Hedera has implemented facade contracts that make HTS tokens look like ERC-20 or ERC-721 tokens to the external world. This allows the users to trade these tokens using normal Ethereum tools such as Ethereum Wallets or dApps. These applications think that the HTS tokens are standard ERC tokens, and they are oblivious to the fact that these tokens are Hedera-native HTS tokens.
2. **Exposing HTS tokens and Hedera accounts to the EVM Smart Contract code**: Hedera has also implemented built-in [System Contracts](../smart-contracts/compiling-smart-contracts.md#system-smart-contracts) that allow Smart Contracts on Hedera to interact with HTS tokens just like they interact with any other contract. Like the above, these smart contracts are oblivious to the fact that they are dealing with Hedera-native HTS tokens. Hedera accounts are also accessible to these EVM-based smart contracts through System Contracts. In the future, other Hedera-native services will also be available using a similar mechanism.

#### Performance

* **Native Services**: Hedera’s native services, including crypto transfers, token transfers, and [Hedera Consensus Service (HCS)](../../support-and-community/glossary.md#hedera-consensus-service-hcs) submit messages, and operate at high throughput, with speeds of up to 10,000 transactions per second (TPS).
* **Smart Contract Layer**: The Hedera smart contract layer can handle up to 15 million gas per second, translating to roughly 350 Ethereum token transfers per second. The actual transaction throughput may vary depending on the gas requirements of each contract call.
