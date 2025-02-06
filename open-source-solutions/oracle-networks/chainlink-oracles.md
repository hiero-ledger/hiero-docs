# Chainlink Oracles

## What is Chainlink?

Chainlink Oracles are decentralized services that securely connect smart contracts to real-world data, events, and computations. By bridging the gap between blockchain applications and off-chain environments, Chainlink Oracles empowers developers to build advanced, feature-rich decentralized applications (dApps) across various industries.

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>➡</strong> <a href="https://docs.chain.link/data-feeds/price-feeds/addresses/?network=hedera&#x26;amp%3Bpage=1&#x26;page=1"><mark style="color:blue;"><strong>PRICE FEED</strong></mark></a></td><td><a href="https://docs.chain.link/data-feeds/price-feeds/addresses/?network=hedera&#x26;amp%3Bpage=1&#x26;page=1">https://docs.chain.link/data-feeds/price-feeds/addresses/?network=hedera&#x26;amp%3Bpage=1&#x26;page=1</a></td></tr><tr><td align="center"><strong>➡</strong> <a href="https://github.com/hedera-dev/hedera-example-chainlink-price-feeds"><mark style="color:blue;"><strong>DEMO GITHUB REPO</strong></mark></a></td><td><a href="https://github.com/hedera-dev/hedera-example-chainlink-price-feeds">https://github.com/hedera-dev/hedera-example-chainlink-price-feeds</a></td></tr><tr><td align="center"><strong>➡</strong> <a href="https://docs.chain.link/data-feeds"><mark style="color:blue;"><strong>CHAINLINK DOCS</strong></mark></a></td><td><a href="https://docs.chain.link/data-feeds/price-feeds/addresses?network=hedera&#x26;page=1">https://docs.chain.link/data-feeds/price-feeds/addresses?network=hedera&#x26;page=1</a></td></tr></tbody></table>

***

## **Getting Started with Chainlink Oracles on Hedera**

To integrate Chainlink Oracles into your Hedera-based dApp, you can start with a ready-to-use Gitpod demonstrating how to get Chainlink price feeds on Hedera using the Chainlink Price Feeds Adapter.

### Try It in Gitpod

[![Open in Gitpod](https://camo.githubusercontent.com/b04f5659467d23b5109ba935a40c00decd264eea25c22d50a118021349eea94f/68747470733a2f2f676974706f642e696f2f627574746f6e2f6f70656e2d696e2d676974706f642e737667)](https://gitpod.io/?autostart=true#https://github.com/ed-marquez/hedera-example-chainlink-price-feeds)

1. Enter your Hedera testnet credentials in the `.env` file
2.  Run the test to get the latest prices for all the price feeds:

    ```bash
    npx hardhat test
    ```

    [![alt text](https://github.com/hedera-dev/hedera-example-chainlink-price-feeds/raw/main/assets/console-output.png)](https://github.com/hedera-dev/hedera-example-chainlink-price-feeds/blob/main/assets/console-output.png)

***

## References

* [**Chainlink Price Feeds**](https://docs.chain.link/data-feeds/price-feeds/addresses/?network=hedera\&amp%3Bpage=1\&page=1)
* [**Testnet LINK Token Contract**](https://docs.chain.link/resources/link-token-contracts#hedera)
* #### [**Using Data Feeds on EVM Chains**](https://docs.chain.link/data-feeds/using-data-feeds#overview)

{% hint style="info" %}
**Have questions?** Join the [Hedera Discord](https://hedera.com/discord) and post them in the [`developer-general`](https://discord.com/channels/373889138199494658/373889138199494660) channel or ask on [Stack Overflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph).
{% endhint %}
