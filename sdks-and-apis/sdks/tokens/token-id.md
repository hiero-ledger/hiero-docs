# Token ID

Constructs a `TokenId`.

| Constructor                            | Description                    |
| -------------------------------------- | ------------------------------ |
| `new TokenId(<shard>,<realm>,<token>)` | Initializes the TokenId object |

```java
new TokenId()
```

## Methods

| Method                                   | Type    | Description                                   |
| ---------------------------------------- | ------- | --------------------------------------------- |
| `TokenId.fromString(<tokenId>)`          | String  | Constructs a token ID from a String value     |
| `TokenId.fromSolidityAddress(<address>)` | String  | Constructs a token ID from a solidity address |
| `TokenId.fromBytes(<bytes>)`             | byte\[] | Constructs a token ID from bytes              |

{% tabs %}
{% tab title="Java" %}
```java
TokenId tokenId = new TokenId(0,0,5);
System.out.println(tokenId);

TokenId tokenIdFromString = TokenId.fromString("0.0.3");
System.out.println(tokenIdFromString);

//Version: 2.0.1
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
const tokenId = new TokenId(0,0,5);
console.log(tokenId.toString());

const tokenIdFromString =  TokenId.fromString("0.0.3");
console.log(tokenIdFromString.toString());

//Version 2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
tokenId := hedera.TokenID {
		Shard: 0,
		Realm: 0,
		Token: 5,
}

//v2.1.0
```
{% endtab %}
{% endtabs %}
