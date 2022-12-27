# Token ID

Constructs a `TokenId`.

| Constructor                            | Description                    |
| -------------------------------------- | ------------------------------ |
| `new TokenId(<shard>,<realm>,<token>)` | Initializes the TokenId object |

```java
new TokenId()
```

## Methods

{% tabs %}
{% tab title="V1" %}
| Method                                   | Type   | Description                                   |
| ---------------------------------------- | ------ | --------------------------------------------- |
| `TokenId.fromtString(<tokenId>)`         | String | Constructs a token ID from a String value     |
| `TokenId.fromSolidityAddress(<address>)` | String | Constructs a token ID from a solidity address |

```java
TokenId tokenId =  TokenId.fromString("0.0.3");
System.out.println(tokenId);
//Version: 1.2.2
```

```javascript
const tokenId = new TokenId(0,0,5);
console.log(tokenId);

const tokenIdFromString =  TokenId.fromString("0.0.3");
console.log(tokenIdFromString);
//Version 1.4.1
```
{% endtab %}
{% endtabs %}
