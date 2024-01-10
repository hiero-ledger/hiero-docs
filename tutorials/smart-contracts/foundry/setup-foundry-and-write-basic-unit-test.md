---
description: >-
  Using an existing Hedera node project with the JavaScript SDK, learn how to set up Foundry to be able to leverage Forge, their command-line tool, to run your smart contract tests written in Solidity. 
---

# How to Setup Foundry and Write a Basic Unit Test

## What you will accomplish

* [ ] Configure Foundry and Forge with a Hedera Project
* [ ] Write unit tests in Solidity
* [ ] Run your tests using Foundry `forge` command
* [ ] Create a Forge Gas Report

## Prerequisites

Before you begin, you should be familiar with the following:

- [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Solidity](https://docs.soliditylang.org/en/latest/)
- [Foundry](https://book.getfoundry.sh/)

Have the following set up on your computer:

* [ ] git installed
    * Minimum version: 2.37
    * Recommended: [Install Git (Github)](https://github.com/git-guides/install-git)
* [ ] A code editor or IDE
    * Recommended: [VS Code. Install VS Code (Visual Studio)](https://code.visualstudio.com/docs/setup/setup-overview)
* [ ] NodeJs + npm installed
    * Minimum version of NodeJs: 18
    * Minimum version of npm: 9.5
    * Recommended for Linux & Mac: [nvm](https://github.com/nvm-sh/nvm)
    * Recommended for Windows: [nvm-windows](https://github.com/coreybutler/nvm-windows)

<details>

<summary>Check your prerequisites set up</summary>

Open your terminal, and enter the following commands.

```shell
git --version
code --version
node --version
npm --version
```

Each of these commands should output some text that includes a version number, for example:

```text
git --version
git version 2.39.2 (Apple Git-143)

code --version
1.81.1
6c3e3dba23e8fadc360aed75ce363ba185c49794
arm64

node --version
v20.6.1

npm --version
9.8.1

```

If the output contains text similar to `command not found`, please install that item.

If the version number that is output is **lower** than the required versions, please re-install or update that item.

If the version number that is output is **same or higher** than the required versions, you have met the prequisites! 🎉

</details>

***

## Get started

### Set up project
To follow along, start with the `main` branch, which is the _default branch_ of this repository. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

```shell
git clone git@github.com:hedera-dev/setup-foundry-and-write-basic-unit-test.git
```

<details>

<summary>Alternative with `git` and SSH</summary>

If you have [configured SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
to work with `git`, you may wish use this command instead:

```shell
git clone git@github.com:hedera-dev/setup-foundry-and-write-basic-unit-test.git
```

</details>


### Add a submodule
Forge manages dependencies by using [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules). Run the steps below to add and install the git submodules necessary to use Forge.

Add the Forge Standard Library repository as a submodule

```shell
git submodule add https://github.com/foundry-rs/forge-std lib/forge-std
```

This command will add the forge standard library into our Hedera project by creating a folder named `lib`. The forge standard library is the preferred testing library when working with Foundry.

Install the submodule dependencies 

```shell
forge install
```

### Remap dependencies
In order to make the import of the forge standard library easier to write, we will remap the dependecy. 

Create a new text file under the root directory named `remappings.txt`

Paste in the following line of code

```
forge-std/=lib/forge-std/src/
```

When we  want to import from `forge-std` we will write: `import "forge-std/Contract.sol"`

### Setup the test file
 
 A complete smart contract named `TodoList.sol` has already been prepared for you and is under the `src` directory. This is the contract we are going to test in this tutorial.

**Step 1: Create a new directory named `test` in the root directory**

```shell
mkdir test
```

 **Step 2: Open the project `setup-foundry-and-write-basic-unit-test`, in a code editor.**


**Step 3: Create a file named TodoList.t.sol under the `test` folder**

Paste the following code in the newly created file. 

```sol
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "src/TodoList.sol";

contract TodoListTest is Test {

      function setUp() public {

    }  
}
```

On line 7 we see our `TodoListTest` contract inherits Forge Standard Library's Test contract which provides us access to the necessary functionality to test our smart contracts.


<figure><img src="../../../../.gitbook/assets/foundry-test-contract.svg" alt="TodoList.t.sol is a test contract that imports forge standard library test contract and our contract."><figcaption><p>TodoList Test Contract</p></figcaption></figure>

 
### Write a test

**Step 1: Create your test instance**

Create an instance of the contract `TodoList.sol` in order to be able to test it.

```solidity
TodoList public todoList;
```

{% hint style="warning" %}
Look for a comment in the code to locate the specific lines of code which you will need to edit. For example, in this step, look for this:
    // Step (1) in the accompanying tutorial
You will need to delete the inline comment that looks like this: /* ... */. Replace it with the correct code.
{% endhint %}


**Step 2: Deploy a new contract everytime you run a test**

The `setup()` function is invoked before each test case is run and is optional. Have the `TodoList.t.sol` test contract deploy a new TodoList contract by adding the following code in the `setUp()` function.

```solidity
todoList = new TodoList();
```

**Step 3: Test CreateTodo() function**

 Create a test that ensures the `createTodo()` function is working as expected. All tests that you want to run must be prefixed with the `test` keyword.

```solidity
   function test_createTodo_returnsNumberOfTodosIncrementedByOne() public {
     // get the current number of todos
     uint256 numberOfTodosBefore = todoList.getNumberOfTodos();

     // create a new todo and save the number of todos
     uint256 numberOfTodosAfter = todoList.createTodo("A new todo for you!");

     assertEq(numberOfTodosAfter, (numberOfTodosBefore + 1), "create todo test");
   }
```

### Build and run your test

In the terminal, ensure you are in the root project directory and build the project.

```shell
forge build
```

You should see output similar to the following:

```
[⠒] Compiling...
[⠔] Compiling 22 files with 0.8.23
[⠑] Solc 0.8.23 finished in 3.44s
Compiler run successful!
```

In the terminal, run your test.

```shell
forge test
```

You should see output similar to the following:

```
[⠢] Compiling...
No files changed, compilation skipped

Running 1 test for test/TodoList.t.sol:TodoListTest
[PASS] test_createTodo_returnsNumberOfTodosIncrementedByOne() (gas: 76346)
Test result: ok. 1 passed; 0 failed; 0 skipped; finished in 1.12ms
 
Ran 1 test suites: 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

By default `forge test` only displays a minimal summary of a test whether it failed or passed. You can display more detailed information by using the `-v` flag and increasing the verbosity. 

In the terminal, re-run your test but include a verbosity level 4. This will display stack traces for all of the tests, including the setup.

```shell
forge test -vvvv
```

You should see output similar to the following:

```
[⠢] Compiling...
No files changed, compilation skipped

Running 1 test for test/TodoList.t.sol:TodoListTest
[PASS] test_createTodo_returnsNumberOfTodosIncrementedByOne() (gas: 76346)
Traces:
  [76346] TodoListTest::test_createTodo_returnsNumberOfTodosIncrementedByOne()
    ├─ [2325] TodoList::getNumberOfTodos() [staticcall]
    │   └─ ← 0
    ├─ [68126] TodoList::createTodo("A new todo for you!")
    │   └─ ← 1
    └─ ← ()

Test result: ok. 1 passed; 0 failed; 0 skipped; finished in 580.08µs
 
Ran 1 test suites: 1 tests passed, 0 failed, 0 skipped (1 total tests)
```


## Forge Gas Reports

Forge has functionality built in to give you [gas reports](https://book.getfoundry.sh/forge/gas-reports) of your contracts. You can specify which contract should generate a gas report in the `foundry.toml` file.

The `foundry.toml` file is a configuration file that is used to configure forge. 

Create a new file in the root directory named `foundry.toml`. Paste the following contents.

```toml
[profile.default]
src = 'src'
out = 'out'
libs = ['lib']

[rpc_endpoints]
h_testnet = "https://testnet.hashio.io/api"
h_mainnet = "https://mainnet.hashio.io/api"

# See more config options https://github.com/foundry-rs/foundry/tree/master/config
```

**Step 5: Add the following line to your foundry.toml file.**

```toml
gas_reports = ["TodoList"]
```

In the terminal, generate a gas report.

```shell
forge test -gas-report
```

You should see output similar to the following:

<figure><img src="../../../../.gitbook/assets/todolist-gas-report.png" alt=""><figcaption><p>Test Contract Gas Report</p></figcaption></figure>

Your output will show you an estimated gas average, median, and max for each contract function used in a test and total deployment cost and size.

## Complete

Congratulations, you have completed how to setup Foundry and write a basic unit test.

You have learned how to:
- ✅ Configure Foundry and forge with a hedera project
- ✅ Write unit tests in Solidity
- ✅ Run your tests using Foundry `forge` command
- ✅ Create a forge gas report

***

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th>
<tr><td align="center"><p>Writer: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr>
</tbody></table>

***