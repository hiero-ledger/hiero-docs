# Send and Receive HBAR Using Solidity Smart Contracts

Smart contracts on Hedera can hold and exchange value in the form of HBAR, Hedera Token Service (HTS) tokens, and even ERC tokens. This is fundamental for building decentralized applications that rely on contracts in areas like DeFi, ESG, NFT marketplaces, DAOs, and more.

Let’s learn how to send and receive HBAR to and from Hedera contracts. [Part 1](https://hedera.com/blog/how-to-send-and-receive-hbar-using-smart-contracts-part-1-using-the-sdk) of the series focused on using the Hedera SDKs. This second part goes over transferring HBAR to and from contracts using Solidity.

Follow these main 3 steps:

1. Create the Hedera accounts needed for testing and deploy a smart contract on the Testnet
2. Move HBAR to the contract using _**fallback**_ and _**receive**_ functions, a _**payable**_ function, and the **SDK**
3. Move HBAR from the contract to Alice using the _**transfer**_, _**send**_, and _**call**_ methods

Throughout the tutorial, you also learn how to check the HBAR balance of the contract by calling a function of the contract itself and by using the SDK query. The last step is to review the transaction history for the contract and the operator account in a mirror node explorer, like [HashScan](https://hashscan.io/#/mainnet/dashboard).

***

## **Prerequisites**&#x20;

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](../../getting-started/introduction.md).
* Set up your environment [here](../../getting-started/environment-set-up.md).

***

## Table of Contents

1. [Create Accounts and Deploy a Contract](send-and-receive-hbar-using-solidity-smart-contracts.md#create-accounts-and-deploy-a-contract)
2. [Get HBAR to ➡ Contract](send-and-receive-hbar-using-solidity-smart-contracts.md#getting-hbar-to-the-contract)
3. [Get HBAR from ⬅ Contract](send-and-receive-hbar-using-solidity-smart-contracts.md#getting-hbar-from-the-contract)
4. [Summary](send-and-receive-hbar-using-solidity-smart-contracts.md#summary)
5. [Additional Resources ](send-and-receive-hbar-using-solidity-smart-contracts.md#additional-resources)

***

## **Create Accounts and Deploy a Contract**

This example involves 3 Hedera accounts, 1 contract, and 1 Hedera Token Service (HTS) token. The Operator account ([your Testnet account credentials](https://portal.hedera.com/register)) is used to build the Hedera client to submit transactions to the Hedera network – that’s the first account. The Treasury and Alice are new accounts (created by the Operator) to represent additional parties in your test – those are the second and third accounts respectively.

A portion of the application file (_**index.js**_) and the entire Solidity contract (_**hbarToAndFromContract.sol**_) are shown in the tabs below.

The Solidity file has functions for getting HBAR to the contract (_**receive**_, _**fallback**_, _**tokenAssociate**_), getting HBAR from the contract (_**transferHbar**_, _**sendHbar**_, _**callHbar**_), and checking the HBAR balance of the contract (_**getBalance**_).

This portion of _**index.js**_ configures and creates the accounts, deploys the contract, and stores the HTS token ID. The functions _**accountCreatorFcn**_ and _**contractDeployFcn**_ create new accounts and deploy the contract to the network, respectively. These functions simplify the account creation and contract deployment process and are reusable in case you need them in the future. This modular approach is used throughout the tutorial.

{% tabs %}
{% tab title="Index.Js" %}
```javascript
// Configure accounts and client
const operatorId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorKey = PrivateKey.fromString(process.env.OPERATOR_PVKEY);
const client = Client.forTestnet().setOperator(operatorId, operatorKey);

async function main() {
  // Create other necessary accounts
  console.log(`\n- Creating accounts...`);
  const initBalance = 100;
  const treasuryKey = PrivateKey.generateED25519();
  const [treasuryAccSt, treasuryId] = await accountCreatorFcn(
    treasuryKey,
    initBalance
  );
  console.log(
    `- Created Treasury account ${treasuryId} that has a balance of ${initBalance} ℏ`
  );

  const aliceKey = PrivateKey.generateED25519();
  const [aliceAccSt, aliceId] = await accountCreatorFcn(aliceKey, initBalance);
  console.log(
    `- Created Alice's account ${aliceId} that has a balance of ${initBalance} ℏ`
  );

  // Import the compiled contract bytecode
  const contractBytecode = fs.readFileSync("hbarToAndFromContract.bin");

  // Deploy the smart contract on Hedera
  console.log(`\n- Deploying contract...`);
  let gasLimit = 100000;

  const [contractId, contractAddress] = await contractDeployFcn(
    contractBytecode,
    gasLimit
  );
  console.log(`- The smart contract ID is: ${contractId}`);
  console.log(
    `- The smart contract ID in Solidity format is: ${contractAddress}`
  );

  const tokenId = AccountId.fromString("0.0.47931765");
  console.log(`\n- Token ID (for association with contract later): ${tokenId}`);
}

main();
```
{% endtab %}

{% tab title="HbarToAndFromContract.Sol" %}
```javascript
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

// Compile with remix for remote imports to work - otherwise keep precompiles locally
import "https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/HederaTokenService.sol";
import "https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/HederaResponseCodes.sol";
 
contract hbarToAndFromContract is HederaTokenService{
    //============================================ 
    // GETTING HBAR TO THE CONTRACT
    //============================================ 
    receive() external payable {}

    fallback() external payable {}

    function tokenAssociate(address _account, address _htsToken) payable external {
        require(msg.value > 2000000000,"Send more HBAR");
        
        int response = HederaTokenService.associateToken(_account, _htsToken);
        if (response != HederaResponseCodes.SUCCESS) {
            revert ("Token association failed");
        }
    }
        
    //============================================ 
    // GETTING HBAR FROM THE CONTRACT
    //============================================ 
    function transferHbar(address payable _receiverAddress, uint _amount) public {
        _receiverAddress.transfer(_amount);
    }

    function sendHbar(address payable _receiverAddress, uint _amount) public {
        require(_receiverAddress.send(_amount), "Failed to send Hbar");
    }

    function callHbar(address payable _receiverAddress, uint _amount) public {
        (bool sent, ) = _receiverAddress.call{value:_amount}("");
        require(sent, "Failed to send Hbar");
    }
    
    //============================================ 
    // CHECKING THE HBAR BALANCE OF THE CONTRACT
    //============================================ 
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```
{% endtab %}
{% endtabs %}

These helper functions in _**index.js**_ use the [**AccountCreateTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/create-an-account) and [**ContractCreateFlow()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/create-a-smart-contract#contractcreateflow) classes of the Hedera SDK. [**ContractCreateFlow()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/create-a-smart-contract#contractcreateflow) stores the bytecode and deploys the contract on Hedera. This single call handles for you the operations [**FileCreateTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/file-storage/create-a-file), [**FileAppendTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/file-storage/append-to-a-file), and [**ContractCreateTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/create-a-smart-contract#contractcreatetransaction).

**Helper Functions:**

{% tabs %}
{% tab title="AccountCreatorFcn" %}
```javascript
async function accountCreatorFcn(pvKey, iBal) {
    const response = await new AccountCreateTransaction()
        .setInitialBalance(new Hbar(iBal))
        .setKey(pvKey.publicKey)
        .execute(client);
    const receipt = await response.getReceipt(client);
    return [receipt.status, receipt.accountId];
}
```
{% endtab %}

{% tab title="ContractDeployFcn" %}
```javascript
async function contractDeployFcn(bytecode, gasLim) {
    const contractCreateTx = new ContractCreateFlow().setBytecode(bytecode).setGas(gasLim);
    const contractCreateSubmit = await contractCreateTx.execute(client);
    const contractCreateRx = await contractCreateSubmit.getReceipt(client);
    const contractId = contractCreateRx.contractId;
    const contractAddress = contractId.toSolidityAddress();
    return [contractId, contractAddress];
}
```
{% endtab %}
{% endtabs %}

<details>

<summary>Console Output ✅</summary>

* _Creating accounts..._
* _Created Treasury account 0.0.47938602 that has a balance of 100 ℏ_
* _Created Alice's account 0.0.47938603 that has a balance of 100 ℏ_
* _Deploying contract..._
* _The smart contract ID is: 0.0.47938605_
* _The smart contract ID in Solidity format is: 0000000000000000000000000000000002db7c2d_
* _Token ID (for association with contract later): 0.0.47931765_

</details>

***

## **Getting HBAR to the Contract**

### **The **_**receive**_**/**_**fallback**_** Functions**

In this scenario, you (Operator) transfer 10 HBAR to the contract by triggering either the _**receive**_ or _**fallback**_ functions of the contract. As described in this [Solidity by Example](https://solidity-by-example.org/sending-ether/) page, the _**receive**_ function is called when _**msg.data**_ is empty, otherwise the _**fallback**_ function is called.

In this case, the helper function _**contractExecuteNoFcn**_ pays HBAR to the contract by using [**ContractExecuteTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/call-a-smart-contract-function) and specifying a _**.setPayableAmount()**_ without calling any specific contract function – thus triggering _**fallback**_. Note from the Solidity code that _**receive**_ and _**fallback**_ are _**external**_ and _**payable**_ functions.

The helper function _**contractCallQueryFcn**_ checks the HBAR balance of the contract by calling the _**getBalance**_ function of the contract – this call is done using [**ContractCallQuery()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/call-a-smart-contract-function-1).

```javascript
	console.log(`
====================================================
GETTING HBAR TO THE CONTRACT
====================================================`);

	// Transfer HBAR to the contract using .setPayableAmount WITHOUT specifying a function (fallback/receive triggered)
	let payableAmt = 10;
	console.log(`- Caller (Operator) PAYS ${payableAmt} ℏ to contract (fallback/receive)...`);
	const toContractRx = await contractExecuteNoFcn(contractId, gasLimit, payableAmt);

	// Get contract HBAR balance by calling the getBalance function in the contract AND/OR using ContractInfoQuery in the SDK
	await contractCallQueryFcn(contractId, gasLimit, "getBalance"); // Outputs the contract balance in the console
```

**Helper Functions:**

{% tabs %}
{% tab title="ContractExecuteNoFcn" %}
```javascript
async function contractExecuteNoFcn(cId, gasLim, amountHbar) {
    const contractExecuteTx = new ContractExecuteTransaction()
        .setContractId(cId)
        .setGas(gasLim)
        .setPayableAmount(amountHbar);
    const contractExecuteSubmit = await contractExecuteTx.execute(client);
    const contractExecuteRx = await contractExecuteSubmit.getReceipt(client);
    return contractExecuteRx;
}
```
{% endtab %}

{% tab title="ContractCallQueryFcn" %}
```javascript
async function contractCallQueryFcn(cId, gasLim, fcnName) {
    const contractQueryTx = new ContractCallQuery()
        .setContractId(cId)
        .setGas(gasLim)
        .setFunction(fcnName);
    const contractQuerySubmit = await contractQueryTx.execute(client);
    const contractQueryResult = contractQuerySubmit.getUint256(0);
    console.log(`- Contract balance (getBalance fcn): ${contractQueryResult * 1e-8} ℏ`);
}
```
{% endtab %}
{% endtabs %}

<details>

<summary>Console Output ✅</summary>

_====================================================_

_GETTING HBAR TO THE CONTRACT_

_====================================================_

* _Caller (Operator) PAYS 10 ℏ to contract (fallback/receive)..._
* _Contract balance (getBalance fcn): 10 ℏ_

</details>

### **Executing a Payable Function**

Now, you (Operator) transfer 21 HBAR to the contract by calling a specific contract function (_**tokenAssociate**_) that is _**payable**_ using the [**ContractExecuteTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/smart-contracts/call-a-smart-contract-function) class and specifying a _**.setPayableAmount()**_. This is done with the helper function _**contractExecuteFcn**_.

In this scenario, _**contractParamsBuilderFcn**_ is used to build the parameters that will be passed to the contract function – that is, the contract and token IDs which are then converted to Solidity addresses.

From the Solidity code, note that the _**tokenAssociate**_ function associates the contract to the HTS token from the first step, and requires more than 20 HBAR to execute (just for fun).

```javascript
	// Transfer HBAR to the contract using .setPayableAmount SPECIFYING a contract function (tokenAssociate)
	payableAmt = 21;
	gasLimit = 800000;
	console.log(`\n- Caller (Operator) PAYS ${payableAmt} ℏ to contract (payable function)...`);
	const Params = await contractParamsBuilderFcn(contractId, [], 2, tokenId);
	const Rx = await contractExecuteFcn(contractId, gasLimit, "tokenAssociate", Params, payableAmt);

	gasLimit = 50000;
	await contractCallQueryFcn(contractId, gasLimit, "getBalance"); // Outputs the contract balance in the console
```

{% tabs %}
{% tab title="ContractParamsBuilderFcn" %}
```javascript
async function contractParamsBuilderFcn(aId, amountHbar, section, tId) {
    let builtParams = [];
    if (section === 2) {
        builtParams = new ContractFunctionParameters()
            .addAddress(aId.toSolidityAddress())
            .addAddress(tId.toSolidityAddress());
    } else if (section === 3) {
        builtParams = new ContractFunctionParameters()
            .addAddress(aId.toSolidityAddress())
            .addUint256(amountHbar * 1e8);
    } else {
    }
    return builtParams;
}
```
{% endtab %}

{% tab title="ContractExecuteFcn" %}
```javascript
async function contractExecuteFcn(cId, gasLim, fcnName, params, amountHbar) {
    const contractExecuteTx = new ContractExecuteTransaction()
        .setContractId(cId)
        .setGas(gasLim)
        .setFunction(fcnName, params)
        .setPayableAmount(amountHbar);
    const contractExecuteSubmit = await contractExecuteTx.execute(client);
    const contractExecuteRx = await contractExecuteSubmit.getReceipt(client);
    return contractExecuteRx;
}
```
{% endtab %}
{% endtabs %}

<details>

<summary>Console Output ✅</summary>

* _Caller (Operator) PAYS 21 ℏ to contract (payable function)..._
* _Contract balance (getBalance fcn): 31 ℏ_

</details>

### **Using **_**TransferTransaction**_** in the SDK**

Lastly in this scenario, the Treasury transfers 30 HBAR to the contract using [**TransferTransaction()**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/transfer-cryptocurrency)**.** This is done with the helper function _**hbar2ContractSdkFcn**_. This scenario is just a quick recap and reminder of [Part 1 of the series](https://hedera.com/blog/how-to-send-and-receive-hbar-using-smart-contracts-part-1-using-the-sdk), so be sure to give that a read for more details.

```javascript
	// Transfer HBAR from the Treasury to the contract deployed using the SDK
	let moveAmt = 30;
	const transferSdkRx = await hbar2ContractSdkFcn(treasuryId, contractId, moveAmt, treasuryKey);
	console.log(`\n- ${moveAmt} ℏ from Treasury to contract (via SDK): ${transferSdkRx.status}`);

	await contractCallQueryFcn(contractId, gasLimit, "getBalance"); // Outputs the contract balance in the console
```

**Helper Functions:**

```javascript
async function hbar2ContractSdkFcn(sender, receiver, amount, pKey) {
    const transferTx = new TransferTransaction()
        .addHbarTransfer(sender, -amount)
        .addHbarTransfer(receiver, amount)
        .freezeWith(client);
    const transferSign = await transferTx.sign(pKey);
    const transferSubmit = await transferSign.execute(client);
    const transferRx = await transferSubmit.getReceipt(client);
    return transferRx;
}
```

<details>

<summary><strong>Console Output ✅</strong></summary>

* _30 ℏ from Treasury to contract (via SDK): SUCCESS_
* _Contract balance (getBalance fcn): 61 ℏ_

</details>

***

## **Getting HBAR from the Contract**

In this section the contract transfers HBAR to Alice using three different methods: _**transfer**_, _**send**_, _**call**_. Each transfer is of 20 HBAR, so by the end the contract should have 1 HBAR left in its balance.

This tutorial focuses on implementation. For additional background and details of these Solidity methods, check out [Solidity by Example](https://solidity-by-example.org/sending-ether/) and [this external article](https://medium.com/daox/three-methods-to-transfer-funds-in-ethereum-by-means-of-solidity-5719944ed6e9) – just remember that on Hedera, the native cryptocurrency transacted is HBAR, not ETH. One thing worth noting from those resources is that _**call**_ is currently the recommended method to use.

### **Contract Transfers HBAR to Alice**

The helper function _**contractExecuteFcn**_ executes the _**transferHbar**_ function of the contract. The helper function _**contractParamsBuilderFcn**_ now builds the contract function parameters from the receiver ID (Alice’s) and the amount of HBAR to be sent. Also note from the previous section that the contract function is executed with a _**gasLimit**_ of only 50,000 gas.

```javascript
	console.log(`
====================================================
GETTING HBAR FROM THE CONTRACT
====================================================`);

	payableAmt = 0;
	moveAmt = 20;

	console.log(`- Contract TRANSFERS ${moveAmt} ℏ to Alice...`);
	const tParams = await contractParamsBuilderFcn(aliceId, moveAmt, 3, []);
	const tRx = await contractExecuteFcn(contractId, gasLimit, "transferHbar", tParams, payableAmt);

	// Get contract HBAR balance by calling the getBalance function in the contract AND/OR using ContractInfoQuery in the SDK
	await showContractBalanceFcn(contractId); // Outputs the contract balance in the console
```

**Helper Functions:**

```javascript
async function showContractBalanceFcn(cId) {
    const info = await new ContractInfoQuery().setContractId(cId).execute(client);
    console.log(`- Contract balance (ContractInfoQuery SDK): ${info.balance.toString()}`);

```

<details>

<summary><strong>Console Output ✅</strong></summary>

_====================================================_

_GETTING HBAR FROM THE CONTRACT_

_====================================================_

* _Contract TRANSFERS 20 ℏ to Alice..._
* _Contract balance (ContractInfoQuery SDK): 41 ℏ_

</details>

### **Contract Sends HBAR to Alice**

The same helper function from before now executes the _**sendHbar**_ function of the contract.

```javascript
	console.log(`\n- Contract SENDS ${moveAmt} ℏ to Alice...`);
	const sParams = await contractParamsBuilderFcn(aliceId, moveAmt, 3, []);
	const sRx = await contractExecuteFcn(contractId, gasLimit, "sendHbar", sParams, payableAmt);

	await showContractBalanceFcn(contractId); // Outputs the contract balance in the console
```

<details>

<summary><strong>Console Output ✅</strong></summary>

* _Contract SENDS 20 ℏ to Alice..._
* _Contract balance (ContractInfoQuery SDK): 21 ℏ_

</details>

### **Contract Calls HBAR to Alice**

Just like above, the helper function _**contractExecuteFcn**_ executes the _**sendHbar**_ function of the contract.

Examine the transaction history for the contract and the operator in the mirror node explorer, [HashScan](https://hashscan.io/#/mainnet/dashboard). You can also obtain additional information of interest using the [mirror node REST API](https://docs.hedera.com/hedera/sdks-and-apis/rest-api). Additional context for that API is provided in [this blog post](https://hedera.com/blog/how-to-look-up-transaction-history-on-hedera-using-mirror-nodes-back-to-the-basics).

The last step is to [**join the Hedera Developer Discord!**](https://hedera.com/discord)

```javascript
	console.log(`\n- Contract CALLS ${moveAmt} ℏ to Alice...`);
	const cParams = await contractParamsBuilderFcn(aliceId, moveAmt, 3, []);
	const cRx = await contractExecuteFcn(contractId, gasLimit, "callHbar", cParams, payableAmt);

	await showContractBalanceFcn(contractId); // Outputs the contract balance in the console

	console.log(`\n- SEE THE TRANSACTION HISTORY IN HASHSCAN (FOR CONTRACT AND OPERATOR): 
https://hashscan.io/#/testnet/contract/${contractId}
https://hashscan.io/#/testnet/account/${operatorId}`);

	console.log(`
====================================================
 THE END - NOW JOIN: https://hedera.com/discord
====================================================\n`);
}
```

<details>

<summary><strong>Console Output ✅</strong></summary>

* _Contract CALLS 20 ℏ to Alice..._
* _Contract balance (ContractInfoQuery SDK): 1 ℏ_
* _SEE THE TRANSACTION HISTORY IN HASHSCAN (FOR CONTRACT AND OPERATOR):_

[_https://hashscan.io/#/testnet/contract/0.0.47938605_](https://hashscan.io/#/testnet/contract/0.0.47938605)[_https://hashscan.io/#/testnet/account/0.0.2520793_](https://hashscan.io/#/testnet/account/0.0.2520793)

</details>

***

## **Summary**

If you run the entire example successfully, your console should look something like:

<figure><img src="https://images.hedera.com/2022-How-to-Send-and-Receive-HBAR-Using-Smart-Contracts-2-Image-1_2022-08-19-160324_onge.png?w=1395&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1676318312&#x26;s=5df20c93e5b39a04550b023da6acb3ee" alt=""><figcaption></figcaption></figure>

This tutorial used the Hedera JavaScript SDK. However, you can try this with the other officially supported [SDKs](../../sdks-and-apis/sdks/) for Java and Go.

**Congratulations! 🎉 Now you know how to send HBAR to and from a contract on Hedera using both the SDK and Solidity! Feel free to reach out in** [**Discord**](https://hedera.com/discord) **if you have any questions!**

***

## Additional Resources

**➡** [**Project Repository**](https://github.com/ed-marquez/hedera-smart-contracts/tree/examples/examples/transfer-hbar2contracts-solidity)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Ed, DevRel Engineer</p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/ed-marquez/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ed-marquez/">https://www.linkedin.com/in/ed-marquez/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://github.com/theekrystallee">https://github.com/theekrystallee</a></td></tr></tbody></table>

a
