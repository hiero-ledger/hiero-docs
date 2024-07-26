# 帐户创建

在Hedera分类账上创建新账户，向网络提交交易并支付交易费以创建该账户。 创建帐户的交易费用包括使用网络资源所需的费用。 在节点之间达成共识，并在整个网络中分享数据。 #x20

您需要访问一个现有的帐户，拥有足够的HBAR 以支付交易费来创建帐户。 假定您没有访问现有账户的权限。 在这种情况下，您可以使用支持的钱包或访问 [Hedera 开发者门户](https://portal.hedera.com/register) 来创建一个帐户。 你也可以让一个现有的 Hedera 帐户的朋友为你慷慨创建一个帐户。 应用程序可以签出"[自动创建帐户](自动创建帐户.md)"功能，让Hedera用户帐户免费。&#x20

当创建一个帐户时，它会存储在Hedera网络上的状态中。 当前状态可以从分类账中查询，并在 [Network Explorer] (../../networks/community miror-nodes.md) 中查看。 每个账户至少有一个公钥和私钥对。 账户上的私钥用于签名和授权涉及账户的交易。 要查看可以设置为帐户的属性，请查看"[帐户属性](帐户属性.md)"部分。

可以通过以下任何方法创建一个帐户。 要使用 SDK 创建帐户，您需要访问一个现有帐户来支付交易费才能创建一个新帐户。

{% hint style="warning" %}
:larg\_orange\_diamond: 支持的钱包可能支持或不支持创建测试网和预览网帐户。
{% endhint %}

<table data-view="cards"><thead><tr><th align="center"></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Hedera 开发者门户网站</strong></td><td><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span> mainnet<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> testnet<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> previewnet</td><td><a href="https://portal.hedera.com/">https://portal.hedera.com/</a></td></tr><tr><td align="center">             <strong>钱包</strong>               </td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> mainnet<br><span data-gb-custom-inline data-tag="emoji" data-code="1f536">:larg_orange_diamond:</span> testnet<br><span data-gb-custom-inline data-tag="emoji" data-code="1f536">:larg_orange_diamond:</span> previewnet</td><td><a href="../../networks/mainnet/mainnet-access.md">mainnet-access.md</a></td></tr><tr><td align="center"><strong>SDK</strong></td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> mainnet<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> testnet<br><span data-gb-custom-inline data-tag="emoji" data-code="2705">:check_mark_buton:</span> previewnet<br></td><td><a href="../../sdks-and-apis/deprecated/sdks/cryptocurrency/">加密货币</a></td></tr></tbody></table>
