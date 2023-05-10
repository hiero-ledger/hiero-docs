# Token types

There are two types of tokens you can create using the Hedera Token Service: fungible and non-fungible tokens. A fungible (`FUNGIBLE_COMMON`) token is a class of tokens that can be interchangeable with another in the same class. Tokens in this class share the same value and share all the same properties. A non-fungible token (`NON_FUNGIBLE_UNIQUE`) is a class of tokens that are not identical to the other tokens in the same class. This token type cannot be interchanged with other tokens and is differentiated by serial numbers that reference each unique token. The SDKs default to creating fungible tokens if the token type during creation is not specified.

### Token Type

#### **FUNGIBLE**

{% tabs %}
{% tab title="Java" %}
```java
TokenType.FUNGIBLE_COMMON

// v2.0.11
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
TokenType.FungibleCommon

// v2.0.28
```
{% endtab %}

{% tab title="Go" %}
```go
hedera.TokenTypeFungibleCommon

// v2.1.14
```
{% endtab %}
{% endtabs %}

#### **NON-FUNGIBLE**

{% tabs %}
{% tab title="Java" %}
```java
TokenType.NON_FUNGIBLE_UNIQUE

// v2.0.11
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
TokenType.NonFungibleUnique

// v2.0.28
```
{% endtab %}

{% tab title="Go" %}
```go
hedera.TokenTypeNonFungibleUnique

// v2.1.14
```
{% endtab %}
{% endtabs %}
