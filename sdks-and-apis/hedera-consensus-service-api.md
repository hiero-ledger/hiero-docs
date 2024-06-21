---
description: >-
  The Hedera Consensus Service (HCS) gRPC API is a public mirror node managed by
  Hedera. It offers the ability to subscribe to HCS topics and receive messages
  for the topic subscribed.
---

# Hedera Consensus Service gRPC API

{% hint style="warning" %}
#### Important Notice: Deprecation of the Insecure Hedera Consensus Service (HCS) Mirror Node Endpoints

We are phasing out the legacy Hedera Consensus Service (HCS) mirror node endpoints. The APIs have transitioned from the legacy _hcs_.&#x20;

`<env>.mirrornode.hedera.com:5600` endpoints to the new `<env>.mirrornode.hedera.com:443` endpoints.

For more details, please read our [blog post announcement](https://hedera.com/blog/deprecation-of-the-insecure-hedera-consensus-service-hcs-mirror-node-endpoints).
{% endhint %}

{% hint style="info" %}
**HCS Mirror Node Endpoints:**\
**PREVIEWNET:** previewnet.mirrornode.hedera.com:443\
**TESTNET**: testnet.mirrornode.hedera.com:443\
**MAINNET**: mainnet.mirrornode.hedera.com:443
{% endhint %}

{% hint style="warning" %}
Requests for the public mainnet mirror node are throttled at 100 requests per second (rps). This may change in the future depending upon performance or security considerations. At this time, no authentication is required.
{% endhint %}

Community-supported mirror node information can be found here:

{% content-ref url="../networks/community-mirror-nodes.md" %}
[community-mirror-nodes.md](../networks/community-mirror-nodes.md)
{% endcontent-ref %}

## Build a Mirror Node Client

If you building your client with a predefined Hedera network (previewnet, testnet, mainnet), you do not need to define the mirror client as it is built in. If you would like to modify the mirror node client, you can use <mark style="color:purple;">`Client.<network>.setMirrorNetwork(<network>)`</mark>.

{% tabs %}
{% tab title="Java" %}
{% code title="Java" %}
```java
// You will need to upgrade to v2.0.6 or higher
Client client = Client.forMainnet();
client.setMirrorNetwork(Collections.singletonList("mainnet.mirrornode.hedera.com:443"))
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// You will need to upgrade to v2.0.23 or higher
const client = Client.forMainnet()
client.setMirrorNetwork("mainnet.mirrornode.hedera.com:443")
```
{% endtab %}

{% tab title="Go" %}
```go
hedera.ClientForMainnet()
client.SetMirrorNetwork([]string{"mainnet.mirrornode.hedera.com:443"})
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**Concurrent Subscription Limit**\
A single client can make a maximum of **5** concurrent subscription calls per connection.
{% endhint %}

### Subscribe to a topic

Please click the link below to see how you can subscribe to a topic.

{% content-ref url="sdks/consensus-service/get-topic-message.md" %}
[get-topic-message.md](sdks/consensus-service/get-topic-message.md)
{% endcontent-ref %}
