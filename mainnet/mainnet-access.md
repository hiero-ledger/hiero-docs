# Mainnet Access

You will need a Hedera Mainnet account to interact with and pay for any of the network services (cryptocurrency, consensus, tokens, files and smart contracts). Your Hedera account is what holds a balance of HBAR to be used for transfers to other accounts or payments for network services.

Create free mainnet accounts by visiting any of these wallet providers:

<table><thead><tr><th>Wallets</th><th data-type="checkbox">Private Key Viewable</th><th data-type="checkbox">SDK-compatible Passphrase</th></tr></thead><tbody><tr><td><a href="https://support.atomicwallet.io/article/19-how-to-view-your-private-keys-backup-phrase">Atomic</a></td><td>true</td><td>false</td></tr><tr><td><a href="https://www.coinomi.com/en/">Coinomi</a></td><td>false</td><td>true</td></tr><tr><td><a href="https://www.exodus.com/hedera-wallet-hbar">Exodus</a> (Desktop)</td><td>true</td><td>false</td></tr><tr><td><a href="https://guarda.com/coins/hedera-wallet/">Guarda</a></td><td>true</td><td>false</td></tr><tr><td><a href="https://www.hashpack.app/">Hashpack</a></td><td>true</td><td>true</td></tr><tr><td><a href="https://wallet.xact.ac/">Xact</a></td><td>true</td><td>true</td></tr><tr><td><a href="https://wallawallet.com/">Wallawallet</a></td><td>true</td><td>true</td></tr><tr><td><a href="https://www.bladewallet.io/">Blade</a></td><td>true</td><td>true</td></tr></tbody></table>

After you receive your account ID and private keys, you can create additional mainnet accounts using the SDKs. When using the SDKs to build your Hedera client, select `client.forMainnet()`. A mainnet node will be picked at random to submit the transaction to.

{% content-ref url="../docs/sdks/cryptocurrency/create-an-account.md" %}
[create-an-account.md](../docs/sdks/cryptocurrency/create-an-account.md)
{% endcontent-ref %}
