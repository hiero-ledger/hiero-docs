---
description: >-
  The Hedera Consensus Service (HCS) gRPC API is a public mirror node managed by
  Hedera for the testnet. It offers the ability to subscribe to HCS topics and
  receive messages for the topic subscribed.
---

# gRPC API

{% hint style="info" %}
**HCS Mirror Node Endpoints:**\
**PREVIEWNET:** hcs.previewnet.mirrornode.hedera.com:5600\
**TESTNET**: hcs.testnet.mirrornode.hedera.com:5600\
**MAINNET**: mainnet-public.mirrornode.hedera.com:443
{% endhint %}

{% hint style="warning" %}
Requests for the public mainnet mirror node are throttled at 100 requests per second (rps). This may change in the future depending upon performance or security considerations. At this time, no authentication is required.
{% endhint %}

Community supported mirror node information can be found here:

{% content-ref url="../core-concepts/mirror-nodes/mirrornet/community-mirror-nodes.md" %}
[community-mirror-nodes.md](../core-concepts/mirror-nodes/mirrornet/community-mirror-nodes.md)
{% endcontent-ref %}

## Build a Mirror Node Client

{% tabs %}
{% tab title="V2" %}
If you building your client with a predefined Hedera network (previewnet, testnet, mainnet), you do not need to define the mirror client as it is built in. If you would like to modify the mirror client, you can use [`Client.<network>.setMirrorNetwork(<network>)`](https://docs.hedera.com/guides/docs/sdks/client#1-configure-your-hedera-network).\
\
**Public Mainnet Mirror Node**\
The default mainnet mirror node points to the whitelisted node when using `Client.forMainnet()`. To establish a connection to the public mainnet mirror node you will need to upgrade to one of the following versions of the SDK that now supports TLS connections.

* **Java:** v2.0.6+
* **JavaScript:** v2.0.23+
* **Go:** v2.1.9+

{% code title="Java" %}
```java
//You will need to upgrade to v2.0.6 or higher
Client client = Client.forMainnet();
client.setMirrorNetwork(Collections.singletonList("mainnet-public.mirrornode.hedera.com:443"))
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//You will need to upgrade to v2.0.23 or higher
const client = Client.forMainnet()
client.setMirrorNetwork("mainnet-public.mirrornode.hedera.com:443")
```
{% endcode %}

{% code title="Go" %}
```go
hedera.ClientForMainnet()
client.SetMirrorNetwork([]string{"mainnet-public.mirrornode.hedera.com:443"})
```
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Constructor                    | Description                         |
| ------------------------------ | ----------------------------------- |
| `new MirrorClient(<endpoint>)` | Initializes the MirrorClient object |

{% code title="Java" %}
```java
final MirrorClient mirrorClient = new MirrorClient(MIRROR_NODE_ADDRESS);
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const mirrorClient = new MirrorClient(mirrorNodeAddress);
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**Concurrent Subscription Limit**\
A single client can make a maximum of **5** concurrent subscription calls per connection.
{% endhint %}

### Subscribe to a topic

Please click the link below to see how you can subscribe to a topic.

{% content-ref url="sdks/consensus/get-topic-message.md" %}
[get-topic-message.md](sdks/consensus/get-topic-message.md)
{% endcontent-ref %}
