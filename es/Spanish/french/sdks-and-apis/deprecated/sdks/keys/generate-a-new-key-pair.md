# Generate a new key pair

## ED25519

Create a new Ed25519 key pair used to sign transactions and queries on the Hedera network. The private key is kept confidential and is used to sign transactions that modify the state of an account, topic, token, smart contract, or file entity on the network. The public key can be shared with other users on the network.

{% tabs %}
{% tab title="V1" %}

| **Method**                               | **Type**          | **Description**                                                |
| ---------------------------------------- | ----------------- | -------------------------------------------------------------- |
| `Ed25519PrivateKey.generate()`           | Ed25519PrivateKey | Generates a Ed25519 private key                                |
| `Ed25519PrivateKey.generate().publicKey` | Ed25519PublicKey  | Gets the corresponding public key to the generated private key |

{% code title="Java" %}

```java
Ed25519PrivateKey newKey = Ed25519PrivateKey.generate();
Ed25519PublicKey newPublicKey = newKey.publicKey;

System.out.println("private key = " + newKey);
System.out.println("public key = " + newPublicKey);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript " %}

```javascript
const privateKey = PrivateKey.generate();
const publicKey = privateKey.publicKey;

console.log("private = " + privateKey);
console.log("public = " + publicKey);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## ECDSA (secp256k1\_)

Create a new ECDSA (secp256k1) key pair used to sign transactions and queries on a Hedera network. The private key is kept confidential and is used to sign transactions that modify the state of an account, topic, token, smart contract, or file entity on the network. The public key can be shared with other users on the network.

{% tabs %}
{% tab title="V1" %}
Not supported
{% endtab %}
{% endtabs %}

##
