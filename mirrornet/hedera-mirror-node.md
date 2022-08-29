# Hedera Mirror Node

The Hedera Consensus Service (HCS) gRPC API is a public mirror node managed by Hedera. It offers the ability to subscribe to HCS topics and receive messages for the topic subscribed. API docs for the mirror nodes can be found here:

{% content-ref url="../docs/mirror-node-api/" %}
[mirror-node-api](../docs/mirror-node-api/)
{% endcontent-ref %}

### Mainnet:

The non-production public mainnet mirror node serves to help developers build their applications without having to run their own mirror node. For production-ready mainnet mirror nodes please check out **** [Dragonglass](https://dragonglass.me/), [Kabuto](https://kabuto.sh/), or [Ledger Works](http://lworks.io/). When building your Hedera client via SDK, you can use `setMirrorNetwork()` and enter the public mainnet mirror node endpoint. The gRPC API requires TLS. The following SDK versions support TLS:

* **Java:** v2.0.6+
* **JavaScript:** v2.0.23+
* **Go:** v2.1.9+

{% hint style="warning" %}
Requests are throttled at 100 requests per second (rps). This may change in the future depending upon performance or security considerations. At this time,  no authentication is required.
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
//You will need to upgrade to v2.0.6 or higher
Client client = Client.forMainnet();
client.setMirrorNetwork(Collections.singletonList("mainnet-public.mirrornode.hedera.com:443"))
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//You will need to upgrade to v2.0.23 or higher
const client = Client.forMainnet()
client.setMirrorNetwork("mainnet-public.mirrornode.hedera.com:443")
```
{% endtab %}

{% tab title="Go" %}
```go
client := hedera.ClientForMainnet()
client.SetMirrorNetwork([]string{"mainnet-public.mirrornode.hedera.com:443"})
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Mainnet Mirror Node Endpoint:** [mainnet-public.mirrornode.hedera.com](http://mainnet-public.mirrornode.hedera.com/):443\
\
**REST API Mainnet Root Endpoint:**[ **** https://mainnet-public.mirrornode.hedera.com](https://mainnet.mirrornode.hedera.com/)
{% endhint %}

### Testnet:

The endpoints provided below allows developers to access the testnet mirror node which contains testnet transaction data.

{% hint style="info" %}
**HCS Testnet Mirror Node Endpoints:** hcs.testnet.mirrornode.hedera.com:5600&#x20;

**REST API Testnet Root Endpoint:**[ **** https://testnet.mirrornode.hedera.com](https://testnet.mirrornode.hedera.com/)
{% endhint %}

### Previewnet:

The endpoints provided below allows developers to access the previewnet mirror node which contains previewnet transaction data.

{% hint style="info" %}
**HCS Preview Testnet Mirror Node Endpoints:** hcs.previewnet.mirrornode.hedera.com:5600

**REST API Preview Testnet Root Endpoint:** [https://previewnet.mirrornode.hedera.com](https://previewnet.mirrornode.hedera.com)
{% endhint %}

