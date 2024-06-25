# CLI Management

The Command Line Interface (CLI) is a core component of Stablecoin Studio and essential for developers aiming to streamline stablecoin management. This guide takes you from installing the SDK and CLI to customizing your config file and initiating stablecoin creation via CLI commands. Whether you're just getting started or already familiar with stablecoin management, this documentation provides the resources to navigate and optimize your stablecoin operations effectively.

***

## Table of Contents

1. [Prerequisites](cli-management.md#prerequisites)
2. [Install Stablecoin Studio](cli-management.md#install-stablecoin-studio)
3. [Configure CLI](cli-management.md#configure-cli)
   1. [Factory Contracts](cli-management.md#factory-contracts)
   2. [Deploy Factory Contracts](cli-management.md#deploy-custom-factory-contract-optional)
4. [CLI Flow](cli-management.md#cli-flow)
5. [Start CLI](cli-management.md#start-cli)
6. [Create Stablecoin](cli-management.md#create-stablecoin)
7. [Operate Stablecoin](cli-management.md#operate-stablecoin)
8. [Configure Proof-of-Reserve](cli-management.md#configure-proof-of-reserve-por)
9. [Additional Resources](cli-management.md#additional-resources)

***

## Prerequisites

- [NodeJS >= 18.13](https://nodejs.org/en)
- [Solidity >= 0.8.16](https://docs.soliditylang.org/en/latest/installing-solidity.html)
- [TypeScript >= 4.7](https://www.npmjs.com/package/typescript)
- [Git Command Line](https://git-scm.com/downloads)
- [Hedera Testnet Account](https://portal.hedera.com/)
- [HashPack Wallet](https://www.hashpack.app/download)

***

## Install Stablecoin Studio

Open a new terminal and navigate to your preferred directory where you want your Stablecoin Studio project to live. Clone the repo using this command:

{% code fullWidth="false" %}

```bash
git clone https://github.com/hashgraph/stablecoin-studio.git
```

{% endcode %}

Install the `npm` package globally:

```bash
npm install -g @hashgraph/stablecoin-npm-cli
```

{% embed url="https://youtu.be/eThs08nUsLI?si=52IX_yl74R8rilVr" %}
How to Issue Stablecoins on Hedera: Studio Intro & Installation\
by Ed Marquez
{% endembed %}

***

## Configure CLI

To use the CLI correctly, it is necessary to generate a configuration file where the default network, their associated accounts, and the factory contract ID will be included. These parameters can be modified later on.

#### Create a config file

From the root of the `cli` directory, start the command line tool and create a configuration file using the `wizard` command:

```bash
cd stablecoin-studio/cli
accelerator wizard
```

The first time you execute the `wizard` command in your terminal, if you haven't added your default configuration path, the interface will prompt you to "Write your config path." To use the default configuration path, hit `enter`. This will walk you through the prompts where you will input your configuration settings and create your `hsca-config-yaml` file.

Let's go over the configuration details:

**`defaultNetwork`**

This sets the default network the application will connect to when it starts. It’s essential for defining the environment where transactions will occur (e.g., `testnet` for testing, `mainnet` for production). We will be using `testnet` for this tutorial.

```yaml
defaultNetwork: testnet
```

**`networks`**

This property contains a list of Hedera networks that the application can connect to. Each entry specifies the name of a network and a list of consensus nodes, allowing you to switch between different Hedera environments easily.

```yaml
networks:
  - name: mainnet
    consensusNodes: []
  - name: previewnet
    consensusNodes: []
  - name: testnet
    consensusNodes: []
```

**`accounts`**

This property holds the credentials for various Hedera accounts. Each account has an `accountId`, a `privateKey`, and a network association. This is critical for performing transactions, as the private key is used to sign them. The `alias` field provides a user-friendly identifier for the account and `importedTokens` can store any tokens imported into this account. You can use the Hedera Developer Portal to create the default testnet account.

```yaml
accounts:
  - accountId: YOUR ACCOUNT ID
    privateKey:
      key: >-
        YOUR PRIVATE KEY
      type: ED25519
    network: testnet
    alias: main
    importedTokens: []
```

**`mirrors`**

This property lists the available Hedera mirror nodes for each network. Mirror nodes hold historical data and can be queried for transactions, records, etc. The `selected` field indicates whether the mirror node is the default one to be used.

```yaml
mirrors:
  - name: HEDERA
    network: testnet
    baseUrl: https://testnet.mirrornode.hedera.com/api/v1
    selected: true
  - name: HEDERA
    network: previewnet
    baseUrl: https://previewnet.mirrornode.hedera.com/api/v1
    selected: true
  - name: HEDERA
    network: mainnet
    baseUrl: https://mainnet.mirrornode.hedera.com/api/v1
    selected: true
```

**`rpcs`**

This property specifies the RPC (Remote Procedure Call) servers available for connecting to Hedera services. RPCs are essential for querying smart contracts, among other functionalities. Similar to mirror nodes, the `selected` field indicates the default RPC to use.

```yaml
rpcs:
  - name: HASHIO
    network: testnet
    baseUrl: https://testnet.hashio.io/api
    selected: true
  - name: HASHIO
    network: previewnet
    baseUrl: https://previewnet.hashio.io/api
    selected: true
  - name: HASHIO
    network: mainnet
    baseUrl: https://mainnet.hashio.io/api
    selected: true
```

**`logs`**

Here, you can specify the path where log files will be stored (`path`) and the level of logging detail you want (`level`). For example, setting the level to `ERROR` will only log error events.

```yaml
logs:
  path: ./logs
  level: ERROR
```

**`factories`**

This property lists factory contract IDs (`id`) and their associated network. Factories are smart contracts that can create other contracts. By listing them here, the application knows which factories it can interact with on each network.

```yaml
factories:
  - id: 0.0.636690
    network: testnet
```

<details>

<summary>Example configured <code>hsca-config.yaml</code> file ✅</summary>

```yaml
defaultNetwork: testnet
networks:
  - name: mainnet
    consensusNodes: []
  - name: previewnet
    consensusNodes: []
  - name: testnet
    consensusNodes: []
accounts:
  - accountId: 0.0.1234
    privateKey:
      key: >-
        302e020100300506032XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
      type: ED25519
    network: testnet
    alias: main
    importedTokens: []
mirrors:
  - name: HEDERA
    network: testnet
    baseUrl: https://testnet.mirrornode.hedera.com/api/v1
    selected: true
  - name: HEDERA
    network: previewnet
    baseUrl: https://previewnet.mirrornode.hedera.com/api/v1
    selected: true
  - name: HEDERA
    network: mainnet
    baseUrl: https://mainnet.mirrornode.hedera.com/api/v1
    selected: true
rpcs:
  - name: HASHIO
    network: testnet
    baseUrl: https://testnet.hashio.io/api
    selected: true
  - name: HASHIO
    network: previewnet
    baseUrl: https://previewnet.hashio.io/api
    selected: true
  - name: HASHIO
    network: mainnet
    baseUrl: https://mainnet.hashio.io/api
    selected: true
logs:
  path: ./logs
  level: ERROR
factories:
  - id: 0.0.636690
    network: testnet
```

</details>

***

### Factory contracts

We provide default contract addresses for factories that we have deployed for anyone to use. They are updated whenever a new version is released.

| Contract name  | Address                                      | Network    |
| -------------- | -------------------------------------------- | ---------- |
| FactoryAddress | 0.0.14455068 | Testnet    |
| FactoryAddress | 0.0.XXXXXX   | Previewnet |

A factory contract is a specialized type of smart contract designed to generate another smart contract. It serves as a template or blueprint for creating multiple instances of other contracts with similar features but possibly different initial states and parameters.

> For example, the `ERC20Factory` is a contract that facilitates the deployment of new ERC20 tokens. The factory uses the `EIP1167` standard for simply and cheaply cloning contract functionality.

### Deploy custom factory contract (optional)

If you want to deploy your own factory contract with custom logic tailored to your specific needs, check out the `/contracts` folder [`README`](https://github.com/hashgraph/hedera-accelerator-stablecoin/blob/main/contracts/README.md) for a comprehensive guide.

***

## CLI Flow

<figure><img src="../../.gitbook/assets/stablecoin-cli-flow-chart.png" alt=""><figcaption></figcaption></figure>

***

## Start CLI

Start the CLI tool using the `wizard` command:

```bash
accelerator wizard
```

When the CLI is started with the configuration file properly configured, the first action will be to select the account you want to operate with. The list of configured accounts belonging to the default network indicated in the configuration file is displayed by default.

If there are no accounts in the file for the default network, a warning message will be displayed and a list of all the accounts in the file will be displayed.

<figure><img src="../../.gitbook/assets/cli start wizard stablecoin studio new banner.png" alt=""><figcaption></figcaption></figure>

#### Main Menu

The main menu will be displayed once you select an account you want to operate with. Choose one of the stablecoin management menu options listed and follow the prompts in your interface. Let's review each option to give you a better understanding of what's available to you:

<details>

<summary><strong>Create a new stablecoin</strong></summary>

This option starts your stablecoin creation process and where you will fill in the details of your stablecoin. Details such as:

1. **Set Factory**: Before anything else, specify the factory you'll be using. You can find the list of deployed factories in our [documentation](https://github.com/hashgraph/hedera-accelerator-stablecoin#deploying-the-stable-coin-factories).
2. **Basic Details**: Input essential information like the stablecoin's name and symbol.
3. **Auto-Renew Account**: This is set by default to your current account, enabling the stablecoin creation process.
4. **Optional Details**: You'll be prompted to add details like decimal count, initial, and max supply. Default values are used if you skip.
5. **Token Keys Ownership**: Decide whether the smart contract should own all underlying token keys like pause and wipe.
6. **KYC**: Choose whether to enable KYC. If yes, specify the KYC key and decide whether to grant KYC to the current account during stablecoin creation.
7. **Custom Fees**: Decide if you want to add custom fees to the token. If yes, specify the fee schedule key.
8. **Role Management**: For any token keys set to the smart contract, you can grant or revoke roles to other accounts.
9. **Proof of Reserve (PoR)**: Optionally, link your stablecoin to an existing PoR contract or deploy a new one, setting an initial reserve. Note that PoR here is demo-quality.
10. **PoR Admin**: If deploying a new PoR, your current account will become its admin by default.
11. **Proxy Admin Ownership**: Choose the account that will own the proxy admin. It can be set to another account or contract.
12. **Configuration File**: Make sure your configuration file contains the right factory and HederaTokenManager contract addresses.
13. **Execute**: Finally, the CLI will use the configuration file to submit the request, and your stablecoin will be created.
14. **Additional Contracts**: Users who wish to use their own contracts can update the configuration file with the new factory contract ID.

Refer to the [ChainLink documentation](https://docs.chain.link/data-feeds/proof-of-reserve/) for more on PoR feeds.

</details>

<details>

<summary>Manage imported tokens</summary>

Stablecoins that we have not created with our account but for which we have been assigned one or several roles must be imported in order to operate them.

1. Add token
2. Refresh token
3. Remove token

</details>

<details>

<summary>Operate with an existing stablecoin</summary>

Once a stablecoin is created or added, you can operate with it. The following list contains all the possible operations a user can perform if they have the appropriate role.

1. **Send Tokens**: Transfer tokens to other accounts.
2. **Cash In**: Mint tokens and transfer them to an account. If you have linked a PoR Feed to your stablecoin, this operation will fail in two cases:
   1. If you try to mint more tokens than the total Reserve (1 to 1 match between the token's total supply and the Reserve)
   2. If you try to mint tokens using more decimals than the Reserve has, for instance, minting 1.001 tokens when the Reserve only has two decimals.

      > _📣 This DOES NOT mean that a stablecoin can not have more decimals than the Reserve, transfers between accounts can use as many decimals as required._

<!---->

3. **Check Details**: Retrieve the stablecoin's details.
4. **Check Balance**: Retrieve an account's balance.
5. **Burn Tokens**: Burn tokens from the treasury account.
6. **Wipe Tokens**: Burn tokens from any account.
7. **Rescue Tokens**: Use the smart contract to transfer tokens from the treasury to a rescue account.
8. **Rescue HBAR**: Similarly, transfer HBAR from the treasury to a rescue account via smart contract.
9. **Freeze Management**: Freeze or unfreeze an account and verify its status. If an account is frozen, it will not be able to transfer any tokens.
10. **Role Management:** Administrators of a stablecoin can manage user roles from this menu. They can grant, revoke, edit (manage the supplier allowance), and check roles.
    1. The available roles are:
       1. `CASHIN_ROLE`
       2. `BURN_ROLE`
       3. `WIPE_ROLE`
       4. `RESCUE_ROLE`
       5. `PAUSE_ROLE`
       6. `FREEZE_ROLE`
       7. `KYC_ROLE`
       8. `DELETE_ROLE`

<!---->

11. **Configuration:** The last option in the menu allows for dual configuration management of both the stable coin and its underlying token. For the stablecoin, you can upgrade its contract and change its proxy admin. For the token, admins can edit attributes like name, symbol, and keys. To transfer proxy admin ownership, the current owner invites a new account ID and can cancel before acceptance. Once accepted, the ownership change is finalized.
12. <mark style="color:red;">**Danger Zone**</mark>: This option groups high-risk stablecoin operations that either impact all token owners or are irreversible. These are placed in a sub-menu to prevent accidental execution.

    1. **Pause/Unpause**: Suspend or resume all token operations.

    2. **Delete Token**: Permanently remove a token. This action is irreversible.

    > _📣 Take caution when using operations in the "Danger Zone" as they have significant impacts and may not be reversible._

_**See the**_ [_**repo**_](https://github.com/hashgraph/hedera-accelerator-stablecoin/blob/main/cli/README.md) _**for more details.**_

</details>

<details>

<summary>List Stablecoins</summary>

This option displays all the stable coins the user has created or added.

</details>

<details>

<summary>Configuration</summary>

This last option allows the user to display the current configuration file, modify the configuration path, change the default network and manage:

- **Accounts**: Allows the user to change the current account, see all configured accounts and also add new accounts and remove existing ones.
- **Mirror nodes**: Allows the user to change the current mirror node, see all configured mirror nodes for the selected Hedera network, add new mirror nodes and remove existing ones except for the one that is being used.
- **JSON-RPC-Relay services**: Allows the user to change the current JSON-RPC-Relay service, see all configured services for the selected Hedera network, add new JSON-RPC-Relay servies and remove existing ones except for the one that is being used.
- **Factory**: Allows the user to change the factory id of the selected Hedera network in the configuration file, to upgrade the factory's proxy, to change the factory's proxy admin owner account and, finally, to view de current factory implementation contract address as well as the factory owner account previously commented.

</details>

***

## Create Stablecoin

Start the CLI tool using the wizard command:

```bash
accelerator wizard
```

Select "Create a new stablecoin" and proceed through the prompts to fill in the details of your new stablecoin.

<figure><img src="../../.gitbook/assets/stablecoin-create-main-menu-cli.png" alt=""><figcaption><p>CLI main menu</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/stablecoin-create-cli-prompts.png" alt=""><figcaption><p>CLI stablecoin creation prompts</p></figcaption></figure>

{% embed url="https://youtu.be/gh7f_VX1iY0?si=nwVuA2YkV-cJje2A" %}
How to Issue Stablecoins on Hedera: Create a Stablecoin\
by Developer Advocate: Michiel Mulders
{% endembed %}

***

## Operate Stablecoin

Start the CLI using the wizard command:

```bash
accelerator wizard
```

Select the "Operate with an existing stablecoin" option and proceed through the prompts to operate your stablecoin.

<figure><img src="../../.gitbook/assets/stablecoin-cli-operate.png" alt=""><figcaption><p>CLI stablecoin operation menu</p></figcaption></figure>

{% embed url="https://youtu.be/41ag-y9cYck?si=RO-6P9TQJLXJdgyQ" %}
How to Issue Stablecoins on Hedera: Stablecoin Administration\
by Developer Advocate: Pathorn Tengkiattrakul
{% endembed %}

🪙 [Here](cli-management.md#operate-with-an-existing-stablecoin) is a list of stablecoin operations.

***

## Configure Proof of Reserve (PoR)

Start the CLI using the wizard command:

```bash
accelerator wizard
```

Select the "Create a new stablecoin" option, then proceed through the prompts.

<figure><img src="../../.gitbook/assets/stablecoin-proof-of-reserve-config-cli.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://youtu.be/a7sNXD5GKWA?si=i__uPkensCQu23P0" %}
How to Issue Stablecoins on Hedera: Create a Stablecoin\
by Developer Advocate: Greg Scullard
{% endembed %}

***

## Additional Resources

{% embed url="https://portal.hedera.com" %}

{% embed url="https://docs.hedera.com/hedera/support-and-community/glossary" %}
Hedera and Web3 Glossary
{% endembed %}

{% embed url="https://www.hashpack.app/download" %}
Hedera Non-Custodial Wallet
{% endembed %}

{% embed url="https://hashscan.io" %}
Hedera Network Explorer
{% endembed %}

{% embed url="https://www.npmjs.com/package/@hashgraph/stablecoin-npm-cli" %}
