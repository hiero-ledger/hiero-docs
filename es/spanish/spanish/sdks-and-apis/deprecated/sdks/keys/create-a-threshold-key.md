# Create a threshold key

Create a key structure that requires the defined threshold value to sign. A threshold key can contain a [Ed25519](../../../sdks/keys/generate-a-new-key-pair.md#ed25519) or [ECDSA](../../../sdks/keys/generate-a-new-key-pair.md#ecdsa-secp256k1) (secp256k1\_)\_ key type. You can use either the public key or the private key to create the key structure. If the threshold requirement is not met when signing transactions, the network will return an "INVALID\_SIGNATURE" error.

{% tabs %}
{% tab title="V1" %}

| Constructor                     | Type | Description                       |
| ------------------------------- | ---- | --------------------------------- |
| `new ThresholdKey(<threshold>)` | int  | Initializes a ThresholdKey object |

```java
new ThresholdKey(<threshold>)
```

| Method           | Type             | Description                        |
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

// require 2 of the 3 keys we generated to sign on anything modifying this account
ThresholdKey thresholdKeys = new ThresholdKey(2).add(publicKey1).add(publicKey2).add(publicKey3);

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

//Create a threshold key of 2/3
const thresholdKeys = new ThresholdKey(2).add(publicKey1).add(publicKey2).add(publicKey3);     

//v1.4.2
```

{% endcode %}
{% endtab %}
{% endtabs %}
