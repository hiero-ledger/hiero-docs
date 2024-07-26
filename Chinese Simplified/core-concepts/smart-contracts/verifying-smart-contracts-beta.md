# 正在验证智能合同

智能合同验证是验证智能合同字节代码上传到网络符合预期智能合同源文件的过程。 Hedera 网络上的合同需要验证_not_。 但这是最佳做法，也是通过查明可加以利用的弱点来维护合同的安全和完整性所必不可少的。 因为智能合约在部署后是不可改变的。 它还通过证明所部署的字节代码与合同的原始源代码相匹配，从而提高透明度并在用户社区内建立信任。

为了启动验证，您可以使用社区托管的 Hedera 镜像节点探索器，如 [HashScan](https://hashscan.io/) ([Arkhia](https://explorer.arkhia.io/) 和 [Dragon Glass](https://app. ragonglass.me/) 目前不支持此功能，它与 [Sourcify](../../support-community/glossary.md#sourcify)：一个团结源代码和元数据验证工具。 一旦您将文件上传到验证工具。源代码重新编译提交的源代码和元数据文件，对照部署的字节代码检查它们。 如果找到匹配，合同的验证状态将更新为 [_<mark style="color:green;">Full (Perfect) Match</mark>_](https://docs.sourcify。 ev/docs/full vs-partial-match/#full 完美-matches)或[_<mark style="color:green;">Partial Match</mark>_](https://docs.sourcify.dev/docs/fullvs-partial-match/#partial-matches)_<mark style="color:green;">.</mark>_

所有社区托管的 Hedera 镜像节点探索者都可以公开获取验证状态。 To learn what differentiates a _Full (Perfect) Match_ from a _Partial Match_, check out the Sourcify documentation [here](https://docs.sourcify.dev/docs/full-vs-partial-match/).

{% hint style="info" %}
\*\*注意：这是一个初步测试版版本，HashScan 用户界面和 API 功能都已计划在即将到来的更新中得到增强。
{% endhint %}

为了验证，您将需要以下项目：

**:right_箭头:** [**智能合同源代码**](验证智能合同-测试.md#智能合同-源代码)

**:right_箭头:** [**元数据文件**](验证 ying-smart-contracts-beta.md#the-metetdata-file)

**:right_箭头:** [**部署智能合同地址**](验证智能合同-测试.md#部署-智能合同-地址)

***

## 智能合同源代码

这是您智能合约的实际代码，以Solidy书写。 源代码包括合同的所有功能、变量和逻辑。 它对验证过程至关重要，在这个过程中，已部署的字节代码与已编译的源代码相比较。

#### 示例：

简单的 `HelloWorld` Solidity 智能合约：

```solidity
pragma solidity ^0.8.17;

contract HelloWorld {
   // the contract's owner, set in the constructor
   address owner;
   
   // the message we're storing, set in the constructor
   string message;
 
   constructor(string memory message_) {
      // set the owner of the contract for 'kill()'
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

## 元数据文件

当您编译团结智能合约时，它生成一个 JSON 元数据文件。 此文件包含智能合同最初编译时使用的设置。 这些设置可以包括编译器版本、优化细节等等。 元数据文件对于确保验证过程中生成的字节代码匹配部署的字节代码至关重要。&#x20

> _元数据不是EVM速度的一部分，因为它是由编译器和诸如Sourcify之类的工具外部处理的。 查看Sourcify的元数据文档_ [_here_](https://docs.sourcify.dev/docs/metadata/#metadata)_._&#x20

您有生成元数据文件的选项。 每个选项推荐的技能水平载于括号内。 选择最符合您智能合同体验的选项：

<details>

<summary>Remix IDE (初学者)</summary>

在 Remix中创建元数据文件， 编译您的智能合约和编译的艺术品将保存在`artifacts/`目录和 `<dynamic_hash>中。 son元数据文件将在 `articfacts/build-info\` 下用于验证。 或者，您可以从Solidity 编译器选项卡复制并粘贴它。 请查看下面的图片。&#x20

![](../../.gitbook/assets/remix-metadata.png)

更详细的文档 [here](https://remix-ide.readthedocs.io/enura/contract\_metadata.html)

**注意：** 从 Remix 拿走字节代码和元数据，然后在Hedera 上将其部署为 _**完整(完美) 匹配**_。 从 Remix _after _ 部署合同后的 Hedera 中的文本代码和元数据生成一个 _**部分匹配**_ 或 _**。部署并重新编译的字节代码不匹配**_ 错误。 _需要通过在 Remix 中编译的合同进行验证，这只是智能合同的 Solidity 文件。_&#x20

</details>

<details>

<summary>硬帽子(中间)</summary>

若要使用 Hardhat 创建 `.json` 元数据文件，请使用 `npx hardhat compile` 命令编译合同。 编译出来的伪影将保存在`artifacts/`目录中，以及`<dynamic_hash>.json`元数据文件将被保存在`artifacts/build-info`中并用于验证。 参见Sourcify Hardhat元数据文档 [here](https://docs.sourcify.dev/docs/metadata/#hardhat).&#x20

<img src="../../.gitbook/assets/hardhat-contract-artifacts.png" alt="" data-size="original">

**注意**：与Hardhat编译的合同需要验证，这只是`build-info`JSON文件。

</details>

<details>

<summary>Foundry (中间)</summary>

若要使用 Foundy 创建元数据文件，请使用 `forge build` 命令编译合同。 编译输出到 `out/CONTRACT_NAME` 文件夹。 `.json`文件包含`rawMetadata`和`metadata`字段'下合同的元数据。 然而，您不需要手动提取元数据以进行验证。 见 Sourcify Foundry metadata 文档 [here](https://docs.sourcify.dev/docs/metadata/#foundry).&#x20

![](../../.gitbook/assets/foundry-out-folder.png)

**注意**: 与 Foundy 编译的合同验证的要求既是 `.json` 元数据，也是 Solidity 源文件。&#x20

</details>

<details>

<summary><strong>团结编译器 (高级)</strong></summary>

您可以将 "--metadata" 标志传递到 Solidity 命令行编译器以获取元数据输出打印。

```
solc --metatacontracts/HelloWorld.sol
```

将元数据写入一个文件

```
solc --metatacontracts/HelloWorld.sol > metadata.json
```

**注:`solc` vs. `solcjs`**

**📣** `solcjs`将不使用 `--metadata` 标志生成元数据。 仅在 `solc` 中支持此选项。

</details>

`HelloWorld`智能合同的示例元数据文件：

```json
{
  "compiler": "0.8.17",
  "language": "Solidity",
  "abi": [
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "message_",
          "type": "string"
        }
      ],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "inputs": [],
      "name": "get_message",
      "outputs": [
        {
          "internalType": "string",
          "name": "",
          "type": "string"
        }
      ],
      "stateMutability": "view",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "message_",
          "type": "string"
        }
      ],
      "name": "set_message",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
  ]
}
```

***

## 部署智能合同地址

即使Hedera 使用 `0.0.XXXXXXX` 账户ID 格式，但它也符合EVM 兼容性的EVM 地址格式。 一旦你的智能合约部署在赫德拉的网络上，你将收到一个地址，就像下面的地址。 这是你部署的智能合同地址。

#### 示例：

部署EVM智能合同地址的一个例子：

```
0x403925982ef5a6461daba0a103bd6be20b9c4216
```

{% hint style="info" %}
_**注意**：`0.0.XXXXXXX` 智能合同地址格式不能用于验证过程。_
{% endhint %}

***

## 来源地的不同实例：黑德拉的自定义方法

必须指出的是，确实存在多种源代码实例，这些实例是针对不同网络的具体需要而设计的。 Hedera 经营一个独立的源代码实例，不同于公众对Sourcify.dev 实例，如以太扫描和其他以太扫描克隆。

运行一个独立的源代码实例使Hedera能够更多地控制核查过程， 使之适应Hedera生态系统的自定义需要。 例如，在测试网重置后，Hedera 需要能够重置测试网智能合同验证 - Sourcify.dev 无法适应。&#x20

> _**验证智能合同测试网重置：** 当Hedera Testnet重置时，合同必须重新部署和验证。 合同将收到一份新的EVM地址和合同ID。 智能合同需要使用新地址验证。_

要记住的一个基本细节是，在HederaSourcify.dev上验证的智能合同不会自动在Sourcify.dev或反之亦然。 希望在多个平台上识别其智能合同的用户应考虑在这两种情况下进行核查。

***

## 验证您的智能合同

学习如何验证您的智能合同：

{% content-ref url="../../tutorials/smart-contracts/how to verify-a-smart-contract-on-hashscan.md" %}
[how-to-verify-a-smart-contract-on-hashscan.md](../../tutorials/smart-contracts/how to verify-a smart-contract-on-hashscan.md)
{% endcontent-ref %}

***

## 额外资源

**➡️** [**Sourcify Documentation**](https://docs.sourcify.dev/docs/intro)

**:right_箭头:** [**HashScan Network Explorer**](https://hashscan.io/)

**:right_箭头:** [**智能合同验证页面**](https://verify.hashscan.io/)

**➡️** [**Full vs Partial Match Documentation**](https://docs.sourcify.dev/docs/fullvs-partial-match/)

**➡️** [**Hardhat Documentation**](https://hardhat.org/hardhat-runner/docs/guides/compile-contracts)

**➡️** [**Solidity Documentation**](https://docs.soliditylang.org/en/v0.8.23/)
