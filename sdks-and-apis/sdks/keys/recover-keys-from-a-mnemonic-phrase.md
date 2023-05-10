# Recover keys from a mnemonic phrase

Recover private keys from a mnemonic phrase.

| **Method**                                        | **Type**         | **Description**                                                                          |
| ------------------------------------------------- | ---------------- | ---------------------------------------------------------------------------------------- |
| `PrivateKey.fromMnemonic(<mnemonic>)`             | Mnemonic         | Recover a private key from a mnemonic phrase compatible with the iOS and Android wallets |
| `PrivateKey.fromMnemonic(<mnemonic, passphrase>)` | Mnemonic. String | Recover a private key from a generated mnemonic phrase and a passphrase                  |

{% tabs %}
{% tab title="Java" %}
```java
//Use the mnemonic to recover the private key
PrivateKey privateKey = PrivateKey.fromMnemonic(mnemonic);
PublicKey publicKey = privateKey.publicKey();

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```java
//Use a recovered mnemonic to recover the private key
const recoveredMnemonic = await Mnemonic.fromString(mnemonic.toString());
const privateKey = await recoveredMnemonic.toPrivateKey();

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```java
recoveredKey, err := hedera.PrivateKeyFromMnemonic(mnemonic, "")
publicKey := recoveredKey.PublicKey()

//v2.0.0
```
{% endtab %}
{% endtabs %}
