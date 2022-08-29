# Create and Transfer an NFT using a Solidity Contract

![](<../../.gitbook/assets/image (3).png>)

## Summary

Besides creating NFTs using Hedera SDK, you can use a Solidity Contract to create, mint, and transfer NFTs by calling contract functions directly. These are the contracts you will need to import into your working directory provided by Hedera:

* [HederaTokenService.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/HederaTokenService.sol)
* [HederaResponseCodes.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/HederaResponseCodes.sol)
* [IHederaTokenService.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/IHederaTokenService.sol)
* [ExpiryHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/ExpiryHelper.sol)
* [FeeHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/FeeHelper.sol)
* [KeyHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/hts-precompile/KeyHelper.sol)

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

{% content-ref url="../environment-set-up.md" %}
[environment-set-up.md](../environment-set-up.md)
{% endcontent-ref %}

If you are interested in creating, minting, and transferring NFTs using Hedera SDK you can find the example [here](https://docs.hedera.com/guides/getting-started/try-examples/create-and-transfer-your-first-nft).

{% hint style="warning" %}
In this example, you will set gas for smart contract transactions multiple times. If you don't have enough gas you will receive an<mark style="color:purple;">`INSUFFICIENT_GAS`</mark> response. If you set the value too high you will be refunded a maximum of 20% of the amount that was set for the transaction.
{% endhint %}

## 1. Create an “NFT Creator” Smart Contract

You can find an NFTCreator Solidity contract sample below together with the contract bytecode obtained by compiling the solidity contract using [Remix IDE](https://remix.ethereum.org/). If you are not familiar with Solidity you can take a look at the docs [here](https://docs.soliditylang.org/en/v0.8.14/).

The following contract is composed of three functions:

* <mark style="color:purple;">`createNft`</mark>
* <mark style="color:purple;">`mintNft`</mark>
* <mark style="color:purple;">`transferNft`</mark>

The important thing to know is that the NFT created in this example will have the contract itself as **Treasury Account, Supply Key,** and **Auto-renew account**. There’s **NO** admin key for the NFT or the contract.

{% tabs %}
{% tab title="NFTCreator.sol" %}
```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.5.0 <0.9.0;
 
import './HederaResponseCodes.sol';
import './IHederaTokenService.sol';
import './HederaTokenService.sol';
import './ExpiryHelper.sol';
 
 
contract NFTCreator is ExpiryHelper {
 
   function createNft(
           string memory name,
           string memory symbol,
           string memory memo,
           uint32 maxSupply, 
           uint32 autoRenewPeriod
       ) external payable returns (address){
 
       IHederaTokenService.TokenKey[] memory keys = new IHederaTokenService.TokenKey[](1);
       // Set this contract as supply
       keys[0] = getSingleKey(HederaTokenService.SUPPLY_KEY_TYPE, KeyHelper.CONTRACT_ID_KEY, address(this));
 
       IHederaTokenService.HederaToken memory token;
       token.name = name;
       token.symbol = symbol;
       token.memo = memo;
       token.treasury = address(this);
       token.tokenSupplyType = true; // set supply to FINITE
       token.maxSupply = maxSupply;
       token.tokenKeys = keys;
       token.freezeDefault = false;
       token.expiry = getAutoRenewExpiry(address(this), autoRenewPeriod); // Contract automatically renew by himself
 
       (int responseCode, address createdToken) = HederaTokenService.createNonFungibleToken(token);
 
       if(responseCode != HederaResponseCodes.SUCCESS){
           revert("Failed to create non-fungible token");
       }
       return createdToken;
   }
 
   function mintNft(
       address token,
       bytes[] memory metadata
   ) external returns(int64){
 
       (int response, , int64[] memory serial) = HederaTokenService.mintToken(token, 0, metadata);
 
       if(response != HederaResponseCodes.SUCCESS){
           revert("Failed to mint non-fungible token");
       }
 
       return serial[0];
   }
 
   function transferNft(
       address token,
       address receiver,
       int64 serial
   ) external returns(int){
 
       HederaTokenService.associateToken(receiver, token);
       int response = HederaTokenService.transferNFT(token, address(this), receiver, serial);
 
       if(response != HederaResponseCodes.SUCCESS){
           revert("Failed to transfer non-fungible token");
       }
 
       return response;
   }
 
}
```
{% endtab %}

{% tab title="NFTCreator_sol_NFTCreator.bin" %}
```
608060405234801561001057600080fd5b50611afc806100206000396000f3fe6080604052600436106100345760003560e01c80630a284cb61461003957806382b562aa14610076578063a1a79cde146100b3575b600080fd5b34801561004557600080fd5b50610060600480360381019061005b9190610d90565b6100e3565b60405161006d9190610e08565b60405180910390f35b34801561008257600080fd5b5061009d60048036038101906100989190610e4f565b610165565b6040516100aa9190610ebb565b60405180910390f35b6100cd60048036038101906100c89190610fb3565b6101d3565b6040516100da9190611091565b60405180910390f35b60008060006100f485600086610370565b9250509150601660030b821461013f576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016101369061112f565b60405180910390fd5b806000815181106101535761015261114f565b5b60200260200101519250505092915050565b600061017183856104e8565b50600061018085308686610600565b9050601660030b81146101c8576040517f08c379a00000000000000000000000000000000000000000000000000000000081526004016101bf906111f0565b60405180910390fd5b809150509392505050565b600080600167ffffffffffffffff8111156101f1576101f0610b7a565b5b60405190808252806020026020018201604052801561022a57816020015b6102176109c0565b81526020019060019003908161020f5790505b50905061023a601060023061071e565b8160008151811061024e5761024d61114f565b5b60200260200101819052506102616109e0565b87816000018190525086816020018190525085816060018190525030816040019073ffffffffffffffffffffffffffffffffffffffff16908173ffffffffffffffffffffffffffffffffffffffff16815250506001816080019015159081151581525050848160a0019063ffffffff16908163ffffffff1681525050818160e0018190525060008160c0019015159081151581525050610301308561075d565b816101000181905250600080610316836107bb565b91509150601660030b8214610360576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161035790611282565b60405180910390fd5b8094505050505095945050505050565b600080606060008061016773ffffffffffffffffffffffffffffffffffffffff1663278e0b8860e01b8989896040516024016103ae9392919061140f565b604051602081830303815290604052907bffffffffffffffffffffffffffffffffffffffffffffffffffffffff19166020820180517bffffffffffffffffffffffffffffffffffffffffffffffffffffffff83818316178352505050506040516104189190611489565b6000604051808303816000865af19150503d8060008114610455576040519150601f19603f3d011682016040523d82523d6000602084013e61045a565b606091505b5091509150816104b657601560008067ffffffffffffffff81111561048257610481610b7a565b5b6040519080825280602002602001820160405280156104b05781602001602082028036833780820191505090505b506104cb565b808060200190518101906104ca91906115dd565b5b8260030b9250809550819650829750505050505093509350939050565b600080600061016773ffffffffffffffffffffffffffffffffffffffff166349146bde60e01b868660405160240161052192919061164c565b604051602081830303815290604052907bffffffffffffffffffffffffffffffffffffffffffffffffffffffff19166020820180517bffffffffffffffffffffffffffffffffffffffffffffffffffffffff838183161783525050505060405161058b9190611489565b6000604051808303816000865af19150503d80600081146105c8576040519150601f19603f3d011682016040523d82523d6000602084013e6105cd565b606091505b5091509150816105de5760156105f3565b808060200190518101906105f29190611675565b5b60030b9250505092915050565b600080600061016773ffffffffffffffffffffffffffffffffffffffff16635cfc901160e01b8888888860405160240161063d94939291906116a2565b604051602081830303815290604052907bffffffffffffffffffffffffffffffffffffffffffffffffffffffff19166020820180517bffffffffffffffffffffffffffffffffffffffffffffffffffffffff83818316178352505050506040516106a79190611489565b6000604051808303816000865af19150503d80600081146106e4576040519150601f19603f3d011682016040523d82523d6000602084013e6106e9565b606091505b5091509150816106fa57601561070f565b8080602001905181019061070e9190611675565b5b60030b92505050949350505050565b6107266109c0565b60405180604001604052808581526020016107518560405180602001604052806000815250866108dd565b81525090509392505050565b610765610a52565b82816020019073ffffffffffffffffffffffffffffffffffffffff16908173ffffffffffffffffffffffffffffffffffffffff168152505081816040019063ffffffff16908163ffffffff168152505092915050565b60008060008061016773ffffffffffffffffffffffffffffffffffffffff1634639dc711e060e01b876040516024016107f49190611a26565b604051602081830303815290604052907bffffffffffffffffffffffffffffffffffffffffffffffffffffffff19166020820180517bffffffffffffffffffffffffffffffffffffffffffffffffffffffff838183161783525050505060405161085e9190611489565b60006040518083038185875af1925050503d806000811461089b576040519150601f19603f3d011682016040523d82523d6000602084013e6108a0565b606091505b5091509150816108b357601560006108c8565b808060200190518101906108c79190611a86565b5b8160030b915080945081955050505050915091565b6108e5610a95565b600184036109035760018160000190151590811515815250506109b9565b600284036109485781816020019073ffffffffffffffffffffffffffffffffffffffff16908173ffffffffffffffffffffffffffffffffffffffff16815250506109b8565b6003840361095e578281604001819052506109b7565b60048403610974578281606001819052506109b6565b600584036109b55781816080019073ffffffffffffffffffffffffffffffffffffffff16908173ffffffffffffffffffffffffffffffffffffffff16815250505b5b5b5b5b9392505050565b6040518060400160405280600081526020016109da610a95565b81525090565b6040518061012001604052806060815260200160608152602001600073ffffffffffffffffffffffffffffffffffffffff16815260200160608152602001600015158152602001600063ffffffff16815260200160001515815260200160608152602001610a4c610a52565b81525090565b6040518060600160405280600063ffffffff168152602001600073ffffffffffffffffffffffffffffffffffffffff168152602001600063ffffffff1681525090565b6040518060a00160405280600015158152602001600073ffffffffffffffffffffffffffffffffffffffff1681526020016060815260200160608152602001600073ffffffffffffffffffffffffffffffffffffffff1681525090565b6000604051905090565b600080fd5b600080fd5b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b6000610b3182610b06565b9050919050565b610b4181610b26565b8114610b4c57600080fd5b50565b600081359050610b5e81610b38565b92915050565b600080fd5b6000601f19601f8301169050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052604160045260246000fd5b610bb282610b69565b810181811067ffffffffffffffff82111715610bd157610bd0610b7a565b5b80604052505050565b6000610be4610af2565b9050610bf08282610ba9565b919050565b600067ffffffffffffffff821115610c1057610c0f610b7a565b5b602082029050602081019050919050565b600080fd5b600080fd5b600067ffffffffffffffff821115610c4657610c45610b7a565b5b610c4f82610b69565b9050602081019050919050565b82818337600083830152505050565b6000610c7e610c7984610c2b565b610bda565b905082815260208101848484011115610c9a57610c99610c26565b5b610ca5848285610c5c565b509392505050565b600082601f830112610cc257610cc1610b64565b5b8135610cd2848260208601610c6b565b91505092915050565b6000610cee610ce984610bf5565b610bda565b90508083825260208201905060208402830185811115610d1157610d10610c21565b5b835b81811015610d5857803567ffffffffffffffff811115610d3657610d35610b64565b5b808601610d438982610cad565b85526020850194505050602081019050610d13565b5050509392505050565b600082601f830112610d7757610d76610b64565b5b8135610d87848260208601610cdb565b91505092915050565b60008060408385031215610da757610da6610afc565b5b6000610db585828601610b4f565b925050602083013567ffffffffffffffff811115610dd657610dd5610b01565b5b610de285828601610d62565b9150509250929050565b60008160070b9050919050565b610e0281610dec565b82525050565b6000602082019050610e1d6000830184610df9565b92915050565b610e2c81610dec565b8114610e3757600080fd5b50565b600081359050610e4981610e23565b92915050565b600080600060608486031215610e6857610e67610afc565b5b6000610e7686828701610b4f565b9350506020610e8786828701610b4f565b9250506040610e9886828701610e3a565b9150509250925092565b6000819050919050565b610eb581610ea2565b82525050565b6000602082019050610ed06000830184610eac565b92915050565b600067ffffffffffffffff821115610ef157610ef0610b7a565b5b610efa82610b69565b9050602081019050919050565b6000610f1a610f1584610ed6565b610bda565b905082815260208101848484011115610f3657610f35610c26565b5b610f41848285610c5c565b509392505050565b600082601f830112610f5e57610f5d610b64565b5b8135610f6e848260208601610f07565b91505092915050565b600063ffffffff82169050919050565b610f9081610f77565b8114610f9b57600080fd5b50565b600081359050610fad81610f87565b92915050565b600080600080600060a08688031215610fcf57610fce610afc565b5b600086013567ffffffffffffffff811115610fed57610fec610b01565b5b610ff988828901610f49565b955050602086013567ffffffffffffffff81111561101a57611019610b01565b5b61102688828901610f49565b945050604086013567ffffffffffffffff81111561104757611046610b01565b5b61105388828901610f49565b935050606061106488828901610f9e565b925050608061107588828901610f9e565b9150509295509295909350565b61108b81610b26565b82525050565b60006020820190506110a66000830184611082565b92915050565b600082825260208201905092915050565b7f4661696c656420746f206d696e74206e6f6e2d66756e6769626c6520746f6b6560008201527f6e00000000000000000000000000000000000000000000000000000000000000602082015250565b60006111196021836110ac565b9150611124826110bd565b604082019050919050565b600060208201905081810360008301526111488161110c565b9050919050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052603260045260246000fd5b7f4661696c656420746f207472616e73666572206e6f6e2d66756e6769626c652060008201527f746f6b656e000000000000000000000000000000000000000000000000000000602082015250565b60006111da6025836110ac565b91506111e58261117e565b604082019050919050565b60006020820190508181036000830152611209816111cd565b9050919050565b7f4661696c656420746f20637265617465206e6f6e2d66756e6769626c6520746f60008201527f6b656e0000000000000000000000000000000000000000000000000000000000602082015250565b600061126c6023836110ac565b915061127782611210565b604082019050919050565b6000602082019050818103600083015261129b8161125f565b9050919050565b600067ffffffffffffffff82169050919050565b6112bf816112a2565b82525050565b600081519050919050565b600082825260208201905092915050565b6000819050602082019050919050565b600081519050919050565b600082825260208201905092915050565b60005b8381101561132b578082015181840152602081019050611310565b8381111561133a576000848401525b50505050565b600061134b826112f1565b61135581856112fc565b935061136581856020860161130d565b61136e81610b69565b840191505092915050565b60006113858383611340565b905092915050565b6000602082019050919050565b60006113a5826112c5565b6113af81856112d0565b9350836020820285016113c1856112e1565b8060005b858110156113fd57848403895281516113de8582611379565b94506113e98361138d565b925060208a019950506001810190506113c5565b50829750879550505050505092915050565b60006060820190506114246000830186611082565b61143160208301856112b6565b8181036040830152611443818461139a565b9050949350505050565b600081905092915050565b6000611463826112f1565b61146d818561144d565b935061147d81856020860161130d565b80840191505092915050565b60006114958284611458565b915081905092915050565b60008160030b9050919050565b6114b6816114a0565b81146114c157600080fd5b50565b6000815190506114d3816114ad565b92915050565b6114e2816112a2565b81146114ed57600080fd5b50565b6000815190506114ff816114d9565b92915050565b600067ffffffffffffffff8211156115205761151f610b7a565b5b602082029050602081019050919050565b60008151905061154081610e23565b92915050565b600061155961155484611505565b610bda565b9050808382526020820190506020840283018581111561157c5761157b610c21565b5b835b818110156115a557806115918882611531565b84526020840193505060208101905061157e565b5050509392505050565b600082601f8301126115c4576115c3610b64565b5b81516115d4848260208601611546565b91505092915050565b6000806000606084860312156115f6576115f5610afc565b5b6000611604868287016114c4565b9350506020611615868287016114f0565b925050604084015167ffffffffffffffff81111561163657611635610b01565b5b611642868287016115af565b9150509250925092565b60006040820190506116616000830185611082565b61166e6020830184611082565b9392505050565b60006020828403121561168b5761168a610afc565b5b6000611699848285016114c4565b91505092915050565b60006080820190506116b76000830187611082565b6116c46020830186611082565b6116d16040830185611082565b6116de6060830184610df9565b95945050505050565b600081519050919050565b600082825260208201905092915050565b600061170e826116e7565b61171881856116f2565b935061172881856020860161130d565b61173181610b69565b840191505092915050565b61174581610b26565b82525050565b60008115159050919050565b6117608161174b565b82525050565b61176f81610f77565b82525050565b600081519050919050565b600082825260208201905092915050565b6000819050602082019050919050565b6000819050919050565b6117b4816117a1565b82525050565b600060a0830160008301516117d26000860182611757565b5060208301516117e5602086018261173c565b50604083015184820360408601526117fd8282611340565b915050606083015184820360608601526118178282611340565b915050608083015161182c608086018261173c565b508091505092915050565b600060408301600083015161184f60008601826117ab565b506020830151848203602086015261186782826117ba565b9150508091505092915050565b60006118808383611837565b905092915050565b6000602082019050919050565b60006118a082611775565b6118aa8185611780565b9350836020820285016118bc85611791565b8060005b858110156118f857848403895281516118d98582611874565b94506118e483611888565b925060208a019950506001810190506118c0565b50829750879550505050505092915050565b6060820160008201516119206000850182611766565b506020820151611933602085018261173c565b5060408201516119466040850182611766565b50505050565b600061016083016000830151848203600086015261196a8282611703565b915050602083015184820360208601526119848282611703565b9150506040830151611999604086018261173c565b50606083015184820360608601526119b18282611703565b91505060808301516119c66080860182611757565b5060a08301516119d960a0860182611766565b5060c08301516119ec60c0860182611757565b5060e083015184820360e0860152611a048282611895565b915050610100830151611a1b61010086018261190a565b508091505092915050565b60006020820190508181036000830152611a40818461194c565b905092915050565b6000611a5382610b06565b9050919050565b611a6381611a48565b8114611a6e57600080fd5b50565b600081519050611a8081611a5a565b92915050565b60008060408385031215611a9d57611a9c610afc565b5b6000611aab858286016114c4565b9250506020611abc85828601611a71565b915050925092905056fea2646970667358221220cdc42bff95d0a45152dc9da522a29db234e626280cc22363dd793efc0ff7428664736f6c634300080e0033
```
{% endtab %}
{% endtabs %}

## 2. Store your bytecode on the Hedera Network

Store your contract on Hedera using <mark style="color:purple;">`ContractCreateFlow()`</mark>. This single call performs <mark style="color:purple;">`FileCreateTransaction()`</mark>,<mark style="color:purple;">`FileAppendTransaction()`</mark>, and <mark style="color:purple;">`ContractCreateTransaction()`</mark> for you. See the difference [here](https://docs.hedera.com/guides/docs/sdks/smart-contracts/create-a-smart-contract).

{% tabs %}
{% tab title="Java" %}
```java
// Create contract
ContractCreateFlow createContract = new ContractCreateFlow()
   .setBytecode(bytecode) // Contract bytecode
   .setGas(150_000); // Increase if revert

TransactionResponse createContractTx = createContract.execute(client);
TransactionReceipt createContractRx = createContractTx.getReceipt(client);
// Get the new contract ID
ContractId newContractId = createContractRx.contractId;
                
System.out.println("Contract created with ID: " + newContractId);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create contract
const createContract = new ContractCreateFlow()
    .setGas(150000) // Increase if revert
    .setBytecode(bytecode); // Contract bytecode
const createContractTx = await createContract.execute(client);
const createContractRx = await createContractTx.getReceipt(client);
const contractId = createContractRx.contractId;

console.log(`Contract created with ID: ${contractId} \n`);
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
createContract := hedera.NewContractCreateFlow().
	SetGas(150000).
	SetBytecode([]byte(bytecode))

//Sign the transaction with the client operator key and submit to a Hedera network
txResponse, err := createContract.Execute(client)
if err != nil {
	panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
	panic(err)
}

//Get the contract ID
newContractId := *receipt.ContractID

fmt.Printf("The new contract ID is %v\n", newContractId)
```
{% endtab %}
{% endtabs %}

## 3. Execute the Contract to Create an NFT

The parameters you need to specify for this contract call are Name, Symbol, Memo, Maximum Supply, and Expiration. Setting an expiration date in seconds is required because entities on Hedera will need to pay a “rent” to persist. In this case, the contract entity will pay all NFT auto-renewal fees.

{% hint style="warning" %}
Note that expiration must be between 82 and 91 days **specified in seconds**. This window will change when [HIP-372](https://hips.hedera.com/hip/hip-372) replaces [HIP-16](https://hips.hedera.com/hip/hip-16).
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
// Create NFT using contract
ContractExecuteTransaction createToken = new ContractExecuteTransaction()
		.setContractId(newContractId) // Contract id
		.setGas(300_000) // Increase if revert
		.setPayableAmount(new Hbar(20)) // Increase if revert
		.setFunction("createNft", new ContractFunctionParameters()
        .addString("Fall Collection") // NFT Name
        .addString("LEAF") // NFT Symbol
        .addString("Just a memo") // NFT Memo
        .addUint32(10) // NFT max supply
        .addUint32(7_000_000)); // Expiration: Needs to be between 6999999 and 8000001

TransactionResponse createTokenTx = createToken.execute(client);
TransactionRecord createTokenRx = createTokenTx.getRecord(client);

String tokenIdSolidityAddr = createTokenRx.contractFunctionResult.getAddress(0);
AccountId tokenId = AccountId.fromSolidityAddress(tokenIdSolidityAddr);

System.out.println("Token created with ID: " + tokenId);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create NFT from precompile
const createToken = new ContractExecuteTransaction()
    .setContractId(contractId)
    .setGas(300000) // Increase if revert
    .setPayableAmount(20) // Increase if revert
    .setFunction("createNft",
        new ContractFunctionParameters()
        .addString("Fall Collection") // NFT name
        .addString("LEAF") // NFT symbol
        .addString("Just a memo") // NFT memo
        .addUint32(250) // NFT max supply
        .addUint32(7000000) // Expiration: Needs to be between 6999999 and 8000001
        );
const createTokenTx = await createToken.execute(client);
const createTokenRx = await createTokenTx.getRecord(client);
const tokenIdSolidityAddr = createTokenRx.contractFunctionResult.getAddress(0);
const tokenId = AccountId.fromSolidityAddress(tokenIdSolidityAddr);

console.log(`Token created with ID: ${tokenId} \n`);
```
{% endtab %}

{% tab title="Go" %}
```go
contractParams := hedera.NewContractFunctionParameters().
	AddString("Fall Collection"). // NFT name
	AddString("LEAF").            // NFT symbol
	AddString("Just a memo").     // NFT memo
	AddUint32(250).               // NFT max supply
	AddUint32(7000000)            // Expiration: Needs to be between 6999999 and 8000001
//Create NFT
createToken, err := hedera.NewContractExecuteTransaction().
	//The contract ID
	SetContractID(newContractId).
	//The max gas
	SetGas(300000).
	SetPayableAmount(hedera.NewHbar(20)).
	//The contract function to call and parameters
	SetFunction("createNft", contractParams).
	Execute(client)

if err != nil {
	panic(err)
}

//Get the record
txRecord, err := createToken.GetRecord(client)
if err != nil {
	panic(err)
}

//Get transaction status
contractResult, err := txRecord.GetContractExecuteResult()
if err != nil {
	panic(err)
}
tokenIdSolidityAddr := hex.EncodeToString(contractResult.GetAddress(0))

tokenId, err := hedera.AccountIDFromSolidityAddress(tokenIdSolidityAddr)
if err != nil {
	panic(err)
}

fmt.Printf("Token created with ID: %v\n", tokenId)
```
{% endtab %}
{% endtabs %}

## 4. Execute the Contract to Mint a New NFT

After the token ID is created, you mint each NFT under that ID using the <mark style="color:purple;">`mintNft`</mark> function. For the minting, you must specify the token ID as a Solidity address and the NFT metadata.

Both the NFT image and metadata live in the InterPlanetary File System (IPFS), which provides decentralized storage. The file metadata.json contains the metadata for the NFT. An IPFS URI pointing to the metadata file is used during minting of a new NFT. Notice that the metadata file contains a URI pointing to the NFT image.&#x20;

{% hint style="info" %}
For the latest NFT Token Metadata JSON Schema see [HIP-412](https://hips.hedera.com/hip/hip-412).
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
// Mint NFT
ContractExecuteTransaction mintToken = new ContractExecuteTransaction()
		.setContractId(newContractId)
		.setGas(1_500_000)
		.setFunction("mintNft", new ContractFunctionParameters()
        .addAddress(tokenIdSolidityAddr) // Token address
        .addBytesArray(byteArray)); // Metadata

TransactionResponse mintTokenTx = mintToken.execute(client);
TransactionRecord mintTokenRx = mintTokenTx.getRecord(client);
// NFT serial number
long serial = mintTokenRx.contractFunctionResult.getInt64(0);

System.out.println("Minted NFT with serial: " + serial);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// ipfs URI
metadata = "ipfs://bafyreie3ichmqul4xa7e6xcy34tylbuq2vf3gnjf7c55trg3b6xyjr4bku/metadata.json";
 
// Mint NFT
const mintToken = new ContractExecuteTransaction()
    .setContractId(contractId)
    .setGas(1000000)
    .setFunction("mintNft",
        new ContractFunctionParameters()
        .addAddress(tokenIdSolidityAddr) // Token address
        .addBytesArray([Buffer.from(metadata)]) // Metadata
        );
const mintTokenTx = await mintToken.execute(client);
const mintTokenRx = await mintTokenTx.getRecord(client);
const serial = mintTokenRx.contractFunctionResult.getInt64(0);

console.log(`Minted NFT with serial: ${serial} \n`);
```
{% endtab %}

{% tab title="Go" %}
```go
// ipfs URI
metadata := "ipfs://bafyreie3ichmqul4xa7e6xcy34tylbuq2vf3gnjf7c55trg3b6xyjr4bku/metadata.json"
bytesArray := [][]byte{}
bytesArray = append(bytesArray, []byte(metadata))

// Add token address to params
mintParams, err := hedera.NewContractFunctionParameters().
	AddAddress(tokenIdSolidityAddr)

if err != nil {
	panic(err)
}

// Add metadata to params
mintParams = mintParams.AddBytesArray(bytesArray)

// Mint NFT
mintToken, err := hedera.NewContractExecuteTransaction().
	//The contract ID
	SetContractID(newContractId).
	//The max gas
	SetGas(1000000).
	//The contract function to call and parameters
	SetFunction("mintNft", mintParams).
	Execute(client)

if err != nil {
	panic(err)
}

//Get the record
mintRecord, err := mintToken.GetRecord(client)
if err != nil {
	panic(err)
}

//Get transaction status
mintResult, err := mintRecord.GetContractExecuteResult()
if err != nil {
	panic(err)
}
serial := mintResult.GetInt64(0)

fmt.Printf("Minted NFT with serial: %v\n", serial)
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="metadata.json" %}
```json
{
    "name": "LEAF1.jpg",
    "creator": "Mother Nature",
    "description": "Autumn",
    "type": "image/jpg",
    "format": "none",
    "properties": {
        "city": "Boston",
        "season": "Fall",
        "decade": "20's"
    },
    "image": "ipfs://bafybeig35bheyqpi4qlnuljpok54ud753bnp62fe6jit343hv3oxhgnbfm/LEAF1.jpg"
}
```
{% endtab %}
{% endtabs %}

## 5. Execute the Contract to Transfer the NFT

The NFT is minted to the contract address because the contract is the treasury for the token. Now transfer the NFT to another account or contract address. In this example, you will transfer the NFT to Alice. For the transfer, you must specify the token address and NFT serial number.&#x20;

The <mark style="color:purple;">`transferNft`</mark> function in the Solidity contract contains a call to an <mark style="color:purple;">`associateToken`</mark> function that will automatically associate Alice to the token ID. This association transaction must be signed using Alice's private key. After signing, Alice will receive the NFT.

{% tabs %}
{% tab title="Java" %}
```java
// Transfer NFT to Alice
ContractExecuteTransaction transferToken = new ContractExecuteTransaction()
		.setContractId(newContractId)
		.setGas(1_500_000)
		.setFunction("transferNft", new ContractFunctionParameters()
        .addAddress(tokenIdSolidityAddr) // Token id
        .addAddress(aliceId.toSolidityAddress()) // Token receiver (Alice)
        .addInt64(serial)) // Serial number
		.freezeWith(client) // Freeze transaction using client
		.sign(aliceKey); //Sign using Alice Private Key

TransactionResponse transferTokenTx = transferToken.execute(client);
TransactionReceipt transferTokenRx = transferTokenTx.getReceipt(client);

System.out.println("Trasnfer status: " + transferTokenRx.status);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Transfer NFT to Alice
const transferToken = await new ContractExecuteTransaction()
    .setContractId(contractId)
    .setGas(1000000)
    .setFunction("transferNft",
        new ContractFunctionParameters()
        .addAddress(tokenIdSolidityAddr) // Token address
        .addAddress(aliceId.toSolidityAddress()) // Token receiver (Alice)
        .addInt64(serial)) // NFT serial number
    .freezeWith(client) // freezing using client
    .sign(aliceKey); // Sign transaction with Alice
const transferTokenTx = await transferToken.execute(client);
const transferTokenRx = await transferTokenTx.getReceipt(client);

console.log(`Transfer status: ${transferTokenRx.status} \n`);
```
{% endtab %}

{% tab title="Go" %}
```go
// Add token address to params
transferParams, err := hedera.NewContractFunctionParameters().
	AddAddress(tokenIdSolidityAddr)
// Add Alice address to params
transferParams, err = transferParams.AddAddress(aliceAccountId.ToSolidityAddress())
if err != nil {
	panic(err)
}
transferParams = transferParams.AddInt64(serial)

// Transfer NFT
transferToken, err := hedera.NewContractExecuteTransaction().
	//The contract ID
	SetContractID(newContractId).
	//The max gas
	SetGas(1000000).
	//The contract function to call and parameters
	SetFunction("transferNft", transferParams).
	FreezeWith(client)

if err != nil {
	panic(err)
}

transferSubmit, err := transferToken.Sign(aliceKey).Execute(client)
if err != nil {
	panic(err)
}

//Get the record
transferRecord, err := transferSubmit.GetReceipt(client)
if err != nil {
	panic(err)
}

fmt.Printf("Transfer status: %v\n", transferRecord.Status)
```
{% endtab %}
{% endtabs %}

## Code Check ✅

{% tabs %}
{% tab title="Java" %}
```java
package _nft_hscs_hts.hedera;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Objects;
import java.util.concurrent.TimeoutException;

import com.hedera.hashgraph.sdk.*;
import io.github.cdimascio.dotenv.Dotenv;

public class Deploy {
	
	// Account creation function
	private static AccountId accountCreator(PrivateKey pvKey, int iBal , Client client) throws TimeoutException, PrecheckStatusException, ReceiptStatusException {
		AccountCreateTransaction transaction = new AccountCreateTransaction()
		    .setKey(pvKey.getPublicKey())
		    .setInitialBalance(new Hbar(iBal));

		TransactionResponse txResponse = transaction.execute(client);

		TransactionReceipt receipt = txResponse.getReceipt(client);

		return receipt.accountId;

	}

        public static void main( String[] args ) throws TimeoutException, PrecheckStatusException, ReceiptStatusException, IOException
	{
	        // ipfs URI
	        String metadata = ("ipfs://bafyreie3ichmqul4xa7e6xcy34tylbuq2vf3gnjf7c55trg3b6xyjr4bku/metadata.json");
		byte[][] byteArray = new byte[1][metadata.length()];
		byteArray[0] = metadata.getBytes();
		
		AccountId operatorId = AccountId.fromString(Objects.requireNonNull(Dotenv.load().get("ACCOUNT_ID")));
           PrivateKey operatorKey = PrivateKey.fromString(Objects.requireNonNull(Dotenv.load().get("PRIVATE_KEY")));
           
           Client client = Client.forTestnet();
           client.setOperator(operatorId, operatorKey);
           
           PrivateKey aliceKey = PrivateKey.generateED25519();
           AccountId aliceId = accountCreator(aliceKey, 100, client);
           System.out.print(aliceId);
           
           String bytecode = Files.readString(Paths.get("./NFTCreator_sol_NFTCreator.bin"));
           
           // Create contract
           ContractCreateFlow createContract = new ContractCreateFlow()
                .setBytecode(bytecode) // Contract bytecode
                .setGas(150_000); // Increase if revert
   
           TransactionResponse createContractTx = createContract.execute(client);
           TransactionReceipt createContractRx = createContractTx.getReceipt(client);
           // Get the new contract ID
           ContractId newContractId = createContractRx.contractId;
                   
           System.out.println("Contract created with ID: " + newContractId);
           
           // Create NFT using contract
           ContractExecuteTransaction createToken = new ContractExecuteTransaction()
           		.setContractId(newContractId) // Contract id
           		.setGas(300_000) // Increase if revert
           		.setPayableAmount(new Hbar(20)) // Increase if revert
           		.setFunction("createNft", new ContractFunctionParameters()
                      .addString("Fall Collection") // NFT Name
                      .addString("LEAF") // NFT Symbol
                      .addString("Just a memo") // NFT Memo
                      .addUint32(10) // NFT max supply
                      .addUint32(7_000_000)); // Expiration: Needs to be between 6999999 and 8000001
   
           TransactionResponse createTokenTx = createToken.execute(client);
           TransactionRecord createTokenRx = createTokenTx.getRecord(client);
   
           String tokenIdSolidityAddr = createTokenRx.contractFunctionResult.getAddress(0);
           AccountId tokenId = AccountId.fromSolidityAddress(tokenIdSolidityAddr);
   
           System.out.println("Token created with ID: " + tokenId);
           
           // Mint NFT
           ContractExecuteTransaction mintToken = new ContractExecuteTransaction()
           		.setContractId(newContractId)
           		.setGas(1_500_000)
           		.setFunction("mintNft", new ContractFunctionParameters()
                      .addAddress(tokenIdSolidityAddr) // Token address
                      .addBytesArray(byteArray)); // Metadata
   
           TransactionResponse mintTokenTx = mintToken.execute(client);
           TransactionRecord mintTokenRx = mintTokenTx.getRecord(client);
           // NFT serial number
           long serial = mintTokenRx.contractFunctionResult.getInt64(0);
   
           System.out.println("Minted NFT with serial: " + serial);
           
           // Transfer NFT to Alice
           ContractExecuteTransaction transferToken = new ContractExecuteTransaction()
           		.setContractId(newContractId)
           		.setGas(1_500_000)
           		.setFunction("transferNft", new ContractFunctionParameters()
                      .addAddress(tokenIdSolidityAddr) // Token id
                      .addAddress(aliceId.toSolidityAddress()) // Token receiver (Alice)
                      .addInt64(serial)) // Serial number
           		.freezeWith(client) // Freeze transaction using client
           		.sign(aliceKey); //Sign using Alice Private Key
   
           TransactionResponse transferTokenTx = transferToken.execute(client);
           TransactionReceipt transferTokenRx = transferTokenTx.getReceipt(client);
   
           System.out.println("Trasnfer status: " + transferTokenRx.status);
        
    }
}
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const fs = require('fs');
const { AccountId,
       PrivateKey,
       Client,
       ContractCreateFlow,
       ContractExecuteTransaction,
       ContractFunctionParameters,
       AccountCreateTransaction,
       Hbar
       } = require('@hashgraph/sdk');
 
// Setup your .env path
require('dotenv').config({path: __dirname + '/../../.env'});
 
// ipfs URI
metadata = "ipfs://bafyreie3ichmqul4xa7e6xcy34tylbuq2vf3gnjf7c55trg3b6xyjr4bku/metadata.json";
 
const operatorKey = PrivateKey.fromString(process.env.PRIVATE_KEY);
const operatorId = AccountId.fromString(process.env.ACCOUNT_ID);
 
const client = Client.forTestnet().setOperator(operatorId, operatorKey);
 
// Account creation function
async function accountCreator(pvKey, iBal) {
   const response = await new AccountCreateTransaction()
       .setInitialBalance(new Hbar(iBal))
       .setKey(pvKey.publicKey)
       .execute(client);
   const receipt = await response.getReceipt(client);
   return receipt.accountId;
}
 
const main = async () => {
  
   // Init Alice account
   const aliceKey = PrivateKey.generateED25519();
   const aliceId = await accountCreator(aliceKey, 100);
 
   const bytecode = fs.readFileSync('./binaries/NFTCreator_sol_NFTCreator.bin');
 
   // Create contract
   const createContract = new ContractCreateFlow()
       .setGas(150000) // Increase if revert
       .setBytecode(bytecode); // Contract bytecode
   const createContractTx = await createContract.execute(client);
   const createContractRx = await createContractTx.getReceipt(client);
   const contractId = createContractRx.contractId;
 
   console.log(`Contract created with ID: ${contractId} \n`);
 
   // Create NFT from precompile
   const createToken = new ContractExecuteTransaction()
       .setContractId(contractId)
       .setGas(300000) // Increase if revert
       .setPayableAmount(20) // Increase if revert
       .setFunction("createNft",
           new ContractFunctionParameters()
           .addString("Fall Collection") // NFT name
           .addString("LEAF") // NFT symbol
           .addString("Just a memo") // NFT memo
           .addUint32(250) // NFT max supply
           .addUint32(7000000) // Expiration: Needs to be between 6999999 and 8000001
           );
   const createTokenTx = await createToken.execute(client);
   const createTokenRx = await createTokenTx.getRecord(client);
   const tokenIdSolidityAddr = createTokenRx.contractFunctionResult.getAddress(0);
   const tokenId = AccountId.fromSolidityAddress(tokenIdSolidityAddr);
 
   console.log(`Token created with ID: ${tokenId} \n`);
 
   // Mint NFT
   const mintToken = new ContractExecuteTransaction()
       .setContractId(contractId)
       .setGas(1000000)
       .setFunction("mintNft",
           new ContractFunctionParameters()
           .addAddress(tokenIdSolidityAddr) // Token address
           .addBytesArray([Buffer.from(metadata)]) // Metadata
           );
   const mintTokenTx = await mintToken.execute(client);
   const mintTokenRx = await mintTokenTx.getRecord(client);
   const serial = mintTokenRx.contractFunctionResult.getInt64(0);
 
   console.log(`Minted NFT with serial: ${serial} \n`);
  
   // Transfer NFT to Alice
   const transferToken = await new ContractExecuteTransaction()
       .setContractId(contractId)
       .setGas(1000000)
       .setFunction("transferNft",
           new ContractFunctionParameters()
           .addAddress(tokenIdSolidityAddr) // Token address
           .addAddress(aliceId.toSolidityAddress()) // Token receiver (Alice)
           .addInt64(serial)) // NFT serial number
       .freezeWith(client) // freezing using client
       .sign(aliceKey); // Sign transaction with Alice
   const transferTokenTx = await transferToken.execute(client);
   const transferTokenRx = await transferTokenTx.getReceipt(client);
 
   console.log(`Transfer status: ${transferTokenRx.status} \n`);
 
}
 
main();
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"encoding/hex"
	"fmt"
	"io/ioutil"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {
	godotenv.Load("../.env")

	metadata := "ipfs://bafyreie3ichmqul4xa7e6xcy34tylbuq2vf3gnjf7c55trg3b6xyjr4bku/metadata.json"
	bytesArray := [][]byte{}
	bytesArray = append(bytesArray, []byte(metadata))

	var client *hedera.Client
	var err error

	client = hedera.ClientForTestnet()

	operatorId, err := hedera.AccountIDFromString(os.Getenv("OPERATOR_ID"))
	if err != nil {
		panic(err)
	}

	operatorKey, err := hedera.PrivateKeyFromString(os.Getenv("OPERATOR_PVKEY"))
	if err != nil {
		panic(err)
	}

	client.SetOperator(operatorId, operatorKey)

	aliceKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		panic(err)
	}

	//Create the transaction
	accountCreate := hedera.NewAccountCreateTransaction().
		SetKey(aliceKey.PublicKey()).
		SetInitialBalance(hedera.NewHbar(100))

	//Sign the transaction with the client operator private key and submit to a Hedera network
	accountCreateSubmit, err := accountCreate.Execute(client)
	if err != nil {
		panic(err)
	}

	//Request the receipt of the transaction
	accountCreateReceipt, err := accountCreateSubmit.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	//Get the account ID
	aliceAccountId := *accountCreateReceipt.AccountID

	// Make sure to close client after running
	defer func() {
		err = client.Close()
		if err != nil {
			println(err.Error(), ": error closing client")
			return
		}
	}()

	// Read bytecode
	bytecode, err := ioutil.ReadFile("./NFTCreator_sol_NFTCreator.bin")
	if err != nil {
		println(err.Error(), ": error reading bytecode")
		return
	}

	//Create the transaction
	createContract := hedera.NewContractCreateFlow().
		SetGas(150000).
		SetBytecode([]byte(bytecode))

	//Sign the transaction with the client operator key and submit to a Hedera network
	txResponse, err := createContract.Execute(client)
	if err != nil {
		panic(err)
	}

	//Request the receipt of the transaction
	receipt, err := txResponse.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	//Get the contract ID
	newContractId := *receipt.ContractID

	fmt.Printf("The new contract ID is %v\n", newContractId)

	contractParams := hedera.NewContractFunctionParameters().
		AddString("Fall Collection"). // NFT name
		AddString("LEAF").            // NFT symbol
		AddString("Just a memo").     // NFT memo
		AddUint32(250).               // NFT max supply
		AddUint32(7000000)            // Expiration: Needs to be between 6999999 and 8000001
	//Create NFT
	createToken, err := hedera.NewContractExecuteTransaction().
		//The contract ID
		SetContractID(newContractId).
		//The max gas
		SetGas(300000).
		SetPayableAmount(hedera.NewHbar(20)).
		//The contract function to call and parameters
		SetFunction("createNft", contractParams).
		Execute(client)

	if err != nil {
		panic(err)
	}

	//Get the record
	txRecord, err := createToken.GetRecord(client)
	if err != nil {
		panic(err)
	}

	//Get transaction status
	contractResult, err := txRecord.GetContractExecuteResult()
	if err != nil {
		panic(err)
	}
	tokenIdSolidityAddr := hex.EncodeToString(contractResult.GetAddress(0))

	tokenId, err := hedera.AccountIDFromSolidityAddress(tokenIdSolidityAddr)
	if err != nil {
		panic(err)
	}

	fmt.Printf("Token created with ID: %v\n", tokenId)

	// Add token address to params
	mintParams, err := hedera.NewContractFunctionParameters().
		AddAddress(tokenIdSolidityAddr)

	if err != nil {
		panic(err)
	}

	// Add metadata to params
	mintParams = mintParams.AddBytesArray(bytesArray)

	// Mint NFT
	mintToken, err := hedera.NewContractExecuteTransaction().
		//The contract ID
		SetContractID(newContractId).
		//The max gas
		SetGas(1000000).
		//The contract function to call and parameters
		SetFunction("mintNft", mintParams).
		Execute(client)

	if err != nil {
		panic(err)
	}

	//Get the record
	mintRecord, err := mintToken.GetRecord(client)
	if err != nil {
		panic(err)
	}

	//Get transaction status
	mintResult, err := mintRecord.GetContractExecuteResult()
	if err != nil {
		panic(err)
	}
	serial := mintResult.GetInt64(0)

	fmt.Printf("Minted NFT with serial: %v\n", serial)

	// Add token address to params
	transferParams, err := hedera.NewContractFunctionParameters().
		AddAddress(tokenIdSolidityAddr)
	// Add Alice address to params
	transferParams, err = transferParams.AddAddress(aliceAccountId.ToSolidityAddress())
	if err != nil {
		panic(err)
	}
	transferParams = transferParams.AddInt64(serial)

	// Transfer NFT
	transferToken, err := hedera.NewContractExecuteTransaction().
		//The contract ID
		SetContractID(newContractId).
		//The max gas
		SetGas(1000000).
		//The contract function to call and parameters
		SetFunction("transferNft", transferParams).
		FreezeWith(client)

	if err != nil {
		panic(err)
	}

	transferSubmit, err := transferToken.Sign(aliceKey).Execute(client)
	if err != nil {
		panic(err)
	}

	//Get the record
	transferRecord, err := transferSubmit.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	fmt.Printf("Transfer status: %v\n", transferRecord.Status)
}
```
{% endtab %}
{% endtabs %}

