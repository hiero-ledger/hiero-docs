# Create a threshold key

Create a key structure that requires the defined threshold value to sign. A threshold key can contain a [Ed25519](generate-a-new-key-pair.md#ed25519) or [ECDSA](generate-a-new-key-pair.md#ecdsa-secp256k1) (secp256k1\_)\_ key type. You can use either the public key or the private key to create the key structure. If the threshold requirement is not met when signing transactions, the network will return an "INVALID\_SIGNATURE" error.

| **Method**                                | **Type** | **Description**                                                                                                                    |
| ----------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `KeyList.withThreshold(<thresholdValue>)` | int      | The number of keys required to sign transactions to modify the account i.e. transfers, update, etc |

{% tabs %}
{% tab title="Java" %}

```java
//Generate 3 keys
PrivateKey key1 = PrivateKey.generate();.
PublicKey publicKey1 = key1.getPublicKey();

PrivateKey key2 = PrivateKey.generate();
PublicKey publicKey2 = key2.getPublicKey();

PrivateKey key3 = PrivateKey.generate();
PublicKey publicKey3 = key3.getPublicKey();

PrivateKey[] keys = new PrivateKey[3]; //You can also use the 3 public keys here

keys[0] = key1;
keys[1] = key2;
keys[2] = key3;


//A key structure that requires one of the 3 keys to sign
KeyList thresholdKey = KeyList.withThreshold(1);

//Add the three keys to the thresholdKey
Collections.addAll(thresholdKey, keys);

System.out.println("The 1/3 threshold key structure" +thresholdKey);

//v2.0.0
```

**Sample Output:**

```
KeyList{threshold=1,  
keys=[

302e020100300506032b657004220420984bd6b4e0cac783654f30c8797655953c6ab432e78bc09a34fbda594c6395ed, 

302e020100300506032b657004220420a4a7bd506f33868416d53eff55b3e8a254e17accf6cb37f44975792ededac120, 

302e020100300506032b657004220420f8a6f2ba3174391e619a87506fb0b86c6e481809563a797f4f84715d1a471695]  
}
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Generate our key lists
const privateKeyList = [];
const publicKeyList = [];
for (let i = 0; i < 4; i += 1) {
     const privateKey = PrivateKey.generate();
     const publicKey = privateKey.publicKey;
     privateKeyList.push(privateKey);
     publicKeyList.push(publicKey);
     console.log(`${i}: pub key:${publicKey}`);
     console.log(`${i}: priv key:${privateKey}`);
}

// Create our threshold key
const thresholdKey =  new KeyList(publicKeyList,1); 

console.log("The 1/3 threshold key structure" +thresholdKey);

//2.0.2
```

{% endtab %}

{% tab title="Go" %}

```go
//Generate 3 keys
key1, err := hedera.GeneratePrivateKey()

if err != nil {
    panic(err)
}

publicKey1 := key1.PublicKey()

key2, err := hedera.GeneratePrivateKey()

if err != nil {
    panic(err)
}

publicKey2:= key2.PublicKey()

key3, err := hedera.GeneratePrivateKey()

if err != nil {
   panic(err)
}

publicKey3 := key3.PublicKey()

//Create a key list where all 3 keys are required to sign

keys := make([]hedera.PublicKey, 3)

keys[0] = publicKey1
keys[1] = publicKey2
keys[2] = publicKey3

//A key structure that requires one of the 3 keys to sign
thresholdKey := hedera.KeyListWithThreshold(1).
        AddAllPublicKeys(keys)

fmt.Printf("The 1/3 threshold key structure %v\n", thresholdKey)

//v2.0.0
```

{% endtab %}
{% endtabs %}
