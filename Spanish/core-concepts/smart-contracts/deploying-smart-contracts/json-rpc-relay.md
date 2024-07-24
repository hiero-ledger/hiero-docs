# Relé JSON-RPC

El [Relé Hedera JSON-RPC](https://github.com/hashgraph/hedera-json-rpc-relay) es un proyecto de código abierto que implementa el estándar Ethereum JSON-RPC. El relé JSON-RPC permite a los desarrolladores interactuar con nodos Hedera usando herramientas conocidas de Ethereum. Esto permite a desarrolladores y usuarios de Ethereum desplegar, consultar y ejecutar contratos como normalmente lo harían. Echa un vistazo a la especificación interactiva[ OpenRPC Specification](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/hashgraph/hedera-json-rpc-relay/main/docs/openrpc. son\&uiSchema%5BappBar%5D%5Bui:splitView%5D=false\&uiSchema%5BappBar%5D%5Bui:input%5D=false\&uiSchema%5BappBar%5D%5Bui:examplesDropdown%5D=false) y una simple [lista de endpoints](https://github. om/hashgraph/hedera-json-rpc-relay/blob/main/docs/rpc-api.md).&#x20

## Puntos decimales HBAR&#x20

El relé Hedera JSON RPC **`msg.value`** usa 18 decimales cuando devuelve HBAR. Como resultado, el valor **`gasPrice`** devuelve 18 decimales puesto que sólo se utiliza desde el Relé RPC JSON. Consulte la [página HBAR](../../../sdks-and-apis/sdks/hbars.md) para una lista de APIs de Hedera y los decimales que regresan.&#x20

## Relés JSON-RPC alojados en la comunidad

Cualquiera en la comunidad puede configurar su propio repetidor JSON RPC que las aplicaciones pueden utilizar para desplegar, consultar y ejecutar contratos inteligentes. La lista de repetidores RPC Hedera JSON alojados en la comunidad y puntos finales para previewnet, testnet, y mainnet pueden encontrarse en la tabla de abajo y en sus documentos asociados o sitio web. 20 x

#### Puntos de relé JSON-RPC

<table><thead><tr><th width="132">Red</th><th width="96" align="center">ID de Cadena</th><th width="266" align="center">Hashio RPC URL</th><th align="center">URL RPC de tercera web</th></tr></thead><tbody><tr><td><strong>Mainnet</strong></td><td align="center">295</td><td align="center"><a href="https://mainnet.hashio.io/api">https://mainnet.hashio.io/api</a></td><td align="center"><a href="https://295.rpc.thirdweb.com">https://295.rpc.thirdweb.com</a></td></tr><tr><td><strong>Testnet</strong></td><td align="center">296</td><td align="center"><a href="https://testnet.hashio.io/api">https://testnet.hashio.io/api</a></td><td align="center"><a href="https://296.rpc.thirdweb.com">https://296.rpc.thirdweb.com</a></td></tr><tr><td><strong>Vista previa</strong></td><td align="center">297</td><td align="center"><a href="https://previewnet.hashio.io/api">https://previewnet.hashio.io/api</a></td><td align="center"><a href="https://297.rpc.thirdweb.com">https://297.rpc.thirdweb.com</a></td></tr></tbody></table>

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>Hashio</strong></td><td><a href="../../../.gitbook/assets/hashio (1).png">hashio (1).png</a></td><td><a href="https://swirldslabs.com/hashio/">https://swirldslabs.com/hashio/</a></td></tr><tr><td align="center"><strong>Arkhia</strong></td><td><a href="../../../.gitbook/assets/arkhia-logo.png">arkhia-logo.png</a></td><td><a href="https://www.arkhia.io/features/#api-services">https://www.arkhia.io/features/#api-services</a></td></tr><tr><td align="center"><strong>nube de validación</strong></td><td><a href="../../../.gitbook/assets/validation cloud logo.png">validación de nube logo.png</a></td><td><a href="https://docs.validationcloud.io/about/hedera/json-rpc-relay-api">https://docs.validationcloud.io/about/hedera/json-rpc-relay-api</a></td></tr><tr><td align="center"><strong>tercera web</strong></td><td><a href="../../../.gitbook/assets/thirdweb-logo.jpg">thirdweb-logo.jpg</a></td><td><a href="https://thirdweb.com/hedera">https://thirdweb.com/hedera</a></td></tr></tbody></table>

{% hint style="info" %}
**Nota:** Si quieres añadir tu propio repetidor JSON-RPC alojado a esta lista, por favor abra un problema en el [repositorio de GitHub de Hedera docs](https://github. om/hashgraph/hedera-docs). Por favor, asegúrese de visitar los sitios web alojados en la comunidad para revisar cualquier limitación específica a su instancia.&#x20;
{% endhint %}

{% content-ref url="../../../tutorials/more-tutorials/json-rpc-connections/" %}
[json-rpc-connections](../../tutorials/more-tutorials/json-rpc-connections/)
{% endcontent-ref %}

## FAQ

<details>

<summary>Are there Community hosted relays?</summary>

- [**Hashio**](https://swirldslabs.com/hashio/)&#x20
- [**Arkhia**](https://www.arkhia.io/features/#api-services)
- [**Validation Cloud**](https://docs.validationcloud.io/about/hedera/json-rpc-relay-api)

</details>

<details>

<summary>¿Cómo me conecto a la red Hedera a través de RPC?</summary>

La guía de configuración para conectarse a la Red Hedera a través de RPC se puede encontrar [here](../../tutorials/more-tutorials/json-rpc-connections/).

</details>

<details>

<summary>Where can I find the Hedera JSON-RPC relay endpoints?</summary>

Los endpoints para previewnet, testnet y mainnet se pueden encontrar en [Hashio](https://swirldslabs.com/hashio/), accesible a través del sitio web [Swirlds LATI](https://swirldslabs.com/). Siéntete libre de unirte a la discusión en [Stack Overflow](https://stackoverflow.com/questions/76153239/how-can-i-connect-to-hedera-testnet-over-rpc/76153290#76153290) para más preguntas.

</details>

<details>

<summary><strong>¿Cómo maneja Hedera los decimales en HBAR y los precios del gas?</strong></summary>

El Relé JSON-RPC `msg.value` usa 18 decimales cuando devuelve HBAR. El valor `gasPrice` también devuelve 18 decimales. _Échale un vistazo a la_ [_página HBAR_](../../sdks-and-apis/sdks/hbars.md) _para la lista completa de APIs de Hedera y su representación decimal._&#x20

</details>

<details>

<summary>How can I contribute or log errors?</summary>

Para contribuir o registrar errores, por favor consulte la [Guía de contribución](../../../support-and-community/contributing-guide.md) y envíelos como problemas en el [repositorio de GitHub](https://github.com/hashgraph/hedera-json-rpc-relay/issues).

</details>
