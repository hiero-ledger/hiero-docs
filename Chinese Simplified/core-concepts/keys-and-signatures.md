# 密钥和签名

## 关键类型：ECDSA vs Ed25519

一个密钥可以是支持系统 [Ed25519](../supportand-community/glossary.md#public key)的[公用key](../support-community/glossary.md#ed25519)，[ECDSA secp256k1](../supportand-community/glossary.md#ecdsa-secp256k1)，或[智能合同]的ID(../support-community/glossary.md#smart-contract). 相应的算法生成相互独特的公钥和私钥。 公共密钥可以在[网络探索者](../supportand-community/glossary.md#network-explorer)或[REST APIs](../supportand-community/glossary.md#rest-api)中共享并对其他网络用户可见。 [私钥](../support-community/glossary.md#private-key)是所有者的秘密，并且授权所有者修改实体 (帐户、令牌等)。&#x20

私钥 _只有丢失后才能恢复，如果创建时您可以访问关联的恢复短语。 键值是可变的，可以在为实体设置后更新。 一般而言，您需要当前的密钥来签名交易以更新密钥。&#x20

### 选择 ECDSA 和 Ed25519 键

ECDSA和 ED25519 键之间的选择主要取决于您的特定用例：

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>ECDSA</strong></td><td><strong>Ed25519</strong></td></tr><tr><td><strong>使用案例</strong></td><td>如果您想要使用 <a href="../support-and-community/glossary.md#metamask">MetaMask</a> 或需要更多的 EVM 开发者配刀的支持，理想会实现。 由于相关的EVM地址，申请与Ethereum 或EVM兼容网络接口的应用。</td><td>如果关键长度、安全性和性能很重要，则优先考虑。 对于同等程度的安全而言，ECDSA 公用钥匙的长度是同一水平的两倍。</td></tr></tbody></table>

{% hint style="info" %}
**注意**：Hedera wallets such [HashPack](https://www.hashpack.app/) 支持这两种密钥类型。
{% endhint %}

## 关键结构

Hedera 支持以下关键结构类型：

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th width="240"></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Description</strong></td><td><strong>示例</strong></td></tr><tr><td><strong>简单密钥</strong></td><td>一个帐户上的单个密钥。</td><td><strong>Account</strong> <strong>Key</strong> <br>       { <br>           Key 1 <br>        }<br>Only one key is required to sign for the account.</td></tr><tr><td><strong>密钥列表</strong></td><td>密钥列表中的所有密钥都需要签名涉及该帐户的交易。</td><td><strong>账户密钥</strong><br>     <strong>KeyList (3/3)</strong> <br> <br>               Key 1 <br>               Key 2 <br>               Key 3 <br>          }<br>列表中的所有三个密钥都必须为该账户签字。</td></tr><tr><td><strong>阈值密钥</strong></td><td>需要一个定义为阈值的子集来签名涉及到账户的交易总数量的密钥。</td><td><strong>账户密钥</strong><br>      <strong>阈值(1/3)</strong> <br> <br>              Key 1 <br>              Key 2 <br>              Key 3 <br>          }<br>密钥列表中的三个密钥中的一个是必须为该账户签字。</td></tr></tbody></table>

{% hint style="info" %}
🔔 密钥结构可以嵌套。 这意味着您可以有一个更复杂的密钥系统, 键盘中的密钥列表, 键盘列表中的阈值等. 嵌套密钥列表的示例可以查看 [here](https://hashscan.io/mainnet/adminKey/0.0.2)。
{% endhint %}

所有交易类型都支持指定一个关键字段的上述关键结构。 交易若要成功，所提供的签名必须符合定义的关键结构要求。

## 常见问题

<details>

<summary>Hedera中的一个密钥是什么？</summary>

Hedera 中的一个密钥可以是 [ED25519](../support-community/glossary.md#publickey)等支持系统的[公用钥匙](../support-community/glossary.md#publickey)。 d#ed25519), [ECDSA secp256k1](../supportand-community/glossary.md#ecdsa-secp256k), 或 [智能合同]的ID(../supportand-community/glossary.md#smart-contract). 相应的算法生成相互独特的公钥和私钥。 公共密钥可以在[网络探索者](../supportand-community/glossary.md#network-explorer)或REST API中共享并对其他网络用户可见。 [私人密钥](../support-community/glossary.md#private-key)是保密的，并授权所有者修改实体(帐户、令牌等)。

</details>

<details>

<summary>What happens if I lose my private key?</summary>

私钥只有在用您可以访问的关联恢复短语创建时才能恢复。 在您的私人密钥允许访问以修改您的Hedera 实体，如帐户和代币时，保持它们的安全性是至关重要的。

</details>
