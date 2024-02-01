# The Power of Native Hedera Tokens as ERC-20 Tokens: A step-by-step guide

In this tutorial, you’ll learn how to make Hedera native tokens work like Ethereum's ERC-20 tokens using the [Hashio](https://swirldslabs.com/hashio/) JSON-RPC instance.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>1.</strong> <a href="use-native-tokens-as-erc20-tokens.md#prerequisites"><strong>PREREQUISITES</strong></a></td><td><a href="use-native-tokens-as-erc20-tokens.md#prerequisites">#prerequisites</a></td></tr><tr><td align="center"><strong>2.</strong> <a href="use-native-tokens-as-erc20-tokens.md#project-setup"><strong>PROJECT SETUP</strong></a></td><td><a href="use-native-tokens-as-erc20-tokens.md#project-setup">#project-setup</a></td></tr><tr><td align="center"><strong>3.</strong> <a href="use-native-tokens-as-erc20-tokens.md#project-contents"><strong>PROJECT CONTENTS</strong></a></td><td><a href="use-native-tokens-as-erc20-tokens.md#project-contents">#project-contents</a></td></tr><tr><td align="center"><strong>4.</strong> <a href="use-native-tokens-as-erc20-tokens.md#running-the-project"><strong>RUNNING THE PROJECT</strong></a></td><td><a href="use-native-tokens-as-erc20-tokens.md#running-the-project">#running-the-project</a></td></tr></tbody></table>

***

## Prerequisites

* Basic understanding of [TypeScript](https://www.typescriptlang.org/) and [Solidity](https://docs.soliditylang.org/en/latest/).
* Get a [Hedera testnet account](https://portal.hedera.com/register).
* Have [`ts-node`](https://www.npmjs.com/package/ts-node) installed.

***

## Table of Contents

1. [Project Setup](use-native-tokens-as-erc20-tokens.md#project-setup)
2. [Project Configuration](use-native-tokens-as-erc20-tokens.md#project-configuration)
3. [Project Contents](use-native-tokens-as-erc20-tokens.md#project-contents)
4. [Running the Project](use-native-tokens-as-erc20-tokens.md#running-the-project)

***

## Project Setup

To make the setup process simple, you'll use a pre-configured token wrapper project from the `token-wrapper` [repository](https://github.com/Swiss-Digital-Assets-Institute/token-wrapper).

Open a terminal window and navigate to your preferred directory where your project will live. Run the following command to clone the repo and install dependencies to your local machine:

```bash
git clone https://github.com/Swiss-Digital-Assets-Institute/token-wrapper.git
cd token-wrapper
npm install
```

The `dotenv` package is used to manage environment variables in a separate `.env` file, which is loaded at runtime. This helps protect sensitive information like your private keys and API secrets, but it's still best practice to add `.env` to your `.gitignore` to prevent you from pushing your credentials to GitHub.

***

## Project Configuration

In this step, you will update and configure the Hardhat configuration file that defines tasks, stores Hedera account private key information, and Hashio Testnet RPC URL. First, rename the `.env.example` file to `.env`. and update the `.env` files with the following code.

**Environment Variables**

The `.env` file defines environment variables used in the project. The `MY_ACCOUNT_ID` and `MY_PRIVATE_KEY` variables contains the _ECDSA_ _**Account ID**_ and _**DER Encoded Private Key,**_ respectively for the Hedera Testnet account. The `HEX_ENCODED_PRIVATE_KEY` variable contains the HEX _**Encoded Private Key.**_

The `JSON_RPC_RELAY_URL` variable contains the [HashIO](https://swirldslabs.com/hashio/) Testnet endpoint URL. This is the JSON-RPC instance that will submit the transactions to the Hedera test network to test, create and deploy your smart contract.

{% code title=".env" %}
```bash
MY_ACCOUNT_ID =
MY_PRIVATE_KEY = 
HEX_ENCODED_PRIVATE_KEY = 
JSON_RPC_RELAY_URL = https://testnet.hashio.io/api
```
{% endcode %}

***

## Project Contents

In this step, you'll examine the descriptions of the project contents in your existing project. If you don't need to review the project contents, you can proceed directly to [Running the project](use-native-tokens-as-erc20-tokens.md#running-the-project).

{% tabs %}
{% tab title="artifacts/" %}
This directory contains compiled smart contracts used for generating TypeScript bindings with Typechain. We compile these contracts using Hardhat, a versatile Ethereum development tool. You'll find the configuration in **`hardhat.config.js`**.

Think of Hardhat as your all-in-one environment for Ethereum development tasks like compiling, deploying, testing, and debugging.
{% endtab %}

{% tab title="contracts/" %}
This directory contains the smart contracts that will be compiled and deployed to the Hedera network. This directory contains the smart contracts that will be compiled and deployed to the Hedera network. Let's take a look at the smart contracts in this directory:

* **ERC20.sol** - This is the ERC20 token contract.
* **HederaResponseCodes.sol** - This is a contract that contains the response codes for the Hedera network.
* **HederaTokenService.sol** - This contract provides the transactions to interact with the tokens created on Hedera.
* **IHederaTokenService.sol** - This is the interface for the HederaTokenService contract.
* **Vault.sol** - This contract, when deployed, manages the functionality of a vault and serves as a testing ground to understand how Hedera native tokens interact with the ERC20 contract.
{% endtab %}

{% tab title=" scripts/" %}
In this directory, you'll find two main scripts: _**ERC20.ts**_ and _**utils.ts.**_ They play a crucial role in our interaction with smart contracts. Here's how it all unfolds:

We kick things off by setting up our environment and defining the essential variables. Using the _**Hedera SDK,**_ we create a new fungible token, which gives us a unique tokenId.

To streamline our interaction with smart contracts, we utilize the Typechain class to create instances of **`contractERC20`** and **`contractVault`**. We provide **`contractERC20`** with the tokenId's solidity address and the Ethereum provider. Similarly, we create an instance for **`contractVault`** using the **`vaultContractAddress`** (after deployed) and the provider. This approach ensures that both contracts are seamlessly integrated into our development process.

With our environment ready, we dive into interactions with the contracts. We demonstrate a token transfer operation, moving native tokens created on Hedera from one account to another through the _**ERC20 contract.**_ After that, we check how the balance of the receiving account changes. We do this in two ways: first, by calling a function in the _**SDK,**_ and second, by using the **`balanceOf`** function in the _**ERC20 contract.**_

In our second example, we deposit 1000 tokens into the _**Vault contract**_ and then withdraw them. We keep an eye on how this affects the Vault contract's balance. It's worth noting that in Hedera, you need to associate the recipient account with the token before making transfers. To handle this requirement, we use the **`associate`** function from the _**HederaTokenService**_ contract, which establishes the connection between the _**Vault contract**_ and the token. Once associated, we can easily deposit and withdraw tokens.

{% code title="ERC20.ts" %}
```typescript
console.clear();
import { AccountId, PrivateKey, TokenAssociateTransaction, Client, AccountBalanceQuery } from "@hashgraph/sdk";
import { ethers } from "ethers";
import { createFungibleToken, createAccount, deployContract } from "./utils";

import { ERC20__factory } from '../typechain-types/factories/ERC20__factory';
import { Vault__factory } from '../typechain-types/factories/Vault__factory';

import { config } from "dotenv";

config();

// Provider to connect to the Ethereum network
const provider = new ethers.providers.JsonRpcProvider(process.env.JSON_RPC_RELAY_URL!);

// Wallet to sign the transactions
const wallet = new ethers.Wallet(process.env.HEX_ENCODED_PRIVATE_KEY!, provider);

// Client to interact with the Hedera network
const client = Client.forTestnet();
const operatorPrKey = PrivateKey.fromStringECDSA(process.env.HEX_ENCODED_PRIVATE_KEY!);
const operatorAccountId = AccountId.fromString(process.env.MY_ACCOUNT_ID!);
client.setOperator(operatorAccountId, operatorPrKey);

async function main() {

    // Create a fungible token with the SDK
    const tokenId = await createFungibleToken("TestToken", "TT", operatorAccountId, operatorPrKey.publicKey, client, operatorPrKey);

    // Create an account for Alice with 10 hbar using the SDK
    const aliceKey = PrivateKey.generateED25519();
    const aliceAccountId = await createAccount(client, aliceKey, 10);
    console.log(`- Alice account id created: ${aliceAccountId!.toString()}`);

    // Take the address of the tokenId
    const tokenIdAddress = tokenId!.toSolidityAddress();
    console.log(`- tokenIdAddress`, tokenIdAddress);

    // We connect to the ERC20 contract using typechain
    const account = wallet.connect(provider);
    const accountAddress = account.address;
    console.log(`- accountAddress`, accountAddress);

    const contractERC20 = ERC20__factory.connect(
        tokenIdAddress,
        account
    );

    // We deploy the Vault contract using the SDK and take the address
    const contractVaultId = await deployContract(client, Vault__factory.bytecode, 4000000);
    const contractVaultAddress = contractVaultId!.toSolidityAddress();
    console.log(`- contractVaultId`, contractVaultId!.toString());

    // We connect to the Vault contract using typechain
    const contractVault = Vault__factory.connect(
        contractVaultAddress,
        wallet
    );

    // We set Alice as the operator, now she is the one interacting with the hedera network
    const aliceClient = client.setOperator(aliceAccountId!, aliceKey);

    // We associate the Alice account with the token
    const tokenAssociate = await new TokenAssociateTransaction()
        .setAccountId(aliceAccountId!)
        .setTokenIds([tokenId!])
        .execute(aliceClient);

    const tokenAssociateReceipt = await tokenAssociate.getReceipt(aliceClient);
    console.log(`- tokenAssociateReceipt ${tokenAssociateReceipt.status.toString()}`);

    const aliceAccountAddress = aliceAccountId!.toSolidityAddress();
    // We transfer 10 tokens to Alice using the ERC20 contract
    const transfer = await contractERC20.transfer(aliceAccountAddress, 10, { gasLimit: 1000000 })
    const transferReceiptWait = await transfer.wait();
    console.log(`- Transfer`, transferReceiptWait);

    // We check the balance tokenId from Alice using the SDK
    const balanceAliceNativeToken = new AccountBalanceQuery()
        .setAccountId(aliceAccountId!)

    const transactionQuery = await balanceAliceNativeToken.execute(client);
    const balanceTokenSDK = transactionQuery.tokens!.get(tokenId!);
    console.log("- Balance from Alice using the SDK", balanceTokenSDK.toString());

    // We check the balance tokenId from Alice using the ERC20 contract
    const balanceAliceERC20 = await contractERC20.balanceOf(aliceAccountAddress);
    console.log("- Balance from Alice using the ERC20", parseInt(balanceAliceERC20.toString()));

    // We associate the Vault contract with the token so we can transfer tokens to it
    const associate = await contractVault.associateFungibleToken(tokenIdAddress, { gasLimit: 1000000 })
    const associateReceipt = await associate.wait();
    console.log(`- Associate`, associateReceipt);

    // We deposit 1000 tokens to the Vault contract
    const deposit = await contractERC20.transfer(contractVaultAddress, 1000, { gasLimit: 1000000 })
    const depositReceipt = await deposit.wait();
    console.log(`- Deposit tokens to the Vault contract`, depositReceipt);

    // We check the balance of the Vault contract after the deposit
    let balanceVaultERC20 = await contractERC20.balanceOf(contractVaultAddress);
    console.log(`- Balance of the Vault contract before the withdraw`, parseInt(balanceVaultERC20.toString()));

    // We withdraw 100 tokens from the Vault contract
    const withdraw = await contractVault.withdraw(tokenIdAddress, { gasLimit: 1000000 })
    const withdrawReceipt = await withdraw.wait();
    console.log(`- Withdraw tokens from the Vault contract`, withdrawReceipt);

    // We check again the balance of the Vault contract after the withdraw to see if it has changed
    balanceVaultERC20 = await contractERC20.balanceOf(contractVaultAddress);
    console.log(`- Balance of the Vault contract after the withdraw`, parseInt(balanceVaultERC20.toString()));

}

main();
```
{% endcode %}
{% endtab %}

{% tab title="typechain-types/" %}
&#x20;This directory contains the typescript bindings generated by typechain. These bindings are used to interact with the smart contracts.
{% endtab %}
{% endtabs %}

***

## Running the project

Now that you have your project set up and configured, we can run it.

To do so, run the following command:

```bash
npm run compile
ts-node scripts/ERC20.ts
```

The first command compiles the smart contracts and generates the typescript bindings. The second command runs the ERC20 script.

To see the transactions on the Hedera network, you can use the [**Hedera Testnet Explorer**](https://hashscan.io/testnet).

> _**Note:** At the top of the explorer page, remember to switch the network to **TESTNET** before you search for the transaction._

<details>

<summary>console check ✅</summary>

```bash
- The token ID is: 0.0.2576296
- Alice account id created: 0.0.2576297
- tokenIdAddress 0000000000000000000000000000000000274fa8
- accountAddress 0x398520c72090d7aaBe37e2c49ab1cAe8e662AEa0
- contractVaultId 0.0.2576303
- tokenAssociateReceipt SUCCESS
- Transfer {
	to: '0x0000000000000000000000000000000000274Fa8',
	from: '0x398520c72090d7aaBe37e2c49ab1cAe8e662AEa0',
	contractAddress: '0x0000000000000000000000000000000000274Fa8',
	transactionIndex: 1,
	gasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	logsBloom: '0x00000000000000040000000000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000080000000000000000000000008000000000000000000000000000000000000000000000000000020000000000000000000100000000000000000000010000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000080000000000000000',
	blockHash: '0xe9714ef3227d3c809981ce42eb81d9c0fcd8e8082f4931628ab1d296b3e0d5bd',
	transactionHash: '0x5b730724e757a45b21f833a2f8bf8ae510cb7fa61d5defcde68f4432dfcf9940',
	logs: [
	{
		transactionIndex: 1,
		blockNumber: 2490825,
		transactionHash: '0x5b730724e757a45b21f833a2f8bf8ae510cb7fa61d5defcde68f4432dfcf9940',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x000000000000000000000000000000000000000000000000000000000000000a',
		logIndex: 0,
		blockHash: '0xe9714ef3227d3c809981ce42eb81d9c0fcd8e8082f4931628ab1d296b3e0d5bd'
	}
	],
	blockNumber: 2490825,
	confirmations: 1,
	cumulativeGasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	effectiveGasPrice: BigNumber { _hex: '0x03179fcad000', _isBigNumber: true },
	status: 1,
	type: 2,
	byzantium: true,
	events: [
	{
		transactionIndex: 1,
		blockNumber: 2490825,
		transactionHash: '0x5b730724e757a45b21f833a2f8bf8ae510cb7fa61d5defcde68f4432dfcf9940',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x000000000000000000000000000000000000000000000000000000000000000a',
		logIndex: 0,
		blockHash: '0xe9714ef3227d3c809981ce42eb81d9c0fcd8e8082f4931628ab1d296b3e0d5bd',
		args: [Array],
		decode: [Function (anonymous)],
		event: 'Transfer',
		eventSignature: 'Transfer(address,address,uint256)',
		removeListener: [Function (anonymous)],
		getBlock: [Function (anonymous)],
		getTransaction: [Function (anonymous)],
		getTransactionReceipt: [Function (anonymous)]
	}
	]
}
- Balance from Alice using the SDK 10
- Balance from Alice using the ERC20 10
- Associate {
	to: '0x0000000000000000000000000000000000274faF',
	from: '0x398520c72090d7aaBe37e2c49ab1cAe8e662AEa0',
	contractAddress: '0x0000000000000000000000000000000000274faF',
	transactionIndex: 0,
	gasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
	blockHash: '0x2a7393098da0e5f82da2230dfecb124b5be86d52bea2bb45924658c8c8a83ad3',
	transactionHash: '0x513f8ecbd25c8326c52c161e47d56f9f40d59e179ca670ec49bf0d316561d863',
	logs: [],
	blockNumber: 2490828,
	confirmations: 1,
	cumulativeGasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	effectiveGasPrice: BigNumber { _hex: '0x03179fcad000', _isBigNumber: true },
	status: 1,
	type: 2,
	byzantium: true,
	events: []
}
- Deposit tokens to the Vault contract {
	to: '0x0000000000000000000000000000000000274Fa8',
	from: '0x398520c72090d7aaBe37e2c49ab1cAe8e662AEa0',
	contractAddress: '0x0000000000000000000000000000000000274Fa8',
	transactionIndex: 0,
	gasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	logsBloom: '0x00800000000000040000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000008000000000000000000000000000000000000000000000004000000000000000000000000100000000000000000000010000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000000000000000001000000000000000000000080000000000000000',
	blockHash: '0xb8553decd07d595c77eaa17c019e0f506ab0632da614971b006a6b1353d5e57a',
	transactionHash: '0x3b312bb272236d00e750294d81d3c7441208b84c9f6f97b2a25757d19c0b444f',
	logs: [
	{
		transactionIndex: 0,
		blockNumber: 2490831,
		transactionHash: '0x3b312bb272236d00e750294d81d3c7441208b84c9f6f97b2a25757d19c0b444f',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x00000000000000000000000000000000000000000000000000000000000003e8',
		logIndex: 0,
		blockHash: '0xb8553decd07d595c77eaa17c019e0f506ab0632da614971b006a6b1353d5e57a'
	}
	],
	blockNumber: 2490831,
	confirmations: 2,
	cumulativeGasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	effectiveGasPrice: BigNumber { _hex: '0x03179fcad000', _isBigNumber: true },
	status: 1,
	type: 2,
	byzantium: true,
	events: [
	{
		transactionIndex: 0,
		blockNumber: 2490831,
		transactionHash: '0x3b312bb272236d00e750294d81d3c7441208b84c9f6f97b2a25757d19c0b444f',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x00000000000000000000000000000000000000000000000000000000000003e8',
		logIndex: 0,
		blockHash: '0xb8553decd07d595c77eaa17c019e0f506ab0632da614971b006a6b1353d5e57a',
		args: [Array],
		decode: [Function (anonymous)],
		event: 'Transfer',
		eventSignature: 'Transfer(address,address,uint256)',
		removeListener: [Function (anonymous)],
		getBlock: [Function (anonymous)],
		getTransaction: [Function (anonymous)],
		getTransactionReceipt: [Function (anonymous)]
	}
	]
}
- Balance of the Vault contract before the withdraw 1000
- Withdraw tokens from the Vault contract {
	to: '0x0000000000000000000000000000000000274faF',
	from: '0x398520c72090d7aaBe37e2c49ab1cAe8e662AEa0',
	contractAddress: '0x0000000000000000000000000000000000274faF',
	transactionIndex: 0,
	gasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	logsBloom: '0x00800000000000040000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000008000000000000000000000000000000000000000000000004000000000000000000000000100000000000000000000010000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001000000000000000000000002000000000000000000000000000000000000000000000000000000000000000000000000000000001000000000000000000000080000000000000000',
	blockHash: '0x3de5a72720f9b221d5e436b2ccaf3b1219436d653a943528a20569ba94ee397b',
	transactionHash: '0x7a661f21f9dd4a99764b4f5f216f29ff5a20207cd4f57d0ca1a25f12af2e6d2b',
	logs: [
	{
		transactionIndex: 0,
		blockNumber: 2490835,
		transactionHash: '0x7a661f21f9dd4a99764b4f5f216f29ff5a20207cd4f57d0ca1a25f12af2e6d2b',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x00000000000000000000000000000000000000000000000000000000000003e8',
		logIndex: 0,
		blockHash: '0x3de5a72720f9b221d5e436b2ccaf3b1219436d653a943528a20569ba94ee397b'
	}
	],
	blockNumber: 2490835,
	confirmations: 1,
	cumulativeGasUsed: BigNumber { _hex: '0x0c3500', _isBigNumber: true },
	effectiveGasPrice: BigNumber { _hex: '0x03179fcad000', _isBigNumber: true },
	status: 1,
	type: 2,
	byzantium: true,
	events: [
	{
		transactionIndex: 0,
		blockNumber: 2490835,
		transactionHash: '0x7a661f21f9dd4a99764b4f5f216f29ff5a20207cd4f57d0ca1a25f12af2e6d2b',
		address: '0x0000000000000000000000000000000000274Fa8',
		topics: [Array],
		data: '0x00000000000000000000000000000000000000000000000000000000000003e8',
		logIndex: 0,
		blockHash: '0x3de5a72720f9b221d5e436b2ccaf3b1219436d653a943528a20569ba94ee397b',
		removeListener: [Function (anonymous)],
		getBlock: [Function (anonymous)],
		getTransaction: [Function (anonymous)],
		getTransactionReceipt: [Function (anonymous)]
	}
	]
}
- Balance of the Vault contract after the withdraw 0
```

</details>

#### **Congratulations! 🎉 You have successfully learned to use native Hedera tokens as ERC20 tokens.** Feel free to reach out if you have any questions:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Lucía, Developer </p><p>(Hashgraph Association)</p><p><a href="https://github.com/luciamunozdev">GitHub</a> | <a href="https://twitter.com/luciamunozdev">Twitter</a> | <a href="https://www.linkedin.com/in/luciamunozmartinez/">LinkedIn</a></p></td><td><a href="https://twitter.com/luciamunozdev">https://twitter.com/luciamunozdev</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://github.com/theekrystallee">https://github.com/theekrystallee</a></td></tr></tbody></table>
