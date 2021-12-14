# Create a key list

Create a key list key structure where all the keys in the list are required to sign transactions that modify accounts, topics, tokens, smart contracts, or files. If all the keys in the key list key structure do not sign, the transaction will fail and return an "INVALID\_SIGNATURE" error. A key list can have repeated keys. A signature for the repeated key will count as many times as the key is listed in the key list. For example, a key list has three keys. Two of the three public keys in the list are the same. When a user signs a transaction with the repeated key it will account for two out of the three keys required signature.&#x20;

{% tabs %}
{% tab title="V2" %}
| Method               | Type | Description                 |
| -------------------- | ---- | --------------------------- |
| `KeyList.of(<keys>)` | Key  | Keys to add to the key list |

{% code title="Java" %}
```java
//Generate 3 keys
PrivateKey key1 = PrivateKey.generate();
PublicKey publicKey1 = key1.getPublicKey();

PrivateKey key2 = PrivateKey.generate();
PublicKey publicKey2 = key2.getPublicKey();

PrivateKey key3 = PrivateKey.generate();
PublicKey publicKey3 = key3.getPublicKey();

//Create a key list where all 3 keys are required to sign
KeyList keyStructure = KeyList.of(key1, key2, key3);

System.println(keyStructure)

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```java
//Generate 3 keys
const key1 = PrivateKey.generate();
const publicKey1 = key1.publicKey;

const key2 = PrivateKey.generate();
const publicKey2 = key2.publicKey;

const key3 = PrivateKey.generate();
const publicKey3 = key3.publicKey;

//Create a list of the keys
const publicKeyList = [];
    
publicKeyList.push(publicKey1);
publicKeyList.push(publicKey2);
publicKeyList.push(publicKey3);

//Create a key list where all 3 keys are required to sign
const keys = new KeyList(publicKeyList);
//v2.0.13
```
{% endcode %}

{% code title="Go" %}
```java
//Generate 3 keys
key1, err := hedera.GeneratePrivateKey()

if err != nil {
    panic(err)
}

publicKey1, err := key1.PublicKey()

key2, err := hedera.GeneratePrivateKey()

if err != nil {
    panic(err)
}

publicKey2, err := key2.PublicKey()

key3, err := hedera.GeneratePrivateKey()

if err != nil {
    panic(err)
}

publicKey3, err := key3.PublicKey()

//Create a key list where all 3 keys are required to sign
keys := make([]hedera.PublicKey, 3)

keys[0] = publicKey1
keys[1] = publicKey2
keys[2] = publicKey3

keyStructure := hedera.NewKeyList().AddAllPublicKeys(keys)

fmt.Printf("The key list is %v\n", keyStructure) 

//v2.0.0
```
{% endcode %}

**Sample Output**

`KeyList{threshold=null, `\
`     keys=[302e020100300506032b6570042204201cd556de918842179791d9edd75cdd2b5d34c5c73b0239ec0b34c67eedc020fd, 302e020100300506032b6570042204209ca1ce4463b71c72bba0219c37e18347a5145a9797c6546a6c99e50255c54be3, 302e020100300506032b657004220420982bb43f4947e8376e2f0ebfde086d24323b04d731da29446e5bc399ffbe06e1]`\
`}`
{% endtab %}

{% tab title="V1" %}
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

