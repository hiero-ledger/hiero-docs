# 一. 导言

通过这个短的 _Getting Started_ 系列，您将学习创建账户、传输HBAR、签名和向Hedera Testnet提交交易的基本知识。 Hedera Testnet允许您在非生产环境中玩Hedera API。 你会看到开始使用我们的 [Hedera SDKs] (../sdks-andapis/) 编程语言是多么容易。 以下是创建测试网账户和接收HBAR 以构建、测试和部署Hedera dApp的两个路径：

**:right_箭头:** [**Hedera Faucet**](introduction.md#hedera-faucet)

**:right_箭头:** [**Hedera Developer Portal**](introduction.md#hedera-developer-portal-profile)

***

### Hedera Faucet

Hedera faucet允许您匿名接收测试网，无需创建开发者门户帐户。 若要使用匿名动物群，请访问[Hedera faucet](https://portal.hedera)。 om/faucet) 并连接您的钱包，或输入EVM钱包地址或Hedera账户ID来启动此进程。

<figure><img src="../.gitbook/assets/faucet-receive-hbar.png" alt=""><figcaption></figcaption></figure>

输入EVM地址将方便使用[自动帐户创建流程](../corre-concepts/accounts/auto-account-creatation.md#auto-account-creation-evm-address-alias)创建帐户。 复制并保存新的 Hedera 帐户 ID 和您管理的私钥，以便在下一个页面上设置编码环境。

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt=""><figcaption></figcaption></figure>

动物群的最大分销限制为每24小时**100 HBAR**。

<figure><img src="../.gitbook/assets/faucet-wallet-timer.png" alt=""><figcaption></figcaption></figure>

如果您在24小时内尝试超过一次，您将收到错误消息，并且在您的帐户有资格填充时提示您返回。

<figure><img src="../.gitbook/assets/faucet-receive-error.png" alt=""><figcaption></figcaption></figure>

***

### Hedera 开发者门户网站

Hedera 开发者门户允许您创建测试网账户，在创建时接收HBAR。 访问 [Hedera 开发者门户](https://portal.hedera.com/register) 并遵循创建账户的指示。

<figure><img src="../.gitbook/assets/portal testnet account.png" alt="Screenshot of the Hedera Developer portal (portal.hedera.com/register) account creation page."><figcaption></figcaption></figure>

帐户创建后，您的门户测试网帐户将自动收到\*\*1000HBAR， \* 和您将看到您的帐户 ID 和来自Portal仪表盘的密钥对(见下面的图像)。 为编码环境设置步骤复制您的帐户 ID 和 DER编码的私钥。

<figure><img src="../.gitbook/assets/faucet-der-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
**注意**：开发者门户网站上的测试网账户每日充值限制为 1000 HBAR。 帐户 _**做不**_ 自动充值。 为了保持您的余额，您必须每24小时通过Portal仪表板手动请求一次填充。

为了清晰起见，每次您都不会在您的账户余额中添加1000个HBAR。 相反，如果您的账户余额低于此阈值，您的账户余额将充值至 1000 HBAR。 例如，如果您的账户余额是 500 HBAR， 填写只能添加足够的 HBAR 来将您的余额提升到 1000 HBAR，而不是额外的 1000 HBAR。&#x20;
{% endhint %}
