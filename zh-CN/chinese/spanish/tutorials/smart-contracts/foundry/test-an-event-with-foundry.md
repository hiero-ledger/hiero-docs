---
description: In this tutorial, you'll learn how to use Foundry's 'cheatcodes'—special commands that allow you to test and manipulate blockchain states. We'll focus on the `vm.expectEmit` cheatcode to test events.
---

# How to Test A Solidity Event

## What you will accomplish

- [ ] Use the `vm.expectEmit` Cheatcode
- [ ] Test a Solidity event

## Prerequisites

Before you begin, you should be familiar with the following:

- [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Solidity](https://docs.soliditylang.org/en/latest/)
- [Foundry](https://book.getfoundry.sh/)

<details>

<summary>Also, you should have the following set up on your computer ⬇</summary>

- [x] git installed
  - Minimum version: 2.37
  - Recommended: [Install Git (Github)](https://github.com/git-guides/install-git)
- [x] A code editor or IDE
  - Recommended: [VS Code. Install VS Code (Visual Studio)](https://code.visualstudio.com/docs/setup/setup-overview)
- [x] NodeJs + npm installed
  - Minimum version of NodeJs: 18
  - Minimum version of npm: 9.5
  - Recommended for Linux & Mac: [nvm](https://github.com/nvm-sh/nvm)
  - Recommended for Windows: [nvm-windows](https://github.com/coreybutler/nvm-windows)
- [x] foundry `forge` and `cast` installed
  - `forge` Minimum version: 0.2.0
  - `cast` Minimum version: 0.2.0

</details>

<details>

<summary>Check your prerequisites set up ⬇</summary>

Open your terminal, and enter the following commands.

```shell
git --version
code --version
node --version
npm --version
forge --version
cast --version
```

Each of these commands should output some text that includes a version number, for example:

```
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

forge --version
0.2.0 (6fcbbd8 2023-12-15T00:29:51.472038000Z)

cast --version
0.2.0 (6fcbbd8 2023-12-15T00:29:51.851258000Z)

```

If the output contains text similar to `command not found`, please install that item.

</details>

***

## Get started

### Set up project

To follow along, start with the `main` branch, which is the _default branch_ of this repository. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

{% hint style="warning" %}
Learn how to setup foundry by completing the [Setup Foundry and Write a Basic Unit Test tutorial](https://docs.hedera.com/hedera/tutorials/smart-contracts/foundry/setup-foundry-and-write-basic-unit-test). This tutorial will not walkthrough setting up Foundry.
{% endhint %}

`forge` manages dependencies by using git submodules. Clone the following project and pass `--recurse-submodules` to the `git clone` command to automatically initialize and update the submodule in the repository.

```shell
git clone --recurse-submodules git@github.com:hedera-dev/test-an-event-with-foundry.git
```

### Install the submodule dependencies

```shell
forge install
```

Open the project `test-an-event-with-foundry`, in a code editor.

If you completed the previous tutorial, you may notice the contents of the contract `TodoList.sol` have changed. Specifcally, there is a CreateTodo event that has been declared and is emitted in the `createTodo()` function.

### Write the test

An almost-complete test has already been prepared for you. It's located at `test/TodoList.t.sol`. You will only need to make a few modifications (outlined below) for it to run successfully.

{% hint style="warning" %}
Look for a comment in the code to locate the specific lines of code which you will need to edit. For example, in this step, look for this: // Step (1) in the accompanying tutorial You will need to delete the inline comment that looks like this: /\* ... \*/. Replace it with the correct code.
{% endhint %}

#### Step 1: Define the expected event

Declare the `CreateTodo` event in your test contract. This event is identical to the one that is declared in `TodoList.sol`.

```solidity
event CreateTodo(address indexed creator, uint256 indexed todoIndex, string description);
```

#### Step 2: Get the current number of todos

Grab the number of todos, as it will be used to create the expected event according to the current state of the contract.

```solidity
uint256 numberOfTodosBefore = todoList.getNumberOfTodos();
```

#### Step 3: Specify the event data to test

The cheatcode `vm.expectEmit()` will be used to check if the event is emitted. This cheatcode expects four inputs:

- `bool checkTopic1` asserts the first index
- `bool checkTopic2` asserts the second index
- `bool checkTopic3` asserts the data for index 3
- `bool checkData` asserts the remaining data emitted by the event
- `address emitter` asserts the emitting address matches

The event being tested includes two indexed arguments: `address indexed creator` and `uint256 indexed todoIndex`. Therefore, we want to assert the matching of topic 1, topic 2, the non-indexed data, and the emitting address with our actual event.

```solidity
vm.expectEmit(true, true, false, true, address(todoList));
```

#### Step 4: Emit the expected event

Emit the expected event with the following parameters:

- the `creator` of the todo is this test contract with address `0x7FA9385bE102ac3EAc297483Dd6233D62b3e1496`,
- the `todoIndex` is the current `numberOfTodos` + 1,
- the `description` is set to "a new todo."

```solidity
emit CreateTodo(address(this), numberOfTodosBefore + 1, 'a new todo');
```

#### Step 5: Execute the contract function that emits the event

Execute `TodoList.sol`'s `createTodo()` function.

```solidity
todoList.createTodo(address(this), 'a new todo');
```

### Run the test

#### Step 6: Execute the test

```shell
forge test --match-test test_emit_createTodoEvent -vvvv
```

You should see output similar to the following:

```
[⠢] Compiling...
No files changed, compilation skipped

Running 1 test for test/TodoList.t.sol:TodoListTest
[PASS] test_emit_createTodoEvent() (gas: 84576)
Traces:
  [84576] TodoListTest::test_emit_createTodoEvent()
    ├─ [2325] TodoList::getNumberOfTodos() [staticcall]
    │   └─ ← 0
    ├─ [0] VM::expectEmit(true, true, false, true, TodoList: [0x5615dEB798BB3E4dFa0139dFa1b3D433Cc23b72f])
    │   └─ ← ()
    ├─ emit CreateTodo(creator: TodoListTest: [0x7FA9385bE102ac3EAc297483Dd6233D62b3e1496], todoIndex: 1, description: "a new todo")
    ├─ [70920] TodoList::createTodo(TodoListTest: [0x7FA9385bE102ac3EAc297483Dd6233D62b3e1496], "a new todo")
    │   ├─ emit CreateTodo(creator: TodoListTest: [0x7FA9385bE102ac3EAc297483Dd6233D62b3e1496], todoIndex: 1, description: "a new todo")
    │   └─ ← 1
    └─ ← ()

Test result: ok. 1 passed; 0 failed; 0 skipped; finished in 1.10ms
 
Ran 1 test suites: 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

The test passed and shows the event is working as expected.

## Complete

Congratulations, you have completed how to test a solidity event using Foundry. You have learned how to:

- [x] Use the vm.expectEmit Cheatcode
- [x] Test a Solidity event

***

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr><tr><td align="center"><p>Editor: Michael Garber, Developer Advocate</p><p><a href="https://github.com/mgarbs">GitHub</a> | <a href="https://twitter.com/michaelgarber87">Twitter</a></p></td><td><a href="https://twitter.com/michaelgarber87">https://twitter.com/michaelgarber87
</a></td></tr></tbody></table>

***
