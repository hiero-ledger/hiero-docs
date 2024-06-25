---
description: >-
  Hardhat and EthersJs tutorial - HSCS workshop. Learn how to enable custom
  logic & processing on Hedera through smart contracts.
---

# Hardhat and EthersJs

## Video

{% embed url="https://www.youtube.com/watch?v=HfoU_CRp8Dk" %}
Hedera Smart Contract Service Workshop Part 5/6 | Hardhat & EtherJs
{% endembed %}

## Hardhat and EthersJs

[Hardhat](https://hardhat.org/) is a development framework, that is designed specifically to enable smart contract development workflows. [EthersJs](https://docs.ethers.org/v5/) is a software library, which enables client application development in Javascript. These two work very well together as hardhat integrates strongly with EthersJs by augmenting EthersJs with many convenience and utility functions that improve the developer experience of smart contract development.

## Prerequisites

* ✅ Complete the [Introduction](https://github.com/theekrystallee/hedera-docs/blob/master/tutorials/smart-contracts/hscs-workshop/00-intro.md) section of this same tutorial.
* ✅ Optionally, complete the [Hedera SDK JS](https://github.com/theekrystallee/hedera-docs/blob/master/tutorials/smart-contracts/hscs-workshop/01-hederasdkjs.md) section of this tutorial.

## Set up the project

To follow along, enter the `hederasdkjs` directory within the [accompanying tutorial GitHub repository](https://github.com/hedera-dev/hedera-smart-contracts-workshop), which you should already have cloned in the Intro section earlier.

Then install dependencies from npm.

```shell
cd ./hardhat
npm install
```

### Step J1: Copy smart contract

We have already written the smart contract in the Intro section of this tutorial. Let's copy that into this directory so that we may continue working on it.

```shell
cp ../intro/trogdor.sol ./contracts/trogdor.sol
```

## Hardhat REPL

A Read-Evaluate-Print Loop (REPL) is an environment which takes input from you, executes that input, and prints the result as output; then start over again. The POSIX-compliant shell that you have been using, such as `bash` or `zsh`, is an example of this.

Hardhat has its own REPL feature, which executes commands in the context of the smart contract project you are developing, and does so while connected to a specific EVM-compatible network.

Let's fire up the Hardhat REPL, connected to Hedera Testnet.

```shell
npx hardhat console --network hederatestnet
```

{% hint style="info" %}
Note that the network name here is `hederatestnet`, which is defined within the configuration file `hardhat.config.js`.
{% endhint %}

You will notice that the prompt prefix changes, and is now `>` . Whatever commands you enter now are no longer going to be executed by the regular shell, but instead by the Hardhat REPL. You can enter a `.exit` command to exit the Hardhat REPL, and return to your regular shell at any time. Let's execute a few commands within the Hardhat REPL before we do exit.

### Get block number

Hedera is a distributed ledger technology (DLT), however, it is not a blockchain. A blockchain network groups transactions together into blocks, and achieves network consensus on whether or not a block of transactions is valid, and which block (and therefore transactions) is the next one that should be added to the network. Hedera does not do that, instead it using a different consensus algorithm (Hashgraph), which achieves consensus on individual transactions, and adds them to the network as individual transactions, without a grouping of any kind.

That being said, in order to attain interoperability with EVM-compatible networks, the concept of blocks was introduced, in a manner that does not involve the consensus algorithm. In effect it simply deems all transactions successfully added to the network to be part of the same block based on their timestamp, once approximately every 2 seconds.

JSON-RPC is a Remote Procedure Call protocol, where the requests and responses are serialised in JSON. Importantly, Ethereum has defined a JSON-RPC API, and this API has become the de-facto standard API to interact with EVM-compatible networks.

{% hint style="info" %}
* [Ref: Hedera: What is Hashgraph Consensus](https://hedera.com/learning/hedera-hashgraph/what-is-hashgraph-consensus)
* [Ref: HIP-415: Introduction of Blocks](https://hips.hedera.com/hip/hip-415)
* [Ref: JSON-RPC: Specification](https://www.jsonrpc.org/specification)
* [Ref: Ethereum: JSON-RPC API](https://ethereum.org/en/developers/docs/apis/json-rpc/)
{% endhint %}

Let's verify that we are able to interact with Hedera Testnet using JSON-RPC by issuing an `eth_getBlockByNumber` JSON-RPC request. The expected response will be the most recent block's number on the network.

```js
(await require('hardhat').network.provider.send('eth_getBlockByNumber', ['latest', false])).number
```

This does indeed respond with a block number, in hexadecimal.

```
'0x6f8741'
```

### Check block number on Hashscan

* Convert this to decimal: `0x6f8741` --> `7309121`
* Check on Hashscan: https://hashscan.io/testnet/blocks --> latest block is `7309352` --> more, as new blocks occur every 2 seconds, approximately
* Click on it: https://hashscan.io/testnet/block/7309352 --> timestamp is `3:41:10.0120 PM Jul 14, 2023` --> matches time now

### Address

Let's continue with the REPL, and issue another command. This time it will not be a JSON-RPC request, but rather querying Hardhat itself to see which account we'll be using by default when performing any requests.

{% hint style="info" %}
The accounts that Hardhat uses are generated from the seed phrase in the `.env` file, plus the derivation path, using logic similar to the following code.

```javascript
const seedPhrase = process.env.BIP39_SEED_PHRASE;
const accounts = {
  mnemonic: seedPhrase,
  path: "m/44'/60'/0'/0",
};
```

Note that this has already been done for you in `hardhat.config.js`.
{% endhint %}

```js
(await hre.ethers.getSigners())[0].address
```

{% hint style="info" %}
Here, `hre` is a global object exported by Hardhat, and it stands for Hardhat Runtime Environment.

The `hre.ethers` object exposes an instance of the EthersJs software library that has been initialised by Hardhat using the configuration from `hardhat.config.js`. This includes the _signers_ which are a list of several accounts.
{% endhint %}

This outputs an EVM address of a Hedera EVM account.

```
'0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796'
```

If you have completed the Hedera SDK JS section of this tutorial, you will notice that this is **different** from the account used there, which was `0x7394111093687e9710b7a7aeba3ba0f417c54474`. This is because the script used for the Hedera SDK JS account was configured to use the operator account. Hardhat, on the other hand, uses one of the EVM accounts generated using the BIP-39 seed phrase. These were generated during _Step B4: Fund several Hedera EVM accounts_ in the Intro section of this tutorial.

<details>

<summary>Hardhat accounts</summary>

If you take a look at the EVM addresses generated by the script - this was its filtered output:

```
#0 EVM address: 07ffaadfe3a598b91ee08c88e5924be3eff35796
#1 EVM address: 1c29e31d241f0d06f3763221f5224a6b82f09cce
```

If you run the signer address query 2 times, changing only the **index** with each query, you will get exact matches.

```javascript
> (await hre.ethers.getSigners())[0].address
'0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796'
> (await hre.ethers.getSigners())[1].address
'0x1C29e31D241F0D06F3763221F5224A6b82f09Cce'
```

</details>

Alright, you've completed the checks needed, verifying that you are able to successfully connected to Hedera Testnet.

Exit the REPL.

```js
.exit
```

You'll now return to your regular shell.

### Check address on Hashscan

* Copy the EVM address that Hardhat uses by default
* Go to Hashscan, and search for that address
* You should get redirected to an Account page
  * For example, from: `https://hashscan.io/testnet/account/0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796`
  * to: `https://hashscan.io/testnet/account/0.0.3996359`
* This verifies that the account exists
* Check that the account has a balance of HBAR
* If it does not exist, or does not have balance, you'll need to create or fund it before proceeding
  * To do so, you'll need to repeat [_Step B4: Fund several Hedera EVM accounts_](https://github.com/theekrystallee/hedera-docs/blob/master/tutorials/smart-contracts/setup/README.md#step-b4-fund-several-hedera-evm-accounts) from the Setup section of this tutorial.

## Compiling smart contracts

### Using hardhat to compile smart contracts

In the Hedera SDK JS section of this tutorial, in the _Using `solc` to compile smart contracts_ step, you manually installed a specific version of the Solidity compiler, and then ran that in the shell.

Hardhat takes care of that for you, it will simply read which version of the Solidity compiler has been set in in the configuration in `hardhat.config.js`, and then install that particular version if necessary, and run it, and manage its outputs including caching where relevant.

```shell
npx hardhat compile
```

Let's take a look at the compiled outputs, and where Hardhat stores them.

* Build cache:`hardhat/cache/solidity-files-cache.json`
* ABI: `hardhat/artifacts/contracts/trogdor.sol/Trogdor.json`
* Bytecode + other compiler outputs: `hardhat/artifacts/build-info/${SOME_HASH}.json`

You do not need to do anything with these, just good to know what is happening behind the scenes.

## HAPIs and EVM transactions

However, Hardhat + EthersJs do not understand HAPIs, and are only aware of the EVM transaction model. Thankfully, Hedera supports a HAPI named `EthereumTransaction`, defined in HIP-410. All the different types of EVM transactions are supported through this single HAPI.

This is the low-level protocol supported by the Hedera network which is the gateway for software libraries, developer tools, developer frameworks, and even end use software such as wallets, which were originally designed to work with Ethereum, to also work on Hedera, and is an integral part of Hedera's EVM-compatibility.

{% hint style="info" %}
* [Ref: HIP-410 - Wrapping Ethereum Transaction Bytes in a Hedera Transaction](https://hips.hedera.com/hip/hip-410)
{% endhint %}

In this section of the tutorial, you are using Hardhat and EthersJs, and this framework/ software library are unaware of HAPIs, including `ContractCreateTransaction`, `ContractExecuteTransaction`, and `ContractCallQuery`, which you used earlier.

The only API that they are able to use is JSON-RPC, and this is where the `EthereumTransaction` HAPI comes into play. When you send a JSON-RPC request to an RPC endpoint for a Hedera network, that gets converted into an `EthereumTransaction` HAPI. The same happens in reverse with the response.

## Deploying smart contracts

### Using Hardhat to deploy smart contracts

Let's begin by entering the REPL again.

```shell
npx hardhat console --network hederatestnet
```

Next, let's use the EthersJs APIs to deploy the smart contract.

{% hint style="info" %}
Note that this will send an EVM deployment transaction to Hedera Testnet, using the following sequence:

`ContractFactory.deployTransaction` EthersJs Javascript API --> `eth_sendRawTransaction` JSON-RPC request --> `EthereumTransaction` HAPI
{% endhint %}

A deployment is performed via a transaction, just like any other interaction with the network which may change the state of the network. The transaction needs to be performed by an account, in this case, since you are performing an `EthereumTransaction`, the account needs to be an EVM account.

{% hint style="info" %}
`EthereumTransaction`s may only be signed using ECDSA secp256k1 keys, which Hedera EVM accounts use.

Hedera-native accounts such as the operator account, on the other hand, use EdDSA Ed25519 keys, and therefore `EthereumTransaction`s may not be signed by them.
{% endhint %}

The account signing the transaction needs to pay for the cost of processing the transaction. This is a variable fee known as _gas_. The account that you'll use for signing is, by default, the first EVM account generated in the script from the Intro section of this tutorial, i.e. the one with the hierarchical derivation path of `m/44'/60'/0'/0/0`.

<details>

<summary>Gas-related terminology</summary>

On Hedera networks, gas is paid for using HBAR.

**Gas** is denominated in tinybars, and is equal to **gas price** multiplied by **gas units**. Both of these are variable in different ways:

* **Gas price** depends on the demand for, and supply of, computational resources of the nodes in the Hedera network. Gas price usually increases with demand levels, and against supply levels.
* **Gas units** depend on the computational and storage costs as metered by the EVM when processing that specific transaction. When processing a transaction, each bytecode has a specific numeric cost, and a running tally of their sum is tracked during transaction processing.

</details>

Back to the Hardhat REPL, enter the following command to obtain the default signer, which is the first EVM account mentioned above.

```javascript
deployer = (await hre.ethers.getSigners())[0];
```

This will output a `SignerWithAddress` object, similar to this:

```javascript
SignerWithAddress {
  _isSigner: true,
  address: '0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796',
  _signer: JsonRpcSigner {
    _isSigner: true,
    provider: EthersProviderWrapper {
	    /* ... truncated ... */
    },
    _address: '0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796',
    _index: null
  },
  provider: EthersProviderWrapper {
    /* ... truncated ... */
  }
}
```

Next, instantiate an instance of `ContractFactory`. The Hardhat-augmented version of EthersJs is aware of the directory structure, and where to find the Solidity compiler's outputs: The binary file containing the EVM bytecode, and the JSON file containing the ABI.

```javascript
trogdorFactory = await hre.ethers.getContractFactory('Trogdor');
```

This will output a `ContractFactory` object, similar to this:

```javascript
ContractFactory {
  bytecode: '0x608060405234801561001057600080fd5b5061024f806100206000396000f3fe6080604/*...truncated...*/f119714d7ccd15a6a111ef132d4f9418c614ca4145164736f6c63430008110033',
  interface: Interface {
    fragments: /* ... truncated ... */
    _abiCoder: AbiCoder { coerceFunc: null },
    functions: {
      'MIN_FEE()': [FunctionFragment],
      'amounts(address)': [FunctionFragment],
      'burninate()': [FunctionFragment],
      'totalBurnt()': [FunctionFragment]
    },
    errors: {},
    events: { 'Burnination(address,uint256)': [EventFragment] },
    structs: {},
    deploy: ConstructorFragment /* ... truncated ... */,
    _isInterface: true
  },
  signer: SignerWithAddress {
    _isSigner: true,
    address: '0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796',
    _signer: JsonRpcSigner /* ... truncated ... */,
    provider: EthersProviderWrapper /* ... truncated ... */
  }
}
```

You can observe that it has the EVM bytecode, and has parsed the ABI into an interface.

Next, deploy the smart contract. Note that this transaction includes a network request, and so expect to wait for several seconds - it will not be instantaneous like the previous commands.

```javascript
trogdor = await trogdorFactory.deploy();
```

This will output a `Contract` object, similar to this:

```javascript
Contract {
  interface: Interface {
    fragments: /* ... truncated ... */,
    _abiCoder: AbiCoder { coerceFunc: null },
    functions: /* ... truncated ... */,
    errors: {},
    events: { 'Burnination(address,uint256)': [EventFragment] },
    structs: {},
    deploy: ConstructorFragment /* ... truncated ... */,
    _isInterface: true
  },
  provider: EthersProviderWrapper /* ... truncated ... */,
  signer: SignerWithAddress /* ... truncated ... */,
  callStatic: /* ... truncated ... */,
  estimateGas: /* ... truncated ... */,
  functions: {
    'MIN_FEE()': [Function (anonymous)],
    'amounts(address)': [Function (anonymous)],
    'burninate()': [Function (anonymous)],
    'totalBurnt()': [Function (anonymous)],
    MIN_FEE: [Function (anonymous)],
    amounts: [Function (anonymous)],
    burninate: [Function (anonymous)],
    totalBurnt: [Function (anonymous)]
  },
  populateTransaction: /* ... truncated ... */,
  filters: {
    'Burnination(address,uint256)': [Function (anonymous)],
    Burnination: [Function (anonymous)]
  },
  _runningEvents: {},
  _wrappedEmits: {},
  address: '0xeB9922B24D82603A543C764A3e4c9BC451FB8752',
  resolvedAddress: Promise {
    '0xeB9922B24D82603A543C764A3e4c9BC451FB8752',
    [Symbol(async_id_symbol)]: 2916,
    [Symbol(trigger_async_id_symbol)]: 2135
  },
  'MIN_FEE()': [Function (anonymous)],
  'amounts(address)': [Function (anonymous)],
  'burninate()': [Function (anonymous)],
  'totalBurnt()': [Function (anonymous)],
  MIN_FEE: [Function (anonymous)],
  amounts: [Function (anonymous)],
  burninate: [Function (anonymous)],
  totalBurnt: [Function (anonymous)],
  deployTransaction: {
    hash: '0x0649b37b5cefdb06b2f3139464b1df48498d93af9428906156dfd75831ac0cde',
    type: 2,
    accessList: null,
    blockHash: '0x910f76e621d2905f6bc2b77ace84adbead9e08c7121b2e1c7bf972965165568b',
    blockNumber: 7623259,
    transactionIndex: 6,
    confirmations: 1,
    from: '0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796',
    maxFeePerGas: BigNumber { value: "194" },
    gasLimit: BigNumber { value: "320000" },
    to: '0x0000000000000000000000000000000000eD3909',
    value: BigNumber { value: "0" },
    nonce: 145,
    data: '0x608060405234801561001057600080fd5b5061024f806100206000396000f3fe6080604/*...truncated...*/f119714d7ccd15a6a111ef132d4f9418c614ca4145164736f6c63430008110033',
    r: '0x15f671fe22c9d56fb6725acde2008737549cec491e8c6f703dd308dafd1c7df7',
    s: '0x68c6a4479a71cf783ab8f3fb2bcece0e7ec3441ff298f3c2ff75d80585bcb9a8',
    v: 1,
    creates: null,
    chainId: 296,
    wait: [Function (anonymous)]
  }
}
```

There is much more information on this object, compared to the object from the previous step, because it has been deployed.

Let's examine the deployment transaction for the smart contract in a bit more detail.

```javascript
trogdorDeployment = await trogdor.deployTransaction.wait();
```

This will output an object, similar to this:

```js
{
  to: '0x0000000000000000000000000000000000eD3909',
  from: '0x00000000000000000000000000000000003CFac7',
  contractAddress: '0xeB9922B24D82603A543C764A3e4c9BC451FB8752',
  transactionIndex: 6,
  gasUsed: BigNumber { value: "320000" },
  logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000/*...truncated...*/00000000000000000000000000000000000000000000000000000000000000000000000',
  blockHash: '0x910f76e621d2905f6bc2b77ace84adbead9e08c7121b2e1c7bf972965165568b',
  transactionHash: '0x0649b37b5cefdb06b2f3139464b1df48498d93af9428906156dfd75831ac0cde',
  logs: [],
  blockNumber: 7623259,
  confirmations: 145,
  cumulativeGasUsed: BigNumber { value: "3520000" },
  effectiveGasPrice: BigNumber { value: "1940000000000" },
  status: 1,
  type: 0,
  byzantium: true,
  events: []
}
```

The smart contract has just been deployed!

We will need the `contractAddress` property, so copy that for later use.

### Using Hashscan to check smart contract deployment

* Copy the output smart contract account address, e.g. `0xeB9922B24D82603A543C764A3e4c9BC451FB8752`.
* Visit [Hashscan](https://hashscan.io/testnet/dashboard)
* Paste the copied address into the search box
* You should get redirected to a "Contract" page, e.g. `https://hashscan.io/testnet/contract/0.0.15546633`
* In it, you can see the EVM address, e.g. `0xeb9922b24d82603a543c764a3e4c9bc451fb8752`
* Under "Contract Bytecode", you can see "Runtime Bytecode"

## Interacting with smart contacts

### Using Hardhat to interact with smart contracts

While you still have the Hardhat REPL open, let's continue by interacting with the smart contract that you have just deployed.

Issue a query of the `MIN_FEE` constant.

```javascript
await trogdor.functions.MIN_FEE();
```

This should reply with a `BigNumber` object, whose value is `100`.

```javascript
[ BigNumber { value: "100" } ]
```

<details>

<summary><code>BigNumber</code> library vs <code>bigint</code> primitive type</summary>

When EthersJs, and other similar JavaScript libraries such as Web3Js, were originally created, the JavaScript language did not have a means to represent integers greater than `2^53`.

Therefore, to be able to handle `uint256` and other large number types, the `BigNumber` library was used.

Now this is redundant, because JavaScript has since added a `bigint` primitive type, which is able to comfortably handle `uint256` values. Future versions of EthersJs and Web3Js are likely to deprecate the use of `BigNumber`.

* [Ref: MDN - BigInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/BigInt)

</details>

Issue a similar query, this time invoking the `totalBurnt` function.

```javascript
await trogdor.functions.totalBurnt();
```

This returns a value of `0`, which is to be expected, because we have yet to invoke `burninate`.

```javascript
[ BigNumber { value: "0" } ]
```

That is precisely what we'll do next: Invoke `burninate`. This is a `payable` function, which expects the transaction to contain a minimum of `100` tinybars sent along with it. Let's sent `123` tinybars.

```javascript
await trogdor.functions.burninate({ value: 123n * 10_000_000_000n });
```

<details>

<summary>Tinybars vs Weibars</summary>

If you compare the Hedera SDK JS section of this tutorial, to this section of the tutorial, you might notice that there is a difference here.

When using Hedera SDK JS:

```javascript
.setPayableAmount(new Hbar(123, HbarUnit.Tinybar))
```

When using EthersJs:

```javascript
{ value: 123n * 10_000_000_000n }
```

You might think: Why does the number of tinybar need to be multiplied by 10 million?

The _Value of gas price and value fields_ part of HIP-410 offers this explanation.

_For Ethereum transactions, we introduce the concept of “WeiBars”, which are 1 to 10^-18th the value of a HBAR. This is to maximize compatibility with third party tools that expect ether units to be operated on in fractions of 10^18, also known as a Wei. Thus, 1 tinyBar is 10^10 weiBars or 10 gWei. When calculating gas prices and transferred value the fractional parts below a tiny bar are dropped when converted to tinyBars._

* [Ref: HIP-410 - Wrapping Ethereum Transaction Bytes in a Hedera Transaction - Value of gas price and value fields](https://hips.hedera.com/hip/hip-410#value-of-gas-price-and-value-fields)

The _compatibility_ above refers to may software libraries, developer tools, developer frameworks, and even end user software such as wallets, assuming/ hardcoding the value of the "full unit" in relation to the "fractional unit" of the native currency of that network. Since 1 HBAR = 10^8 tinybar, but 1 Ether = 10^18 wei, when using client software originally designed to interact with Ethereum, you need to be aware of this and convert between the two.

</details>

This will output some details about the transaction.

```javascript
{
  hash: '0x12d7e21b0b284a191b962f6fb960b81ba2fcbe68ed5c1b0a560b29ed5dfafbe2',
  type: 2,
  accessList: null,
  blockHash: '0x6469685f147658e4ca83a3aa9571c5b5bc673ddc03b853b9400e52743a82e3de',
  blockNumber: 7662273,
  transactionIndex: 11,
  confirmations: 1,
  from: '0x07ffAaDFe3a598b91ee08C88e5924be3EfF35796',
  maxFeePerGas: BigNumber { value: "197" },
  gasLimit: BigNumber { value: "320000" },
  to: '0x0000000000000000000000000000000000eD3909',
  value: BigNumber { value: "123" },
  nonce: 146,
  data: '0x3024480d',
  r: '0x7dc8e42fbfed582b8fba03b6df14b6d0e4c23eef946ec32a8ae3585c5788c12e',
  s: '0x43d5189f6e3c0fd5170c5d64ae0dc872d470a5799af679b547409354bdf1ad94',
  v: 1,
  creates: null,
  chainId: 296,
  wait: [Function (anonymous)]
}
```

However, there is nothing we really need to do with this information.

Let's query both `MIN_FEE` and `totalBurnt` again. The value returned for `MIN_FEE` is expected to be the same, since there are no functions which modify its value, and in fact it is marked as `constant`. The value returned for `totalBurnt`, on the other hand, is expected to increase from its previous value, since the balance of the smart contract should increase each time the `burninate` function is successfully invoked.

Query `MIN_FEE`.

```javascript
await trogdor.functions.MIN_FEE();
```

This returns the same value of `100`, as expected.

```javascript
[ BigNumber { value: "100" } ]
```

Query `totalBurnt`.

```javascript
await trogdor.functions.totalBurnt();
```

This returns a new value of `123`, as expected as well.

```javascript
[ BigNumber { value: "123" } ]
```

Let's also query the `amounts` function, which was auto-generated by the Solidity compiler for the mapping of the same name. This function expects an `address` parameter. Let's use the address of the same Hedera EVM account that we used to invoke the `burninate` function.

```javascript
acc0EvmAddress = (await hre.ethers.getSigners())[0].address;
await trogdor.functions.amounts(acc0EvmAddress);
```

This returns a value of `123`, which is expected since that was the value that was sent along with the transaction when invoking `burninate`

```javascript
[ BigNumber { value: "123" } ]
```

Let's repeat the above, this time using the address of a different Hedera EVM account which has not invoked the `burninate` function yet.

```javascript
acc1EvmAddress = (await hre.ethers.getSigners())[1].address;
await trogdor.functions.amounts(acc1EvmAddress);
```

This returns a value of `0`, which is expected since this account has not yet invoked `burninate`.

```javascript
[ BigNumber { value: "0" } ]
```

### Using Hashscan to check smart contract interactions

* Visit the "Contract" page for your previously deployed smart contract, e.g. `https://hashscan.io/testnet/contract/0.0.15426051`
* Scroll down to the "Recent Contract Calls" section
* If you see "REFRESH PAUSED" at the top right of this section, press the "play" button next to it to unpause (otherwise it does not load new transactions)
* You should see a list of transactions, with most recent at the top
* There should be a successful transaction, denoted by the absence of an exclamation mark in a red triangle, e.g. `https://hashscan.io/testnet/transaction/1689667816.410045835`
* Scroll down to the "Contract Result" section
* You should see "Result" as `SUCCESS`
* You should also see "Error Message" as `None`
* Scroll down to the "Logs" section
* You should see a single log entry (address, data, index, and topics)
  * The "Address" field matches that of the smart contract
  * The "Index" field should be `0` since there was only a single event that was emitted
  * The "Topics" field corresponds to the hash of the signature of the event that was emitted, e.g. `Burnination(address,uint256)`
  * The "Data" field corresponds to the values of the event parameters, e.g. `0x0000000000000000000000007394111093687e9710b7a7aeba3ba0f417c54474000000000000000000000000000000000000000000000000000000000000007b` is `0x7394111093687e9710b7a7aeba3ba0f417c54474` (your address) and `0x7b` is the amount (`123` when converted to decimal)
