# Pyth Oracles

## What is Pyth?

The Pyth Network is a first-party financial oracle network designed to provide low-latency real-world data to multiple blockchains securely and transparently. Pyth currently supports 400+ real-time price feeds across crypto, equities, ETFs, FX pairs, and commodities and has facilitated more than $100B in total trading volume across over 50 blockchain ecosystems.

## How to integrate with the Pyth Network on Hedera?

The Pyth Price Feeds uses a "pull" price update model, where users are responsible for posting price updates on-chain when needed. In the pull model, developers should integrate Pyth into both their on-chain and off-chain code:

1. On-chain programs should read prices from the Pyth program deployed on the same chain
2. Off-chain frontends and jobs should include Pyth price updates alongside (or within) their application-specific transactions.

Please note that it is possible to replicate the legacy Oracle design with the Pyth scheduler (previously known as "price pusher"). It is an off-chain application that regularly pulls price updates on to a blockchain you can [find here](https://docs.pyth.network/price-feeds/schedule-price-updates/using-scheduler).

## Demonstration: How to integrate with the Pyth Network on Hedera?

This demo is a simple example of using Pyth prices in Hedera and is based on the [tutorial here](https://docs.pyth.network/price-feeds/create-your-first-pyth-app/evm). Follow the instructions in the tutorial to build, deploy, and use this demo.

This demo is similar to the contract written in the tutorial. The only difference is that it uses a different math to calculate the price of 1$ in HBAR because, In the Hedera EVM layer, the native token has only 8 decimal places, while Ethereum has 18 decimal places. This means that the smallest unit of HBAR (1 wei in Hedera EVM) is 0.00000001 HBAR, while the smallest unit of ETH is 0.000000000000000001 ETH. You can see the change in the code in the [MyFirstPythContract.sol](https://github.com/ali-bahjati/hedera-demo-contract/blob/main/contracts/src/MyFirstPythContract.sol).

{% embed url="https://youtu.be/2_ry0sTvnGo" %}

For more details, please visit this [GitHub repository](https://github.com/ali-bahjati/hedera-demo-contract).

If you have any questions, please refer to the Pyth Network [documentation](https://docs.pyth.network/home) or join [Discord](https://discord.gg/invite/PythNetwork).

***

**Contributors:** [**@KemarTiti**](https://github.com/KemarTiti)
