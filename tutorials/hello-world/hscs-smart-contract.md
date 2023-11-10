---
description: >-
  Hello World sequence:
  Write a smart contract in Solidity, compile it,
  then use Hedera Smart Contract Service (HSCS) to deploy it and interact with it.
---

# HSCS: Smart contract

## What you will accomplish

- ✅ Write a smart contract
- ✅ Compile the smart contract
- ✅ Deploy a smart contract
- ✅ Update smart contract state
- ✅ Query smart contract state

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/),
is intended to be used alongside this tutorial.

***

## Prerequisites

Before you begin, you should have completed the "Create and Fund Account" sequence:
[`docs.hedera.com/tutorials/hello-world/create-fund-account`](https://docs.hedera.com/tutorials/hello-world/create-fund-account/).

***

## Steps

### Set up project

To follow along, start with the `main` branch,
which is the *default branch* of the repo.
This gives you the initial state from which you can follow along
with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence.
If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file
and populate it as required.
{% endhint %}

In the terminal, reuse the `.env` file by copying
the one that you have previously created into the directory for this sequence.

```shell
cd 03-hscs-smart-contract-ethersjs/
cp ../00-create-fund-account/.env ./
```

Next, install the dependencies using `npm`.
Then open the script file in a code editor.

```shell
npm install
code script-hscs-smart-contract-ethersjs.js
```

You will also need to install a Solidity compiler.
This time use the `--global` flag.

```shell
npm install --global solc@0.8.17
```

{% hint style="info" %}
Note that although the `npm` package is named `solc`,
the executable exposed on your command line is named `solcjs`.
{% endhint %}

***

### Write the smart contract

An almost-complete smart contract has already been prepared for you,
and you will only need to make one modification (outlined below)
for it to compile successfully.

#### Step 1: Get name stored in mapping

Within the `greet()` function, we would like to access the `names` mapping
and retrieve the name of the account that is invoking this function.
The account is identified by its EVM account alias,
which is available as `msg.sender` within Solidity code.

```js
        string memory name = names[msg.sender];
```

***

### Compile the smart contract

Once you have completed writing the smart contract in Solidity,
you are not able to deploy it onto the network just yet.
You will need to compile it first, using the Solidity compiler installed earlier.

{% hint style="info" %}
HSCS executes a virtual machine - the Ethereum Virtual Machine (EVM) -
which runs the smart contracts.
The EVM executes EVM bytecode that is deployed onto the network.
The Solidity compiler outputs Solidity bytecode,
which is is deployed to the network.
Note that compiled bytecode and deployed bytecode,
while *similar*, are not the same.
{% endhint %}

Invoke the compiler on your Solidity file.
Then list files in the current directory.

```shell
solcjs --bin --abi ./my_contract.sol
ls
```

You should see output similar to the following.

```text
my_contract.sol
my_contract_sol_MyContract.abi
my_contract_sol_MyContract.bin
```

{% hint style="info" %}
The `.abi` file contains JSON,
and describes the interface used to interact with the smart contract.

The `.bin` file contains EVM bytecode,
and this is used in the deployment of the smart contract.
Note that this is not intended to be human readable.
{% endhint %}

***

### Configure RPC Connection

Sign up for an account at [`auth.arkhia.io/signup`](https://auth.arkhia.io/signup).

Click on link in your confirmation email.

Click on the "create project" button in the top-right corner of the Arkhia dashboard.

[![arkhia-init-step-03.png][1]][1]

Fill in whatever you like in the modal dialog that pops up.

[![arkhia-init-step-04][2]][2]

Click on the "Manage" button on the right side of your newly created project.

[![arkhia-init-step-05][3]][3]

In the project details,
copy the "API Key" field,
and also copy the "JSON-RPC" field.

[![arkhia-init-step-06-07][4]][4]

Create a new line in the `.env` file 
with the key as `YOUR_JSON_RPC_URL`,
and with the "JSON-RPC" value followed by the "API key" value.

For example, if the API key field is `ABC123`,
and the JSON-RPC field is `https://pool.arkhia.io/hedera/testnet/json-rpc/v1`,
the new line in your `.env` file should look similar to this:

```text
RPC_URL=https://pool.arkhia.io/hedera/testnet/json-rpc/v1/ABC123
```

***

### Write the script

An almost-complete script has already been prepared for you,
and you will only need to make a few modifications (outlined below)
for it to run successfully.

#### Step 2: Prepare smart contract for deployment

This script uses `ContractFactory` and `Contract` from EthersJs.

The `ContractFactory` class is used to prepare a smart contract for deployment.
To do so, pass in the ABI and bytecode
that were output by the Solidity compiler earlier.
Also pass in the `accountWallet` object,
which is used to authorised transactions,
and needed for the deployment transaction.

```js
    const myContractFactory = new ContractFactory(
        abi, evmBytecode, accountWallet);
```

Upon preparation, it sends a deployment transaction to the network,
and an instance of a `Contract` object is created
based on the result of the deployment transaction.
This is stored in a variable, `myContract`,
which will be used in the next steps.
This has already been done for you in the script.

#### Step 3: Invoke a smart contract transaction

When invoking functions in a smart contract,
you may do so in two different ways:

- (1) With a transaction → Smart contract state may be changed.
- (2) Without a transaction → Smart contract state may be queried, but may not be changed.

The `introduce` function requires a single parameter of type `string`,
and changes the state of the smart contract to store this value.
Enter your name (or other moniker) as the parameter.
For example, if you wish to use "bguiz", the invocation should look like this:

```js
    const myContractWriteTxRequest = await myContract.functions.introduce('bguiz');
```

#### Step 4: Invoke a smart contract query

In the previous step, you changed some state of the smart contract,
which involved submitting a transaction to the network.
This time, you are going to read some state of the smart contract.
This is much simpler to do as no transaction is needed.

Invoke the `greet` function and save its response to a variable, `myContractQueryResult`.
This function does not take any parameters.

```js
    const [myContractQueryResult] = await myContract.functions.greet();
```

***

### Run the script

Run the script using the following command:

```shell
node script-hscs-smart-contract-ethersjs.js
```

You should see output similar to the following:

```text
accountId: 0.0.1201
accountAddress: 0x7394111093687e9710b7a7aEBa3BA0f417C54474
accountExplorerUrl: https://hashscan.io/testnet/address/0x7394111093687e9710b7a7aEBa3BA0f417C54474
myContractAddress: 0x9A6856897a72E790Ae765bFF997396199BDf1B72
myContractExplorerUrl: https://hashscan.io/testnet/address/0x9A6856897a72E790Ae765bFF997396199BDf1B72
myContractWriteTxHash: 0x32684e8d8e60126e171db968a4b20153ba96a40920a036dbebb48e19fb74664d
myContractWriteTxExplorerUrl: https://hashscan.io/testnet/transaction/0x32684e8d8e60126e171db968a4b20153ba96a40920a036dbebb48e19fb74664d
myContractQueryResult: Hello future - bguiz
```

Open the URL, that was output as `myContractExplorerUrl` above,
in your browser and check that:

- (1) The contract exists
- (2) Under the "Contract Details" section,
  its "Compiler Version" field matches the version of
  the Solidity compiler that you used (`0.8.17`)
- (3) Under the "Recent Contract Calls" section,
  There should be 2 transactions

Open the URL, that was output as `myContractWriteTxExplorerUrl` above,
in your browser and check that:

- (1) The transaction exists
- (2) Its "Type" field is "ETHEREUM TRANSACTION"
- (3) Under the "Contract Result" section,
  its "To" field matches the value of `myContractAddress`
  - The earlier one (bottom) should be the deployment transaction.
    Navigate to it.
    - Under the "Contract Result" section, the "Input - Function & Args" field
      should be a fairly long set of hexadecimal values.
    - This is the EVM bytecode output by the Solidity compiler.
  - The later one (top) should be the transaction in which the `introduce` function was invoked.
    Navigate to it.
    - Under the "Contract Result" section, the "Input - Function & Args" field
      should be a fairly short set of hexadecimal values.
    - This is the representation of the function identifier (first 8 characters),
      and the input string value (e.g. `0x5626775697a0` for `bguiz`).
***

## Complete

Congratulations, you have completed this Hello World sequence! 🎉🎉🎉

***

### Next Steps

Now that you have completed this Hello World sequence,
you have interacted with Hedera Smart Contract Service (HSCS).
There are [other Hello World sequences](/tutorials/hello-world/) for
Hedera File Service (HFS),
and Hedera Token Service (HTS),
which you may wish to check out next.

You may also wish to check out the more detailed
[HSCS workshop](https://docs.hedera.com/hedera/tutorials/smart-contracts/hscs-workshop/),
which goes into much more depth.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

To skip ahead to the final state, use the `completed` branch.
This gives you the final state with which you can compare
your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

To see the full set of differences between
the initial and final states of the repo,
you can use `diff`.

```shell
cd 03-hscs-smart-contract-ethersjs/
git diff main..completed -- ./
```

Alternatively, you may view the `diff`` rendered on Github:
[`hedera-dev/hello-future-world/compare/main..completed`](https://github.com/hedera-dev/hello-future-world/compare/main..completed)
(This will show the `diff` for *all* sequences.)

{% hint style="info" %}
Note that the branch names are delimited by `..`, and not by `...`,
as the latter finds the `diff` with the latest common ancestor commit,
which *is not* what we want in this case.
{% endhint %}

</details>

***

  [1]: https://i.stack.imgur.com/JY5Ck.png
  [2]: https://i.stack.imgur.com/wYNj3.png
  [3]: https://i.stack.imgur.com/yhCQp.png
  [4]: https://i.stack.imgur.com/f8A1b.png
