# LayerZero

## What is LayerZero?

LayerZero is an omnichannel interoperability protocol designed to facilitate cross-chain communication. By enabling secure and efficient message passing across chains, LayerZero allows developers to build [decentralized applications (dApps)](../../support-and-community/glossary.md#decentralized-application-dapp) that operate cohesively over multiple blockchains. This capability enhances the functionality and user experience of dApps by leveraging the unique features of various blockchain ecosystems.

***

## Getting Started with LayerZero on Hedera&#x20;

To get started quickly, you can begin with the Gitpod demo, which requires no environment setup. Alternatively, you can go directly into the [LayerZero Quickstart series](https://docs.layerzero.network/v2/developers/evm/getting-started). These guides provide an overview of deploying an Omnichain Application (OApp) on Hedera and other EVM-compatible networks, covering essentials like setting up your LayerZero environment and deploying contracts for cross-chain messaging.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>➡ GITPOD DEMO</strong></td><td><a href="https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp?tab=readme-ov-file#try-it-in-gitpod">https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp?tab=readme-ov-file#try-it-in-gitpod</a></td></tr><tr><td align="center"><strong>➡ LAYERZERO OAPP QUICKSTART</strong></td><td><a href="https://docs.layerzero.network/v2/developers/evm/oapp/overview">https://docs.layerzero.network/v2/developers/evm/oapp/overview</a></td></tr></tbody></table>

### Developer Considerations to Note EVM Differences

Please note the smallest unit of _**HBAR is the tinybar (8 decimal places)**_, while the _**JSON-RPC relay operates 18 decimal places**_ for compatibility with Ethereum tools. This means when dealing with `msg.value`, conversions between tinybars and weibars are necessary. Additionally, [_Hedera’s gas model_](../../core-concepts/smart-contracts/gas-and-fees.md#gas-reservation-and-unused-gas-refund) _charges for at least 80% of gas_, regardless of usage, and event handling often requires querying mirror nodes. Please take these differences into account, especially when calling `quote`. Reference the [_Hedera vs. Ethereum_](../../core-concepts/smart-contracts/understanding-hederas-evm-differences-and-compatibility/) section for a more comprehensive list of differences.

{% hint style="info" %}
**Note**: LayerZero currently does not support Hedera Token Service (HTS).
{% endhint %}

## Additional Resources

* [**Demo Code Repo**](https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp)
* [**LayerZero Scan**](https://layerzeroscan.com/)
* [**Hedera Fee Estimator**](https://hedera.com/fees)
* [**LayerZero  Developer Resources**](https://layerzero.network/developers)
* [**Hedera Testnet LayerZero Endpoint**](https://docs.layerzero.network/v2/developers/evm/technical-reference/deployed-contracts#hedera-testnet)
