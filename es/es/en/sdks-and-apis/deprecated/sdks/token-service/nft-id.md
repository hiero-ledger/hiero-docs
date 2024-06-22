# NFT ID

The ID of a non-fungible token (NFT). The NFT ID is composed of the [**token ID**](../../../sdks/token-service/token-id.md) and a **serial number**.

| Constructor                     | Description                  |
| ------------------------------- | ---------------------------- |
| `new NftId(<tokenId>,<serial>)` | Initializes the NftId object |

```java
new NftId()
```

## Methods

{% tabs %}
{% tab title="V1" %}

| Method                   | Type   | Requirement |
| ------------------------ | ------ | ----------- |
| `NftId.fromString(<id>)` | String | Optional    |

{% code title="Java" %}

```java
new NftId(new TokenId(0,0,2), 56562);

// v1.5.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
new NftId(new TokenId(0,0,2), 56562);

// v1.4.10
```

{% endcode %}
{% endtab %}
{% endtabs %}

�
