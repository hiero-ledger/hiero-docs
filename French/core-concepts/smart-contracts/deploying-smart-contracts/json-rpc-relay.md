# Relais JSON-RPC

Le [relais JSON-RPC Hedera](https://github.com/hashgraph/hedera-json-rpc-relay) est un projet open-source implémentant la norme Ethereum JSON-RPC. Le relais JSON-RPC permet aux développeurs d'interagir avec les nœuds Hedera en utilisant des outils Ethereum familiers. Cela permet aux développeurs et aux utilisateurs d'Ethereum de déployer, de interroger et d'exécuter des contrats comme ils le feraient habituellement. Découvrez l'interactive[ Spécifications OpenRPC](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/hashgraph/hedera-json-rpc-relay/main/docs/openrpc. son\&uiSchema%5BappBar%5D%5Bui:splitView%5D=false\&uiSchema%5BappBar%5D%5Bui:input%5D=false\&uiSchema%5BappBar%5D%5Bui:examplesDropdown%5D=false) et une simple [liste des terminaux](https://github. om/hashgraph/hedera-json-rpc-relay/blob/main/docs/rpc-api.md).&#x20

## Places décimales HBAR

Le relais RPC JSON Hedera **`msg.value`** utilise 18 décimales lorsqu'il renvoie HBAR. En conséquence, la valeur **`gasPrice`** retourne 18 décimales puisqu'elle n'est utilisée que par le Relais RPC JSON. Reportez-vous à la [page HBAR](../../../sdks-and-apis/sdks/hbars.md) pour une liste des API Hedera et des décimales qu'elles retournent.&#x20

## Relais JSON-RPC hébergés par la communauté

N'importe qui dans la communauté peut configurer son propre relais RPC JSON que les applications peuvent utiliser pour déployer, interroger et exécuter des contrats intelligents. La liste des relais et des terminaux RPC Hedera JSON hébergés par la communauté pour previewnet, testnet, et le réseau principal se trouve dans le tableau ci-dessous et dans leurs docs ou sites Web associés. #x20

#### Points de terminaison du relais JSON-RPC

<table><thead><tr><th width="132">Réseau</th><th width="96" align="center">ID de la chaîne</th><th width="266" align="center">Hashio RPC URL</th><th align="center">3ème URL RPC web</th></tr></thead><tbody><tr><td><strong>Réseau Principal</strong></td><td align="center">295</td><td align="center"><a href="https://mainnet.hashio.io/api">https://mainnet.hashio.io/api</a></td><td align="center"><a href="https://295.rpc.thirdweb.com">https://295.rpc.thirdweb.com</a></td></tr><tr><td><strong>Testnet</strong></td><td align="center">296</td><td align="center"><a href="https://testnet.hashio.io/api">https://testnet.hashio.io/api</a></td><td align="center"><a href="https://296.rpc.thirdweb.com">https://296.rpc.thirdweb.com</a></td></tr><tr><td><strong>Aperçu net</strong></td><td align="center">297</td><td align="center"><a href="https://previewnet.hashio.io/api">https://previewnet.hashio.io/api</a></td><td align="center"><a href="https://297.rpc.thirdweb.com">https://297.rpc.thirdweb.com</a></td></tr></tbody></table>

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Hashio</strong></td><td><a href="../../../.gitbook/assets/hashio (1).png">hashio (1).png</a></td><td><a href="https://swirldslabs.com/hashio/">https://swirldslabs.com/hashio/</a></td></tr><tr><td align="center"><strong>Arkhia</strong></td><td><a href="../../../.gitbook/assets/arkhia-logo.png">arkhia-logo.png</a></td><td><a href="https://www.arkhia.io/features/#api-services">https://www.arkhia.io/features/#api-services</a></td></tr><tr><td align="center"><strong>Validation du Cloud</strong></td><td><a href="../../../.gitbook/assets/validation cloud logo.png">validation du cloud logo.png</a></td><td><a href="https://docs.validationcloud.io/about/hedera/json-rpc-relay-api">https://docs.validationcloud.io/about/hedera/json-rpc-relay-api</a></td></tr><tr><td align="center"><strong>troisième web</strong></td><td><a href="../../../.gitbook/assets/thirdweb-logo.jpg">troisième-logo.jpg</a></td><td><a href="https://thirdweb.com/hedera">https://thirdweb.com/hedera</a></td></tr></tbody></table>

{% hint style="info" %}
**Note :** Si vous voulez ajouter votre propre relais JSON-RPC hébergé à cette liste, veuillez ouvrir un problème dans le [dépôt GitHub de Hedera doc](https://github. om/hashgraph/hedera-docs). Veuillez vous assurer de visiter les sites hébergés par la communauté pour examiner toutes les limitations spécifiques à leur instance.&#x20;
{% endhint %}

{% content-ref url="../../../tutorials/more-tutorials/json-rpc-connections/" %}
[json-rpc-connections](../../../tutorials/more-tutorials/json-rpc-connections/)
{% endcontent-ref %}

## Foire Aux Questions

<details>

<summary>Are there Community hosted relays?</summary>

- [**Hashio**](https://swirldslabs.com/hashio/)&#x20
- [**Arkhia**](https://www.arkhia.io/features/#api-services)
- [**Nuage de validation**](https://docs.validationcloud.io/about/hedera/json-rpc-relay-api)

</details>

<details>

<summary>How do I connect to the Hedera Network over RPC?</summary>

Le guide de configuration pour se connecter au réseau Hedera via RPC se trouve à [here](../../tutorials/more-tutorials/json-rpc-connections/).

</details>

<details>

<summary>Where can I find the Hedera JSON-RPC relay endpoints?</summary>

Les points de terminaison pour previewnet, testnet et mainnet peuvent être trouvés sur [Hashio](https://swirldslabs.com/hashio/), accessible via le site web [Swirlds Labs](https://swirldslabs.com/). N'hésitez pas à vous joindre à la discussion sur [Stack Overflow](https://stackoverflow.com/questions/76153239/how-can-i-connect-to-hedera-testnet-over-rpc/76153290#76153290) pour plus de questions.

</details>

<details>

<summary><strong>Comment Hedera gère-t-il les décimales en HBAR et les prix du gaz ?</strong></summary>

Le relais JSON-RPC `msg.value` utilise 18 décimales lorsqu'il renvoie HBAR. La valeur `gasPrice` retourne également 18 décimales. _Découvrez le_ [page _HBAR_](../../../sdks-and-apis/sdks/hbars.md) _pour la liste complète des API Hedera et leur représentation décimale._&#x20

</details>

<details>

<summary>How can I contribute or log errors?</summary>

Pour contribuer ou enregistrer des erreurs, veuillez vous référer au [Guide de contribution](../../../support-and-community/contributing-guide.md) et les soumettre comme problèmes dans le [dépôt GitHub](https://github.com/hashgraph/hedera-json-rpc-relay/issues).

</details>
