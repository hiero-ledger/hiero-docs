# Deploy Your First Smart Contract

In this tutorial, you will learn how to create a simple smart contract on Hedera using Solidity.

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](../../getting-started/introduction.md).
* Set up your environment [here](../../getting-started/environment-set-up.md).

***

## Table of Contents

1. [Create Smart Contract](deploy-your-first-smart-contract.md#1.-create-a-hello-hedera-smart-contract)
2. [Store Bytecode on Hedera](deploy-your-first-smart-contract.md#2.-store-the-smart-contract-bytecode-on-hedera)
3. [Deploy Hedera Smart Contract](deploy-your-first-smart-contract.md#3.-deploy-a-hedera-smart-contract)
4. [Call Contract Functions](deploy-your-first-smart-contract.md#4.-call-contract-functions)

***

## 1. Create a "Hello Hedera" Smart Contract

Create a smart contract in solidity using the [remix IDE](https://remix.ethereum.org/#optimize=false\&runs=200\&evmVersion=null\&version=soljson-v0.8.7+commit.e28d00a7.js). The "Hello Hedera" contract Solidity file is sampled below along with the "Hello Hedera" JSON file that is produced after the contract has been compiled. You can use remix to create and compile the contract yourself or you can copy the files below into your project. If you are not familiar with Solidity you can check out the docs [here](https://docs.soliditylang.org/en/v0.8.9/). Hedera supports the latest version of Solidity (v0.8.9) on previewnet and testnet.

The contract stores two variables the <mark style="color:blue;">`owner`</mark> and <mark style="color:blue;">`message`</mark>. The constructor passes in the <mark style="color:blue;">`message`</mark> parameter. The <mark style="color:blue;">`set_message`</mark> function allows the owner to update the message variable and the <mark style="color:blue;">`get_message`</mark> function allows you to return the message.

The HelloHedera.sol will serve as a reference to the contract that was compiled. The HelloHedera.json file contains the <mark style="color:blue;">`data.bytecode.object`</mark> field that will be used to store the contract bytecode in a file on the Hedera network.

{% tabs %}
{% tab title="HelloHedera.sol" %}
```solidity
pragma solidity >=0.7.0 <0.8.9;

contract HelloHedera {
    // the contract's owner, set in the constructor
    address owner;

    // the message we're storing
    string message;

    constructor(string memory message_) {
        // set the owner of the contract for `kill()`
        owner = msg.sender;
        message = message_;
    }

    function set_message(string memory message_) public {
        // only allow the owner to update the message
        if (msg.sender != owner) return;
        message = message_;
    }

    // return a string
    function get_message() public view returns (string memory) {
        return message;
    }

    // recover the funds of the contract
    function kill() public { if (msg.sender == owner) selfdestruct(payable(msg.sender)); }
}
```
{% endtab %}

{% tab title="HelloHedera.json" %}
```json
{
  "deploy": {
    "VM:-": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "main:1": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "ropsten:3": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "rinkeby:4": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "kovan:42": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "görli:5": {
      "linkReferences": {},
      "autoDeployLib": true
    },
    "Custom": {
      "linkReferences": {},
      "autoDeployLib": true
    }
  },
  "data": {
    "bytecode": {
      "linkReferences": {},
      "object": "608060405234801561001057600080fd5b506040516105583803806105588339818101604052602081101561003357600080fd5b810190808051604051939291908464010000000082111561005357600080fd5b8382019150602082018581111561006957600080fd5b825186600182028301116401000000008211171561008657600080fd5b8083526020830192505050908051906020019080838360005b838110156100ba57808201518184015260208101905061009f565b50505050905090810190601f1680156100e75780820380516001836020036101000a031916815260200191505b50604052505050336000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff160217905550806001908051906020019061014492919061014b565b50506101e8565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061018c57805160ff19168380011785556101ba565b828001600101855582156101ba579182015b828111156101b957825182559160200191906001019061019e565b5b5090506101c791906101cb565b5090565b5b808211156101e45760008160009055506001016101cc565b5090565b610361806101f76000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c80632e9826021461003b57806332af2edb146100f6575b600080fd5b6100f46004803603602081101561005157600080fd5b810190808035906020019064010000000081111561006e57600080fd5b82018360208201111561008057600080fd5b803590602001918460018302840111640100000000831117156100a257600080fd5b91908080601f016020809104026020016040519081016040528093929190818152602001838380828437600081840152601f19601f820116905080830192505050505050509192919290505050610179565b005b6100fe6101ec565b6040518080602001828103825283818151815260200191508051906020019080838360005b8381101561013e578082015181840152602081019050610123565b50505050905090810190601f16801561016b5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16146101d1576101e9565b80600190805190602001906101e792919061028e565b505b50565b606060018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156102845780601f1061025957610100808354040283529160200191610284565b820191906000526020600020905b81548152906001019060200180831161026757829003601f168201915b5050505050905090565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106102cf57805160ff19168380011785556102fd565b828001600101855582156102fd579182015b828111156102fc5782518255916020019190600101906102e1565b5b50905061030a919061030e565b5090565b5b8082111561032757600081600090555060010161030f565b509056fea26469706673582212201644465f5f73dfd73a518b57770f5adb27f025842235980d7a0f4e15b1acb18e64736f6c63430007000033",
      "opcodes": "PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x40 MLOAD PUSH2 0x558 CODESIZE SUB DUP1 PUSH2 0x558 DUP4 CODECOPY DUP2 DUP2 ADD PUSH1 0x40 MSTORE PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x33 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 MLOAD PUSH1 0x40 MLOAD SWAP4 SWAP3 SWAP2 SWAP1 DUP5 PUSH5 0x100000000 DUP3 GT ISZERO PUSH2 0x53 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP4 DUP3 ADD SWAP2 POP PUSH1 0x20 DUP3 ADD DUP6 DUP2 GT ISZERO PUSH2 0x69 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 MLOAD DUP7 PUSH1 0x1 DUP3 MUL DUP4 ADD GT PUSH5 0x100000000 DUP3 GT OR ISZERO PUSH2 0x86 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 DUP4 MSTORE PUSH1 0x20 DUP4 ADD SWAP3 POP POP POP SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0xBA JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x9F JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xE7 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP PUSH1 0x40 MSTORE POP POP POP CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x144 SWAP3 SWAP2 SWAP1 PUSH2 0x14B JUMP JUMPDEST POP POP PUSH2 0x1E8 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x18C JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x1BA JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x1BA JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x1B9 JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x19E JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x1C7 SWAP2 SWAP1 PUSH2 0x1CB JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x1E4 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x1CC JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST PUSH2 0x361 DUP1 PUSH2 0x1F7 PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER ",
      "sourceMap": "33:623:0:-:0;;;186:160;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;301:10;293:5;;:18;;;;;;;;;;;;;;;;;;331:8;321:7;:18;;;;;;;;;;;;:::i;:::-;;186:160;33:623;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;:::i;:::-;;;:::o;:::-;;;;;;;;;;;;;;;;;;;;;:::o;:::-;;;;;;;"
    },
    "deployedBytecode": {
      "immutableReferences": {},
      "linkReferences": {},
      "object": "608060405234801561001057600080fd5b50600436106100365760003560e01c80632e9826021461003b57806332af2edb146100f6575b600080fd5b6100f46004803603602081101561005157600080fd5b810190808035906020019064010000000081111561006e57600080fd5b82018360208201111561008057600080fd5b803590602001918460018302840111640100000000831117156100a257600080fd5b91908080601f016020809104026020016040519081016040528093929190818152602001838380828437600081840152601f19601f820116905080830192505050505050509192919290505050610179565b005b6100fe6101ec565b6040518080602001828103825283818151815260200191508051906020019080838360005b8381101561013e578082015181840152602081019050610123565b50505050905090810190601f16801561016b5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16146101d1576101e9565b80600190805190602001906101e792919061028e565b505b50565b606060018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156102845780601f1061025957610100808354040283529160200191610284565b820191906000526020600020905b81548152906001019060200180831161026757829003601f168201915b5050505050905090565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106102cf57805160ff19168380011785556102fd565b828001600101855582156102fd579182015b828111156102fc5782518255916020019190600101906102e1565b5b50905061030a919061030e565b5090565b5b8082111561032757600081600090555060010161030f565b509056fea26469706673582212201644465f5f73dfd73a518b57770f5adb27f025842235980d7a0f4e15b1acb18e64736f6c63430007000033",
      "opcodes": "PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER ",
      "sourceMap": "33:623:0:-:0;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;352:182;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;:::i;:::-;;563:90;;;:::i;:::-;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;352:182;486:5;;;;;;;;;;472:19;;:10;:19;;;468:32;;493:7;;468:32;519:8;509:7;:18;;;;;;;;;;;;:::i;:::-;;352:182;;:::o;563:90::-;607:13;639:7;632:14;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;563:90;:::o;-1:-1:-1:-;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;:::i;:::-;;;:::o;:::-;;;;;;;;;;;;;;;;;;;;;:::o"
    },
    "gasEstimates": {
      "creation": {
        "codeDepositCost": "173000",
        "executionCost": "infinite",
        "totalCost": "infinite"
      },
      "external": {
        "get_message()": "infinite",
        "set_message(string)": "infinite"
      }
    },
    "methodIdentifiers": {
      "get_message()": "32af2edb",
      "set_message(string)": "2e982602"
    }
  },
  "abi": [
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "message_",
          "type": "string"
        }
      ],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "inputs": [],
      "name": "get_message",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "message_",
          "type": "string"
        }
      ],
      "name": "set_message",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
  ]
}
```
{% endtab %}
{% endtabs %}

***

## 2. Store the Smart Contract Bytecode on Hedera

Create a file using the <mark style="color:blue;">`FileCreateTransaction()`</mark> API to store the hex-encoded byte code of the "Hello Hedera" contract. Once the file is created, you can obtain the file ID from the receipt of the transaction.

You can alternatively use the [<mark style="color:purple;">`CreateContractFlow()`</mark>](../../sdks-and-apis/sdks/smart-contracts/create-a-smart-contract.md#contractcreateflow) API that creates the bytecode file for you and subsequently creates the contract on Hedera in a single API.

{% hint style="warning" %}
_**Note:** The bytecode is required to be hex-encoded. It should not be the actual data the hex represents._
{% endhint %}



{% tabs %}
{% tab title="Java" %}
```java
//Import the compiled contract from the HelloHedera.json file
Gson gson = new Gson();
JsonObject jsonObject;

InputStream jsonStream = HelloHederaSmartContract.class.getClassLoader().getResourceAsStream("HelloHedera.json");
jsonObject = gson.fromJson(new InputStreamReader(jsonStream, StandardCharsets.UTF_8), JsonObject.class);

//Store the "object" field from the HelloHedera.json file as hex-encoded bytecode
String object = jsonObject.getAsJsonObject("data").getAsJsonObject("bytecode").get("object").getAsString();
byte[] bytecode = object.getBytes(StandardCharsets.UTF_8);

//Create a file on Hedera and store the hex-encoded bytecode
FileCreateTransaction fileCreateTx = new FileCreateTransaction()
        //Set the bytecode of the contract
        .setContents(bytecode);

//Submit the file to the Hedera test network signing with the transaction fee payer key specified with the client
TransactionResponse submitTx = fileCreateTx.execute(client);

//Get the receipt of the file create transaction
TransactionReceipt fileReceipt = submitTx.getReceipt(client);

//Get the file ID from the receipt
FileId bytecodeFileId = fileReceipt.fileId;

//Log the file ID
System.out.println("The smart contract bytecode file ID is " +bytecodeFileId)

//v2 Hedera Java SDK
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Import the compiled contract from the HelloHedera.json file
let helloHedera = require("./HelloHedera.json");
const bytecode = helloHedera.data.bytecode.object;

//Create a file on Hedera and store the hex-encoded bytecode
const fileCreateTx = new FileCreateTransaction()
        //Set the bytecode of the contract
        .setContents(bytecode);

//Submit the file to the Hedera test network signing with the transaction fee payer key specified with the client
const submitTx = await fileCreateTx.execute(client);

//Get the receipt of the file create transaction
const fileReceipt = await submitTx.getReceipt(client);

//Get the file ID from the receipt
const bytecodeFileId = fileReceipt.fileId;

//Log the file ID
console.log("The smart contract byte code file ID is " +bytecodeFileId)
```
{% endtab %}

{% tab title="Go" %}
```go
//Import and parse the compiled contract from the HelloHedera.json file
helloHedera, err := ioutil.ReadFile("./HelloHedera.json")
if err != nil {
	println(err.Error(), ": error reading HelloHedera.json")
	return
}

var contract contract = contract{}

err = json.Unmarshal([]byte(helloHedera), &contract)
if err != nil {
	println(err.Error(), ": error unmarshaling the json file")
	return
}

bytecode := []byte(contract.Object)

//Create a file on Hedera and store the hex-encoded bytecode
fileTx, err := hedera.NewFileCreateTransaction().
	//Set the bytecode of the contract
	SetContents([]byte(bytecode)).
	//Submit the transaction to a Hedera network
	Execute(client)

if err != nil {
	println(err.Error(), ": error creating file")
	return
}

//Get the receipt of the file create transaction
fileReceipt, err := fileTx.GetReceipt(client)
if err != nil {
	println(err.Error(), ": error getting file create transaction receipt")
	return
}

//Get the file ID
byteCodefileID := *fileReceipt.FileID

//Log the file ID
fmt.Printf("contract bytecode file: %v\n", bytecodeFileID)
```
{% endtab %}
{% endtabs %}

***

## 3. Deploy a Hedera Smart Contract

Create the contract and set the file ID to the file ID that stores the hex-encoded byte code from the previous step. You will also need to set gas the value that will create the contract and pass the constructor parameters using <mark style="color:blue;">`ContractFunctionParameters()`</mark> API<mark style="color:purple;">.</mark> In this example, "hello from Hedera!" was passed to the constructor. After the transaction is successfully executed, you can get the contract ID from the receipt.

{% hint style="warning" %}
_**Note:** You will need to set the gas value high enough to deploy the contract. If you don't have enough gas, you will receive an `INSUFFICIENT_GAS` response._
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
// Instantiate the contract instance
 ContractCreateTransaction contractTx = new ContractCreateTransaction()
      //Set the file ID of the Hedera file storing the bytecode
      .setBytecodeFileId(newFileId)
      //Set the gas to instantiate the contract
      .setGas(100_000)
      //Provide the constructor parameters for the contract
      .setConstructorParameters(new ContractFunctionParameters().addString("Hello from Hedera!"));

//Submit the transaction to the Hedera test network
TransactionResponse contractResponse = contractTx.execute(client);

//Get the receipt of the file create transaction
TransactionReceipt contractReceipt = contractResponse.getReceipt(client);

//Get the smart contract ID
ContractId newContractId = contractReceipt.contractId;

//Log the smart contract ID
System.out.println("The smart contract ID is " + newContractId);

//v2 Hedera Java SDK
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Instantiate the contract instance
const contractTx = await new ContractCreateTransaction()
      //Set the file ID of the Hedera file storing the bytecode
      .setBytecodeFileId(bytecodeFileId)
      //Set the gas to instantiate the contract
      .setGas(100000)
      //Provide the constructor parameters for the contract
      .setConstructorParameters(new ContractFunctionParameters().addString("Hello from Hedera!"));

//Submit the transaction to the Hedera test network
const contractResponse = await contractTx.execute(client);

//Get the receipt of the file create transaction
const contractReceipt = await contractResponse.getReceipt(client);

//Get the smart contract ID
const newContractId = contractReceipt.contractId;

//Log the smart contract ID
console.log("The smart contract ID is " + newContractId);

//v2 JavaScript SDK
```
{% endtab %}

{% tab title="Go" %}
```go
// Instantiate the contract instance
contractTx, err := hedera.NewContractCreateTransaction().
	//Set the file ID of the Hedera file storing the bytecode
	SetBytecodeFileID(bytecodeFileID).
	//Set the gas to instantiate the contract
	SetGas(100000).
	//Provide the constructor parameters for the contract
	SetConstructorParameters(hedera.NewContractFunctionParameters().
		AddString("Hello from hedera")).
	Execute(client)

if err != nil {
	println(err.Error(), ": error creating contract")
	return
}

//Get the receipt of the contract create transaction
contractReceipt, err := contractTx.GetReceipt(client)
if err != nil {
	println(err.Error(), ": error retrieving contract creation receipt")
	return
}

//Get the contract ID from the receipt
newContractID := *contractReceipt.ContractID

//v2 Hedera Go SDK
```
{% endtab %}
{% endtabs %}

***

## 4. Call Contract Functions

### Call the <mark style="color:purple;">`get_message`</mark> contract function

In the previous step, the contract message variable was set to "hello from Hedera!." You can return this message from the contract by submitting a query that will return the stored message string. The <mark style="color:blue;">`ContractCallQuery()`</mark> similarly does not modify the state of the contract like other Hedera queries. It only reads stored values.

{% tabs %}
{% tab title="Java" %}
```java
// Calls a function of the smart contract
ContractCallQuery contractQuery = new ContractCallQuery()
     //Set the gas for the query
     .setGas(100000) 
     //Set the contract ID to return the request for
     .setContractId(newContractId)
     //Set the function of the contract to call 
     .setFunction("get_message" )
     //Set the query payment for the node returning the request
     //This value must cover the cost of the request otherwise will fail 
     .setQueryPayment(new Hbar(2)); 

//Submit to a Hedera network
ContractFunctionResult getMessage = contractQuery.execute(client);
//Get the message
String message = getMessage.getString(0); 

//Log the message
System.out.println("The contract message: " + message);

//v2 Hedera Java SDK
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Calls a function of the smart contract
const contractQuery = await new ContractCallQuery()
     //Set the gas for the query
     .setGas(100000)
     //Set the contract ID to return the request for
     .setContractId(newContractId)
     //Set the contract function to call
     .setFunction("get_message" )
     //Set the query payment for the node returning the request
     //This value must cover the cost of the request otherwise will fail
     .setQueryPayment(new Hbar(2));

//Submit to a Hedera network
const getMessage = await contractQuery.execute(client);

// Get a string from the result at index 0
const message = getMessage.getString(0);

//Log the message
console.log("The contract message: " + message);

//v2 Hedera JavaScript SDK
```
{% endtab %}

{% tab title="Go" %}
```go
// Calls a function of the smart contract
contractQuery, err := hedera.NewContractCallQuery().
	//Set the contract ID to return the request for
	SetContractID(newContractID).
	//Set the gas for the query
	SetGas(100000).
	//Set the query payment for the node returning the request
        //This value must cover the cost of the request otherwise will fail
	SetQueryPayment(hedera.NewHbar(2)).
	//Set the contract function to call
	SetFunction("getMessage", nil). // nil -> no parameters
	//Submit the query to a Hedera network
	Execute(client)
	
if err != nil {
	println(err.Error(), ": error executing contract call query")
	return
}

// Get a string from the result at index 0
getMessage := contractQuery.GetString(0)

//Log the message
fmt.Printf("The contract message: ", getMessage)

//v2 Hedera Go SDK
```
{% endtab %}
{% endtabs %}

### Call the <mark style="color:purple;">`set_message`</mark> contract function

Call the <mark style="color:blue;">`set_message`</mark> function of the contract. To do this you will need to use the <mark style="color:blue;">`ContractExecuteTransaction()`</mark> API. This transaction will update the contract message. Once the transaction is successfully submitted you can verify the message was updated by requesting <mark style="color:blue;">`ContractCallQuery()`</mark>. The message returned from the contract should now log "Hello from Hedera again!"

{% tabs %}
{% tab title="Java" %}
```java
 //Create the transaction to update the contract message
 ContractExecuteTransaction contractExecTx = new ContractExecuteTransaction()
        //Set the ID of the contract
        .setContractId(newContractId)
        //Set the gas for the call
        .setGas(100_000)
        //Set the function of the contract to call
        .setFunction("set_message", new ContractFunctionParameters().addString("Hello from Hedera again!"));

//Submit the transaction to a Hedera network and store the response
TransactionResponse submitExecTx = contractExecTx.execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt2 = submitExecTx.getReceipt(client);

//Confirm the transaction was executed successfully 
System.out.println("The transaction status is" +receipt2.status);

//Query the contract for the contract message
 ContractCallQuery contractCallQuery = new ContractCallQuery()
        //Set ID of the contract to query
        .setContractId(newContractId)
        //Set the gas to execute the contract call
        .setGas(100_000)
        //Set the contract function
        .setFunction("get_message")
        //Set the query payment for the node returning the request
        //This value must cover the cost of the request otherwise will fail 
        .setQueryPayment(new Hbar(2));
 
 //Submit the query to a Hedera network
ContractFunctionResult contractUpdateResult = contractCallQuery.execute(client);

//Get the updated message
String message2 = contractUpdateResult.getString(0);

//Log the updated message
System.out.println("The contract updated message: " + message2);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
 //Create the transaction to update the contract message
const contractExecTx = await new ContractExecuteTransaction()
        //Set the ID of the contract
        .setContractId(newContractId)
        //Set the gas for the contract call
        .setGas(100000)
        //Set the contract function to call
        .setFunction("set_message", new ContractFunctionParameters().addString("Hello from Hedera again!"));

//Submit the transaction to a Hedera network and store the response
const submitExecTx = await contractExecTx.execute(client);

//Get the receipt of the transaction
const receipt2 = await submitExecTx.getReceipt(client);

//Confirm the transaction was executed successfully 
console.log("The transaction status is " +receipt2.status.toString());

//Query the contract for the contract message
const contractCallQuery = new ContractCallQuery()
        //Set the ID of the contract to query
        .setContractId(newContractId)
        //Set the gas to execute the contract call
        .setGas(100000)
        //Set the contract function to call
        .setFunction("get_message")
        //Set the query payment for the node returning the request
        //This value must cover the cost of the request otherwise will fail 
        .setQueryPayment(new Hbar(2));

//Submit the transaction to a Hedera network 
const contractUpdateResult = await contractCallQuery.execute(client);

//Get the updated message at index 0
const message2 = contractUpdateResult.getString(0);

//Log the updated message to the console
console.log("The updated contract message: " + message2);

//v2 Hedera JavaScript SDK
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction to update the contract message
contractExecTx, err := hedera.NewContractExecuteTransaction().
	  //Set the ID of the contract
          SetContractID(newContractID).
          //Set the gas to execute the call
          SetGas(100000).
          //Set the contract function to call
	  SetFunction("setMessage", hedera.NewContractFunctionParameters().
		AddString("Hello from Hedera again!")).
	  Execute(client)

if err != nil {
	println(err.Error(), ": error executing contract")
	return
}

//Get the receipt of the transaction
receipt2, err := contractExecTx.GetReceipt(client)

//Confirm the transaction was executed successfully
fmt.Printf("The transaction status is", receipt2.Status))

//Query the contract for the contract message
contractCallQuery, err := hedera.NewContractCallQuery().
	//Set the contract ID to query
	SetContractID(newContractID).
	//Set the gas to execute the contract call
	SetGas(100000).
        //Set the query payment for the node returning the request
        //This value must cover the cost of the request otherwise will fail
        SetQueryPayment(hedera.NewHbar(2)).
        //Set the contract function to call
	SetFunction("get_message", nil). // nil -> no parameters
	//Submit the query to a Hedera network node
	Execute(client)
	
if err != nil {
	println(err.Error(), ": error executing contract call query")
	return
}

// Get a string from the result at index 0
getMessage2 := contractCallQuery.GetString(0)

//Log the message
fmt.Printf("The updated contract message: ", getMessage2)

//v2 Hedera Go SDK
```
{% endtab %}
{% endtabs %}

#### Congratulations :tada:! You have completed the following:

* Created a simple smart contract on Hedera
* Interacted with contract functions

{% embed url="https://www.youtube.com/watch?amp%3Bt=24s&v=L9Tm6yn_ayY" %}
Video tutorial
{% endembed %}

***

## Additional Resources

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Simi, Sr. Software Manager </p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan">LinkedIn</a></p></td><td></td><td></td><td><a href="https://www.linkedin.com/in/shunjan">https://www.linkedin.com/in/shunjan</a></td></tr></tbody></table>
