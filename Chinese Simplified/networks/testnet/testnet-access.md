# Testnet 账户

您将需要一个 Hedera **Testnet** 或 **Previewnet** 帐户来与任何网络服务(加密货币、共识、令牌、文件和智能合同)交互并支付费用。 您的 Hedera Testnet 账户持有HBAR 余额，用于转账到其他账户或支付网络服务。

### 第 1 步：创建Hedera 门户网站

To create your Hedera Portal profile, register [here](https://portal.hedera.com/register) and complete your profile. 一旦您完成了配置，请从网络下拉菜单中选择测试网络 (Testnet 或 Previewnet) 并创建一个帐户。 帐户创建后，您的门户帐户将自动收到1000 HBAR。&#x20

您可以轻松地将您的 `accountId`, `public key` 和 `private key` 信息复制到剪贴板以便在配置您的 SDK 环境测试时使用。

{% hint style="info" %}
_**注意：** 当预览网或测试网被重置时，将生成新账户 ID。 在预览网和测试网重置期间，公钥和私钥对应保持一致。 If you receive an invalid account ID response from the network it is likely you need to update your previewnet or testnet account ID._ [_Create an Personal Access Token/API key_](../../tutorials/more-tutorials/how-to-create-a-personal-access-token-api-key-on-the-hedera-portal.md) _to streamline the process of account recreation and management when there is a network reset._&#x20;
{% endhint %}

![](../../.gitbook/assets/portal-testnet-dashboard.png)

您现在已准备好在测试网上构建您的应用程序！
