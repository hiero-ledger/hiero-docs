---
description: >-
  A step-by-step guide on deploying a smart contract to Hedera testnet and
  Hedera Local Node using Hardhat.
---

# Deploy a Smart Contract Using Hardhat and Hedera JSON-RPC Relay

In this tutorial, you will walk through the step-by-step guide on deploying [smart contracts](../../support-and-community/glossary.md#smart-contract) using Hardhat and Hedera JSON-RPC Relay. [**Hardhat**](https://hardhat.org/) is a development environment for Ethereum. It consists of different components for editing, compiling, debugging, and deploying smart contracts and dApps, all working together to create a complete development environment.&#x20;

The [**Hedera JSON-RPC Relay**](https://github.com/hashgraph/hedera-json-rpc-relay) is an implementation of Ethereum JSON-RPC APIs for Hedera and utilizes both Hedera Consensus Nodes and Mirror Nodes to support RPC queries defined in the [JSON-RPC Specification](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/hashgraph/hedera-json-rpc-relay/main/docs/openrpc.json\&uiSchema%5BappBar%5D%5Bui:splitView%5D=false\&uiSchema%5BappBar%5D%5Bui:input%5D=false\&uiSchema%5BappBar%5D%5Bui:examplesDropdown%5D=false). The [**Hedera Local Node**](https://github.com/hashgraph/hedera-local-node) project enables developers to establish their own local network for development and testing. The local network comprises the consensus node, mirror node, JSON-RPC relay, and other Hedera products, and can be set up using the CLI tool and Docker. This setup allows you to seamlessly build and deploy smart contracts from your local environment.

By the end of this tutorial, you'll be equipped to deploy smart contracts on the Hedera Testnet or your local Hedera node, leveraging Hardhat's tools for testing, compiling, and deploying.

***

## Prerequisites

* Basic understanding of smart contracts.
* Basic understanding of [Node.js](https://nodejs.org/en/download) and JavaScript.
* Basic understanding of [Hardhat Ethereum Development Tool](https://hardhat.org/hardhat-runner/docs/guides/project-setup).

***

## Table of Contents&#x20;

1. [Project Setup](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#step-1-set-up-project)
2. [Project Configuration](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#step-2-configure-project)
   1. [Environment Variables](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#environment-variables)
   2. [Hardhat Config File](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#hardhat-configuration-file)
3. [Compile Smart Contract](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#step-3-compile-contract)
4. [Deploy Smart Contract](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#step-4-test-and-deploy-contract)
   1. [Next Steps](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#next-steps)
5. [Additional Resources](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#additional-resources)

***

## Step 1: Set Up Project

To simplify the setup process, you can clone a boilerplate Hardhat example project from the `hedera-hardhat-example-project` [repository](https://github.com/hashgraph/hedera-hardhat-example-project). Open a terminal window and navigate to your preferred directory where your Hardhat project will be stored.&#x20;

Run the following command to clone the repo, change into the directory, and install dependencies:

```bash
git clone https://github.com/hashgraph/hedera-hardhat-example-project.git
cd hedera-hardhat-example-project
npm install
```

Open the project in Visual Studio Code or your IDE of choice. The project structure of the repo you just cloned should look like this:

```
hedera-hardhat-example-project
├── node_modules
├── contracts
├── scripts
├── test
├── .env.example
├── .gitignore
├── hardhat.config.js
├── package-lock.json
├── package.json
└── README.md
```

Let's review the Hardhat project folders/content. For more information regarding Hardhat projects, check out the [Hardhat docs](https://hardhat.org/hardhat-runner/docs/guides/project-setup). If you do not need to review the project contents, you can skip this optional step.

<details>

<summary>contracts/</summary>

The `contracts/` folder contains the source file for the Greeter smart contract.\
\
Let's review the `Greeter.sol` contract in the `hedera-example-hardhat-project/contracts` folder. At the top of the file, the `SPDX-License-Identifier` defines the license, in this case, the MIT license. The `pragma solidity ^0.8.9;` line specifies the Solidity compiler version to use. These two lines are crucial for proper licensing and compatibility.

{% code title="Greeter.sol" %}
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
{% endcode %}

_**NOTE**: The pragma solidity line must match the version of Solidity defined in the_ [_module exports_](https://github.com/hashgraph/hedera-hardhat-example-project/blob/main/hardhat.config.js#L34) _of your `hardhat.config.js` file._

</details>

<details>

<summary>scripts/</summary>

The `scripts/` folder contains test scripts for locally testing a smart contract before deploying it. Please read the comments to help you understand the code and its purpose.

**contractCall.js**

Calls the `setGreeting` function from the Greeter contract and sets the greeter message to "Greeter."

{% code title="contractCall.js" %}
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
{% endcode %}

**contractViewCall.js**

Returns the current greeter message value stored with the Greeter contract.

{% code title="contractViewCall.js" %}
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
{% endcode %}

**deployContract.js**

Deploys the Greeter contract and returns the contract public address.

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

**showBalance.js**

Returns the balance of the specified wallet address (account) in tinybars. Tinybars are the unit in which Hedera accounts hold HBAR balances.

{% code title="showBalance.js" %}
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
{% endcode %}

</details>

<details>

<summary>test/</summary>

The `test/` folder contains the test files for the project.\
\
The `rpc.js` file is located in this folder in the `hedera-example-hardhat-project` project and references the Hardhat [tasks](https://github.com/hashgraph/hedera-hardhat-example-project/blob/main/hardhat.config.js#L7) that are defined in the `hardhat.config file`. When the command `npx hardhat test` is run, the program executes the `rpc.js` file.

{% code title="rpc.js" %}
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
{% endcode %}

</details>

<details>

<summary>.env.example</summary>

A file that stores your environment variables like your accounts, private keys, and references to Hedera network. Details of this file are available in [Step 2](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#environment-variables) of this tutorial.

</details>

<details>

<summary>hardhat.config.js</summary>

The Hardhat configuration file. This file includes information about the Hedera network RPC URLs, accounts, and tasks defined. Details of this file are available in [Step 2](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#hardhat-configuration-file) of this tutorial.

</details>

***

## Step 2: Configure Project

In this step, you will update and configure your environment variables and Hardhat configuration files that define tasks, store account private keys, and RPC endpoint URLs. First, rename the `.env.exmaple` file to `.env`.

### Environment Variables

The `.env` file securely stores environment variables, such as your Hedera network endpoints and private keys, which are then imported into the `hardhat.config.js` file. This helps protect sensitive information like your private keys and API secrets, but it's still best practice to add `.env` to `.gitignore` file to prevent you from committing and pushing your credentials to GitHub. Go to the tab corresponding to your deployment path and follow the steps to set up your environment variables.

{% tabs %}
{% tab title="local node" %}
**Prerequisite**: A Hedera Local Node set up and running ([setup tutorial](../local-node/how-to-set-up-a-hedera-local-node.md)).

**Hedera Local Node environment variables**

The variables are predefined for the purposes of this tutorial.

{% code title=".env" %}
```bash
# Your Hedera Local Node ECDSA account alias private key
LOCAL_NODE_OPERATOR_PRIVATE_KEY=0x105d050185ccb907fba04dd92d8de9e32c18305e097ab41dadda21489a211524
# Your Hedera Local Node JSON-RPC endpoint URL
LOCAL_NODE_ENDPOINT='http://localhost:7546/'
```
{% endcode %}

**Variables explained**

* **`LOCAL_NODE_OPERATOR_PRIVATE_KEY`**: This is your Alias ECDSA hex-encoded private key for your Hedera Local Node. Replace the example value with your actual private key. Once you set up your local node and run the command to start, the accounts list for alias ECDSA private keys will be generated and returned to your console (see screenshot below). Replace the example value with your actual private key.

<figure><img src="../../.gitbook/assets/ecdsa-account-alias-cli.png" alt="" width="563"><figcaption></figcaption></figure>

* **`LOCAL_NODE_ENDPOINT`**: This is the URL endpoint for your Hedera Local Node's JSON-RPC Relay. Typically, this would be your `localhost` followed by the port number (`http://localhost:7546/`).

<figure><img src="../../.gitbook/assets/local-node-address-7546.png" alt="" width="563"><figcaption></figcaption></figure>
{% endtab %}

{% tab title="testnet" %}
**Prerequisite**: A Hedera testnet account from the [Hedera Developer Portal](https://portal.hedera.com/).

**Hedera Testnet environment variables**

{% code title=".env" %}
```bash
# Your testnet account ECDSA hex-encoded private key
TESTNET_OPERATOR_PRIVATE_KEY=0xb46751179bc8aa9e129d34463e46cd924055112eb30b31637b5081b56ad96129
# Your testnet JSON-RPC Relay endpoint URL
TESTNET_ENDPOINT='https://testnet.hashio.io/api'
```
{% endcode %}

**Variables explained**

* **`TESTNET_OPERATOR_PRIVATE_KEY`**: This is your ECDSA hex-encoded private key for the Hedera Testnet. Replace the example value with your actual private key.

<figure><img src="../../.gitbook/assets/portal-hex.png" alt="" width="563"><figcaption></figcaption></figure>



* **`TESTNET_ENDPOINT`**: This is the URL endpoint for the Hedera Testnet's JSON-RPC Relay. Replace the example URL with the one you're using.&#x20;

For this tutorial, we'll use Hashio, an instance of the [Hedera JSON-RPC relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md) hosted by [Swirlds Labs](https://swirldslabs.com/). You can use any JSON-RPC instance the community supports.

{% content-ref url="../more-tutorials/json-rpc-connections/hashio.md" %}
[hashio.md](../more-tutorials/json-rpc-connections/hashio.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}

Configuring these environment variables enables your Hardhat project to interact with the Hedera network or your local node. Let's review the Hardhat configuration file, where these environment variables are loaded into.

### Hardhat Configuration File

The Hardhat config (`hardhat.config.js`) file serves as the central configuration file for your Hardhat project. This file is crucial for specifying various settings, including Hardhat tasks, network configurations, compiler options, and testing settings. Let’s review the configuration settings.

#### Required Packages and Mocha Settings

These first lines import the required Hardhat plugins and the `dotenv` module that loads environment variables from the `.env` file. This will allow you to keep your private keys secure while using them in your dApp and will keep you from committing these to GitHub.&#x20;

{% code title="hardhat.config.js" %}
```javascript
require("@nomicfoundation/hardhat-toolbox");
require("@nomicfoundation/hardhat-chai-matchers");
require("@nomiclabs/hardhat-ethers");
require("dotenv").config(); // Import dotenv library to access the .env file
```
{% endcode %}

#### Hardhat Tasks

These lines define tasks that are accessed and executed from the [`test/`](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#test) or [`scripts/`](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md#scripts) folders.

{% code title="hardhat.config.js" %}
```javascript
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
```
{% endcode %}

#### Solidity Compiler Settings

Here, the Solidity compiler version is set to "0.8.9". The optimizer is enabled with 500 runs to improve the contract's efficiency.

{% code title="hardhat.config.js" %}
```javascript
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
```
{% endcode %}

#### Network Configurations

The `networks` object is essential for defining the Hedera networks your Hardhat project will connect to. Additionally, the `defaultNetwork` key specifies the network Hardhat will default to if none is specified at deployment.

{% tabs %}
{% tab title="local node" %}
{% code title=".env" %}
```javascript
// Specifies which network configuration will be used by default when you run Hardhat commands. 
  defaultNetwork: "local",
  networks: {
    // Defines the configuration settings for connecting to Hedera local node
    local: {
      // Specifies URL endpoint for Hedera local node pulled from the .env file
      url: process.env.LOCAL_NODE_ENDPOINT,
      // Your local node operator private key pulled from the .env file
      accounts: [process.env.LOCAL_NODE_OPERATOR_PRIVATE_KEY],
    },
  },
```
{% endcode %}
{% endtab %}

{% tab title="testnet" %}
{% code title=".env" %}
```javascript
  // Specifies which network configuration will be used by default when you run Hardhat commands.
  defaultNetwork: "testnet",
  networks: {
    // Defines the configuration settings for connecting to Hedera testnet
    testnet: {
      // Specifies URL endpoint for Hedera testnet pulled from the .env file
      url: process.env.TESTNET_ENDPOINT,
      // Your ECDSA testnet account private key pulled from the .env file
      accounts: [process.env.TESTNET_OPERATOR_PRIVATE_KEY],
    },
  },
```
{% endcode %}
{% endtab %}
{% endtabs %}

**Key/value breakdown:**

* **`defaultNetwork`**: This property specifies which network configuration will be used by default when you run Hardhat commands.&#x20;
* **`networks`**: This property contains configurations for different networks you might connect to.
* **`url`**:  This specifies the URL endpoint for the network. The value is pulled from the `.env` file where the environment variables are defined.
* **`accounts`**: This lists the private keys for the accounts you'll use when connecting to the network. The value is pulled from the `.env` file where the environment variables are defined.

***

## Step 3: Compile Contract

Now that your project is configured compile your contract. Run the following command in the `hedera-hardhat-example-project` terminal:

```bash
npx hardhat compile
```

<details>

<summary>Console check ✅</summary>

```bash
Compiling...
Compiled 1 contract successfully
```

</details>

The compiled artifacts will be saved in the `artifacts/` directory by default, or whatever your [configured artifacts path](https://hardhat.org/hardhat-runner/docs/config#path-configuration) is. The metadata file generated in this directory will be used for the [smart contract verification process](../../core-concepts/smart-contracts/verifying-smart-contracts-beta.md) in a later step.&#x20;

<figure><img src="../../.gitbook/assets/hardhat-artifacts-metadata-file.png" alt="" width="373"><figcaption></figcaption></figure>

After the initial compilation, if you don't modify any files, nothing will be compiled when you run the `compile` command. To force a compilation you can use the `--force` flag or run `npx hardhat clean` to clear the cache and delete the artifacts to recompile.

***

## Step 4: Test and Deploy Contract

Once your contract is compiled successfully, deploy the `Greeter.sol` smart contract. There are additional steps required if you're deploying to your local node:

<details>

<summary>🛠️ local node additional steps</summary>

Before you deploy your contract, let's ensure you have all the necessary tools open and running to avoid any issues.&#x20;

* Have two terminals open. One for each of these two project directories:
  * `hedera-local-node`
  * `hedera-hardhat-example-project`
* Have Docker open and start your local node.
  * In the `hedera-local-node` terminal, start your local node by running `hedera start -d`.

_**Note**: If you have not set up your Hedera Local Node, you can do so by following_ [_this_](../local-node/how-to-set-up-a-hedera-local-node.md) _tutorial and returning to this step once you complete the setup._

</details>

#### Test

Test your contract before deploying it. In the `hedera-hardhat-example-project` terminal, run the following command to compile and test your contract:

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

#### Deploy

In the same terminal, run the following command to deploy your contract to the default network specified in your config file:

```bash
npx hardhat deploy-contract
```

Alternatively, you can target any network configured in your Hardhat config file. For testnet:&#x20;

```bash
npx hardhat run --network testnet scripts/deployContract.js
```

<details>

<summary>console check ✅</summary>

```bash
Greeter deployed to: 0x157B93c04a294AbD88cF608672059814b3ea38aE
```

</details>

### Next Steps

{% tabs %}
{% tab title="local node" %}
**Stop Local Node**

Stop your local node and remove Docker containers by running `hedera stop` or `docker compose down` in your `hedera-local-node` terminal. Reference the [_Stop Your Local Node_](../local-node/how-to-set-up-a-hedera-local-node.md#stop-your-local-network) section of the local node setup tutorial.&#x20;

#### Deploy on Hedera Testnet

If you're up for it, follow the steps to deploy on the Hedera testnet and verify your contract.&#x20;
{% endtab %}

{% tab title="tesnet" %}
**View Contract on HashScan Network Explorer**

You can view the contract you deployed by searching the smart contract _public_ address in a supported [Hedera Network Explorer](../../networks/community-mirror-nodes.md). For this example, we will use the [HashScan](https://hashscan.io/mainnet/dashboard) Network Explorer. Copy and paste your deployed `Greeter.sol` public contract address into the HashScan search bar.

The Network Explorer will return the information about the contract created and deployed to the Hedera Testnet. The "EVM Address" field is the public address of the contract that was returned to you in your terminal. The terminal returned the public address with the "0x" hex encoding appended to the public address. You will also notice a contract ID in `0.0.contractNumber` (`0.0.3478001`) format. This is the _contract ID_ used to reference the contract entity in the Hedera Network.

<img src="../../.gitbook/assets/new hashscan (2).png" alt="" data-size="original">

**Verify Contract**

Additionally, you can verify your contract using the HashScan verification feature (beta). Follow these [steps](how-to-verify-a-smart-contract-on-hashscan.md) to learn how.

_**Note:** At the top of the explorer page, remember to switch the network to **TESTNET** before you search for the contract._
{% endtab %}
{% endtabs %}

**Congratulations! 🎉 You have successfully learned how to deploy a smart contract using Hardhat and Hedera JSON-RPC Relay. Feel free to reach out in** [**Discord**](https://hedera.com/discord)**!**

***

## Additional Resources

**➡** [**Project Repository**](https://github.com/hashgraph/hedera-hardhat-example-project)

**➡** [**Hedera Local Node Repository**](https://github.com/hashgraph/hedera-local-node)

**➡** [**Hedera JSON-RPC Relay Repository**](https://github.com/hashgraph/hedera-json-rpc-relay)

**➡** [**Hedera Local Node Setup Tutorial**](../local-node/how-to-set-up-a-hedera-local-node.md)

**➡**[ **Hardhat Documentation**](https://hardhat.org/hardhat-runner/docs/getting-started#overview)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://github.com/theekrystallee">https://github.com/theekrystallee</a></td></tr><tr><td align="center"><p>Editor: Simi, Sr. Software Manager</p><p><a href="https://github.com/SimiHunjan">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/shunjan/">https://www.linkedin.com/in/shunjan/</a></td></tr><tr><td align="center"><p>Editor: Nana, Sr Software Manager </p><p><a href="https://github.com/Nana-EC">GitHub</a> | <a href="https://www.linkedin.com/in/nconduah/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/nconduah/">https://www.linkedin.com/in/nconduah/</a></td></tr><tr><td align="center"><p>Editor: Georgi, Sr Software Dev (LimeChain) </p><p><a href="https://github.com/georgi-l95">GitHub</a> | <a href="https://www.linkedin.com/in/georgi-dimitorv-lazarov/">LinkedIn</a></p></td><td><a href="https://github.com/georgi-l95">https://github.com/georgi-l95</a></td></tr></tbody></table>
