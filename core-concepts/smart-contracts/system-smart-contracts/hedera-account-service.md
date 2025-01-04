# Hedera Account Service

## 📣 ECRECOVER Support for ECDSA Hedera Accounts

EVM developers should note that `ECRECOVER` natively supports ECDSA accounts on Hedera. Aliases for these accounts, derived using Keccak-256(publicKey), are fully compatible with Ethereum's `ECRECOVER` logic. This enables seamless interaction with ECDSA accounts on Hedera using `ECRECOVER`, just like standard Ethereum accounts.

To verify an ECDSA signature, developers can call `ECRECOVER(messageHash, r, s, v)` in Ethereum smart contracts. If the recovered address matches the alias of a Hedera account, the signer is confirmed to control the account. No special integration is needed—`ECRECOVER` functions as a standard EVM precompiled contract on Hedera

{% hint style="info" %}
**Note**: This functionality is specific to ECDSA accounts with Keccak-256(publicKey) aliases. For ED25519 accounts, Hedera offers alternative authorization methods, such as `isAuthorized()` or `isAuthorizedRaw()`, to verify control of an account. For details, refer to the [Hedera Account Service System Contract](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-account-service). See below for details on these two precompile functions.
{% endhint %}

***

## Signature Validation Precompiles (HIP-632)

To extend Hedera’s compatibility with Ethereum’s EVM, [HIP-632](https://hips.hedera.com/hip/hip-632) introduced two new precompile functions for signature validation: `isAuthorizedRaw` and `isAuthorized`. These precompiles enable smart contracts on Hedera to validate both ED25519 and ECDSA (secp256k1) Hedera account signatures, making it easier for developers to build applications that operate across Hedera-native and EVM-based ecosystems.&#x20;

Additionally, these functions allow developers to verify account authorization directly within smart contracts. This extends the capabilities of Ethereum’s `ECRECOVER` by supporting:

* Simple raw signature verification for both ED25519 and ECDSA keys, similar to how Ethereum's `ECRECOVER` checks the signature against the provided message hash.
* Account authorization verification for ECDSA accounts, confirming that the account associated with the signature is authorized to execute transactions without being restricted to `ECRECOVER`.
* Comprehensive account authorization using protobuf signature maps, which allows for a more complex and flexible account validation process, ensuring compatibility with Hedera's advanced account model. May include multi-sig or threshold key structure.

By addressing these use cases, HIP-632 provides a seamless way for developers to handle authorization and signature validation across both Hedera and EVM-compatible environments.

#### **isAuthorizedRaw (address, messageHash, signatureBlob)**

* Validates whether a given ED25519 or ECDSA signature is valid for a message against the public key associated with the specified account alias.
* Operates similarly to Ethereum's `ECRECOVER` for single key structure but supports both secp256k1 (Ethereum) and ED25519 (Hedera) cryptography.
* Returns `true` if the signature is valid and linked to the account, otherwise `false`.

#### **isAuthorized (address, message, signatureBlob)**

* Extends **isAuthorizedRaw** by validating that the specific Hedera account (referenced by either an account number alias or EVM address alias) is authorized to execute the transaction.
* Supports Hedera’s complex account key structures, including multi-sig and threshold key requirements.
* Provides a way to confirm authorization based on Hedera’s advanced account-based key management.
* Returns `true` if the signature is valid and linked to the account, otherwise `false`.

<table><thead><tr><th width="171">Parameter</th><th>Description</th></tr></thead><tbody><tr><td><strong>address</strong></td><td>A 20-byte identifier used to represent an account on the Hedera network or an EVM-compatible account.</td></tr><tr><td><strong>message</strong></td><td>The original plaintext data or payload that the signature is derived from. This is the information that was signed to produce the signature.</td></tr><tr><td><strong>messaHash</strong></td><td>A cryptographic hash of the <code>message</code>, calculated using an algorithm like SHA-256 or Keccak-256. This is typically what is signed instead of the raw message.</td></tr><tr><td><strong>signatureBlob</strong></td><td>A concatenation of the digital signature components, typically including <code>r</code>, <code>s</code>, and <code>v</code> values for ECDSA, or the equivalent data for ED25519 signatures.</td></tr></tbody></table>

#### Behavior and Cost

* Both functions return a boolean indicating whether the signature is valid and is authorized for the account.
* These methods incur gas costs proportional to the computational resources required for signature validation, including cryptographic hashing of the message and verifying the signature against the public key.
* The additional gas charge varies depending on the type of cryptographic operation (ED25519 vs. ECDSA) and the size of the associated key structures.

{% embed url="https://docs.hedera.com/hedera/core-concepts/smart-contracts/gas-and-fees#gas-schedule-and-fee-calculation" %}
Gas fee schedule and calculation
{% endembed %}

**Reference**: [HIP-632](https://hips.hedera.com/hip/hip-632)

***

##
