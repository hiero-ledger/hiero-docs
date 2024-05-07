# Supra Oracles

## Overview

[Supra](https://supra.com/) is a novel, high-throughput Oracle & IntraLayer offering a vertically integrated toolkit of cross-chain solutions. These solutions include data oracles, asset bridges, and automation networks that aim to interlink all public and private blockchains. Supra provides decentralized Oracle price feeds to deliver real-world data to web3 ecosystems through various on-chain and off-chain use cases. Oracles ensure that the data from the real world is accurate, which is crucial for decentralized applications (dApps) that rely on real-world, real-time data. This is important for dApps that need real-time information, such as the prices of cryptocurrencies and various other assets. The Supra x Hedera integration aims to bring speed, security, and accuracy to real-time data feeds, enhancing the functionality and reliability of dApps on the Hedera network.

## **Developer Considerations**

Contract calls, such as _`eth_call`_ and _`eth_estmateGas`_, go to the mirror node, which limits those to a small data payload size. Our engineering team has successfully upgraded the API call data payload capacity to 24 KB. This enhancement is designed to fetch a single price pair from the data feed efficiently, ensuring a more streamlined retrieval process.

{% hint style="info" %}
_**Please Note:** While this update offers improved performance to pull single price pairs, attempting to pull more than one price pair at a time may surpass the new 24 KB data payload limit. Should this limit be exceeded, the API will return an error message. We recommend structuring your API calls accordingly to avoid any potential disruptions._
{% endhint %}

