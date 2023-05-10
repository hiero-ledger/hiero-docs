# Generate a mnemonic phrase

Generate a 12 or 24-word mnemonic phrase that can be used to recover the private keys that are associated with it.

| **Method**              | **Type** | **Description**                                                               |
| ----------------------- | -------- | ----------------------------------------------------------------------------- |
| `Mnemonic.generate24()` | Mnemonic | Generates a 24-word recovery phrase that can be used to recover a private key |
| `Mnemonic.generate12()` | Mnemonic | Generates a 12-word recovery phrase that can be used to recover a private key |

{% tabs %}
{% tab title="Java" %}
```java
// 24-word recovery phrase
Mnemonic mnemonic = Mnemonic.generate24();
System.out.println("mnemonic 24 word = " + mnemonic);


//12 word recovery phrase
Mnemonic mnemonic12 = Mnemonic.generate12();
System.out.println("mnemonic 12 word = " + mnemonic12);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// generate a 24-word mnemonic
const mnemonic = await Mnemonic.generate();

console.log(mnemonic)
```
{% endtab %}

{% tab title="Go" %}
```java
//Generate 24 word mnemonic
mnemonic24, err := hedera.GenerateMnemonic()

if err != nil {
  panic(err)
}

privateKey, err := mnemonic24.ToPrivateKey( /* passphrase */ "")

if err != nil {
    panic(err)
}

publicKey := privateKey.PublicKey()

fmt.Printf("mnemonic = %v\n", mnemonic)

//v2.0.0
```
{% endtab %}
{% endtabs %}
