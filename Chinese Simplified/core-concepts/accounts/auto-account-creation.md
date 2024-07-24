# 自动创建帐户

自动创建帐户是一个独特的流程，应用程序如钱包和交换，可以即时创建免费的用户“帐户”，即使没有互联网连接。 应用程序可以通过生成一个 \*\*帐户别名来制作这些。 \* 用于指定Hedera交易中账户别名的别名ID 格式包括碎片识别码， realance ID, and account 别名 <mark style="color:purple;">'<shardNum><realmNum><alias></mark> 这是一个替代帐户标识符，与标准帐号 <mark style="color:purple;"><shardId><shardId><realmId><accountNum></mark><mark style="color:blue;"></mark>

账户别名可以是支持的类型之一：

<details>

<summary>公钥</summary>

公共密钥别名可以是 ED25519 或 ECDSA secp256k1 公钥类型。 \
\
**Example**\
\
ECDSA secp256k1 Public Key:\
`02d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
DER Encoded ECDSA secp256k1 Public Key Alias:\
`302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
ECDSA secp256k1 Public Key Alias Account ID: \
`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`
\
\
\
\
EDDSA ED25519 Public Key:\
`1a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
DER Encoded EDDSA ED25519 Public Key Alias:\
`302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
EDDSA ED25519 Public Key Alias Account ID: \
`0.0.302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`

</details>

<details>

<summary>EVM 地址</summary>

EVM 地址别名是通过使用一个 `ECDSA secp256k1` 公共密钥的 32 字节的 `Keccak-256 ` 最右20字节创建的。 这种计算是按照[Ethereum Yellow Paper](https://eferum.github.io/yellowpaper/paper.pdf)所述的方式进行的。 EVM 地址不等同于ECDSA 公用钥匙。 \
\
The acceptable format for Hedera transactions is the EVM Address Alias Account ID. Ethereum 公开地址表示帐户地址的可接受格式是十六进制编码公共地址。 \
\
**Example**\
\
EVM Address: `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Encoded EVM Address: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
EVM Address Alias Account ID: `0.0.b794f5ea0ba39494ce839613fffba74279579268`

</details>

<mark style="color:purple;"><shardNum><shardNum> 。。。<realmNum><alias></mark> 格式只有在 `TransferTransaction`, `AccountInfoQuery`, 和 `AccountBalanceQuery` 事务类型中指定时才能被接受。 如果此格式用于指定任何其他交易类型的帐户，交易将不会成功。&#x20

参考Hedera改进建议： [HIP-583](https://hips.hedera.com/hip/hip-583)

## **自动创建账户流程**

### **1. 创建账户别名**

创建账户别名并将其转换为别名账户 ID 格式。 别名帐户 ID 格式要求将碎片编号和 realm 编号附加到账户别名。 这种形式的帐户纯粹是一个本地帐户，即不在Hedera 网络注册。&#x20

### **2. 将代币存入账户别名 ID**

别名帐户 ID 一旦创建，应用程序可以创建一个交易来为用户传输别名帐户 ID 。 用户可以将HBAR、自定义可互换或不可互换的代币转移到别名账户 ID。 这将促成开立赫德拉官方账户。 当使用自动账户创建流程时，转到别名账户ID的第一个令牌将自动关联到账户。

代币初始传输到别名帐户 ID 将做几件事：

1. 系统将首先创建一个系统启动的交易，在Hedera创建一个新帐户。 这是为了在赫代拉正式创建一个新帐户。 此交易发生了一个 nanosecond 后续的令牌传输交易。&#x20
2. 在创建新帐户后，系统将分配一个新的帐号。 和帐户别名将与帐户一起存储在别名字段状态中。 新账户将在账户备注字段中设置“自动创建账户”。
   - 对于使用公钥别名创建的帐户，帐户将被分配给与别名公钥匹配的帐户公钥。&#x20
   - 对于通过EVM地址别名创建的帐户，该帐户将不会有一个帐户公钥，创建一个[空帐户](自动帐户创建.md#auto-account-creat-evm-address-alias)。
3. 一旦新账户被正式创建，用户实例化的令牌传输交易将会传输令牌到新账户。&#x20
4. 指定用于支付代币转账交易费用的帐户也将在 tinybar中收取帐户创建交易费用。&#x20

上述互动引入了[父母和儿童事务](../transactions-and-queries.md#嵌套-交易)的概念。 父交易是指将代币从发送者帐户转到目的地帐户的交易。 子交易是为创建帐户而启动的交易。 这个概念很重要，因为父交易记录或收据不会返回新的帐号ID。 您必须获得交易记录或收到子交易记录。 父和子交易将共享相同的交易ID，除非子交易有附加值。 &#x20

{% hint style="info" %}
**父交易**：负责将代币转移到别名账户 ID 目的帐户的交易。

**子交易**：创建新账户的交易
{% endhint %}

### **3. 获取新帐号**

您可以通过以下任何方式获取新帐号：

- 请求父交易记录或收据，并设置子交易记录布尔标志等于真。&#x20
- 通过使用父转账交易的交易ID和递增0到1的nonce值来请求交易收据或记录创建交易。
- 在 `AccountInfoQuery` 交易请求中指定帐户别名帐户 ID。 响应将返回帐户的帐户编号ID。
- 检查与代币转账值相等的转账账户的父转账交易记录列表。

## 自动创建账户：EVM地址别名

使用[EVM 地址别名](帐户-properties.md#account-alias-evm-address)创建的自动帐户创建流程导致创建一个 **空帐户**。 Holdo账户是不完整的账户，有一个账户号和别名，但不是一个账户密钥。 空账户可以接受此状态中的账户转账令牌。 它不能从帐户传输令牌或修改任何帐户属性，直到帐户密钥添加完毕。

将空账户更新为完整账户， Hedera 交易需要指定一个交易费用支付者账户。 它还必须与ECDSA 对应EVM 地址别名的私人密钥签署交易。 当空账户成为完整账户时， 您可以使用该账户支付交易费或更新账户属性，因为您可以使用普通的 Hedera 账户。

## 示例：

<details>

<summary>Auto-create an account using a public key alias</summary>

:black\_circle: [Java](https://github.com/hashgraph/hedera-sdk-java/blob/development/examples/src/main/java/AccountAliasExample.java) \
:black\_circle: [JavaScript](https://github.com/hashgraph/hedera-sdk-js/blob/develop/exampes/account-alias.js)
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk/blob/develop/exampes/alias\_id\_example/main.go) &#x20

</details>

<details>

<summary>Auto-create an account using an EVM address (public address) alias</summary>

:black\_circle: [Java ](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AutoCreateAccountTransferTransactionExample.java)\
:black\_circle: [JavaScript ](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/transfer-using-evm-address.js)\
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/account\_create\_token\_transfer/main.go)

</details>
