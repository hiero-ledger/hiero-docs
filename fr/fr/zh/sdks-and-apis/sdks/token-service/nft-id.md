# NFT ID

The ID of a non-fungible token (NFT). The NFT ID is composed of the [**token ID**](token-id.md) and a **serial number**.

| Constructor                     | Description                  |
| ------------------------------- | ---------------------------- |
| `new NftId(<tokenId>,<serial>)` | Initializes the NftId object |

```java
new NftId()
```

## Methods

| Method                   | Type                                                           | Requirement |
| ------------------------ | -------------------------------------------------------------- | ----------- |
| `NftId.fromString(<id>)` | String                                                         | Optional    |
| `NftId.fromBytes(<id>)`  | bytes \[] | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
new NftId(new TokenId(0,0,2), 56562);

// v2.0.11
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
new NftId(new TokenId(0,0,2), 56562);

// v2.0.28 
```

{% endtab %}

{% tab title="Go" %}

```java
nftId := hedera.NftID{
    TokenID: tokenId,
		SerialNumber: serialNum,
}

// v2.1.13
```

{% endtab %}
{% endtabs %}
