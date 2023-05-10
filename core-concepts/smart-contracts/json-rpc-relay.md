# JSON-RPC Relay

The [Hedera JSON-RPC Relay](https://github.com/hashgraph/hedera-json-rpc-relay) is an open-source project that implements the Ethereum JSON-RPC standard. The JSON-RPC relay allows developers to interact with Hedera nodes using familiar Ethereum tools. This allows Ethereum developers and users to deploy, query, and execute contracts as they usually would. Check out the interactable[ OpenRPC Specification](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/hashgraph/hedera-json-rpc-relay/main/docs/openrpc.json\&uiSchema%5BappBar%5D%5Bui:splitView%5D=false\&uiSchema%5BappBar%5D%5Bui:input%5D=false\&uiSchema%5BappBar%5D%5Bui:examplesDropdown%5D=false) and a simple [list of endpoints](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/rpc-api.md). The Hedera JSON-RPC relay implementation is in beta, offers limited functionality today, and is only available to developers.&#x20;

## HBAR decimal places&#x20;

_The JSON RPC Relay **`msg.value`** uses 18 decimals when it returns HBAR. As a result, the **`gasPrice`** value returns 18 decimal places since it is only utilized from the JSON RPC Relay. Refer to the_ [_HBAR page_](../../sdks-and-apis/sdks/hbars.md) _for a list of Hedera APIs and the decimal places they return._&#x20;

## Community Hosted JSON-RPC Relays

Anyone in the community can set up their own JSON RPC relay that applications can use to deploy, query, and execute smart contracts. The endpoints for previewnet, testnet, and mainnet can be found in their associated docs or website. You will find the list of community-hosted Hedera JSON RPC Relays below. If you want to add your hosted JSON RPC relay to this list, please open an issue in the [Hedera docs GitHub repository](https://github.com/hashgraph/hedera-docs). Please be sure to visit the community-hosted websites to review any limitations specific to their instance.&#x20;

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Hashio</strong></td><td><a href="../../.gitbook/assets/hashio (1).png">hashio (1).png</a></td><td><a href="https://swirldslabs.com/hashio/">https://swirldslabs.com/hashio/</a></td></tr><tr><td align="center"><strong>Arkhia</strong></td><td><a href="../../.gitbook/assets/arkhia logo.png">arkhia logo.png</a></td><td><a href="https://www.arkhia.io/features/#api-services">https://www.arkhia.io/features/#api-services</a></td></tr></tbody></table>

## FAQs

<details>

<summary>Are there Community hosted relays?</summary>

* [**Hashio**](https://swirldslabs.com/hashio/)&#x20;
* [**Arkhia**](https://www.arkhia.io/features/#api-services)

</details>

<details>

<summary>How many decimals are there in HBAR?</summary>

_Check out the_ [_HBAR page_](../../sdks-and-apis/sdks/hbars.md) _for the full list of Hedera APIs and their decimal representation._&#x20;

</details>

<details>

<summary>How can I contribute feedback or log errors?</summary>

To contribute feedback or log errors, please refer to the [Contributing Guide](../../support-and-community/contributing-guide.md) and submit them as issues in the [GitHub repository](https://github.com/hashgraph/hedera-json-rpc-relay/issues).

</details>
