---
description: "Hello World sequence: Write a smart contract in Solidity, compile it, then use Hedera Smart Contract Service (HSCS) to deploy it and interact with it."
---

# HSCS: Smart Contract

## What you will accomplish

- [ ] Write a smart contract
- [ ] Compile the smart contract
- [ ] Deploy a smart contract
- [ ] Update smart contract state
- [ ] Query smart contract state

***

## Prerequisites

Before you begin, you should have **completed** the following Hello World sequence:

- [create-fund-account.md](create-fund-account.md "mention")

***

## Get started: Set up project

To follow along, start with the `main` branch, which is the _default branch_ of the repo. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence. If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file and populate it as required.
{% endhint %}

In the terminal, from the `hello-future-world` directory, enter the subdirectory for this sequence.

```shell
cd 03-hscs-smart-contract-ethersjs/
```

Reuse the `.env` file by copying the one that you have previously created into the directory for this sequence.

```shell
cp ../00-create-fund-account/.env ./
```

<details>

<summary>Check that you have copied the `.env` file correctly</summary>

To do so, use the `pwd` command to check that you are indeed in the right subdirectory within the repo.

```shell
pwd
```

This should output a path that ends with `/hello-future-world/03-hscs-smart-contract-ethersjs`. If not, you will need to start over.

```
/some/path/hello-future-world/03-hscs-smart-contract-ethersjs
```

Next, use the `ls` command to check that the `.env` file has been copied into this subdirectory.

```shell
ls -a
```

The first few line of the output should look display `.env`. If not, you'll need to start over.

```
.
..
.env
```

</details>

Next, install the dependencies using `npm`. You will also need to install a Solidity compiler, using the `--global` flag.

```shell
npm install && npm install --global solc@0.8.17
```

{% hint style="info" %}
[Solidity](https://docs.soliditylang.org/) is a programming language that was designed specifically for writing smart contracts in. The Solidity compiler outputs bytecode that can be run by an [Ethereum Virtual Machine (EVM)](https://ethereum.org/en/developers/docs/evm/) implementation; including Hyperledger Besu's EVM, which powers Hedera Smart Contract Service.
{% endhint %}

{% hint style="info" %}
Note that although the `npm` package is named `solc`, the executable exposed on your command line is named `solcjs`.
{% endhint %}

Then, open both of the following files in a code editor, such as VS Code.

- `my_contract.sol`
- `script-hscs-smart-contract-ethersjs.js`

***

## Write the smart contract

An almost-complete smart contract has already been prepared for you, `my_contract.sol`. You will only need to make one modification (outlined below) for it to compile successfully.

### Step 1: Get the name stored in mapping

Within the `greet()` function, we would like to access the `names` mapping and retrieve the name of the account that is invoking this function. The account is identified by its EVM account alias, which is available as `msg.sender` within the Solidity code.

{% code title="my_contract.sol" overflow="wrap" %}

```solidity
        string memory name = names[msg.sender];
```

{% endcode %}

{% hint style="info" %}
_**Note**: This smart contract has two functions, `introduce` and `greet`. You will invoke both of them later on._
{% endhint %}

***

## Compile the smart contract

Once you have completed writing the smart contract in Solidity, you will need to compile it using the Solidity compiler installed earlier.

Invoke the compiler on your Solidity file. Then list files in the current directory.

```shell
solcjs --bin --abi ./my_contract.sol
ls
```

You should see an output similar to the following:

```
my_contract.sol
my_contract_sol_MyContract.abi
my_contract_sol_MyContract.bin
```

{% hint style="info" %}

- The `.abi` file contains JSON and describes the interface used to interact with the smart contract.
- The `.bin` file contains EVM bytecode, and this is used in the deployment of the smart contract.

Note that while the `.abi` file is human-readable, the `.bin` file is _not intended_ to be human-readable.
{% endhint %}

***

## Configure RPC Connection

1. Sign up for an account at [`auth.arkhia.io/signup`](https://auth.arkhia.io/signup). If prompted, click on the link in your confirmation email.
2. Click on the <mark style="background-color:yellow;">**`+`**</mark><mark style="background-color:yellow;">`CREATE PROJECT`</mark> button in the top-right corner of the Arkhia dashboard.

[![](../../.gitbook/assets/hello-world--hscs--arkhia-01-create-project.png)](../../.gitbook/assets/hello-world--account--arkhia-01-create-project.png)

3. Fill in whatever you like in the modal dialog that pops up.

[![](../../.gitbook/assets/hello-world--hscs--arkhia-02-project-form.png)](../../.gitbook/assets/hello-world--account--arkhia-02-project-form/)

4. Click on the "Manage" button on the right side of your newly created project.

[<img src="../../.gitbook/assets/hello-world--hscs--arkhia-03-manage-project.png" alt="" data-size="original">](../../.gitbook/assets/hello-world--account--arkhia-03-manage-project/)

Now, you should see the project details.

- Under "Network", select "Hedera Testnet".
- Copy the "JSON-RPC Relay" field.
- In the "Security" section, copy the "API Key" field.

In the `.env` file, edit the property with the key `RPC_URL`, to replace `YOUR_JSON_RPC_URL` with the "JSON-RPC Relay" value, followed by a `/` character, and finally, the "API key" value that you have just copied.

For example, if the JSON-RPC field is `https://pool.arkhia.io/hedera/testnet/json-rpc/v1`, and if the API key field is `ABC123`, the line in your `.env` file should look like this:

{% code title=".env" overflow="wrap" %}

```shell
RPC_URL=https://pool.arkhia.io/hedera/testnet/json-rpc/v1/ABC123
```

{% endcode %}

{% hint style="warning" %}
**Note**: Ensure that you have a **`/`** just before the API key.
{% endhint %}

<details>

<summary>Alternative RPC configuration</summary>

Arkhia is one of multiple options for JSON-RPC connections. This tutorial covers the available options: [How to Connect to Hedera Networks Over RPC](https://docs.hedera.com/hedera/tutorials/more-tutorials/json-rpc-connections).

</details>

***

## Write the script

An almost complete script has already been prepared for you, `script-hscs-smart-contract-ethersjs.js`. You will only need to make a few modifications (outlined below) for it to run successfully.

### Step 2: Prepare smart contract for deployment

Initialize an instance of `ContractFactory` from EthersJs.

{% code title="script-hscs-smart-contract-ethersjs.js" overflow="wrap" %}

```js
    const myContractFactory = new ContractFactory(
        abi, evmBytecode, accountWallet);
```

{% endcode %}

{% hint style="info" %}
**Note**: The `ContractFactory` class is used to prepare a smart contract for deployment. To do so, pass in the ABI and bytecode output by the Solidity compiler earlier. Also, pass in the `accountWallet` object, which is used to authorize transactions and is needed for the deployment transaction.

Upon preparation, it sends a deployment transaction to the network, and an instance of a `Contract` object is created based on the result of the deployment transaction. This is stored in a variable, `myContract`, which will be used in the next steps. This has already been done for you in the script.
{% endhint %}

### Step 3: Invoke a smart contract transaction

The `introduce` function requires a single parameter of type `string`, and changes the state of the smart contract to store this value. Enter your name (or nickname) as the parameter. For example, if you wish to use "bguiz", the invocation should look like this:

{% code title="script-hscs-smart-contract-ethersjs.js" overflow="wrap" %}

```js
    const myContractWriteTxRequest = await myContract.functions.introduce('bguiz');
```

{% endcode %}

### Step 4: Invoke a smart contract query

In the previous step, you **changed** some state of the smart contract, which involved submitting a transaction to the network. This time, you are going to **read** some state of the smart contract. This is much simpler to do as no transaction is needed.

Invoke the `greet` function and save its response to a variable, `myContractQueryResult`. This function does not take any parameters.

{% code title="script-hscs-smart-contract-ethersjs.js" overflow="wrap" %}

```js
    const [myContractQueryResult] = await myContract.functions.greet();
```

{% endcode %}

{% hint style="info" %}
When invoking functions in a smart contract, you may do so in two different ways:

- With a transaction **→** Smart contract state may be changed.
- Without a transaction **→** Smart contract state may be queried but may not be changed.
  {% endhint %}

***

## Run the script

In the terminal, run the script using the following command:

```shell
node script-hscs-smart-contract-ethersjs.js
```

You should see output similar to the following:

```
accountId: 0.0.1201
accountAddress: 0x7394111093687e9710b7a7aEBa3BA0f417C54474
accountExplorerUrl: https://hashscan.io/testnet/address/0x7394111093687e9710b7a7aEBa3BA0f417C54474
myContractAddress: 0x9A6856897a72E790Ae765bFF997396199BDf1B72
myContractExplorerUrl: https://hashscan.io/testnet/address/0x9A6856897a72E790Ae765bFF997396199BDf1B72
myContractWriteTxHash: 0x32684e8d8e60126e171db968a4b20153ba96a40920a036dbebb48e19fb74664d
myContractWriteTxExplorerUrl: https://hashscan.io/testnet/transaction/0x32684e8d8e60126e171db968a4b20153ba96a40920a036dbebb48e19fb74664d
myContractQueryResult: Hello future - bguiz
```

Open `myContractExplorerUrl` in your browser and check that:

<img src="../../.gitbook/assets/hello-world--hscs--contract.drawing.svg" alt="HSCS contract in Hashscan, with annotated items to check." class="gitbook-drawing">

- The contract exists
- Under the "Contract Bytecode" section, its "Compiler Version" field matches the version of the Solidity compiler that you used (`0.8.17`) **(2)**
- Under the "Recent Contract Calls" section, There should be _two_ transactions:
  - The transaction with the _earlier timestamp_ (bottom) should be the _deployment_ transaction. **(3A)**
    - Navigate to this transaction by clicking on the timestamp.
    - Under the "Contract Result" section, the "Input - Function & Args" field should be a _relatively long_ set of hexadecimal values.
    - This is the EVM bytecode output by the Solidity compiler.
    - Navigate back to the Contract page (browser `⬅` button).
  - The transaction with the _later timestamp_ (top) should be the _function invocation_ transaction, of the `introduce` function. **(3B)**
    - Navigate to this transaction by clicking on the timestamp.
    - Under the "Contract Result" section, the "Input - Function & Args" field should be a _relatively short_ set of hexadecimal values.
    - This is the representation of
      - the function identifier as the first _eight_ characters (e.g. `0xc63193f6` for the `introduce` function), and
      - the input string value (e.g. `0x5626775697a0` for `bguiz`).
    - Navigate back to the Contract page (browser `⬅` button).

<details>

<summary>Additional checks</summary>

The steps above are sufficient to check that you have deployed and interacted with the same contract successfully. You may optionally wish to perform these additional checks as well.

Open `myContractWriteTxExplorerUrl` in your browser. This should be the same page as "the transaction with the later timestamp" in **3B** from the previous checks. Check that:

- The transaction exists
- Its "Type" field is "ETHEREUM TRANSACTION"
- Under the "Contract Result" section, its "From" field matches the value of `accountId`
- Under the "Contract Result" section, its "To" field matches the value of `myContractAddress`

</details>

***

## Complete

Congratulations, you have completed the **Hedera Smart Contract Service** Hello World sequence! 🎉🎉🎉

You have learned how to:

- [x] Write a smart contract
- [x] Compile the smart contract
- [x] Deploy a smart contract
- [x] Update smart contract state
- [x] Query smart contract state

***

## Next Steps

Now that you have completed this Hello World sequence, you have interacted with Hedera Smart Contract Service (HSCS). There are [other Hello World sequences](./) for Hedera File Service (HFS), and Hedera Token Service (HTS), which you may wish to check out next.

You may also wish to check out the more detailed [HSCS workshop](https://docs.hedera.com/hedera/tutorials/smart-contracts/hscs-workshop/), which goes into greater depth.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/), is intended to be used alongside this tutorial.

To skip ahead to the final state, use the `completed` branch. You may use this to compare your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

Alternatively, you may view the `completed` branch on Github: [`github.com/hedera-dev/hello-future-world/tree/completed/03-hscs-smart-contract-ethersjs`](https://github.com/hedera-dev/hello-future-world/tree/completed/03-hscs-smart-contract-ethersjs)

</details>

***

**Writer**: [Brendan](https://blog.bguiz.com/) **Editors**: [Abi](https://github.com/a-ridley), [Michiel](https://www.linkedin.com/in/michielmulders/), [Ryan](https://www.linkedin.com/in/ryaneh/), [Krystal](https://www.linkedin.com/in/theekrystallee/)
