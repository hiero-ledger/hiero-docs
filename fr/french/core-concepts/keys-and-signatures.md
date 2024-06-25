# Keys and Signatures

## Key Types: ECDSA vs Ed25519

A key can be a [public key](../support-and-community/glossary.md#public-key) of a supported system [Ed25519](../support-and-community/glossary.md#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k1), or an ID of a [smart contract](../support-and-community/glossary.md#smart-contract). The corresponding algorithm generates public and private keys which are unique to one another. The public key can be shared and visible to other network users in a [Network Explorer](../support-and-community/glossary.md#network-explorer) or [REST APIs](../support-and-community/glossary.md#rest-api). The [private key](../support-and-community/glossary.md#private-key) is kept secret from the owner and grants access to the owner to modify entities (accounts, tokens, etc.).&#x20

Private keys _can only_ be recovered once lost if created with an associated recovery phrase that you can access. Keys are mutable and can be updated once set for an entity. Generally, you will need the current key to sign the transaction to update the keys.&#x20

### Choosing between ECDSA and Ed25519 Keys

The choice between ECDSA and ED25519 keys primarily depends on your specific use case:

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>ECDSA</strong></td><td><strong>Ed25519</strong></td></tr><tr><td><strong>Use Cases</strong></td><td>Ideal if you want to use <a href="../support-and-community/glossary.md#metamask">MetaMask</a> or need support for more EVM developer tooling. Suited for apps interfacing with Ethereum or EVM-compatible networks due to the associated EVM address.</td><td>Preferred if key length, security, and performance are important. ECDSA public keys are twice as long for the same level of security.</td></tr></tbody></table>

{% hint style="info" %}
**Note**: Hedera wallets such as [HashPack](https://www.hashpack.app/) support both key types.
{% endhint %}

## Key Structures

Hedera supports the following key structure types:

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th width="240"></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Description</strong></td><td><strong>Example</strong></td></tr><tr><td><strong>Simple Key</strong></td><td>A single key on an account.</td><td><strong>Account</strong> <strong>Key</strong> <br>       { <br>           Key 1 <br>        }<br>Only one key is required to sign for the account.</td></tr><tr><td><strong>Key List</strong></td><td>All keys in the key list are required to sign transactions involving the account.</td><td><strong>Account Key</strong><br>     <strong>KeyList (3/3)</strong> <br>          { <br>               Key 1 <br>               Key 2 <br>               Key 3 <br>          }<br>All three keys in the list are required to sign for the account.</td></tr><tr><td><strong>Threshold Key</strong></td><td>A subset of keys defined as the threshold are required to sign the transaction that involve the account out of the total number of keys.</td><td><strong>Account Key</strong><br>      <strong>ThresholdKey (1/3)</strong> <br>          { <br>              Key 1 <br>              Key 2 <br>              Key 3 <br>          }<br>One out of the three keys in the key list is required to sign for the account.</td></tr></tbody></table>

{% hint style="info" %}
🔔 Key structures can be nested. This means you can have a more complex key system with key lists inside of threshold keys, threshold keys inside keys lists, etc. An example of a nested key list can be viewed [here](https://hashscan.io/mainnet/adminKey/0.0.2).
{% endhint %}

All transaction types support the above key structures that specify a key field. For a transaction to be successful, the provided signatures must match the defined key structure requirements.

## FAQ

<details>

<summary>What is a key in Hedera?</summary>

A key in Hedera can be a [public key](../support-and-community/glossary.md#public-key) of a supported system such as [ED25519](../support-and-community/glossary.md#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k), or an ID of a [smart contract](../support-and-community/glossary.md#smart-contract). The corresponding algorithm generates public and private keys which are unique to one another. The public key can be shared and visible to other network users in a [Network Explorer](../support-and-community/glossary.md#network-explorer) or REST APIs. The [private key](../support-and-community/glossary.md#private-key) is kept secret and grants access to the owner to modify entities (accounts, tokens, etc.).

</details>

<details>

<summary>What happens if I lose my private key?</summary>

Private keys can only be recovered once lost if created with an associated recovery phrase that you can access. It's crucial to keep your private keys safe and secure as they grant access to modify your Hedera entities, like accounts and tokens.

</details>
