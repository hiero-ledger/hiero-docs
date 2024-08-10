---
description: >-
  How to configure a JSON-RPC endpoint that enables the communication between
  EVM-compatible developer tools using the Hedera JSON-RPC Relay
---

# Configuring Hedera JSON-RPC Relay endpoints

[Hedera JSON-RPC Relay](https://github.com/hashgraph/hedera-json-rpc-relay) is a server run by you on your own computer - decentralization for the win!

As such, it:

* Is free to use on Hedera Previewnet and Hedera Testnet
* Does not have any sign-up requirements
* Does not have any rate limits
* Requires several additional steps required to set it up, plus developer/command line skills

While this combination may be considered less user-friendly, it offers the highest levels of reliability among RPC endpoints.

This also makes the Hedera JSON-RPC Relay a good alternative for local development and testing; and also a potential option for contributing infrastructure to the Hedera ecosystem.

To connect to Hedera networks via your own instance of Hedera JSON-RPC Relay, use this URL when initializing the wallet/ web3 provider instance:

{% tabs %}
{% tab title="Hedera Mainnet" %}
```
http://localhost:7546
```
{% endtab %}

{% tab title="Hedera Testnet" %}
```
http://localhost:7546
```
{% endtab %}

{% tab title="Hedera Previewnet" %}
```
http://localhost:7546
```
{% endtab %}
{% endtabs %}

<details>

<summary>Notes</summary>

(1) The RPC endpoint URL, including the port number `7546`, is the same for whichever network you intend to connect to: Hedera Previewnet, Hedera Testnet, and Hedera Mainnet. The selection of network depends upon the configuration file, which we will create in subsequent steps.

(2) The `hedera-json-rpc-relay` server is designed to be able to be deployed in your own cloud instances. For _non-production_ use cases, a Docker compose file is provided. For _production_ use cases Kubernetes Helm charts are provided. However, both the Docker and Kubernetes options are beyond the scope of this tutorial. This tutorial focuses on simply configuring and running the server directly.

</details>

To get this service running, you will need to do the following pre-requisite steps:

(1) Clone the git project:

{% code overflow="wrap" %}
```shell
git clone -b main --single-branch  https://github.com/hashgraph/hedera-json-rpc-relay.git
```
{% endcode %}

<details>

<summary>Alternative with <code>git</code> and SSH</summary>

If you have [configured SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) to work with `git`, you may wish use this command instead:

{% code overflow="wrap" %}
```shell
git clone -b main --single-branch git@github.com:hashgraph/hedera-json-rpc-relay.git
```
{% endcode %}

</details>

(2) Enter the directory that you have cloned, and install dependencies. It is recommended that you have NodeJS version `20` or later for this.

```sh
cd hedera-json-rpc-relay
npm install
```

(3) Build the project's sub-packages.

```sh
npm run build
```

(4) Create or edit a file named `.env` in the root directory of this project, with the following fields set:

{% tabs %}
{% tab title="Hedera Mainnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.mainnet.sample" %}
{% endtab %}

{% tab title="Hedera Testnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.testnet.sample" %}
{% endtab %}

{% tab title="Hedera Previewnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.previewnet.sample" %}
{% endtab %}
{% endtabs %}

{% hint style="success" %}
The following steps require that you already have an account on the Hedera network that you are connecting to. This account should be funded with some HBAR.

If you have yet to set one up, for **Testnet** you may use the Hedera Faucet. See [Hedera Faucet instructions](https://docs.hedera.com/hedera/getting-started/introduction#hedera-developer-portal-profile).

For either **Previewnet or Testnet** you may use the Hedera Portal. See [Hedera Developer Portal Profile instructions](https://docs.hedera.com/hedera/getting-started/introduction#hedera-developer-portal-profile).

Note that setting up a **Mainnet** account and funding it is out of scope for this article.
{% endhint %}

(5a) Copy your account ID value into the `.env` file in the `OPERATOR_ID_MAIN` field.

(5b) Copy your account's private key into the `.env` file in the `OPERATOR_KEY_MAIN` field.

For example, if your account ID is `0.0.12345`, your private key is `0xa1b2c3`, and you are connecting to Testnet, the `.env` file should look like the following.

```sh
HEDERA_NETWORK=testnet
OPERATOR_ID_MAIN=0.0.12345
OPERATOR_KEY_MAIN=0xa1b2c3
CHAIN_ID=0x128
MIRROR_NODE_URL=https://testnet.mirrornode.hedera.com/
```

<details>

<summary>"Operator" and "payer account" concepts</summary>

Like other EVM-compatible networks, transactions must be paid for in the native currency. This is true for Hedera as well, where all transactions are paid for, denominated in HBAR.

Unlike other EVM-compatible networks, when an EVM transaction is submitted on a Hedera network, that transaction can be paid for by a **different** "payer account". The `hedera-json-rpc-relay` takes care of this automatically for you, wrapping the transaction. This is why there is a need for an `OPERATOR_ID_MAIN` and `OPERATOR_KEY_MAIN`, as this is the "payer account".

This effectively means that running and instance of `hedera-json-rpc-relay` on Hedera Mainnet is **not free**. On other Hedera networks, e.g. Hedera Testnet, where HBAR are obtained for free, it is effectively **free**. Apart from HBAR costs, the relay service is indeed free to use, and you are really limited only by your own hardware.

</details>

(6) Run `npm run start` to start the RPC relay server.

Now you have an instance of Hedera JSON-RPC Relay running locally, and you are ready to send RPC requests to your selected Hedera network!

{% hint style="success" %}
Full reference configuration options for Hedera JSON-RPC Relay: [`docs/configuration.md`](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/configuration.md).
{% endhint %}
