---
cover: ../../.gitbook/assets/HH-Eco-Cat-Hero-Desktop R1 (1).webp
coverY: -625.8620689655172
---

# 主要账户

若要与各种Hedera Mainnet 服务进行互动并访问这些服务，例如帐户、主题、令牌、文件和智能合同，您将需要一个 Hedera 帐户。 您的 Hedera 账户也持有HBAR 余额，可用来支付交易费或转账到其他账户。

通过访问这些钱包提供者中的任何一个创建免费的主网帐户：

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="https://atomicwallet.io/"><mark style="color:purple;"><strong>ATOMIC</strong></mark></a></td><td>:check_mark_buton: 私钥可查看</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 2.39.29 PM (1).png">屏幕截图2022-12-20at 2.39.29 PM (1).png</a></td><td><a href="https://atomicwallet.io/">https://atomicwallet.io/</a></td></tr><tr><td align="center"><a href="https://www.bladewallet.io/"><mark style="color:purple;"><strong>BLADE</strong></mark></a></td><td><p>:check_mark_buton: 私钥可查看</p><p>:check_mark_buton: SDK兼容的密码</p></td><td></td><td><a href="../../.gitbook/assets/blade-wallet-logo.png">blade-wallet-logo.png</a></td><td><a href="https://www.bladewallet.io/">https://www.bladewallet.io/</a></td></tr><tr><td align="center"><a href="https://www.coinomi.com/en/"><mark style="color:purple;"><strong>COINOMI</strong></mark></a></td><td>:check_mark_buton: SDK兼容的密码</td><td></td><td><a href="../../.gitbook/assets/coinomi-logo.png">coinomi-logo.png</a></td><td><a href="https://www.coinomi.com/en/">https://www.coinomi.com/en/</a></td></tr><tr><td align="center"><a href="https://www.exodus.com/hedera-wallet-hbar"><mark style="color:purple;"><strong>EXODUS</strong></mark></a></td><td>:check_mark_buton: 私钥可查看</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 3.11.05 PM.png">2022-12-20屏幕截图为3.11.05 PM.png</a></td><td><a href="https://www.exodus.com/hedera-wallet-hbar">https://www.expus.com/hedera-wallet-hbar</a></td></tr><tr><td align="center"><a href="https://guarda.com/"><mark style="color:purple;"><strong>GUARDA</strong></mark></a></td><td>:check_mark_buton: 私钥可查看</td><td></td><td><a href="../../.gitbook/assets/GUARDA.png">GUARDA.png</a></td><td><a href="https://guarda.com/">https://guarda.com/</a></td></tr><tr><td align="center"><a href="https://www.hashpack.app/"><mark style="color:purple;"><strong>HASHPACK</strong></mark></a></td><td><p>:check_mark_buton: 私钥可查看</p><p>:check_mark_buton: SDK兼容的密码</p></td><td></td><td><a href="../../.gitbook/assets/HASHPACK.png">HASHPACK.png</a></td><td><a href="https://www.hashpack.app/">https://www.hashpack.app/</a></td></tr><tr><td align="center"><a href="https://www.kabila.app/"><mark style="color:purple;"><strong>KABILA</strong></mark></a></td><td><p>:check_mark_buton: 私钥可查看</p><p>:check_mark_buton: SDK兼容的密码</p></td><td></td><td><a href="../../.gitbook/assets/kabila-docs-logo.png">kabila-docs-logo.png</a></td><td><a href="https://www.kabila.app/">https://www.kabila.app/</a></td></tr><tr><td align="center"><a href="https://wallawallet.com/"><mark style="color:purple;"><strong>WALLAWALLET</strong></mark></a></td><td><p>:check_mark_buton: 私钥可查看</p><p>:check_mark_buton: SDK兼容的密码</p></td><td></td><td><a href="../../.gitbook/assets/WALLA (1).png">WALLA (1).png</a></td><td><a href="https://wallawallet.com/">https://wallawallet.com/</a></td></tr></tbody></table>

| 功能                                                                                                    | 描述                                   |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------ |
| :check_mark_buton: 私钥可查看    | 您可以访问与钱包为您创建的主网账户相关联的私钥              |
| :check_mark_buton: SDK兼容的密码 | 钱包创建的密码短语与 SDK 兼容，可以用来恢复为您创建的钱包账户的私钥 |

### 创建新的主机账户

一旦您从支持的钱包获取了您的主网账户，您可以使用 SDK 创建额外的主网账户。

要做到这一点，你需要将你的Hedera客户端指向主网 (`Client.forMainnet()`)，并使用 `AccountCreateTransaction` API 来创建一个新帐户。 交易费用支付者(SDK中称为“运营商”)信息应设置为您从上述钱包之一创建的主网账户(\`setOperator(\<mainnetAccountId, mainnetAccountPrivateKey)。

{% content-ref url="../../sdks-and-apis/sdks/accountsand-hbar/create-an-account.md" %}
[create-an-account.md](../../sdks-and-apis/sdks/accountsand-hbar/create-an-account.md)
{% endcontent-ref %}
