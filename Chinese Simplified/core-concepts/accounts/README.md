# 账户

账户是与Hedera网络互动和使用Hedera服务的中心起点。 Hedera 账户是一个实体，一个独特的对象类型，存储在分类账中，它持有令牌。 账户可以持有本机Hedera 可互换令牌(HBAR)，自定义可互换令牌，以及Hedera 网络上创建的自定义不可互换令牌(NFT)。&#x20

Hedera 原生令牌 (Sx) 是一个实用令牌，主要用于支付交易费用和与网络互动时的查询费用。 HBAR 符号表示为“每秒”。  应用程序可以参考HBAR作为令牌，但是网络返回tinybars（tscar）的信息，这是HBAR的名称。 100 000 000吨的平均值相当于1千吨。 这包括交易费或账户 HBAR 余额。&#x20

您通过提交更改分类账状态的交易或提交从分类账中读取数据的查询请求，与网络交互。 大多数交易和查询都有[交易费](../../nets/mainnet/fees/)由HBAR收费。 与自定义令牌用户在 Hedera 网络上创建的不同，没有令牌代表 HBAR 令牌。&#x20

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><a href="account-creation.md">         <strong>Account Creation</strong></a></td><td><a href="account-creation.md">帐户创建.md</a></td></tr><tr><td>   <a href="auto-account-creation.md"><strong>Auto Account Creation</strong></a></td><td><a href="auto-account-creation.md">自动创建帐户。md</a></td></tr><tr><td><a href="account-properties.md">     <strong>帐户属性</strong></a></td><td><a href="account-properties.md">帐户属性.md</a></td></tr></tbody></table>

## 常见问题

<details>

<summary>What is a Hedera account?</summary>

Hedera 帐户是Hedera 网络中唯一可以持有令牌的实体。 这些可以是 Hedera 的原生可互换令牌 (HBAR)，自定义可互换令牌或 [不可互换令牌 (NFTs)](../../supportand-community/glossary.md#不可互换令牌-token-nft)。

</details>

<details>

<summary>How are new accounts created on Hedera?</summary>

通过向网络提交交易和支付交易费创建新账户。 您需要访问一个拥有足够的 HBAR 帐户来支付这笔费用。 如果您没有访问现有帐户，您可以使用支持的钱包，请访问 [Hedera 开发者门户](https://portal)。 edera.com/, 或者在应用程序中使用“自动创建帐户”功能。

</details>

<details>

<summary>What is the 'Auto Account Creation' feature?</summary>

[自动创建帐户](自动创建帐户.md) 允许应用程序通过创建帐户别名立即生成免费的用户帐户。&#x20

</details>

<details>

<summary>What is a "hollow" account?</summary>

如果通过[自动创建账户](自动创建账户](自动创建账户)创建账户(自动创建账户地址)，则产生一个“空”账户。 此帐户有一个帐户号码和别名，但没有帐户密钥。 它可以接受代币转账，但在帐户密钥添加、完成之前不能转移代币或修改帐户属性。

</details>
