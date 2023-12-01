---
description: >-
  Hello World sequence: Write a smart contract in Solidity, compile it, then use
  Hedera Smart Contract Service (HSCS) to deploy it and interact with it.
---

# HSCS: Smart Contract

## What you will accomplish

* [ ] Write a smart contract
* [ ] Compile the smart contract
* [ ] Deploy a smart contract
* [ ] Update smart contract state
* [ ] Query smart contract state

***

## Prerequisites

Before you begin, you should have completed the "Create and Fund Account" sequence:&#x20;

{% content-ref url="create-fund-account.md" %}
[create-fund-account.md](create-fund-account.md)
{% endcontent-ref %}

***

## Get started

### Set up project

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

Next, install the dependencies using `npm`.

```shell
npm install
```

You will also need to install a Solidity compiler.
This time use the `--global` flag.

```shell
npm install --global solc@0.8.17
```

{% hint style="info" %}
[Solidity](https://docs.soliditylang.org/) is a programming language that was designed specifically for writing smart contracts in. The Solidity compiler outputs bytecode that can be run by an [Ethereum Virtual Machine (EVM)](https://ethereum.org/en/developers/docs/evm/) implementation.

Note that although the `npm` package is named `solc`, the executable exposed on your command line is named `solcjs`.
{% endhint %}

Then open both these files in a code editor, such as VS Code.

* `my_contract.sol`
* `script-hscs-smart-contract-ethersjs.js`

***

### Write the smart contract

An almost-complete smart contract has already been prepared for you, `my_contract.sol`.
You will only need to make one modification (outlined below)
for it to compile successfully.

#### Step 1: Get name stored in mapping

Within the `greet()` function, we would like to access the `names` mapping and retrieve the name of the account that is invoking this function. The account is identified by its EVM account alias, which is available as `msg.sender` within Solidity code.

```js
        string memory name = names[msg.sender];
```

***

### Compile the smart contract

Once you have completed writing the smart contract in Solidity, you will need to compile it, using the Solidity compiler installed earlier.

Invoke the compiler on your Solidity file. Then list files in the current directory.

```shell
solcjs --bin --abi ./my_contract.sol
ls
```

You should see output similar to the following.

```
my_contract.sol
my_contract_sol_MyContract.abi
my_contract_sol_MyContract.bin
```

{% hint style="info" %}
- The `.abi` file contains JSON, and describes the interface used to interact with the smart contract.
- The `.bin` file contains EVM bytecode, and this is used in the deployment of the smart contract.

Note that while the `.abi` file is human readable, the `.bin` file is *not intended* to be human readable.
{% endhint %}

***

### Configure RPC Connection

Sign up for an account at [`auth.arkhia.io/signup`](https://auth.arkhia.io/signup).

If prompted to do so, click on the link in your confirmation email.

Click on the "create project" button in the top-right corner of the Arkhia dashboard.

[![](../../.gitbook/assets/hello-world--hscs--arkhia-01-create-project.png)](../../.gitbook/assets/hello-world--account--arkhia-01-create-project.png "Arkhia RPC Configuration - 01 - Create Project")

Fill in whatever you like in the modal dialog that pops up.

[![](../../.gitbook/assets/hello-world--hscs--arkhia-02-project-form.png)](../../.gitbook/assets/hello-world--account--arkhia-02-project-form "Arkhia RPC Configuration - 02 - Project Form")

Click on the "Manage" button on the right side of your newly created project.

[![](../../.gitbook/assets/hello-world--hscs--arkhia-03-manage-project.png)](../../.gitbook/assets/hello-world--account--arkhia-03-manage-project "Arkhia RPC Configuration - 03 - Manage Project")

Now you should see the project details.

* (1) In the "Services" section, under "Network", select "Hedera Testnet".
* (2) Copy the "JSON-RPC" field.
* (3) In the "Security" section, copy the "API Key" field.

<img src="../../.gitbook/assets/hello-world--hscs--arkhia-04-project-details.drawing.svg" alt="Arkhia RPC Configuration - 04 - Project Details" class="gitbook-drawing">

In the `.env` file, edit the property with the key `RPC_URL`, replacing `YOUR_JSON_RPC_URL` with the "JSON-RPC" value followed by the "API key" value that you have just copied.

For example, if the API key field is `ABC123`, and the JSON-RPC field is `https://pool.arkhia.io/hedera/testnet/json-rpc/v1`, the line in your `.env` file should look like to this:

```
RPC_URL=https://pool.arkhia.io/hedera/testnet/json-rpc/v1/ABC123
```

<details>

<summary>Alternative RPC configuration</summary>

Arkhia is one of several different options for JSON-RPC connections.
This tutorial covers all of the different options:
[How to Connect to Hedera Networks Over RPC](https://docs.hedera.com/hedera/tutorials/more-tutorials/json-rpc-connections).

</details>

***

### Write the script

An almost-complete script has already been prepared for you, `script-hscs-smart-contract-ethersjs.js`.
You will only need to make a few modifications (outlined below)
for it to run successfully.

#### Step 2: Prepare smart contract for deployment

This script uses `ContractFactory` and `Contract` from EthersJs.

The `ContractFactory` class is used to prepare a smart contract for deployment. To do so, pass in the ABI and bytecode that were output by the Solidity compiler earlier. Also pass in the `accountWallet` object, which is used to authorised transactions, and needed for the deployment transaction.

```js
    const myContractFactory = new ContractFactory(
        abi, evmBytecode, accountWallet);
```

Upon preparation, it sends a deployment transaction to the network, and an instance of a `Contract` object is created based on the result of the deployment transaction. This is stored in a variable, `myContract`, which will be used in the next steps. This has already been done for you in the script.

#### Step 3: Invoke a smart contract transaction

When invoking functions in a smart contract, you may do so in two different ways:

* (1) With a transaction → Smart contract state may be changed.
* (2) Without a transaction → Smart contract state may be queried, but may not be changed.

The `introduce` function requires a single parameter of type `string`,
and changes the state of the smart contract to store this value.
Enter your name (or nickname) as the parameter.
For example, if you wish to use "bguiz", the invocation should look like this:

```js
    const myContractWriteTxRequest = await myContract.functions.introduce('bguiz');
```

#### Step 4: Invoke a smart contract query

In the previous step, you changed some state of the smart contract, which involved submitting a transaction to the network. This time, you are going to read some state of the smart contract. This is much simpler to do as no transaction is needed.

Invoke the `greet` function and save its response to a variable, `myContractQueryResult`. This function does not take any parameters.

```js
    const [myContractQueryResult] = await myContract.functions.greet();
```

***

### Run the script

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

* (1) The contract exists
* (2) Under the "Contract Details" section,
  its "Compiler Version" field matches the version of
  the Solidity compiler that you used (`0.8.17`)
* (3) Under the "Recent Contract Calls" section,
  There should be 2 transactions:
  * (A) The transaction with the earlier timestamp (bottom) should be the deployment transaction.
    * Navigate to this transaction by clicking on the timestamp.
    * Under the "Contract Result" section, the "Input - Function & Args" field
      should be a *fairly long* set of hexadecimal values.
    * This is the EVM bytecode output by the Solidity compiler.
    * Navigate back to the Contract page (browser `⬅` button).
  * (B) The transaction with the later timestamp (top) should be the transaction in which the `introduce` function was invoked.
    * Navigate to this transaction by clicking on the timestamp.
    * Under the "Contract Result" section, the "Input - Function & Args" field
      should be a *fairly short* set of hexadecimal values.
    * This is the representation of the function identifier (first 8 characters),
      and the input string value (e.g. `0x5626775697a0` for `bguiz`).
    * Navigate back to the Contract page (browser `⬅` button).

<img src="../../.gitbook/assets/hello-world--hscs--contract.drawing.svg" alt="HSCS contract in Hashscan, with annotated items to check." class="gitbook-drawing">

Open `myContractWriteTxExplorerUrl` in your browser.
Note that this should be the same page as "the transaction with the later timestamp".
Check that:

* (1) The transaction exists
* (2) Its "Type" field is "ETHEREUM TRANSACTION"
* (3) Under the "Contract Result" section,
  its "From" field matches the value of `accountId`
* (4) Under the "Contract Result" section,
  its "To" field matches the value of `myContractAddress`

<img src="../../.gitbook/assets/hello-world--hscs--transaction.drawing.svg" alt="HSCS transaction in Hashscan, with annotated items to check." class="gitbook-drawing">

***

## Complete

Congratulations, you have completed the **Hedera Smart Contract Service** Hello World sequence! 🎉🎉🎉

You have learned how to:

* [x] Write a smart contract
* [x] Compile the smart contract
* [x] Deploy a smart contract
* [x] Update smart contract state
* [x] Query smart contract state

***

### Next Steps

Now that you have completed this Hello World sequence, you have interacted with Hedera Smart Contract Service (HSCS). There are [other Hello World sequences](./) for Hedera File Service (HFS), and Hedera Token Service (HTS), which you may wish to check out next.

You may also wish to check out the more detailed [HSCS workshop](https://docs.hedera.com/hedera/tutorials/smart-contracts/hscs-workshop/), which goes into much more depth.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/), is intended to be used alongside this tutorial.

To skip ahead to the final state, use the `completed` branch. This gives you the final state with which you can compare your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

To see the full set of differences between the initial and final states of the repo, you can use `diff`.

```shell
cd 03-hscs-smart-contract-ethersjs/
git diff main..completed -- ./
```

Alternatively, you may view the `diff` rendered on Github: [`hedera-dev/hello-future-world/compare/main..completed`](https://github.com/hedera-dev/hello-future-world/compare/main..completed) (This will show the `diff` for _all_ sequences.)

Note that the branch names are delimited by `..`, and not by `...`, as the latter finds the `diff` with the latest common ancestor commit, which _is not_ what we want in this case.

</details>

***

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody>
<tr><td align="center"><p>Writer: Brendan, DevRel Engineer</p><p><a href="https://github.com/bguiz">GitHub</a> | <a href="https://blog.bguiz.com">Blog</a></p></td><td><a href="https://blog.bguiz.com">https://blog.bguiz.com</a></td></tr>
<tr><td align="center"><p>Editor: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr>
<tr><td align="center"><p>Editor: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr>
<tr><td align="center"><p>Editor: Ryan Arndt, DevRel Education</p><p><a href="https://github.com/swirlds-ryan">GitHub</a> | <a href="https://www.linkedin.com/in/ryaneh/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ryaneh/">https://www.linkedin.com/in/ryaneh/</a></td></tr>
</tbody></table>

***
