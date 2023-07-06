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

(1) `7546` is the default port number for this project, and you can change this in its config if you wish.

(2) The RPC endpoint URL is the same for whichever network you intend to connect to: Hedera Previewnet, Hedera Testnet, and Hedera Mainnet. The selection of network depends upon the configuration file, which we will create in subsequent steps.

(3) The `hedera-json-rpc-relay` server is designed to be able to be deployed in your own cloud instances. For _non-production_ use cases, a Docker compose file is provided. For _production_ use cases Kubernetes Helm charts are provided.

</details>

To get this service running, you will need to do the following pre-requisite steps:

* (1) Clone the git project:

```bash
git clone git@github.com:hashgraph/hedera-json-rpc-relay.git
```

* (2) Create or edit a file named `.env` in the root directory of this project, with the following fields set:

{% tabs %}
{% tab title="Hedera Mainnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.mainnet.sample#L1-L5" %}
{% endtab %}

{% tab title="Hedera Testnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.testnet.sample#L1-L5" %}
{% endtab %}

{% tab title="Hedera Previewnet" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-json-rpc-relay/blob/f9d5ebaa80/docs/examples/.env.previewnet.sample#L1-L5" %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
On either Hedera Previewnet or Hedera Testnet, continue on to **step 3**.

\
On Hedera Mainnet, you can proceed directly to **step 8**. You are advised to utilize an account already funded with HBAR for the _`OPERATOR_ID_MAIN`_ and_`OPERATOR_KEY_MAIN`_ values. Please note that setting up a Hedera Mainnet account and funding it is out of scope for this article.
{% endhint %}

<details>

<summary>The "payer account" concept</summary>

Like on any other EVM-compatible networks, transactions must be paid for in the native currency. This is true for Hedera as well, where all transactions are paid for, denominated in HBAR.

Unlike other EVM-compatible networks, when an EVM transaction is submitted on a Hedera network, that transaction can be paid for by a different "payer account". The  `hedera-json-rpc-relay` takes care of this automatically for you, wrapping the transaction. This is why there is a need for an `OPERATOR_ID_MAIN` and `OPERATOR_KEY_MAIN`, as this is the "payer acount".

This effectively means that running and instance of `hedera-json-rpc-relay` on Hedera Mainnet is **not free**. On other Hedera networks, e.g. Hedera Testnet, where HBAR are obtained for free, it is effectively **free**. Apart from HBAR costs, the relay service is indeed free to use, and you are really limited only by your own hardware.

</details>

* (3) Visit the [Hedera Portal](https://portal.hedera.com/), and create a Testnet account. You will have the option to switch to Previewnet in subsequent steps.

<figure><img src="https://i.stack.imgur.com/tgkvS.png" alt="" width="375"><figcaption></figcaption></figure>

* (4) Copy-paste the confirmation code sent to your email:

<figure><img src="https://i.stack.imgur.com/4H9XT.png" alt="" width="375"><figcaption></figcaption></figure>

* (5) Fill out this form:

<figure><img src="https://i.stack.imgur.com/atW69.png" alt="" width="375"><figcaption></figcaption></figure>

* (6) In the top-left, select between Hedera Testnet (default) and Previewnet:

<figure><img src="https://i.stack.imgur.com/2A2ua.png" alt="" width="563"><figcaption></figcaption></figure>

* (7) From the next screen that shows your accounts, copy the value of the "DER-encoded private key" and replace `YOUR_OPERATOR_KEY` in the `.env` file with it.
* (8) From the same screen, copy the value of "Account ID" and set the value of the `OPERATOR_ID_MAIN` variable in the `.env` file with it.
* (9) Run `npm install` to install dependencies. Recommended that you have NodeJS version `18` or later for this.
* (10) Run `npm run setup` to link dependencies within its sub-packages.
* (11) Run `npm run build` to build the full project.
* (12) Run `npm run start` to start the RPC relay server.

Now you're ready to connect to an RPC endpoint via a locally running instance of Hedera JSON-RPC Relay!

Full reference configuration options for Hedera JSON-RPC Relay: [`docs/configuration.md`](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/configuration.md).
