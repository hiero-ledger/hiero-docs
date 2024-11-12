---
description: >-
  How to configure a JSON-RPC endpoint that enables communication between
  EVM-compatible developer tools using Hashio
---

# Configuring Hashio RPC endpoints

[Hashio](https://www.hashgraph.com/hashio/) is a public RPC endpoint hosted by Swirlds Labs. As a _public_ endpoint, it:

* Is free to use
* Does not have any sign-up requirements
* Has significantly restrictive rate limits

While this combination may be considered less reliable, it offers the highest levels of ease of use among RPC endpoints.

To connect to the Hedera networks via Hashio, simply use this URL when initializing the wallet or web3 provider instance:

{% tabs %}
{% tab title="Hedera Mainnet" %}
```
https://mainnet.hashio.io/api
```
{% endtab %}

{% tab title="Hedera Testnet" %}
```
https://testnet.hashio.io/api
```
{% endtab %}

{% tab title="Hedera Previewnet" %}
```
https://previewnet.hashio.io/api
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
_**Please note**: Hashio is For development and testing purposes only. Production use cases are strongly encouraged to use_ [_commercial-grade JSON-RPC relays_](https://docs.hedera.com/hedera/core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay#community-hosted-json-rpc-relays) _or host their own instance of the_ [_Hedera JSON-RPC Relay_](https://github.com/hashgraph/hedera-json-rpc-relay?tab=readme-ov-file#hedera-json-rpc-relay)_._
{% endhint %}

No further settings or configurations are needed!
