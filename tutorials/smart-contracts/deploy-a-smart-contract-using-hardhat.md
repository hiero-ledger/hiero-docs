# Deploy a Smart Contract Using Hardhat

In this tutorial, you'll be guided through setting up a Hardhat project and deploying a Hedera smart contract to the **Hedera Testnet** using the [**Hashio**](https://swirldslabs.com/hashio/) JSON-RPC instance.

**Hardhat** is a development environment for Ethereum smart contracts. It consists of different components for editing, compiling, debugging, and deploying your smart contracts and dApps, all working together to create a complete development environment. By the end of this tutorial, you'll have learned how to deploy smart contracts using Hardhat on the Hedera Testnet.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>1.</strong> <a href="deploy-a-smart-contract-using-hardhat.md#prerequisites"><strong>PREREQUISITES</strong></a></td><td><a href="deploy-a-smart-contract-using-hardhat.md#prerequisites">#prerequisites</a></td></tr><tr><td align="center"><strong>2.</strong> <a href="deploy-a-smart-contract-using-hardhat.md#hardhat-project-setup"><strong>HARDHAT SETUP</strong></a></td><td><a href="deploy-a-smart-contract-using-hardhat.md#hardhat-project-setup">#hardhat-project-setup</a></td></tr><tr><td align="center"><strong>3.</strong> <a href="deploy-a-smart-contract-using-hardhat.md#project-contents"><strong>PROJECT CONTENTS</strong></a></td><td><a href="deploy-a-smart-contract-using-hardhat.md#project-contents">#project-contents</a></td></tr><tr><td align="center"><strong>4.</strong> <a href="deploy-a-smart-contract-using-hardhat.md#test-and-deploy"><strong>DEPLOY CONTRACT</strong></a></td><td><a href="deploy-a-smart-contract-using-hardhat.md#test-and-deploy-contract">#test-and-deploy-contract</a></td></tr></tbody></table>

## Prerequisites

* Basic understanding of smart contracts.
* Basic understanding of Node.js or Javascript.
* Basic understanding of the [Hedera JSON RPC Relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md).
* Basic understanding of [Hardhat Ethereum Development Tool](https://hardhat.org/hardhat-runner/docs/guides/project-setup).

## Hardhat Project Setup

To make the setup process simple, you'll use a pre-configured Hardhat project from the `hedera-hardhat-example-project` [repository](https://github.com/hashgraph/hedera-hardhat-example-project).

Open a terminal window and navigate to your preferred directory where your Hardhat project will live. Run the following command to clone the repo and install dependencies to your local machine:

```bash
git clone https://github.com/hashgraph/hedera-hardhat-example-project.git
cd hedera-hardhat-example-project
npm install
```

The `dotenv` package is used to manage environment variables in a separate `.env` file, which is loaded at runtime. This helps protect sensitive information like your private keys and API secrets, but it's still best practice to add `.env` to your `.gitignore` to prevent you from pushing your credentials to GitHub.

## Project Configuration

In this step, you will update and configure the Hardhat configuration file that defines tasks, stores Hedera account private key information, and Hashio Testnet RPC URL. First, rename the `.env.example` file to `.env`. and update the `.env` and `hardhat.config.js` files with the following code.

But first, in order to deploy the contract to the _Hedera Testnet_, you will need to get a testnet account and key. To get a testnet account, create an _ECDSA_ account using the [**Hedera Developer Portal**](https://portal.hedera.com/). Once you have your ECDSA account and HEX encoded private key, add the private key to the `TESTNET_OPERATOR_PRIVATE_KEY` variable in the `.env` file.

<details>

<summary>Create your testnet account 🛠️</summary>

#### Create Hedera Portal Profile (Faucet)

The _Hedera Testnet_ account allows you to interact with our [APIs](../../sdks-and-apis/) and pay for the transaction fees. Visit the [Hedera portal](https://portal.hedera.com/register) to create your _Hedera Testnet_ account and follow the instructions.

<img src="../../.gitbook/assets/portal testnet account.png" alt="" data-size="original">

Once you have completed the instructions, you will receive a _Hedera Testnet_ **account ID** (0.0.x) and your **private/public key pair** on your testnet page. You will need to copy over your ECDSA _**HEX Encoded private key**_ and paste it in the `TESTNET_OPERATOR_PRIVATE_KEY` .env file variable.

<img src="../../.gitbook/assets/new portal 2 (1).png" alt="" data-size="original">

_**Note:** Your Hedera Testnet account will be credited with 10,000 test **HBAR** upon creation that can only be utilized on the Hedera test network. Your balance will be topped up daily to 10,000 test **HBAR** when you use your funds._

</details>

#### Environment Variables

The `.env` file defines environment variables used in the Hardhat configuration file. The `TESTNET_OPERATOR_PRIVATE_KEY` variable contains the _ECDSA_ _**Hex Encoded Private Key**_ for the Hedera Testnet account used in the `testnet` network Hardhat configuration.

The `TESTNET_ENDPOINT` variable contains the [HashIO](https://swirldslabs.com/hashio/) Testnet endpoint URL. This is the JSON-RPC instance that will submit the transactions to the Hedera test network to test, create and deploy your smart contract.

<pre class="language-bash"><code class="lang-bash"><strong># operator/receiver keys referenced in the hardhat.config account variable
</strong>TESTNET_OPERATOR_PRIVATE_KEY=0xb46751179bc8aa9e129d34463e46cd924055112eb30b31637b5081b56ad96129

# testnet endpoint referenced in the hardhat.config url variable
TESTNET_ENDPOINT='https://testnet.hashio.io/api'
</code></pre>

#### Hardhat Configuration

The `hardhat.config.js` file defines tasks for Hardhat, including `show-balance`, `transfer-hbars`, `deploy-contract`, `contract-view-call`, and `contract-call`. It exports a configuration object that includes the Solidity version and settings, default network, and network settings for the `testnet` network.

The `url` property is set to the `TESTNET_ENDPOINT` environment variable, and `accounts` to an array containing the testnet private key imported from the `.env` file.

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("@nomicfoundation/hardhat-chai-matchers");
require("@nomiclabs/hardhat-ethers");
//import dotenv library to access environment variables stored in .env file
require("dotenv").config();

//define hardhat task here, which can be accessed in our test file (test/rpc.js) by using hre.run('taskName')
task("show-balance", async () => {
  const showBalance = require("./scripts/showBalance");
  return showBalance();
});

task("deploy-contract", async () => {
  const deployContract = require("./scripts/deployContract");
  return deployContract();
});

task("contract-view-call", async (taskArgs) => {
  const contractViewCall = require("./scripts/contractViewCall");
  return contractViewCall(taskArgs.contractAddress);
});

task("contract-call", async (taskArgs) => {
  const contractCall = require("./scripts/contractCall");
  return contractCall(taskArgs.contractAddress, taskArgs.msg);
});

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  mocha: {
    timeout: 3600000,
  },
  solidity: {
    version: "0.8.9",
    settings: {
      optimizer: {
        enabled: true,
        runs: 500,
      },
    },
  },
  //this specifies which network should be used when running Hardhat tasks
  defaultNetwork: "testnet",
  networks: {
    testnet: {
      //HashIO testnet endpoint from the TESTNET_ENDPOINT variable in the project .env the file
      url: process.env.TESTNET_ENDPOINT,
      //the Hedera testnet account ECDSA private
      //the public address for the account is derived from the private key
      accounts: [
        process.env.TESTNET_OPERATOR_PRIVATE_KEY,
      ],
    },
  },
};
```

## Project Contents

In this step, you'll look at the descriptions of the Hardhat project contents. For more information regarding Hardhat projects, check out the [Hardhat docs](https://hardhat.org/hardhat-runner/docs/guides/project-setup). If you do not need to review the project contents you can skip to "[Test and Deploy](deploy-a-smart-contract-using-hardhat.md#test-and-deploy)."

#### contracts/

The `contracts/` folder contains the source file for the Greeter smart contract.\
\
Let's review the `Greeter.sol` smart contract in the `hedera-example-hardhat-project/contracts` folder. At the top of the file, the `SPDX-License-Identifier` defines the license, in this case, the MIT license. The `pragma solidity ^0.8.9;` line specifies the Solidity compiler version to use. These two lines are crucial for proper licensing and compatibility.

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract Greeter {
    string private greeting;

    event GreetingSet(string greeting);

    //This constructor assigns the initial greeting and emit GreetingSet event
    constructor(string memory _greeting) {
        greeting = _greeting;

        emit GreetingSet(_greeting);
    }

    //This function returns the current value stored in the greeting variable
    function greet() public view returns (string memory) {
        return greeting;
    }

    //This function sets the new greeting msg from the one passed down as parameter and emit event
    function setGreeting(string memory _greeting) public {
        greeting = _greeting;

        emit GreetingSet(_greeting);
    }
}
```

> _NOTE: The pragma solidity line must match the version of Solidity defined in the_ [_module exports_](https://github.com/hashgraph/hedera-hardhat-example-project/blob/main/hardhat.config.js#L34) _of your `hardhat.config.js` file._

#### scripts/

The `scripts/` folder contains the automation scripts for the test file. This project contains 4 scripts.

The `scripts/` folder contains test scripts for locally testing a smart contract before deploying it. Please read the comments to help you understand the code and its purpose:

{% tabs %}
{% tab title="contractCall.js" %}
Calls the `setGreeting` function from the Greeter contract and sets the greeter message to "Greeter."\\

```javascript
const { ethers } = require('hardhat');

//This function accepts two parameters - address and msg
//Retrieves the contract from the address and set new greeting
module.exports = async (address, msg) => {

  //Assign the first signer, which comes from the first privateKey from our configuration in hardhat.config.js, to a wallet variable. 
  const wallet = (await ethers.getSigners())[0];

  //Assign the greeter contract object in a variable, this is used for already deployed contract, which we have the address for. ethers.getContractAt accepts:
  //name of contract as first parameter
  //address of our contract
  //wallet/signer used for signing the contract calls/transactions with this contract 
  const greeter = await ethers.getContractAt('Greeter', address, wallet);

  //using the greeter object(which is our contract) we can call functions from the contract. In this case we call setGreeting with our new msg
  const updateTx = await greeter.setGreeting(msg);

  console.log(`Updated call result: ${msg}`);

  return updateTx;
};
```
{% endtab %}

{% tab title="contractViewCall.js" %}
Returns the current greeter message value stored with the Greeter contract.\\

```javascript
const { ethers } = require("hardhat");

module.exports = async (address) => {
  //Assign the first signer, which comes from the first privateKey from our configuration in hardhat.config.js, to a wallet variable.
  const wallet = (await ethers.getSigners())[0];

  //Assign the greeter contract object in a variable, this is used for already deployed contract, which we have the address for. ethers.getContractAt accepts:
  //name of contract as first parameter
  //address of our contract
  //wallet/signer used for signing the contract calls/transactions with this contract
  const greeter = await hre.ethers.getContractAt("Greeter", address, wallet);
  //using the greeter object(which is our contract) we can call functions from the contract. In this case we call greet which returns our greeting msg
  const callRes = await greeter.greet();

  console.log(`Contract call result: ${callRes}`);

  return callRes;
};
```
{% endtab %}

{% tab title="deployContract.js" %}
Deploys the Greeter contract and returns the contract public address. \\

```javascript
const { ethers } = require("hardhat");

module.exports = async () => {
  //Assign the first signer, which comes from the first privateKey from our configuration in hardhat.config.js, to a wallet variable.
  let wallet = (await ethers.getSigners())[0];

  //Initialize a contract factory object
  //name of contract as first parameter
  //wallet/signer used for signing the contract calls/transactions with this contract
  const Greeter = await ethers.getContractFactory("Greeter", wallet);
  //Using already intilized contract facotry object with our contract, we can invoke deploy function to deploy the contract.
  //Accepts constructor parameters from our contract
  const greeter = await Greeter.deploy("initial_msg");
  //We use wait to recieve the transaction (deployment) receipt, which contains contractAddress
  const contractAddress = (await greeter.deployTransaction.wait())
    .contractAddress;

  console.log(`Greeter deployed to: ${contractAddress}`);

  return contractAddress;
};
```
{% endtab %}

{% tab title="showBalance.js" %}
Returns the balance of the specified wallet address (account) in tinybars. Tinybars are the unit in which Hedera accounts hold HBAR balances.\\

```javascript
const { ethers } = require("hardhat");

module.exports = async () => {
  //Assign the first signer, which comes from the first privateKey from our configuration in hardhat.config.js, to a wallet variable.
  const wallet = (await ethers.getSigners())[0];
  //Wallet object (which is essentially signer object) has some built in functionality like getBalance, getAddress and more
  const balance = (await wallet.getBalance()).toString();
  console.log(`The address ${wallet.address} has ${balance} weibars`);

  return balance;
};
```
{% endtab %}
{% endtabs %}

#### test/

The `test/` folder contains the test files for the project.\
\
The `rpc.js` file is located in the `test` folder of the `hedera-example-hardhat-project` project and references the Hardhat [tasks](https://github.com/hashgraph/hedera-hardhat-example-project/blob/main/hardhat.config.js#L7) that are defined in the hardhat.config file. When the command `npx hardhat test` is run, the program executes the `rpc.js` file.

```javascript
const hre = require("hardhat");
const { expect } = require("chai");

describe("RPC", function () {
  let contractAddress;
  let signers;

  before(async function () {
    signers = await hre.ethers.getSigners();
  });

  it("should be able to get the account balance", async function () {
    const balance = await hre.run("show-balance");
    expect(Number(balance)).to.be.greaterThan(0);
  });

  it("should be able to deploy a contract", async function () {
    contractAddress = await hre.run("deploy-contract");
    expect(contractAddress).to.not.be.null;
  });

  it("should be able to make a contract view call", async function () {
    const res = await hre.run("contract-view-call", { contractAddress });
    expect(res).to.be.equal("initial_msg");
  });

  it("should be able to make a contract call", async function () {
    const msg = "updated_msg";
    await hre.run("contract-call", { contractAddress, msg });
    const res = await hre.run("contract-view-call", { contractAddress });
    expect(res).to.be.equal(msg);
  });
});
```

#### .env

A file that stores your environment variables like your accounts, private keys, and references to Hedera network ([see previous step](deploy-a-smart-contract-using-hardhat.md#environment-variables)).

#### hardhat.config.js

The Hardhat project configuration file. This file includes information about the Hedera network RPC URLs, accounts, and tasks defined ([see previous step](deploy-a-smart-contract-using-hardhat.md#hardhat-configuration)).

## Test and Deploy

Now that you have your project set up and configured, let's deploy the `Greeter.sol` smart contract to the Hedera Testnet using [Hash](https://swirldslabs.com/hashio/)[io](https://swirldslabs.com/hashio/). Hashio is an instance of the [Hedera JSON-RPC relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md) hosted by [Swirlds Labs](https://swirldslabs.com/) and provides convenient access to the Hedera network for transactions and data querying. You can use any JSON-RPC instance supported by the community.

Run the following command in your terminal to run the `hedera-hardhat-example-project/test/rpc.js` test file on the Hedera testnet.

```bash
npx hardhat test
```

Tests should pass with "<mark style="color:green;">**4 passing**</mark>" returned to the console. Otherwise, an error message will appear indicating the issue.

<details>

<summary>console check ✅</summary>

```shell
  RPC
The address 0xe261e26aECcE52b3788Fac9625896FFbc6bb4424 has 99999999999999991611392 weibars
    ✔ should be able to get the account balance (1127ms)
Greeter deployed to: 0xEc3D74D360a53Fe7104Be6aB4e25e27a90bF6aE4
    ✔ should be able to deploy a contract (11810ms)
Contract call result: initial_msg
    ✔ should be able to make a contract view call (265ms)
Updated call result: updated_msg
Contract call result: updated_msg
    ✔ should be able to make a contract call (4068ms)


  4 passing (34s)
```

</details>

Lastly, run the following command to deploy the contract to the Hedera Testnet:

```bash
npx hardhat deploy-contract
```

<details>

<summary>console check ✅</summary>

```bash
Greeter deployed to: 0x157B93c04a294AbD88cF608672059814b3ea38aE
```

</details>

You've successfully deployed your contract to the Hedera Testnet! You can view the contract you deployed by searching the smart contract _public_ address in a supported [Hedera Network Explorer](../../networks/community-mirror-nodes.md). For this example, we will use the [HashScan](https://hashscan.io/mainnet/dashboard) Network Explorer. Copy and paste your deployed `Greeter.sol` public contract address into the HashScan search bar.

The Network Explorer will return the information about the contract created and deployed to the Hedera Testnet. The "EVM Address" field is the public address of the contract that was returned to you in your terminal. The terminal returned the public address with the "0x" hex encoding appended to the public address. You will also notice a contract ID in `0.0.contractNumber` (`0.0.3478001`) format. This is the _contract ID_ used to reference the contract entity in the Hedera Network.

> _**Note:** At the top of the explorer page, remember to switch the network to **TESTNET** before you search for the contract._

<figure><img src="../../.gitbook/assets/new hashscan (2).png" alt=""><figcaption><p>Hashscan transaction</p></figcaption></figure>

#### **Congratulations! 🎉 You have successfully learned how to deploy a smart contract using Hardhat.** Feel free to reach out if you have any questions:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr><tr><td align="center"><p>Editor: Simi, Sr. Software Manager</p><p><a href="https://github.com/SimiHunjan">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/shunjan/">https://www.linkedin.com/in/shunjan/</a></td></tr></tbody></table>
