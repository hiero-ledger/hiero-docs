# 创建智能合同

[智能合同](../../support-community/glossary.md#smart-contract) 是一个不可变的程序，由一组逻辑(状态变量、函数、事件处理器等)组成。 或者可以在[分布式分类账技术](../../supportand-community/glossary.md#分布式分类账-技术-dlt)上部署、储存和访问的规则，例如Hedera。 智能合同中包含的功能可以更新和管理合同状态，并读取部署合同中的数据。 他们还可以在网络上创建和呼叫其他智能合约功能。 智能合同是安全的、可篡改的和透明的，提供了新水平的信任和效率。

Hedera 支持任何编译到Ethereum Mainnet的语言。 这包括 [Solidity](../../supportand-community/glossary.md#solidity) 和 [Vyper](../../supportand-community/glossary.md#vyper)。 这些编程语言编译代码并生成 [bytecode](../../supportand-community/glossary.md#bytecode)，[Etherum虚拟机(EVM)](../../supportand-community/glossary.md#efum-virtal-mye-evm)可以解释和理解。

- 要了解更多有关团结编程语言的信息，请查看团结团队 [here](https://docs.soliditylang.org/en/v0.8.19/)
- 要了解更多关于Vyper的信息，请查阅Vyper团队 [here](https://docs.vyperlang.org/en/stable/)。

此外，还有许多工具可用来编写和编译智能合同，包括流行的 [Remix IDE](../../supportand-community/glossary.md#remix-ide) 和 [Hardhat](../../supportand-community/glossary.md#hardhat)。 Remix IDE是一个方便用户的平台，您可以方便地编写和编译您的智能合约，并执行其他任务，例如调试和测试。 使用这些工具，您可以创建强大和安全的智能合约，用于各种目的。 从简单的代币转账到复杂的金融工具。

**示例**

以下是用团结编程语言编写的智能合同的一个非常简单的例子。 智能合同定义了 `所有者` 和 `message` 状态变量， 还有一些函数，如`set_message` (通过写入更改状态细节)和`get_message` (该函数读取状态细节)。

```solidity
pragma solidity >=0.7.0 <0.8.9;

contract HelloHedera {
    // the contract's owner, set in the constructor
    address owner;

    // the message we're storing
    string message;

    constructor(string memory message_) {
        // set the owner of the contract for `kill()`
        owner = msg.sender;
        message = message_;
    }

    function set_message(string memory message_) public {
        // only allow the owner to update the message
        if (msg.sender != owner) return;
        message = message_;
    }

    // return a string
    function get_message() public view returns (string memory) {
        return message;
    }
}
```

***

## 创建合同时您应该考虑的事项

**自动令牌关联**

自动关联栏位是您批准的一个或多个栏位，这些栏位允许代币在没有对每个代币类型的明确授权的情况下发送到您的合同。 如果未设置此属性。 您必须先批准每个令牌才能通过SDK中的`TokenAssociateTransaction`成功传输到合同。 了解更多关于自动令牌关联 [here](../accounts/account-properties.md#automatic-token-associations)。

在通过 Hedera SDK 配置 `ContractCreateTransaction` API时，此功能是唯一可访问的。 如果您正在Hedera使用Hardhat和Hedera JSON RPC 中的工具，在Hedera部署一个合同， 请注意，此属性无法配置，因为EVM工具与Hedera的独特功能不兼容。

**管理员密钥**

合同可以有一个[admin key](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_create.proto#L117)。 这个概念是赫德拉合同的原生概念，允许更新合同帐户属性。 请注意这不会影响合同 [bytecode](../../supportand-community/glossary.md#bytecode)并且与升级无关。 If the admin key is not set, you will not be able to update the following Hedera native properties (noted in [ContractUpdateTransactionBody](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto) protobuf) for your contract once it is deployed:

- [`autoReform Period`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L78)
- [`memoField`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L88)
- [`max_automatic_token_associations`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L105)
- [`auto_refor_account_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L111)
- [`staked_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L116)
- [`decline_rewid`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L134)

如果您通过硬件等工具部署合同，您不能设置管理员密钥栏。 如果需要的话，可以通过使用Hedera [SDKs]部署合同来设置此字段 (../../sdks-andapis/sdks/)&#x20

**最大合同存储大小**

Hedera 上的合约存储大小限制为16,384,000 密钥对(\~100MB).&#x20

**Rent**

对于今天部署在黑德拉的合同来说，租金尚未启用，您将想熟悉租金概念， 因为它可能会影响网络上维护您的合同状态的费用。 请参阅智能合同租金文档 [here](智能合同-租用.md)。

**交易和天然气费**

Hedera的交易费和与部署合同有关的EVM费。 要查看基本费用列表, 请查看费用页面 [here](../../nets/mainnet/fees/) 和费用估计计算器 [here](https://hedera.com/fees)。

***

## 智能合同常见问题解答

<details>

<summary>智能合约是什么？</summary>

智能合约是一个用EVM可以解释的语言编写的程序。 请参阅 [glossary](../../support-community/glossary.md) 以了解更多关键词和定义。

</details>

<details>

<summary>What programming language does Hedera support for smart contracts?</summary>

赫德拉支持团结和维珀。

</details>

<details>

<summary>我可以使用 Remix IDE 或其他以太坊生态系统工具编写和编译我的智能合约吗？ </summary>

您可以使用 Remix IDE 或其他 Etherum 生态系统工具在Hedera 编写、编译和部署您的智能合同。 查看我们的EVM兼容工具 [here](../../#evm-compatible-tools).&#x20

</details>

<details>

<summary>Where can I find the smart contracts that are deployed to each Hedera network (previewnet, testnet, mainnet)?</summary>

在您最喜欢的信任区块资源管理器上(也称为Heder上的镜像节点探索器)。 要查看社区托管的探索者，请查看网络浏览器工具页 [here](../../networks/community miror-nodes.md).&#x20

</details>

<details>

<summary>Which ERC token standards are supported on Hedera?</summary>

Hedera 支持 ERC-20 和 ERC-721 令牌标准，并且可以找到支持标准的完整列表 [here](tokens-managed by smart-mart-contracts/)。

</details>
