# How to Test a Solidity Event with Foundry 

Foundry supports the use of cheatcodes. Cheatcodes allow you to go beyond testing the outputs of your smart contracts. They allow developers to manipulate the state of the blockchain, test for reverts, 
and events. In this tutorial, we will focus on the cheatcode `vm.expectEmit` to test a solidity event.

## What you will accomplish

- ✅ Use the vm.expectEmit Cheatcode
- ✅ Test a Solidity event


---

### Steps

### Set up project

To follow along, start with the `main` branch,
which is the *default branch* of this repo.
This gives you the initial state from which you can follow along
with the steps as described in the tutorial.

{% hint style="warning" %}
Learn how to `Setup Foundry and Write a Basic Unit Test` by completing the linked tutorial. This tutorial will not walkthrough setting up Foundry.
{% endhint %}

`forge` manages dependencies by using git submodules. Clone the following project and pass `--recurse-submodules` to the `git clone` command to automatically initialize and update the submodule in the repository.

```shell
git clone --recurse-submodules git@github.com:hedera-dev/test-an-event-with-foundry.git
```

<details>

<summary>Alternative with `git` and SSH</summary>

If you have [configured SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
to work with `git`, you may wish use this command instead:

```shell
git clone --recurse-submodules https://github.com/hedera-dev/test-an-event-with-foundry.git
```

</details>

Open the project `test-an-event-with-foundry`, in a code editor.

 
 Open the contract located in  `src/TodoList.sol` in your code editor.

 If you completed the previous tutorial, you may notice the contents of the contract `TodoList.sol` have changed. Specifcally, there is a CreateTodo event that has been declared and is emitted in the `createTodo()` function.


### Write the test

An almost-complete test has already been prepared for you. It's located at `test/TodoList.t.sol`.
You will only need to make a few modifications (outlined below)
for it to run successfully.

{% hint style="warning" %}
Look for a comment in the code to locate the specific lines of code which you will need to edit. For example, in this step, look for this:
    // Step (1) in the accompanying tutorial
You will need to delete the inline comment that looks like this: /* ... */. Replace it with the correct code.
{% endhint %}

#### Step 1: Define the expected event

Declare the CreateTodo event in your test contract. This CreateTodo is identical to the one that is declared in `TodoList.sol`.

```solidity
event CreateTodo(address indexed creator, uint256 indexed todoIndex, string description);
```

#### Step 2: Get the current number of todos

Get the number of todos in order to generate the expected event based on the current state of the contract.

```solidity
uint256 numberOfTodosBefore = todoList.getNumberOfTodos();
```


#### Step 3: Specify the event data to test

The cheatcode `vm.expectEmit()` will be used to check if the event is emitted.
This cheatcode expects four inputs:

* `bool checkTopic1` asserts the first index
* `bool checkTopic2` asserts the second index
* `bool checkTopic3` asserts the data for index 3
* `bool checkData` asserts the remaining data emitted by the event
* `address emitter` asserts the emitting address matches

The event being tested includes two indexed arguments: `address indexed creator` and `uint256 indexed todoIndex`. Therefore, we want to assert the matching of topic 1, topic 2, the non-indexed data, and the emitting address with our actual event.

```solidity
vm.expectEmit(true, true, false, true, address(todoList));
```


#### Step 4: Emit the expected event

Emit the expected event with the following parameters: the `creator` of the todo is this test contract, the `todoIndex` is the current `numberOfTodos` + 1, and the `description` is set to "a new todo."

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

## Complete

Congratulations, you have completed how to test a solidity event using Foundry.
You have learned how to:
- ✅ Use the vm.expectEmit Cheatcode
- ✅ Test a Solidity event
