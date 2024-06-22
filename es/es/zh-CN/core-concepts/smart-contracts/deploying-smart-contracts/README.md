# Deploying Smart Contracts

After compiling your smart contract, you can deploy it to the Hedera network. The constructor's "_init code_" includes the contract's entire bytecode. When deploying, the EVM is expected to be supplied with both the smart contract [bytecode](../../../support-and-community/glossary.md#bytecode) and the gas required to execute and deploy the contract. Post-deployment, the constructor is removed, leaving only the `runtime_bytecode` for future contract interactions.

## Ethereum Virtual Machine (EVM)

The [Ethereum Virtual Machine (EVM)](../../../support-and-community/glossary.md#ethereum-virtual-machine-evm) is a run-time environment for executing smart contracts written in EVM native programming languages, like Solidity. The source code must be compiled into bytecode for the EVM to execute a given smart contract.

On Hedera, users can interact with the EVM-compatible environment in several ways. They can submit `ContractCreate`, `EthereumTransaction`, or make `eth_sendRawTransaction` RPC calls with the contract bytecode directly. These various paths provide flexibility for developers to deploy and manage smart contracts efficiently.

When the EVM receives the bytecode, it will be further broken down into **operation codes** ([opcodes](../../../support-and-community/glossary.md#opcodes)). The EVM opcodes represent the specific instructions it can perform. Each opcode is one byte and has its own gas cost associated with it. The cost per opcode for the Ethereum Shanghai fork can be found [here](https://www.evm.codes/?fork=london).

#### Smart Contract Opcode Example

```solidity
PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x40 MLOAD PUSH2 0x558 CODESIZE SUB DUP1 PUSH2 0x558 DUP4 CODECOPY DUP2 DUP2 ADD PUSH1 0x40 MSTORE PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x33 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 MLOAD PUSH1 0x40 MLOAD SWAP4 SWAP3 SWAP2 SWAP1 DUP5 PUSH5 0x100000000 DUP3 GT ISZERO PUSH2 0x53 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP4 DUP3 ADD SWAP2 POP PUSH1 0x20 DUP3 ADD DUP6 DUP2 GT ISZERO PUSH2 0x69 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 MLOAD DUP7 PUSH1 0x1 DUP3 MUL DUP4 ADD GT PUSH5 0x100000000 DUP3 GT OR ISZERO PUSH2 0x86 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 DUP4 MSTORE PUSH1 0x20 DUP4 ADD SWAP3 POP POP POP SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0xBA JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x9F JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xE7 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP PUSH1 0x40 MSTORE POP POP POP CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x144 SWAP3 SWAP2 SWAP1 PUSH2 0x14B JUMP JUMPDEST POP POP PUSH2 0x1E8 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x18C JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x1BA JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x1BA JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x1B9 JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x19E JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x1C7 SWAP2 SWAP1 PUSH2 0x1CB JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x1E4 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x1CC JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST PUSH2 0x361 DUP1 PUSH2 0x1F7 PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER
```

#### References:

- [https://ethervm.io/](https://ethervm.io/)

***

## Hyperledger Besu EVM

The Hedera network nodes utilize the [HyperLedger Besu EVM ](../../../support-and-community/glossary.md#hyperledger-besu-evm)Client written in Java as an execution layer for Ethereum-type transactions. The codebase is up to date with the current Ethereum Mainnet hard forks. The Besu EVM client library is used without hooks for the consensus, networking, and storage features used by Ethereum. Instead, Hedera hooks into its own Hashgraph consensus, Gossip communication, and [Virtual Merkle Trees](../../../support-and-community/glossary.md#virtual-merkle-tree) components for greater fault tolerance, finality, and scalability. The Besu EVM client is configured to use the “Shanghai” hard fork, as of Hedera mainnet release `0.38.6` , of the Ethereum Mainnet with some changes.

### **Shanghai Hard Fork**

The smart contract platform is upgraded to support the EVM visible changes for the “[Shanghai](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/shanghai.md)” hard fork. This includes changes introduced in the "[London](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md)," “[Istanbul](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md),” and “[Berlin](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md)” hard forks. Changes relating to block production, data serialization, and the fee market will not be implemented because they are irrelevant to Hedera’s architecture.

Starting in the Hedera Services [`0.22`](../../../networks/release-notes/services.md#v0.22) release, the intrinsic gas cost and input data will be charged. The intrinsic gas cost is a constant that is charged before any code is executed. The intrinsic gas cost is 21,000 gas. The input data is 16 gas per non-zero byte and 4 gas per zero byte. The input data is the data provided to the external contract function parameters when calling a contract. To learn more about gas fees, check out this [page](https://www.notion.so/hedera/core-concepts/smart-contracts/gas-and-fees).

### EVM Opcodes

The list of supported Opcodes for the Shanghai hard fork can be found [here](https://www.evm.codes/).

Modified descriptions for Solidity functions and opcodes to better reflect the Hedera network.

<table><thead><tr><th width="261">Solidity</th><th width="122.33333333333331">Opcode</th><th>Hedera</th></tr></thead><tbody><tr><td><code>address</code></td><td></td><td>The address is a mapping of shard.realm.number (0.0.10) into a 20 byte Solidity address. The address can be a Hedera account ID or contract ID in Solidity format.</td></tr><tr><td><code>block.basefee</code></td><td><code>BASEFEE</code></td><td>The <code>BASEFEE</code> opcode will return zero. Hedera does not use the Fee Market mechanism this is designed to support.</td></tr><tr><td><code>block.chainid</code></td><td><code>CHAINID</code></td><td>The <code>CHAINID</code> opcode will return 295(hex <code>0x0127</code>) for mainnet, 296( hex <code>0x0128</code>) for testnet, 297( hex <code>0x0129</code>) for previewnet, and 298 (<code>0x12A</code>) for development networks.</td></tr><tr><td><code>block.coinbase</code></td><td><code>COINBASE</code></td><td>The <code>COINBASE</code> operation will return the funding account (Hedera transaction fee collecting account <code>0.0.98</code>).</td></tr><tr><td><code>block.number</code></td><td></td><td>The index of the record file (not recommended, use <code>block.timestamp</code>).</td></tr><tr><td><code>block.timestamp</code></td><td></td><td>The transaction consensus timestamp.</td></tr><tr><td><code>block.difficulty</code></td><td></td><td>Always zero.</td></tr><tr><td><code>block.gaslimit</code></td><td><code>GASLIMIT</code></td><td>The <code>GASLIMIT</code> operation will return the <code>gasLimit</code> of the transaction. The transaction <code>gasLimit</code> will be the lowest of the gas limit requested in the transaction or a global upper gas limit configured for all smart contracts.</td></tr><tr><td><code>msg.sender</code></td><td></td><td>The address of the Hedera contract ID or account ID in Solidity format that called this contract. For the root level or for delegate chains that go to root it is the account ID paying for the transaction.</td></tr><tr><td><code>msg.value</code></td><td></td><td>The value associated to the transaction associated in tinybar.</td></tr><tr><td><code>tx.origin</code></td><td></td><td>The account ID paying for the transaction, regardless of depth.</td></tr><tr><td><code>tx.gasprice</code></td><td></td><td>Fixed (varies with the global fee schedule and exchange rate).</td></tr><tr><td><code>selfdestruct(address payable recipient)</code></td><td></td><td>Address will not be reusable due to Hedera’s account numbering policies.</td></tr><tr><td><code>.code</code></td><td></td><td>Precompile contract addresses will report no code, including HTS System contract.</td></tr><tr><td><code>.codehash</code></td><td></td><td>Precompile contract addresses will report the empty code hash.</td></tr><tr><td></td><td><code>PRNGSEED</code></td><td>This opcode returns a random number based on the n-3 record running hash.</td></tr><tr><td><code>delegateCall</code></td><td></td><td>Contracts may no longer use <code>delegateCall()</code> to invoke system contracts. Contracts should instead use the <code>call()</code> method.</td></tr></tbody></table>

{% hint style="warning" %}
_**HBAR decimal places**_

_The JSON RPC Relay **`msg.value`** uses 18 decimals when it returns HBAR._ This was to provide an equivalent decimal length for web3 tools used across multiple EVM chains. _As a result, the **`gasPrice`** also uses 18 decimal places since it is only utilized from the JSON RPC Relay. Refer to the_ [_HBAR page_](../../../sdks-and-apis/sdks/hbars.md) _for a table of Hedera APIs and the decimal places they return._
{% endhint %}

***

## Limitation on `fallback()` / `receive()` Functions in Hedera Smart Contracts

While developing smart contracts on the Hedera network, it's crucial to note some behavioral differences compared to other networks like Ethereum. When a Hedera smart contract receives HBAR through a crypto transfer, the contract's `fallback()` and `receive()` functions **do not get triggered**.\&#x20

In a typical smart contract on Ethereum, `fallback()` and `receive()` functions serve as "catch-all" mechanisms that execute when the contract receives the native cryptocurrency (Ether in Ethereum's case). Here are the impacted variables for Hedera smart contracts and their _usual_ expected values:

- **`msg.sender`:** The address initiating the contract call.
- **`msg.value`:** The amount of HBAR sent along with the call.

Because the contract's balance may change through native HAPI operations, independent of EVM message calls, it is not possible to preserve balance-related invariants simply by implementing the `receive()` or `fallback()` methods. This is an important design consideration. Users who want the option to entirely disable native operations against their contract are invited to contribute a [Hedera Improvement Proposal (HIP)](https://hips.hedera.com/) for this feature.

Understanding this limitation is crucial for anyone developing smart contracts on Hedera, especially for those who have deployed smart contracts on Ethereum. Developers should implement explicit functions to manage HBAR transfers, given that `fallback()` and `receive()` functions do not trigger in such scenarios.

***

## Gas

When executing the smart contract, the EVM requires the amount of work to be paid in gas. The “work” includes computation, state transitions, and storage. Gas is the unit of measurement used to charge a fee per opcode that is executed by the EVM. Each opcode code has a defined gas cost. Gas reflects the cost necessary to pay for the computational resources used to process transactions.

### **Weibar**

The EVM returns gas information in Weibar (introduced in [HIP-410](https://hips.hedera.com/hip/hip-410)). One weibar is 10^-18th HBAR, which translates to 1 tinybar is 10^10 weibars. As noted in HIP-410, this is to maximize compatibility with third-party tools that expect ether units to be operated on in fractions of 10^18, also known as a Wei.

### **Gas Schedule and Fees**

Gas fees paid for EVM transactions on Hedera can be composed of three different kinds of gas costs:

- Intrinsic Gas
- EVM opcode Gas
- Hedera System Contract Gas

<table><thead><tr><th width="226">Gas Fee Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>Intrinsic Gas</strong></td><td>The minimum amount of gas required to execute a transaction. It is a fixed gas cost that is independent of the specific operations or computations performed within the transaction.<br><br>Intrinsic gas cost: 21,000 gas</td></tr><tr><td><strong>EVM Operation Code</strong></td><td>The gas required to execute the defined operation code(s) for the smart contract call</td></tr><tr><td><strong>Hedera System Contract Transaction</strong></td><td><p>The required gas that is associated with a Hedera-defined transaction like using the Hedera Token Service system contract that allows you to burn (<code>TokenBurnTransaction</code>) or mint (<code>TokenMintTransaction</code>) a token.</p><p>If you are not using a system contract that maps to one of the native Hedera services, you do not need to apply this fee.</p><p>The Hedera transaction gas calculation is: Cost of the transaction in USD x Gas Conversion gas/USD + 20%</p><p>Example System Contracts:</p><ul><li>Hedera Token Service</li><li>Pseudo Random Number Generator (PRNG)</li><li>Exchange Rate</li></ul></td></tr></tbody></table>

### Gas Limit

The gas limit is the maximum amount of gas you are willing to pay for an operation.

The current opcode gas fees are reflective of the [0.22 Hedera Service release](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22).

| Operation                                                               | Shanghai Cost (Gas) | Current Hedera (Gas) |
| ----------------------------------------------------------------------- | -------------------------------------- | --------------------------------------- |
| Code deposit                                                            | 200 \* bytes                           | <p>Max of Shanghai<br>or Hedera</p>     |
| <p><code>BALANCE</code><br>(cold account)</p>                           | 2600                                   | 2600                                    |
| <p><code>BALANCE</code><br>(warm account)</p>                           | 100                                    | 100                                     |
| `EXP`                                                                   | 10 + 50/byte                           | 10 + 50/byte                            |
| <p><code>EXTCODECOPY</code><br>(cold account)</p>                       | 2600 + Mem                             | 2600 + Mem                              |
| <p><code>EXTCODECOPY</code><br>(warm account)</p>                       | 100 + Mem                              | 100 + Mem                               |
| <p><code>EXTCODEHASH</code><br>(cold account)</p>                       | 2600                                   | 2600                                    |
| <p><code>EXTCODEHASH</code><br>(warm account)</p>                       | 100                                    | 100                                     |
| <p><code>EXTCODESIZE</code><br>(cold account)</p>                       | 2600                                   | 2600                                    |
| <p><code>EXTCODESIZE</code><br>(warm account)</p>                       | 100                                    | 100                                     |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>        | <p>375 + 375*topics<br>+ data Mem</p>  | <p>Max of Shanghai<br>or Hedera</p>     |
| <p><code>SLOAD</code><br>(cold slot)</p>                                | 2100                                   | 2100                                    |
| <p><code>SLOAD</code><br>(warm slot)</p>                                | 100                                    | 100                                     |
| <p><code>SSTORE</code><br>(new slot)</p>                                | 22,100                                 | <p>Max of Shanghai<br>or Hedera</p>     |
| <p><code>SSTORE</code><br>(existing slot,<br>cold access)</p>           | 2,900                                  | 2,900                                   |
| <p><code>SSTORE</code><br>(existing slot,<br>warm access)</p>           | 100                                    | 100                                     |
| <p><code>SSTORE</code><br>refund</p>                                    | <p>Only transient<br>storage slots</p> | <p>Only transient<br>storage slots</p>  |
| <p><code>CALL</code> <em>et al</em>.<br>(cold recipient)</p>            | 2,600                                  | 2,600                                   |
| <p><code>CALL</code> <em>et al</em>.<br>(warm recipient)</p>            | 100                                    | 100                                     |
| <p><code>CALL</code> <em>et al</em>.<br>Hbar/Eth Transfer Surcharge</p> | 9,000                                  | 9,000                                   |
| <p><code>CALL</code> <em>et al</em>.<br>New Account Surcharge</p>       | 25,000                                 | _revert_                                |
| <p><code>SELFDESTRUCT</code><br>(cold beneficiary)</p>                  | 2600                                   | 2600                                    |
| <p><code>SELFDESTRUCT</code><br>(warm beneficiary)</p>                  | 0                                      | 0                                       |

The terms 'warm' and 'cold' in the above table correspond with whether the account or storage slot has been read or written to within the current smart contract transaction, even if within a child call frame.

'`CALL` _et al._' includes with limitation: `CALL`, `CALLCODE`, `DELEGATECALL`, and `STATICCALL`

Reference: [HIP-206](https://hips.hedera.com/hip/hip-206)

### Gas Per Second Throttling

Most EVM-compatible networks place a gas limit per block to manage resource allocation. This is done to place a limit on the amount of time spent in block validation so that the miner nodes can produce new nodes quickly. While Hedera does not have blocks or miners, in the context of how a [Nakamoto consensus](https://golden.com/wiki/Nakamoto\_consensus-AMB5VWM) system would use it, we are constrained by the physics of time as to how many blocks we can process.

For smart contract transactions, gas is a better measure of the complexity of the EVM transaction than counting all transactions the same, so metering the limits on gas provides a more reasonable limit on resource consumption.

To allow for more flexibility in what transactions we accept and to mirror Ethereum Mainnet behavior, the transaction limits will be calculated on a per-gas basis for smart contract calls (`ContractCreate`, `ContractCall`, `ContractCallLocalQuery`) in addition to a per-transaction limit. This dual approach allows for better resource management, providing a nuanced way to regulate smart contract executions.\&#x20

The Hedera network has implemented a system gas throttle of **15 million gas per second** in the Hedera Service release [0.22](../../../networks/release-notes/services.md#v0.22).\&#x20

### Gas Reservation and Unused Gas Refund

Hedera throttles transactions prior to consensus, and nodes limit the number of transactions they can submit to the network. Then, at consensus time, if the maximum number of transactions is exceeded, the excess transactions are not evaluated and are canceled with a busy state. Throttling by variable gas amounts provides some challenges to this system, where the nodes only submit a share of their transaction limit.

To address this, throttling will be based on a two-tiered gas measuring system: pre-consensus and post-consensus. Pre-consensus throttling will use the `gasLimit` field specified in the transaction. Post-consensus will use the actual evaluated amount of gas consumed by the transaction, allowing for dynamic adjustments in the system. It is impossible to know the _actual_ evaluated gas pre-consensus because the network state can directly impact the flow of the transaction, which is why pre-consensus uses the `gasLimit` field and will be referred to as the **gas reservation**.

Contract query requests are unique and bypass the consensus stage altogether. These requests are executed solely on the local node that receives them and only influences that specific node's precheck throttle. On the other hand, standard contract transactions go through both the precheck and consensus stages and are subject to both sets of throttle limits. The throttle limits for precheck and consensus may be set to different values.

In order to ensure that the transactions can execute properly, setting a higher gas reservation than will be consumed by execution is common. On Ethereum Mainnet, the entire reservation is charged to the account prior to execution, and the unused portion of the reservation is credited back. However, Ethereum utilizes a memory pool ([mempool](../../../support-and-community/glossary.md#mempool)) and does transaction ordering at block production time, allowing the block limit to be based only on used and not reserved gas.

To help prevent over-reservation, Hedera restricts the amount of unused gas that can be refunded to a maximum of 20% of the original gas reservation. This effectively means that users will be charged for at least 80% of their initial reservation, regardless of actual usage. This rule is designed to incentivize users to make more accurate gas estimates.

For example, if you initially reserve 5 million gas units for creating a smart contract but end up using only 2 million, Hedera will refund you 1 million gas units, i.e., 20% of your initial reservation. This setup aims to balance the network's resource management while incentivizing users to be as accurate as possible in their gas estimations.

### Maximum Gas Per Transaction

Because consensus time execution is now limited by actual gas used and not based on a transaction count, raising the gas limit available for each transaction is safe. Prior to gas-based metering, it would be possible for each transaction to consume the maximum gas per transaction without regard to the other transactions, so limits were based on this worst-case scenario. Now that throttling is the aggregate gas used, we can allow each transaction to consume large amounts of gas without concern for an extreme surge.

When a transaction is submitted to a node with a `gasLimit` that is greater than the per-transaction gas limit, the transaction must be rejected during precheck with a response code of `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`. The transaction must not be submitted to consensus.

Gas throttle per contract call and contract create **15 million gas per second**.

Reference: [HIP-185](https://hips.hedera.com/hip/hip-185)

***

## Deploying Your Smart Contract

**SDK**

You can use a [Hedera SDK](../../../sdks-and-apis/sdks/) to deploy your smart contract bytecode to the network. This approach does not require using any EVM tools like Hardhat or an instance of the Hedera JSON-RPC Relay.

{% content-ref url="../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md" %}
[deploy-your-first-smart-contract.md](../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md)
{% endcontent-ref %}

**Hardhat**

Hardhat can be used to deploy your smart contract by pointing to a community-hosted [JSON-RPC Relay](json-rpc-relay.md). However, EVM tools do not support features that are native to Hedera smart contracts like:

- Admin Key
- Contract Memo
- Automatic Token Associations
- Auto Renew Account ID
- Staking Node ID or Account ID
- Decline Staking Rewards

If you need to set any of the above properties for your contract, you will have to call the `ContractCreateTransaction` API using one of the [Hedera SDKs.](../../../sdks-and-apis/sdks/)

{% content-ref url="../../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md" %}
[deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md](../../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md)
{% endcontent-ref %}
