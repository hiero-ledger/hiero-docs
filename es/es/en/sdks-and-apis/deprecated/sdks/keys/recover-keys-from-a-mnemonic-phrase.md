# Recover keys from a mnemonic phrase

Recover private keys from a mnemonic phrase.

{% tabs %}
{% tab title="V1" %}

| **Method**                                               | **Type**         | **Description**                                                                          |
| -------------------------------------------------------- | ---------------- | ---------------------------------------------------------------------------------------- |
| `Ed25519PrivateKey.fromMnemonic(<mnemonic>)`             | Mnemonic         | Recover a private key from a mnemonic phrase compatible with the iOS and Android wallets |
| `Ed25519PrivateKey.fromMnemonic(<mnemonic, passphrase>)` | Mnemonic, String | Recover a private key from a generated mnemonic phrase and a passphrase                  |

{% code title="Java" %}

```java
//Use the mnemonic to recover the private key
Ed25519PrivateKey privateKey = Ed25519PrivateKey.fromMnemonic(mnemonic);
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Use the mnemonic to recover the private key
Ed25519PrivateKey privateKey = Ed25519PrivateKey.fromMnemonic(mnemonic);
```

{% endcode %}
{% endtab %}
{% endtabs %}
