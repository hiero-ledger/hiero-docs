---
description: >-
  How to configure a JSON-RPC endpoint that enables communication between
  EVM-compatible developer tools using Arkhia
---

# Configuring Arkhia RPC endpoints

[Arkhia](https://www.arkhia.io/features/#api-services) is a third-party organization that runs a JSON-RPC managed service. It is a "freemium" offering, meaning it has a free tier with paid components. As a managed service, it:

* Is free to use up to a point
* Does not have any sign-up requirements
* Has minimally restrictive rate limits

This combination makes it moderately straightforward to use and more reliable than the public RPC endpoint.

To connect to Hedera networks via Arkhia, simply use this URL when initializing the wallet or web3 provider instance:

{% tabs %}
{% tab title="Hedera Mainnet" %}
```
https://pool.arkhia.io/hedera/mainnet/json-rpc/v1/YOUR_API_KEY
```
{% endtab %}

{% tab title="Hedera Testnet" %}
```
https://pool.arkhia.io/hedera/testnet/json-rpc/v1/YOUR_API_KEY
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Note:** Arkhia provides RPC endpoints for Hedera Mainnet and Hedera Testnet but not for Hedera Previewnet.
{% endhint %}

You will need to replace `YOUR_API_KEY` with an Arkhia API, and that requires the following pre-requisite steps:

To get `YOUR_API_KEY`, you will need to do the following pre-requisite steps:

* (1) Sign up for an account at [`auth.arkhia.io/signup`](https://auth.arkhia.io/signup)
* (2) Click on the link in your confirmation email.
* (3) Click on the "create project" button in the top-right corner of the Arkhia dashboard.

<figure><img src="https://i.stack.imgur.com/JY5Ck.png" alt="" width="375"><figcaption></figcaption></figure>

* (4) Fill in whatever you like in the modal dialog that pops up:

<figure><img src="https://i.stack.imgur.com/wYNj3.png" alt="" width="375"><figcaption></figcaption></figure>

* (5) Click on the "Manage" button on the right side of your newly created project:

<figure><img src="https://i.stack.imgur.com/yhCQp.png" alt=""><figcaption></figcaption></figure>

* (6) In the project details, copy the "API Key" field and the "JSON-RPC" field as well (in case it is been updated from what was stated above).

<figure><img src="https://i.stack.imgur.com/f8A1b.png" alt=""><figcaption></figcaption></figure>

* (7) Substitute the value of `YOUR_API_KEY`in the RPC URL with what you have just copied.

Now you're ready to connect to an RPC endpoint via Arkhia!
