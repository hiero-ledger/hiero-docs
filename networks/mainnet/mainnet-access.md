---
cover: ../../.gitbook/assets/HH-Eco-Cat-Hero-Desktop-R1 (1).webp
coverY: -625.8620689655172
---

# Mainnet Accounts

To interact with and access the various Hedera Mainnet services such as accounts, topics, tokens, files, and smart contracts, you will need a Hedera account. Your Hedera account also holds a balance of HBAR, which can be used to make transaction fee payments or transfers to other accounts.

Create free mainnet accounts by visiting any of these wallet providers:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="https://atomicwallet.io/"><mark style="color:purple;"><strong>ATOMIC</strong></mark></a></td><td>✅ Private Key Viewable</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 2.39.29 PM (1).png">Screenshot 2022-12-20 at 2.39.29 PM (1).png</a></td><td><a href="https://atomicwallet.io/">https://atomicwallet.io/</a></td></tr><tr><td align="center"><a href="https://www.bladewallet.io/"><mark style="color:purple;"><strong>BLADE</strong></mark></a></td><td><p>✅ Private Key Viewable</p><p>✅ SDK-compatible Passphrase</p></td><td></td><td><a href="../../.gitbook/assets/blade-wallet-logo.png">blade-wallet-logo.png</a></td><td><a href="https://www.bladewallet.io/">https://www.bladewallet.io/</a></td></tr><tr><td align="center"><a href="https://www.coinomi.com/en/"><mark style="color:purple;"><strong>COINOMI</strong></mark></a></td><td>✅ SDK-compatible Passphrase</td><td></td><td><a href="../../.gitbook/assets/coinomi-logo.png">coinomi-logo.png</a></td><td><a href="https://www.coinomi.com/en/">https://www.coinomi.com/en/</a></td></tr><tr><td align="center"><a href="https://www.exodus.com/hedera-wallet-hbar"><mark style="color:purple;"><strong>EXODUS</strong></mark></a></td><td>✅ Private Key Viewable</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 3.11.05 PM.png">Screenshot 2022-12-20 at 3.11.05 PM.png</a></td><td><a href="https://www.exodus.com/hedera-wallet-hbar">https://www.exodus.com/hedera-wallet-hbar</a></td></tr><tr><td align="center"><a href="https://guarda.com/"><mark style="color:purple;"><strong>GUARDA</strong></mark></a></td><td>✅ Private Key Viewable</td><td></td><td><a href="../../.gitbook/assets/GUARDA.png">GUARDA.png</a></td><td><a href="https://guarda.com/">https://guarda.com/</a></td></tr><tr><td align="center"><a href="https://www.hashpack.app/"><mark style="color:purple;"><strong>HASHPACK</strong></mark></a></td><td><p>✅ Private Key Viewable</p><p>✅ SDK-compatible Passphrase</p></td><td></td><td><a href="../../.gitbook/assets/HASHPACK.png">HASHPACK.png</a></td><td><a href="https://www.hashpack.app/">https://www.hashpack.app/</a></td></tr><tr><td align="center"><a href="https://www.kabila.app/"><mark style="color:purple;"><strong>KABILA</strong></mark></a></td><td><p>✅ Private Key Viewable</p><p>✅ SDK-compatible Passphrase</p></td><td></td><td><a href="../../.gitbook/assets/kabila-docs-logo.png">kabila-docs-logo.png</a></td><td><a href="https://www.kabila.app/">https://www.kabila.app/</a></td></tr><tr><td align="center"><a href="https://wallawallet.com/"><mark style="color:purple;"><strong>WALLAWALLET</strong></mark></a></td><td><p>✅ Private Key Viewable</p><p>✅ SDK-compatible Passphrase</p></td><td></td><td><a href="../../.gitbook/assets/WALLA (1).png">WALLA (1).png</a></td><td><a href="https://wallawallet.com/">https://wallawallet.com/</a></td></tr></tbody></table>

| Feature                     | Description                                                                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ✅ Private Key Viewable      | You have access to the private key associated with the mainnet account the wallet created for you                                                       |
| ✅ SDK-compatible Passphrase | The passphrase created by the wallet is compatible with the SDKs and can be used to recover the private keys for the account the wallet created for you |

### Create new mainnet accounts

Once you have obtained your mainnet account from a supported wallet, you can use the SDKs to create additional mainnet accounts.

To do this, you will need to point your Hedera client to mainnet (`Client.forMainnet()`)and use the `AccountCreateTransaction` API to create a new account. The transaction fee payer (referred to as the `operator` in the SDKs) information should be set to the mainnet account you created from one of the above wallets (`setOperator(<mainnetAccountId, mainnetAccountPrivateKey)`).

{% content-ref url="../../sdks-and-apis/sdks/accounts-and-hbar/create-an-account.md" %}
[create-an-account.md](../../sdks-and-apis/sdks/accounts-and-hbar/create-an-account.md)
{% endcontent-ref %}
