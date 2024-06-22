---
description: Solidity tutorial - HSCS workshop. Learn how to enable custom logic & processing on Hedera through smart contracts.
---

# Solidity

## Video

{% embed url="https://www.youtube.com/watch?v=9sHhuEP-s2U" %}
Hedera Smart Contract Service Workshop Part 3/6 | Solidity
{% endembed %}

## What smart contracts are, and are not

The term "smart contracts" is kind of a poor choice for a name, and is the source of a lot of confusion: Smart contracts are neither smart, and neither are they contracts.

They are, at their core, simply computer programs that are executable. So what is the big deal about them then? What makes them different from "regular" computer programs? The biggest one, that you probably know already, is that they are executed on blockchains/ DLTs. But there are a few others to be aware of:

- They are typically executed within a Virtual Machine (VM)
- Any state changes need to be agreed upon through network consensus
- Any state queries return values agreed upon by network consensus
- While their state is mutable, their code is not

Combine the above with the decentralized nature of blockchains/ DLTs, and you get a special breed of computer programs like no other: Deterministic, p2p execution, that is censorship resistant and interruption resistant.

You can use this powerful technology within the Hedera network too, via the Hedera Smart Contract Service. This workshop will show you how!

To start, open and edit `intro/trogdor.sol`.

## Comments

In Solidity, comment syntax is similar to what you might be familiar with from Javascript. Single line comments use `//`, and extend till the rest of the line. Multi-line comments use `/*` to begin, and `*/` to end.

```solidity
// single line comment

/* this is a
multi-line
comment. */
```

## SPDX License

[SPDX](https://spdx.org/licenses/) defines a list of software licenses, and allows you to reference one using a standard short identifier. The solidity compiler will explicitly check for this as a comment in the first line of any Solidity file. If it is missing, it will output a warning.

```solidity
// SPDX-License-Identifier: MIT
```

## Step A1: Specify solc version number

{% hint style="info" %}
Near the top of the file, you should see the following comment:

```solidity
// NOTE: specify solc version number
// Step (A1) in the accompanying tutorial
```

Note that this type of comment will be present throughout the tutorial repo that accompanies this written tutorial. Each numbered step of a _section heading_ here corresponds to the the same number in a _comment_ there. In the subsequent steps of this tutorial, you will follow the same pattern as above. However, this tutorial does not repeat the comments marking the steps for the remainder of the tutorial and instead only include the new/changed lines of code.
{% endhint %}

The pragmas simply defines the which version of the Solidity compiler, `solc`, it is intended to be compiled with.

```solidity
pragma solidity 0.8.17;
```

Here we simply specify that this file should be compiled with version `0.8.17` only. You may specify more complex rules, similar to `semver` used by npm.

{% hint style="info" %}

- [Ref: Solidity version pragma](https://docs.soliditylang.org/en/develop/layout-of-source-files.html#version-pragma)
  {% endhint %}

## Step A2: Specify name of smart contract

A smart contract is a grouping of state variables, functions, and other things. The solidity code needs to group them, and does so by surrounding them with a pair of squiggly brackets (`{` and `}`). It also needs a name - and we'll name this one `Trogdor`.

```solidity
contract Trogdor {
```

## Step A3: Primitive type state variable

A smart contract persists its state on the virtual machine, and may only be modified during a successful transaction on the network. Solidity supports many different primitive types, here let's use `uint256` as we'll be representing an unsigned integer value.

```solidity
	uint256 public MIN_FEE = 100;
```

The above would work, but in this case, we know that we will not be modifying its value, so instead of a state variable, let's use a state constant instead. This is achieved by adding the `constant` keyword to it.

```solidity
	uint256 public constant MIN_FEE = 100;
```

{% hint style="info" %}

- [Ref: Solidity value types](https://docs.soliditylang.org/en/develop/types.html#value-types)
  {% endhint %}

## Step A4: Dynamic type state variable

Smart contracts can also persist more complex types of data in its state, and this is accomplished using dynamic state variables. A `mapping` is used to represent key-value pairs, and is analogous to a Hashmap in other programming languages.

```solidity
	mapping(address => uint256) public amounts;
```

This mapping stores key-value pairs where the keys are of type `address`, and the values are of type `uint256`.

Note that both `MIN_FEE` and `amounts` have a visibility modifier of `public`. In other cases you might want `internal` or `private`,

{% hint style="info" %}

- [Ref: Solidity mapping type](https://docs.soliditylang.org/en/develop/types.html#mapping-types)
- [Ref: Solidity State Variable Visibility](https://docs.soliditylang.org/en/develop/contracts.html#state-variable-visibility)
  {% endhint %}

## Functions

Functions are the main part of the smart contract where things actually happen: Code is executed, and perhaps state is accessed or updated. The syntax of a function is somewhat similar to Javascript, with the main differences being the addition of types, and of modifiers.

```solidity
function doSomething(uint256 param1)
	public
	pure
	returns(uint256)
{
	return param1;
}
```

In the above example:

- Name: `doSomething`
- Modifiers: `public` and `pure`
- Parameter name: `param1`
- Parameter type: `uint256`
- Return type: `uint256`

## Step A5: Specify function modifiers

The `burninate` function modifies the state of the smart contract, and also accepts payment (in HBAR), so let's go with `public`, `payable` for its modifiers.

```solidity
        public
        payable
```

The `totalBurnt` function does not modify the state of the smart contract, but does access the state. It does not accept any payment. It is intended to only be called by an Externally Owned Account (EOA) or another smart contract. For this let's go with `external`, `view` for its modifiers.

```solidity
        external
        view
```

Just like state variables, functions may have the have visibility modifiers `public`, `private`, and `internal`; however `external` is a new one, and may apply only to functions.

{% hint style="info" %}

- [Ref: Solidity function visibility modifiers](https://docs.soliditylang.org/en/develop/cheatsheet.html#function-visibility-specifiers)
  {% endhint %}

## Step A6: Specify function return values

The `totalBurnt` function performs a query of the smart contract's state. Therefore it should reply with this information. This is done through the `returns` keyword, which specifies the type of the returned value.

```solidity
		returns(uint256)
```

In this case, the function returns a single value of type `uint256`. which specifies one or more return values. Note that Solidity allows functions to return multiple return values, for example `returns(uint256, address)` would mean that it returns both a `uint256` and an `address`.

## Special values accessible within a function

When a smart contract function is invoked, it has access to values that are passed in as parameters. It also has access to the state variables persisted by the smart contract.

Additionally, there are also several special values that are specific to the current block (group of transactions) or specific to the current transaction that are also accessible. Two of these are `msg.sender` and `msg.value`.

{% hint style="info" %}

- [Ref: Solidity block and transaction properties](https://docs.soliditylang.org/en/stable/units-and-global-variables.html#block-and-transaction-properties)
  {% endhint %}

## Step A7: Specify condition for require

The `require` function is used to check for specific conditions within a function invocation. If these conditions are not met, it throws an exception. This causes the function invocation to be reverted, meaning the state of the smart contract would remain as it was before, as if the function invocation was never made.

```solidity
        require(msg.sender != address(0), "zero address not allowed");
```

Within the `burninate` function, we use a `require` to ensure that the transaction is seemingly not from the null address, also known as the zero address. This essentially disallows any transactions sent from that particular address

{% hint style="info" %}
Technically it should not be possible for a transaction to be sent by the zero address. This is done here purely for illustrative purposes.
{% endhint %}

{% hint style="info" %}

- [Ref: Solidity panic and require](https://docs.soliditylang.org/en/develop/control-structures.html#panic-via-assert-and-error-via-require)
  {% endhint %}

## Step A8: Specify error message for require

We have another `require` in this function to ensure that the amount paid (in HBAR) is at least above a certain threshold (the `MIN_FEE` constant).

```solidity
        require(msg.value >= MIN_FEE, "pay at least minimum fee");
```

This function is `payable`, meaning that any value (of HBAR) sent along with the function gets deducted from the balance of the sender's account, and gets added to the balance of the smart contract's account. In other words: The transaction sender pays into the smart contract via this function.

{% hint style="info" %}
The numeric value of `msg.value` is _not_ denominated in HBAR, but rather **tinybar**, when a smart contract is deployed on a Hedera network. This is consistent with `msg.value` being denominated not in Ether, but rather in **wei**, when a smart contract is deployed on an Ethereum network.

There is a key difference though:

- 1 HBAR = 10^8 tinybar (10 million)
- 1 Ether = 10^18 wei (1 billion billion)
  {% endhint %}

In functions which are not `payable`, `msg.value` is guaranteed to be zero. Whereas in functions which are `payable`, `msg.value` could be zero **or more**. In this case, the intent is for the function to reject any function invocations which do not pay enough.

{% hint style="info" %}

- [Ref: Solidity Ether units](https://docs.soliditylang.org/en/develop/units-and-global-variables.html#ether-units)
- [Ref: Stackoverflow full unit of HBAR](https://stackoverflow.com/q/76123094/194982)
  {% endhint %}

## Step A9: Update state

After the checks have been completed successfully, by the `require` statements, we're ready to update the persisted state of this smart contract. In this case, we are keeping track, as a running tally, of the total amount paid by each different address that this function has been invoked with.

```solidity
        amounts[msg.sender] = amounts[msg.sender] + msg.value;
```

This statement increments the current value by the amount paid into the function, keyed on the address that invoked this function.

{% hint style="info" %}

- [Ref: Solidity operators](https://docs.soliditylang.org/en/develop/types.html#operators)
- [Ref: Solidity mapping types](https://docs.soliditylang.org/en/develop/types.html#mapping-types)
  {% endhint %}

## Step A10: Specify an event

The EVM outputs logs, which essentially is information that is persisted on the network, but **not** accessible by smart contracts. Instead they are intended to be accessed by client applications (such as DApps), which typically search for specific events, or listen for specific events.

The canonical use case for events within a smart contract is to create a "history" of actions performed by that smart contract. In this case, let's commemorate each time the `burninate` function is successfully invoked.

```solidity
    event Burnination(address who, uint256 amount);
```

This `event` is named `Burnination`, and whenever it is produced, it is added to the EVM logs, and will contain an `address` and a `uint256`.

## Step A11: Emit an event

Once the `event` has been defined, the smart contract should specify exactly when it should be added to the EVM logs. This is done using `emit`.

```solidity
        emit Burnination(msg.sender, msg.value);
```

Thus, based on where this `emit` statement is located within the function, this `event` is added to the logs upon each time the `burninate` function is invoked, only if both of the `require` statements are satisfied. When it gets added the transaction sender's address and the amount that they paid into the function are logged.

{% hint style="info" %}
Note that this particular smart contract does not include any means to take out the HBAR balance that accrues within it over time each time `burninate` is invoked. This effectively means that the HBAR sent into it is stuck there forever, and hence is effectively lost.

Trogdor would be proud ;)
{% endhint %}
