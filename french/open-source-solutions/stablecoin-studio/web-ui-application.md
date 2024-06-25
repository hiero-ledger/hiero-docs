# Web UI Application

The Stablecoin Studio Web User Interface (Web UI) is designed to provide a more intuitive and user-friendly experience for managing stablecoins. Developed using React, this interface offers a visual representation of all our Web UI capabilities, from initial setup to advanced administration. Whether you're a newcomer looking for an easier entry point or a seasoned developer seeking a more efficient way to manage stablecoins, this documentation offers the insights and resources to harness the full power of our React-based Web UI.

***

## Table of Contents

1. [Interactive Demo](web-ui-application.md#interactive-demo)
2. [Prerequisites](web-ui-application.md#prerequisites)
3. [Installation](web-ui-application.md#install-stablecoin-studio)
4. [Start Web UI](web-ui-application.md#start-the-web-ui)
5. [Create Stablecoins](web-ui-application.md#create-stablecoins)
6. [Operate Stablecoins](web-ui-application.md#operate-stablecoins)
7. [Manage Roles](web-ui-application.md#manage-roles)
8. [Stablecoin Details](web-ui-application.md#stablecoin-details)
9. [Additional Resources](web-ui-application.md#additional-resources)

***

## Interactive Demo

Get firsthand experience with Stablecoin Studio's capabilities using the open-source, React-based demo application sandbox. The sandbox application is built using Stablecoin Studio's TypeScript SDK.

<figure><img src="../../.gitbook/assets/web ui platform user.png" alt=""><figcaption></figcaption></figure>

After setting up a [Hedera testnet account](https://portal.hedera.com/), explore creating and managing stablecoins on Hedera through the interactive demo and follow along. Let's get started and explore the three paths for launching the Stablecoin Studio web application:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><ol><li><a href="https://stablecoinstudio.com/"><strong>Code Sandbox</strong></a></li></ol></td><td>For a quick and effortless start, you can use the pre-configured Code Sandbox environment directly from StablecoinStudio.com. This approach requires no setup and provides a fully functional demo application.</td><td><a href="../../.gitbook/assets/stablecoin-code-sandbox-icon.png">stablecoin-code-sandbox-icon.png</a></td><td><a href="https://www.stablecoinstudio.com">https://www.stablecoinstudio.com</a></td></tr><tr><td><ol start="2"><li><a href="https://gitpod.io/new/#https://github.com/hashgraph/stablecoin-studio"><strong>GitPod Instance</strong></a></li></ol></td><td>Another easy way to get started is by launching a GitPod instance, which automates the initial setup and lets you dive into the application immediately. You can skip the p<em>rerequisites</em> and <em>installation</em> steps.</td><td><a href="../../.gitbook/assets/stablecoin-gitpod-icon.png">stablecoin-gitpod-icon.png</a></td><td><a href="https://gitpod.io/#https://github.com/hashgraph/stablecoin-studio">https://gitpod.io/#https://github.com/hashgraph/stablecoin-studio</a></td></tr><tr><td><ol start="3"><li><a href="web-ui-application.md#prerequisites"><strong>Local Environment</strong></a></li></ol></td><td>A more technical method to build and install on your local machine. This guide focuses on this method, walking you through the steps needed to set up your local environment. Start from the first step: Prerequisites.</td><td><a href="../../.gitbook/assets/stablecoin-local-environment-Icon.png">stablecoin-local-environment-Icon.png</a></td><td><a href="web-ui-application.md#prerequisites">#prerequisites</a></td></tr></tbody></table>

***

## Prerequisites

* [NodeJS >= 18.13](https://nodejs.org/en)
* [Solidity >= 0.8.16](https://docs.soliditylang.org/en/latest/installing-solidity.html)
* [TypeScript >= 4.7](https://www.npmjs.com/package/typescript)
* [Git Command Line](https://git-scm.com/downloads)
* [Hedera Testnet Account](https://portal.hedera.com/)
* [HashPack Wallet](https://www.hashpack.app/download)

***

## Install Stablecoin Studio

Open a new terminal and navigate to your preferred directory location where you want your Stablecoin Studio project to live. Clone the repo, `cd` in to the cloned directory, and install dependencies:

{% code fullWidth="false" %}
```bash
git clone https://github.com/hashgraph/stablecoin-studio.git
cd stablecoin-studio
npm install
```
{% endcode %}

`cd` in to the `web` directory:

```bash
cd web
```

#### Environment variables

Before we can start the web application, we need to configure the environment variables. From the root of the `web` project workspace, rename the `.env.sample` file to `.env` and configure the variables required to run the application. For the purposes of this demo, we will need:

1. The [factory contract ID.](https://github.com/hashgraph/hedera-accelerator-stablecoin/tree/main/cli#factories)
2. The [REST API](https://docs.hedera.com/hedera/sdks-and-apis/rest-api) testnet endpoint.
3. The [JSON-RPC relay ](https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay)testnet endpoint.

The `.env` file contains the following parameters:

<table><thead><tr><th width="278">Environment Variable</th><th>Description</th></tr></thead><tbody><tr><td><strong>REACT_APP_LOG_LEVEL</strong></td><td>Log level for the application. The default value is <code>ERROR</code>. Acceptable values are <code>ERROR</code>, <code>WARN</code>, <code>INFO</code>, <code>HTTP</code>, <code>VERBOSE</code>, <code>DEBUG</code>, and <code>SILLY</code> in order of priority (highest to lowest).</td></tr><tr><td><strong>REACT_APP_FACTORIES</strong></td><td>JSON array with a factory contract ID in Hedera format <code>0.0.XXXXX</code> per environment.</td></tr><tr><td><strong>REACT_APP_MIRROR_NODE</strong></td><td>The var must be a unique mirror node service for Hedera network, and this is the service that would be used when the UI starts. If the service doesn't require an API key to authorize requests the <code>API_KEY</code> and <code>HEADER</code> properties must remain empty. <a href="https://docs.hedera.com/hedera/sdks-and-apis/rest-api">Here</a> is a list of mirror node endpoints.</td></tr><tr><td><strong>REACT_APP_RPC_NODE</strong></td><td>The var must be a unique RPC node service for Hedera network, and this is the service that would be used when the UI starts. If the service doesn't require an API key to authorize requests the <code>API_KEY</code> and <code>HEADER</code> properties must remain empty. <a href="https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay">Here</a> is a list of RPC providers.</td></tr><tr><td><strong>GENERATE_SOURCEMAP</strong></td><td>This is a proprietary Create React App configuration. You can read more information in its <a href="https://create-react-app.dev/docs/advanced-configuration/">Create React App documentation</a>.</td></tr></tbody></table>

<details>

<summary>Example configured <code>.env</code> file ✅</summary>

{% code title=".env" overflow="wrap" fullWidth="false" %}
```bash
REACT_APP_LOG_LEVEL=ERROR
REACT_APP_FACTORIES='[{"Environment":"testnet","STABLE_COIN_FACTORY_ADDRESS":"0.0.467235"}]'
REACT_APP_MIRROR_NODE='[{"Environment":"testnet","BASE_URL":"https://testnet.mirrornode.hedera.com/api/v1/", "API_KEY": "", "HEADER": ""}]'
REACT_APP_RPC_NODE='[{"Environment":"testnet","BASE_URL":"https://testnet.hashio.io/api", "API_KEY": "", "HEADER": ""}]'
GENERATE_SOURCEMAP=false
```
{% endcode %}

</details>

***

## Start the Web UI

Once you have configured your environment variables, start the web UI from the `web` directory:

```bash
npm run start
```

If the application is successfully run, the web application interface will open in a new browser:

<figure><img src="../../.gitbook/assets/stablecoin-ui-start.png" alt=""><figcaption><p>http://localhost:3000/</p></figcaption></figure>

Click "Connect your wallet" and select the wallet ([HashPack](https://www.hashpack.app/) or [MetaMask](https://metamask.io/)) and network you want to interact with. For the purposes of this demo, we will use HashPack and select Testnet.

<div>

<figure><img src="../../.gitbook/assets/stablecoin-ui-connect-wallet.png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/stablecoin-ui-select-testnet.png" alt=""><figcaption></figcaption></figure>

</div>

Now that your project is set up and the web application is running let's create our first stablecoin!

***

## Create Stablecoins

#### Basic details (Required)

To initiate the creation of your stablecoin, head to the top of the interface and click on the <mark style="background-color:purple;">＋</mark> symbol. From the options that appear, select "Create coin." The required fields for basic details will be displayed:

* **HederaTokenManager impl.**: By default, this is set to a factory contract ID provided by Hedera, in the format `0.0.XXXXXX`. Advanced users have the option to deploy their own factory contract implementation.
* **Name**: This is where you name your new stablecoin, for example, "NewStableCoin."
* **Symbol**: Enter a symbol to represent your stablecoin, like "$NSC."

<figure><img src="../../.gitbook/assets/stablecoin-ui-create-new-sc.png" alt=""><figcaption></figcaption></figure>

#### Optional details

While the basic details are mandatory, you also have an option to further specify:

* **Initial Supply**: You can expand on the initial number of tokens that will be minted.
* **Max Supply**: If you chose 'Finite' in the 'Supply Type,' you might want to set an upper limit.
* **Decimals**: You can set additional decimal places if you need more precision.

<figure><img src="../../.gitbook/assets/stablecoin-ui-optional-details.png" alt=""><figcaption></figcaption></figure>

#### Manage permissions

When creating a stablecoin, you have multiple key configuration options. One of those is the “Underlying Token’s Keys Definition.” This determines which accounts control various operations of the stablecoin, such as who can approve [KYC](../../support-and-community/glossary.md#know-your-customer-kyc) checks or which account can change the coin supply. You have the flexibility to set these keys to be controlled by the stablecoin’s initial smart contract, assign them to a different key, or even leave them undefined.

If the KYC key is tied to the smart contract and the supply key isn't tied to the account that creates the stablecoin, you can opt to automatically grant KYC verification to the account creating the stablecoin at the time of its creation.

As for ownership settings, by default, the account that initiates the stablecoin creation also becomes the stablecoin proxy admin owner. However, you’re not locked into this default setup. You can alter this by specifying a different account ID during creation. This could be any account, including specialized accounts like a timelock controller for scheduled operations or a cold wallet for enhanced security.

<figure><img src="../../.gitbook/assets/stablecoin-ui-permission-management.png" alt=""><figcaption></figcaption></figure>

#### Proof-of-reserve (PoR)

Choose if the stablecoin will be linked to a Proof of Reserve (PoR) contract. You can either use an existing PoR contract address or create a new one using the demo implementation included in the project and setting an initial reserve amount.

{% hint style="warning" %}
**Warning:** You can change the PoR contract address at any time, but exercise caution. Altering the address can affect your stablecoin's cash-in functionality, as it refers to a new reserve verification contract. In cases where the new contract has a lower reserve than the previous one, minting new tokens may become restricted.
{% endhint %}

For those using the project's demo PoR contract, you also have the option to modify its reserve amount. This can be done via the PoR admin account used during the stablecoin's deployment. Because the reserve can be changed arbitrarily in the demo, it's intended for demonstration purposes only.

<figure><img src="../../.gitbook/assets/stablecoin-ui-proof-of-reserve.png" alt=""><figcaption></figcaption></figure>

#### Review

Final validation before creating the stablecoin. Review the stablecoin details and click the "Create stablecoin" button. Validate "Execute Smart Contract" and "Associate Token" transactions in your wallet. Once the stablecoin is created, it will be added to the drop-down list of coins you can access (with the account you used to create the stablecoin).

<figure><img src="../../.gitbook/assets/stablecoin-ui-review.png" alt=""><figcaption></figcaption></figure>

***

## Operate Stablecoins

To operate your stablecoin, connect your wallet to the platform. After successful authentication, select the stablecoin you wish to interact with from the drop-down list of available coins. Once the stablecoin information is loaded, navigate to the "Operations" tab.

You'll see a variety of actions and your accessible operations will be tied to the roles assigned to your account for the chosen stablecoin. Here's a quick rundown of what each operation allows:

* **Cash In**: Deposit assets into your stablecoin account.
* **Burn**: Permanently remove specific tokens from circulation.
* **Get Balance**: View the current balance of your stablecoin account.
* **Rescue**: Recover tokens in unique scenarios.
* **Rescue HBAR**: Specialized recovery for HBAR.
* **Wipe**: Clear particular stablecoin balances.
* **Freeze Account**: Temporarily disable transaction capabilities for an account.
* **Unfreeze**: Lift the freeze status from an account.
* **Check Account Frozen Status**: Verify whether an account is frozen.
* **Grant KYC**: Approve an account for KYC verification.
* **Revoke KYC**: Remove previously granted KYC approval.
* **Check KYC**: Confirm the KYC status of an account.
* **Danger Zone**: Access to operations that carry higher risk, generally because they affect every token owner (PAUSE) or can not be rolled back (DELETE).

To carry out an operation, simply click on the corresponding button and follow the on-screen prompts. The platform will automatically perform the operation based on the capabilities your account has been assigned.

The "Operations" tab in Stablecoin Studio is your hub for managing every aspect of your stablecoin, so make sure you're familiar with the roles and capabilities assigned to your account to leverage the suite of operations available to you fully.

<figure><img src="../../.gitbook/assets/stablecoin-ui-operations.png" alt=""><figcaption></figcaption></figure>

***

## Manage Roles

In Stablecoin Studio, role management is a pivotal feature that gives you control over various functions related to your stablecoin. If your account has been designated with the "Admin Role," you unlock the capability to manage other roles for your stablecoin, making governance easier and more secure.

Roles you can manage include:

* **Cash In**: Permits an account to deposit or 'cash in' assets.
* **Burn**: Authorizes an account to remove tokens from circulation permanently.
* **Wipe**: Allows an account to clear specific balances.
* **Rescue**: Grants the ability to recover tokens in special circumstances.
* **Pause**: Enables stopping all transactions temporarily, which is useful in emergency situations.
* **Freeze**: Authorizes freezing specific accounts, disabling their ability to transact.
* **Delete**: Allows the removal of accounts or certain data, irreversible.
* **Admin Role**: Provides overarching administrative privileges, often reserved for key governance participants.

Connect your wallet and select the stablecoin from the drop-down list you want to interact with. Once the stablecoin information loads, head to the "Role management" tab.

<figure><img src="../../.gitbook/assets/stablecoin-ui-role-management.png" alt=""><figcaption></figcaption></figure>

***

## Stablecoin Details

This menu option displays stablecoin details and allows the user to update some token properties, like the name, symbol, and keys. Clicking on the pencil icon located at the top right side of the screen turns the information page into a form where these properties can be modified by the user.

🎉 Congrats on creating your first stablecoin with Stablecoin Studio! View the transaction details on [HashScan](https://hashscan.io/testnet/dashboard) by looking up your new token ID or clicking the [HashScan Explorer link ](https://hashscan.io/testnet/token/0.0.1573710)from the "Token ID" field.

<div>

<figure><img src="../../.gitbook/assets/stablecoin-details-webui.png" alt=""><figcaption><p>HashScan Explorer link</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/stablecoin-hashscan.png" alt=""><figcaption><p>HashScan</p></figcaption></figure>

</div>

***

## Additional Resources

{% embed url="https://portal.hedera.com/" %}
Hedera Developer Portal (Testnet Accounts)
{% endembed %}

{% embed url="https://docs.hedera.com/hedera/support-and-community/glossary" %}
Hedera and Web3 Glossary
{% endembed %}

{% embed url="https://www.hashpack.app/download" %}
Hedera Non-Custodial Wallet
{% endembed %}

{% embed url="https://hashscan.io" %}
Hedera Network Explorer
{% endembed %}

{% embed url="https://www.npmjs.com/package/@hashgraph/stablecoin-npm-sdk" %}
