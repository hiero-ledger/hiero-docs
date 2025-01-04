# Compiling Smart Contracts

Compiling a smart contract involves using the contract's source code to generate its [**bytecode**](../../support-and-community/glossary.md#bytecode) and the contract [**Application** **Binary Interface (ABI)**](../../support-and-community/glossary.md#application-binary-interface-abi). The Ethereum Virtual Machine (EVM) executes the bytecode to understand and execute the smart contract. Meanwhile, other smart contracts use the ABI to understand how to interact with the deployed contracts on the Hedera network.

<figure><img src="../../.gitbook/assets/compiling-smart-contracts-graphic.png" alt="" width="375"><figcaption></figcaption></figure>

**Compiling Solidity**

The compiler for the Solidity programming language is [solc](https://docs.soliditylang.org/en/v0.8.17/installing-solidity.html) ([Solidity](../../support-and-community/glossary.md#solidity) Compiler). You can use the compiler directly or embedded in IDEs like [Remix IDE](https://remix.ethereum.org/#lang=en\&optimize=false\&runs=200\&evmVersion=null\&version=soljson-v0.8.18+commit.87f61d96.js) or tools like Hardhat and Truffle.

***

## **Smart Contract Bytecode**

Bytecode is the machine-readable language that the EVM uses to execute smart contracts. The compiler analyzes the code, checks for syntax errors, enforces language-specific rules, and generates the corresponding bytecode.

**Example:**

This is the example bytecode output, produced in hexadecimal format, when the [`HelloHedera`](../../tutorials/smart-contracts/deploy-your-first-smart-contract.md#hellohedera.sol) smart contract source code is compiled.

```json
608060405234801561001057600080fd5b506040516105583803806105588339818101604052602081101561003357600080fd5b810190808051604051939291908464010000000082111561005357600080fd5b8382019150602082018581111561006957600080fd5b825186600182028301116401000000008211171561008657600080fd5b8083526020830192505050908051906020019080838360005b838110156100ba57808201518184015260208101905061009f565b50505050905090810190601f1680156100e75780820380516001836020036101000a031916815260200191505b50604052505050336000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff160217905550806001908051906020019061014492919061014b565b50506101e8565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061018c57805160ff19168380011785556101ba565b828001600101855582156101ba579182015b828111156101b957825182559160200191906001019061019e565b5b5090506101c791906101cb565b5090565b5b808211156101e45760008160009055506001016101cc565b5090565b610361806101f76000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c80632e9826021461003b57806332af2edb146100f6575b600080fd5b6100f46004803603602081101561005157600080fd5b810190808035906020019064010000000081111561006e57600080fd5b82018360208201111561008057600080fd5b803590602001918460018302840111640100000000831117156100a257600080fd5b91908080601f016020809104026020016040519081016040528093929190818152602001838380828437600081840152601f19601f820116905080830192505050505050509192919290505050610179565b005b6100fe6101ec565b6040518080602001828103825283818151815260200191508051906020019080838360005b8381101561013e578082015181840152602081019050610123565b50505050905090810190601f16801561016b5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16146101d1576101e9565b80600190805190602001906101e792919061028e565b505b50565b606060018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156102845780601f1061025957610100808354040283529160200191610284565b820191906000526020600020905b81548152906001019060200180831161026757829003601f168201915b5050505050905090565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106102cf57805160ff19168380011785556102fd565b828001600101855582156102fd579182015b828111156102fc5782518255916020019190600101906102e1565b5b50905061030a919061030e565b5090565b5b8082111561032757600081600090555060010161030f565b509056fea26469706673582212201644465f5f73dfd73a518b57770f5adb27f025842235980d7a0f4e15b1acb18e64736f6c63430007000033
```

***

## **Smart Contract Application Binary Interface (ABI)**

The ABI is a JSON (JavaScript Object Notation) file that represents the interface definition for the smart contract. It specifies function signatures, input parameters, return types, and other relevant details of the contract's interface. The ABI helps developers understand how to interact with the smart contract in their distributed applications.

**Example:**

This is the example ABI output produced when the [`HelloHedera`](../../tutorials/smart-contracts/deploy-your-first-smart-contract.md#hellohedera.sol) smart contract is compiled.

```json
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

#### **Additional Resources:**

* [Ethereum: Compiling Smart Contracts](https://ethereum.org/en/developers/docs/smart-contracts/compiling/)

***

## Compiling Smart Contract Example

**➡** [**Hardhat Tutorial**](../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md)

## Additional Resources

**➡** [**HTS Precompile Methods**](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/README.md)
