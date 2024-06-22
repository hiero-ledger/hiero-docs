# Create a key list

Create a key list key structure where all the keys in the list are required to sign transactions that modify accounts, topics, tokens, smart contracts, or files. A key \*\*\*\* list can contain a [Ed25519](../../../sdks/keys/generate-a-new-key-pair.md#ed25519) or [ECDSA](../../../sdks/keys/generate-a-new-key-pair.md#ecdsa-secp256k1) (secp256k1\_)\_ key type.

If all the keys in the key list key structure do not sign, the transaction will fail and return an "INVALID\_SIGNATURE" error. A key list can have repeated keys. A signature for the repeated key will count as many times as the key is listed in the key list. For example, a key list has three keys. Two of the three public keys in the list are the same. When a user signs a transaction with the repeated key it will account for two out of the three keys required signature.

{% tabs %}
{% tab title="V1" %}

| **Method**       | **Type**         | **Description**                    |
| ---------------- | ---------------- | ---------------------------------- |
| `add(<key>)`     | Ed25519PublicKey | Add one public key to the key list |
| `addAll(<keys>)` | Ed25519PublicKey | Add all keys to the key list       |

{% code title="Java" %}

```java
//Generate 3 keys
Ed25519PrivateKey key1 = Ed25519PrivateKey.generate();
Ed25519PublicKey publicKey1 = key1.publicKey;

Ed25519PrivateKey key2 = Ed25519PrivateKey.generate();
Ed25519PublicKey publicKey2 = key2.publicKey;

Ed25519PrivateKey key3 = Ed25519PrivateKey.generate();
Ed25519PublicKey publicKey3 = key3.publicKey;

//Add they keys to a key list
KeyList keyList = new KeyList().add(publicKey1).add(publicKey2).add(publicKey3);

//v1.2.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Generate 3 keys
const key1 = await Ed25519PrivateKey.generate();
const publicKey1 = key1.publicKey;

const key2 = await Ed25519PrivateKey.generate();
const publicKey2 = key2.publicKey;

const key3 = await Ed25519PrivateKey.generate();
const publicKey3 = key3.publicKey;

//Add they keys to a key list
const keyList = new KeyList().add(publicKey1).add(publicKey2).add(publicKey3);

//v1.4.2
```

{% endcode %}
{% endtab %}
{% endtabs %}
