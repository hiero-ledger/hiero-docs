# SignaturePair

The client may use any number of bytes from zero to the whole length of the public key for pubKeyPrefix. If zero bytes are used, then it must be that only one primitive key is required to sign the linked transaction; it will surely resolve to INVALID\_SIGNATURE otherwise.

{% hint style="info" %}
**IMPORTANT:** In the special case that a signature is being provided for a key used to authorize a precompiled contract, the pubKeyPrefix must contain the **entire public key**! That is, if the key is a Ed25519 key, the pubKeyPrefix should be 32 bytes long. If the key is a ECDSA(secp256k1) key, the pubKeyPrefix should be 33 bytes long, since we require the compressed form of the public key.
{% endhint %}

Only Ed25519 and ECDSA(secp256k1) keys and hence signatures are currently supported.

| Field          | Type                                   | Description                       | ​                                                                        |
| -------------- | -------------------------------------- | --------------------------------- | ------------------------------------------------------------------------ |
| `pubKeyPrefix` | ​                                      | First few bytes of the public key | ​                                                                        |
| `signature`    | oneof                                  | ​                                 | ​                                                                        |
| ​              | contract                               | ​                                 | smart contract virtual signature (always length zero) |
| ​              | ed25519                                | ​                                 | ed25519 signature                                                        |
| ​              | RSA\_3072        | ​                                 | RSA-3072 signature                                                       |
| ​              | ECDSA\_384       | ​                                 | ECDSA p-384 signature                                                    |
|                | ECDSA\_secp256k1 |                                   | ECDSA(secp256k1) signature                            |

\
\\
