# How to Set Up Foundry to Test Smart Contracts on Hedera

[Foundry](https://book.getfoundry.sh/forge/) provides tools to developers who are developing smart contracts. One of the 3 main components of Foundry is _**Forge**: Foundry’s testing framework_. Tests are written in Solidity and are easily run with the forge test command. This tutorial will dive into configuring Foundry with Hedera to use Forge in order to write and run tests for smart contracts.

{% hint style="info" %}
**Note:** [Hashio](https://swirldslabs.com/hashio/), the SwirldsLabs hosted version of the [JSON-RPC Relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md), is in beta. If issues are encountered while using Foundry and Hashio, please create an issue in the JSON-RPC Relay GitHub [repository](https://github.com/hashgraph/hedera-json-rpc-relay).
{% endhint %}

***

## Prerequisites

* [Install Foundry](https://book.getfoundry.sh/getting-started/installation) and [a Hedera Testnet account](../../getting-started/introduction.md).
* Basic understanding of [TypeScript](https://www.typescriptlang.org/) and [Solidity](https://docs.soliditylang.org/en/latest/).
* Basic understanding of [Foundry](https://book.getfoundry.sh/) and [Forge](https://book.getfoundry.sh/forge/) framework.
* Basic understanding of the Hedera [JSON-RPC Relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md).

***

## Table of Contents

1. [Create Hedera Project](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#create-a-hedera-project-and-install-the-hedera-js-sdk)
2. [Configure Foundry](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#configure-foundry-in-your-hedera-project)
3. [Create Tests](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#create-tests)
4. [Deploy Smart Contract](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#deploy-your-smart-contract)
5. [Forge Gas Reports](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#forge-gas-reports)
6. [Summary](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#summary)
7. [Additional Resources](how-to-set-up-foundry-to-test-smart-contracts-on-hedera.md#additional-resources)

***

## Create a Hedera Project and Install the Hedera JS SDK

Open your terminal and create a directory called `my-hedera-project` by running the following command:

```bash
mkdir my-hedera-project && cd my-hedera-project
```

Initialize a node project and accept all defaults:

```bash
npm init -y
```

Install dependencies and the Hedera JavaScript SDK:

```bash
npm install --save @hashgraph/sdk 
```

In your project root directory, create your `.env` file and fill the contents with your account Id and private key from the [developer portal](https://portal.hedera.com/) where you created your Hedera Testnet account. They will be used to create your Hedera client.

```
OPERATOR_ACCOUNT_ID=<account id>
OPERATOR_PRIVATE_KEY=<private key>
```

Create a file named `index.ts` and create your Hedera client.

```javascript
import { Client, AccountId, PrivateKey, Hbar, TokenId, ContractId } from "@hashgraph/sdk";
import fs from "fs";
import dotenv from "dotenv";
import { deployContract } from "./services/hederaSmatContractService";
 
dotenv.config();
// create your client
const accountIdString = process.env.OPERATOR_ACCOUNT_ID;
const privateKeyString = process.env.OPERATOR_PRIVATE_KEY;

if (accountIdString === undefined || privateKeyString === undefined ) { throw new Error('account id and private key in env file are empty')}

const operatorAccountId = AccountId.fromString(accountIdString);
const address = operatorAccountId.toSolidityAddress();
const operatorPrivateKey = PrivateKey.fromString(privateKeyString);
 
const client = Client.forTestnet().setOperator(operatorAccountId, operatorPrivateKey);
client.setDefaultMaxTransactionFee(new Hbar(100));
```

In the root directory, create two new empty folders: cache and test. Inside the cache folder, create the subfolder `forge-cache`. Inside the test folder, create a subfolder called `foundry`. Your Hedera project directory structure should look similar to this:

```
.
├── cache
      ├──forge-cache
├── contracts
├── test
      ├──foundry
├── .env
├── .gitignore
├── index.ts
├── package-lock.json
├── package.json
└── tsconfig.json
```

#### Create a Sample Foundry Project

Open a new terminal window and Create a sample Foundry project by running the following command in the new terminal:

```bash
forge init <project-name>
```

Your foundry project will have the following directory structure

```
.
├── lib
├── script
├── src
└── test
```

#### Copy the lib/forge-std folder from the sample foundry project into your Hedera Project

Foundry manages dependencies using git submodules by default. Hedera manages dependencies through npm modules. The default sample project comes with one dependency installed: Forge Standard Library. We need to get that over to our Hedera project. Copy the lib/forge folder from the sample Foundry project and put it in your Hedera project. Your Hedera project directory structure will look like this:

```
.
├── cache
      ├──forge-cache
├── contracts
├── lib
├── test
      ├──foundry
├── .env
├── .gitignore
├── index.ts
├── package-lock.json
├── package.json
└── tsconfig.json
```

***

## Configure Foundry in your Hedera project

Foundry’s default directory for contracts is `src/`, but we will need it to map to contracts. Tests will map to our `test/foundry` path, and the `cache_path` will map to our `cache/forge-cache` directory.

Let’s create a Foundry configuration file to update how Foundry behaves. Create a file in the root of your Hedera project and name it `foundry.toml` and add the following lines:

```toml
[profile.default]
src = 'contracts'
out = 'out'
libs = ['node_modules', 'lib']
test = 'test/foundry'
cache_path = 'forge-cache'
```

#### Remap dependencies

Foundry manages dependencies using git submodules and has the ability to remap them to make them easier to import. Let’s create a new file at the project's root directory called `remappings.txt` and write the following lines:

```
ds-test/=lib/forge-std/lib/ds-test/src/
forge-std/=lib/forge-std/src/
```

And what these remappings translate to is:

* To import from `ds-test` we write import “ds-test/TodoList.sol”;
* To import from `forge-std` we write import “forge-std/Contract.sol”;

***

## Create Tests

#### Create a smart contract

Create a smart contract file named `TodoList.sol` under the _contracts_ directory.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

struct Todo {
  uint id;
  string description;
  bool completed;
}

contract TodoList {
    // need to keep count of todos as we insert into map
    uint256 public numberOfTodos = 0;

  mapping(uint => Todo) public todos;

  function createTodo(string memory description) public {
    numberOfTodos++;
    todos[numberOfTodos] = Todo(numberOfTodos, description, false);
  }

  function getTodoById(uint256 id) public view returns (Todo memory) {
    return todos[id];
  }

  function toggleCompleted(uint _id) public {
    Todo memory _todo = todos[_id];
    _todo.completed = !_todo.completed;
    todos[_id] = _todo;
  }
}
```

#### Create tests written in Solidity

Create a file named `TodoList.t.sol` under test/foundry and write the following test:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "contracts/TodoList.sol";

contract TodoListTest is Test {
    TodoList public todoList;
    uint256 numberOfTodos;
  
    // Arrange everything you need to run your tests
    function setUp() public {
        todoList = new TodoList();
    }

    function test_CreateTodo() public {
        // arrange
        todoList.createTodo("Feed my dog");

        // act
        numberOfTodos = todoList.numberOfTodos();

        // assert
        assertEq(numberOfTodos, 1);
    }

    function test_GetTodoById() public {
        // arrange
        todoList.createTodo("Pack my bags");

        // act
        Todo memory todo = todoList.getTodoById(1);

        // assert
        assertEq(todo.description, "Pack my bags");
    }

    function test_ToggleCompleted() public {
        // arrange
        todoList.createTodo("Update my calendar");

        // act
        todoList.toggleCompleted(1);

        // assert
        Todo memory todo = todoList.getTodoById(1);
        assertEq(todo.completed, true);
    }
}
```

#### Build and run your tests

In your terminal, ensure you are in your forge project directory and run the following command to build:

```bash
forge build
```

<figure><img src="https://images.hedera.com/forge-build.png?w=611&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680223873&#x26;s=2ca7a10193d35a10013e8c5c397bdf18" alt=""><figcaption></figcaption></figure>

To run your tests run the following command:

```bash
forge test
```

<figure><img src="https://images.hedera.com/forge-test.png?w=531&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680223875&#x26;s=48aa3022e94b7c2d04206d2bea7e5822" alt=""><figcaption></figcaption></figure>

***

## Deploy Your Smart Contract

#### Step 1: Compile your smart contract using solc

```bash
solcjs --bin contracts/TodoList.sol -o binaries 
```

#### Step 2: Read the compiled bytecode

In your index.ts file import _fs_ in order to read your smart contracts bytecode.

```javascript
import fs from "fs";
const bytecode = fs.readFileSync("binaries/contracts_ERC20FungibleToken_sol_ERC20FungibleToken.bin");
```

#### Step 3: Create a function to deploy your smart contract

```typescript
import { ContractCreateFlow, Client} from '@hashgraph/sdk';
 
/*
* Stores the bytecode and deploys the contract to the Hedera network.
* Returns an array with the contractId and contract solidity address.
*
* Note: This single call handles what FileCreateTransaction(), FileAppendTransaction() and
* ContractCreateTransaction() classes do.
*/
export const deployContract = async (client: Client, bytecode: string | Uint8Array, gasLimit: number) => {
 const contractCreateFlowTxn = new ContractCreateFlow()
   .setBytecode(bytecode)
   .setGas(gasLimit);
 
 console.log(`- Deploying smart contract to Hedera network`)
 const txnResponse = await contractCreateFlowTxn.execute(client);
 
 const txnReceipt = await txnResponse.getReceipt(client);
 const contractId = txnReceipt.contractId;
 if (contractId === null ) { throw new Error("Somehow contractId is null.");}
 
 const contractSolidityAddress = contractId.toSolidityAddress();
 
 console.log(`- The smart contract Id is ${contractId}`);
 console.log(`- The smart contract Id in Solidity format is ${contractSolidityAddress}\n`);
 
 return [contractId, contractSolidityAddress];
}
```

#### Step 4: Call your function that deploys your smart contract to the Hedera network

```javascript
const hederaFoundryExample = async () => {
   // read the bytecode
   const bytecode = fs.readFileSync("binaries/contracts_Counter_sol_Counter.bin");
  
   // Deploy contract
   const gasLimit = 1000000;
   const [contractId, contractSolidityAddress] = await deployContract(client, bytecode, gasLimit);
}
hederaFoundryExample();
```

***

## Forge Gas Reports

Forge has functionality built in to give you [gas reports](https://book.getfoundry.sh/forge/gas-reports) of your contracts. First, configure your `foundry.toml` to specify which contracts should generate a gas report.

Add the below line to your `foundry.toml` file:

```toml
gas_reports = ["TodoList"]﻿
```

<figure><img src="https://images.hedera.com/toml-file.png?w=787&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680230303&#x26;s=fdac820b47bbfe031ffdb390f807fc44" alt=""><figcaption></figcaption></figure>

In order to generate a gas report run the following command in your VSCode terminal:

```javascript
forge test –gas-report
```

<figure><img src="https://images.hedera.com/gas-report-first-article.png?w=818&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680223929&#x26;s=1c04f447a1c32eef4ec55a6157394159" alt=""><figcaption></figcaption></figure>

Your output will show you an estimated gas average, median, and max for each contract function and total deployment cost and size.

***

## Summary

In this tutorial, we have learned how to configure Foundry to work with a Hedera project to test our smart contracts using the [forge](https://book.getfoundry.sh/forge/) framework. We also learned how to generate gas reports for our smart contracts.

Happy Building! Feel free to reach out in [Discord](https://hedera.com/discord) if you have any questions!

***

## Additional Resources

**➡** [**Project Repository**](https://github.com/hedera-dev/hedera-smart-contract-testing-with-foundry)

**➡** [**Foundry Documentation**](https://book.getfoundry.sh/)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Abi, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://www.linkedin.com/in/a-ridley/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/a-ridley/">https://www.linkedin.com/in/a-ridley/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
