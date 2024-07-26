# JSON-RPC 继电器

[Hedera JSON-RPC 继电器](https://github.com/hashgraph/hedera-json-rpc-remedy)是一个开源项目，执行以太坊JSON-RPC 标准。 JSON-RPC 继电器允许开发人员使用熟悉的 Ethereum 工具与Hedera 节点交互。 这使得Etherum开发者和用户能够部署、查询和执行他们通常要签订的合同。 查看交互式[ OpenRPC Specification](https://playground.open rpc.org/?schemaUrl=https://raw.githubusercontent.com/hashgraph/hedera-json-rpc-reiny/main/docs/openrpc)。 son\&uiSchema%5BAppBar%5D%5Bui:splitView%5D=false\&uiSchema%5BappBar%5D%5Bui:input%5D=false\&uiSchema%5BappBar%5D%5Bui:exampown%5D=false) 和一个简单的 [list of endpoints](https://github)。 om/hashgraph/hedera-json-rpc-reiny/blob/main/docs/rpc-api.md).&#x20

## HBAR 十进制位置 &#x20

Hedera JSON RPC 继电器 **`msg.value`** 返回HBAR时使用18个小数。 因此，**`gasPrice`** 值返回18个小数点，因为它只在JSONRPC 继电器中使用。 请参阅[HBAR page](../../../sdks-and/sdks/hbars.md)的Hedera API列表和他们返回的十进制位置。&#x20

## 社区托管JSON-RPC 继电器

社区中的任何人都可以建立自己的JSON RPC 继电器，应用程序可以用来部署、查询和执行智能合同。 社区托管的Hedera JSON RPC 继电器和预览网端口、测试网的列表， 可在下表及其相关文件或网站上查找。 #x20

#### JSON-RPC 中继端点

<table><thead><tr><th width="132">网络</th><th width="96" align="center">链表 ID</th><th width="266" align="center">Hashio RPC URL</th><th align="center">thirdweb RPC URL</th></tr></thead><tbody><tr><td><strong>Mainnet</strong></td><td align="center">295</td><td align="center"><a href="https://mainnet.hashio.io/api">https://mainnet.hashio.io/api</a></td><td align="center"><a href="https://295.rpc.thirdweb.com">https:////295.rpc.thirdweb.com</a></td></tr><tr><td><strong>Testnet</strong></td><td align="center">296</td><td align="center"><a href="https://testnet.hashio.io/api">https://testnet.hashio.io/api</a></td><td align="center"><a href="https://296.rpc.thirdweb.com">https:///296.rpc.thirdweb.com</a></td></tr><tr><td><strong>预览网</strong></td><td align="center">297</td><td align="center"><a href="https://previewnet.hashio.io/api">https://previewnet.hashio.io/api</a></td><td align="center"><a href="https://297.rpc.thirdweb.com">https:///297.rpc.thirdweb.com</a></td></tr></tbody></table>

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Hashio</strong></td><td><a href="../../../.gitbook/assets/hashio (1).png">hashio (1).png</a></td><td><a href="https://swirldslabs.com/hashio/">https://swirldslabs.com/hashio/</a></td></tr><tr><td align="center"><strong>Arkhia</strong></td><td><a href="../../../.gitbook/assets/arkhia-logo.png">arkhia-logo.png</a></td><td><a href="https://www.arkhia.io/features/#api-services">https://www.arkhia.io/features/#api-services</a></td></tr><tr><td align="center"><strong>验证云</strong></td><td><a href="../../../.gitbook/assets/validation cloud logo.png">验证云logo.png</a></td><td><a href="https://docs.validationcloud.io/about/hedera/json-rpc-relay-api">https://docs.validationcloud.io/about/hedera/json-rpc-relie-api</a></td></tr><tr><td align="center"><strong>第三网络</strong></td><td><a href="../../../.gitbook/assets/thirdweb-logo.jpg">thirdweb-logo.jpg</a></td><td><a href="https://thirdweb.com/hedera">https://thirdweb.com/hedera</a></td></tr></tbody></table>

{% hint style="info" %}
**注意：** 如果您想要将您自己托管的 JSON 中继添加到这个列表中。 请在 [Hedera docs GitHub 仓库](https://github) 中打开一个问题。 om/hashgraph/hedera-docs)。 请务必访问社区托管的网站以审查针对其实例的任何限制。&#x20;
{% endhint %}

{% content-ref url="../../../../tutorials/more-tutorials/json-rpc-connections/" %}
[json-rpc-connections](../../../tutorials/more-tutorials/json-rpc-connections/)
{% endcontent-ref %}

## 常见问题

<details>

<summary>Are there Community hosted relays?</summary>

- [**Hashio**](https://swirldslabs.com/hashio/)&#x20
- [**Arkhia**](https://www.arkhia.io/features/#api-services)
- [**Validation Cloud**](https://docs.validationcloud.io/about/hedera/json-rpc-continy-api)

</details>

<details>

<summary>How do I connect to the Hedera Network over RPC?</summary>

通过 RPC 连接到 Hedera 网络的配置指南可以找到 [here](../../../tutorials/more-tutorials/json-rpc-connections/)。

</details>

<details>

<summary>Where can I find the Hedera JSON-RPC relay endpoints?</summary>

The endpoints for previewnet, testnet, and mainnet can be found on [Hashio](https://swirldslabs.com/hashio/), accessible through the [Swirlds Labs](https://swirldslabs.com/) website. 请随时参加关于[堆栈溢出](https://stackoverflow.com/questions/76153239/how-can-i-connect-to-hedera-testnet-overrpc/76153290#76153290)的讨论，以了解更多问题。

</details>

<details>

<summary><strong>How does Hedera handle decimals in HBAR and gas prices?</strong></summary>

JSON-RPC 继电器 `msg.value` 使用18位小数值返回HBAR。 `gasPrice`值也返回18位小数。 查看[_HBAR page_](../../../../sdks-and/sdks/hbars.md) _完整的Hedera API及其十进制的代表权列表。_&#x20

</details>

<details>

<summary>How can I contribute or log errors?</summary>

若要提交或记录错误，请参阅[贡献指南](../../../supportand-community/contributing-guide.md)，并将其作为问题提交到[GitHub 仓库](https://github.com/hashgraph/hedera-json-rpc-remedy/issues)。

</details>
