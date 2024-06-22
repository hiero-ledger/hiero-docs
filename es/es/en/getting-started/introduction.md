# Introduction

Through this short _Getting Started_ series, you will learn the basics of creating an account, transferring HBAR, signing, and submitting transactions to the Hedera Testnet. The Hedera Testnet allows you to play with the Hedera APIs in a non-production environment. You will see how easy it is to get started with one of our [Hedera SDKs](../sdks-and-apis/) in the programming language of your choice. Below are two paths to creating testnet accounts and receiving HBAR to build, test, and deploy Hedera dApps:

**➡** [**Hedera Faucet**](introduction.md#hedera-faucet)

**➡** [**Hedera Developer Portal**](introduction.md#hedera-developer-portal-profile)

***

### Hedera Faucet

The Hedera faucet allows you to anonymously receive testnet HBAR without the hassle of creating a developer portal account. To use the anonymous faucet, visit the [Hedera faucet](https://portal.hedera.com/faucet) and connect your wallet, or enter an EVM wallet address or Hedera account ID to initiate the process.

<figure><img src="../.gitbook/assets/faucet-receive-hbar.png" alt=""><figcaption></figcaption></figure>

Entering an EVM address will facilitate an account creation using the [auto account creation flow](../core-concepts/accounts/auto-account-creation.md#auto-account-creation-evm-address-alias). Copy and save the new Hedera account ID and the private key you're managing for your coding environment setup on the next page.

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt=""><figcaption></figcaption></figure>

The faucet has a maximum dispense limit of **100 HBAR** every 24 hours.

<figure><img src="../.gitbook/assets/faucet-wallet-timer.png" alt=""><figcaption></figcaption></figure>

You will receive an error message and be prompted to return when your account is eligible for a refill if you attempt it more than once within a 24-hour period.

<figure><img src="../.gitbook/assets/faucet-receive-error.png" alt=""><figcaption></figcaption></figure>

***

### Hedera Developer Portal Profile

The Hedera developer portal allows you to create a testnet account to receive HBAR upon creation. Visit the [Hedera developer portal](https://portal.hedera.com/register) and follow the instructions to create an account.

<figure><img src="../.gitbook/assets/portal testnet account.png" alt="Screenshot of the Hedera Developer portal (portal.hedera.com/register) account creation page."><figcaption></figcaption></figure>

After account creation, your portal testnet account will automatically receive **1000 HBAR,** and you'll see your account ID and key pair from the portal dashboard (see image below). Copy your account ID and DER-encoded private key for the coding environment setup step.

<figure><img src="../.gitbook/assets/faucet-der-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
**Note**: Testnet accounts on the developer portal are subject to a daily top-up limit of 1000 HBAR. Accounts _**do not**_ automatically get topped up. To maintain your balance, you must manually request a refill through the portal dashboard every 24 hours.

For clarity, topping up does not add 1000 HBAR to your account balance each time you refill. Instead, your account balance is replenished up to 1000 HBAR if it falls below this threshold. For example, if your account balance is 500 HBAR, refilling will only add enough HBAR to bring your balance to 1000 HBAR, not an additional 1000 HBAR.&#x20;
{% endhint %}
