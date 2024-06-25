# Recover keys from a mnemonic phrase

Recover private keys from a mnemonic phrase.

<table data-header-hidden><thead><tr><th></th><th width="145.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>PrivateKey.fromMnemonic(<mnemonic>)</code></td><td>Mnemonic</td><td>Recover a private key from a mnemonic phrase compatible with the iOS and Android wallets</td></tr><tr><td><code>PrivateKey.fromMnemonic(<mnemonic, passphrase>)</code></td><td>Mnemonic. String</td><td>Recover a private key from a generated mnemonic phrase and a passphrase</td></tr></tbody></table>

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
