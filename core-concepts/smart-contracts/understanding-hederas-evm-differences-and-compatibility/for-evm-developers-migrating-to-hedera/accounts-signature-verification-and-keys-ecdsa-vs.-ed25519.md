# Accounts, Signature Verification & Keys (ECDSA vs. ED25519)

## Overview

Migrating to Hedera’s EVM implementation involves understanding key differences in account models, signature verification, and key types. On Ethereum, addresses are derived from ECDSA public keys, and `ECRECOVER` is commonly used to validate signatures. Hedera, however, supports both ECDSA and ED25519 (Hedera-native account) keys, with dynamic key rotation and aliases that may not directly align with Ethereum’s static address model.

This section helps you navigate ECDSA and ED25519 signature workflows, introduces the `isAuthorized` function from the system contract from [HIP-632](https://hips.hedera.com/hip/hip-632), and clarify how different account key scenarios map onto Hedera’s environment.&#x20;

## Understanding Account Models and Aliases

Hedera’s account model supports both ED25519 and ECDSA keys by identifying accounts by aliases instead of static addresses. This allows dynamic key rotation without changing an account’s ID, unlike EVM's static ECDSA-only approach. Signature validation varies accordingly: ED25519 keys use `isAuthorized` or `isAuthorizedRaw`, while Hedera's ECDSA accounts with aliases can still rely on `ECRECOVER`.

### **Clarifying Account Number Alias vs. EVM Address Alias**

Hedera accounts can have both a native "Hedera account number alias" (e.g., `0.0.xxxx`) and a derived EVM address alias (a 20-byte address similar to Ethereum). The EVM alias makes the account compatible with `ECRECOVER` and other familiar EVM tools. For more details, see the [Smart Contract Addresses](../../smart-contract-addresses.md) page.&#x20;

### **Working With ECDSA Account Aliases in Testing**

If you are writing an Ethereum smart contract and need to interact with an ECDSA Hedera account—particularly one identified by a Hedera account number alias—you must convert that alias into a format compatible with EVM tools. This ensures that when you call functions like `ECRECOVER`, you reference the correct account. Here are some considerations:

* During testing, confirm that your test scripts and configuration files convert the Hedera account number alias (e.g., `0.0.xxxx`) into the corresponding 20-byte alias derived from `Keccak-256(publicKey)`. Doing so allows your EVM testing environment (Hardhat, Truffle) to interact with Hedera accounts like standard EVM addresses.
* Including this conversion step in your test setup saves time and confusion, ensuring your EVM-style smart contracts can reliably work with Hedera’s ECDSA accounts.

### **Key Permutations & Scenarios on Hedera**

* **ECDSA Accounts:**
  * Behave similarly to standard EVM accounts.
  * Allow validation of ECDSA signatures with `ECRECOVER`.
  * Provide smooth interoperability with EVM tools and dApps.
* **ED25519 Accounts:**
  * Require `isAuthorized` or `isAuthorizedRaw` for signature validation.
  * Support complex configurations like multi-key or threshold-based approval.
  * Enhance security and adaptability, but differ from EVM's static address.

This flexibility enables interoperability and more robust security models than standard EVM environments.

***

## Using ECRECOVER for ECDSA Accounts on Hedera

Hedera supports ECDSA accounts, allowing EVM developers to validate ECDSA signatures using familiar tools like `ECRECOVER`. ECDSA accounts on Hedera use aliases derived from `Keccak-256(publicKey)`, ensuring compatibility with Ethereum’s signature workflows.

**Example: Verifying ECDSA Signatures Using ECRECOVER**

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">function verifyECDSASignature(bytes32 messageHash, uint8 v, bytes32 r, bytes32 s) public pure returns (address) {
<strong>    return ecrecover(messageHash, v, r, s);
</strong>}
</code></pre>

**Key Considerations:**

* ECDSA accounts on Hedera behave just like EVM accounts when validating signatures with `ECRECOVER`.
* <mark style="background-color:green;">**Use ECDSA accounts when interacting with EVM-compatible dApps, wallets, or bridges for minimal friction.**</mark>

***

## Using System Contract Functions for ED25519 Accounts

Hedera’s native key type is ED25519, which is not compatible with `ECRECOVER`. To accommodate ED25519 (Hedera-native) accounts and more complex configurations, HIP-632 introduces Hedera Account Service system contract functions:

* [**isAuthorized**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/system-smart-contracts/hedera-account-service#isauthorized-address-message-signatureblob)**:** Validates multiple signatures, supporting threshold or multi-key accounts.
* [**isAuthorizedRaw**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/system-smart-contracts/hedera-account-service#isauthorizedraw-address-messagehash-signatureblob)**:** Validates a single raw ED25519 signature, analogous to `ECRECOVER` but for ED25519 keys.

**Example: Validating ED25519 Signatures**

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">function verifyED25519Signature(address accountAlias, bytes32 messageHash, bytes memory signatureBlob) public returns (bool) {
    (bool success, ) = address(0x167).call(
        abi.encodeWithSignature(
<strong>            "isAuthorizedRaw(address,bytes32,bytes)",
</strong>            accountAlias,
            messageHash,
            signatureBlob
        )
    );
    return success;
}
</code></pre>

**Why This Matters for EVM Developers:**

* **Hedera-Native Accounts:**\
  Most Hedera accounts use ED25519 keys, so `isAuthorizedRaw` is essential for verifying their signatures.
* **Multi-Key and Threshold Accounts:**\
  Use `isAuthorized` for scenarios requiring multiple signatures, ensuring only properly authorized actions occur.

{% hint style="info" %}
_**Note**:_ For detailed parameter formats, consult the [HIP-632](https://hips.hedera.com/hip/hip-632) specification and the Hedera Account Service [documentation](https://docs.hedera.com/hedera/core-concepts/smart-contracts/system-smart-contracts/hedera-account-service). Ensure that `accountAlias`, `messageHash`, and `signatureBlob` adhere to the required formats outlined there.
{% endhint %}

***

## **Practical Use Case: Multi-Key Verification**

Hedera supports advanced account configurations like multi-sig and threshold accounts, which may include both ECDSA and ED25519 keys. Using `isAuthorized`, you can enforce complex signing requirements, such as requiring multiple parties to sign before executing a contract operation.

**Example: DAO Governance Using Multi-Sig**

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">function validateDAOProposal(address accountAlias, bytes memory proposalData, bytes memory signatureBlob) public returns (bool) {
    (bool success, ) = address(0x167).call(
<strong>        abi.encodeWithSignature("isAuthorized(address,bytes,bytes)", accountAlias, proposalData, signatureBlob)
</strong>    );
    return success;
}
</code></pre>

This example demonstrates how you might require multiple signatures to approve a DAO proposal, enhancing the security and trustworthiness of your governance mechanisms.

***

## Key Rotation: Adapting to Hedera’s Dynamic Model

Standard EVM addresses are static since they are derived from a public key hash. Hedera, by contrast, supports dynamic key rotation, letting you update the keys controlling an account without changing the account’s address.

**Why This Matters for EVM Developers:**

* Your applications must dynamically validate the current set of keys each time rather than relying on a static key-to-address mapping.
* By rotating keys, you can enhance security without migrating to a new address.

**Example: Key Rotation in Smart Contracts**

```solidity
contract KeyRotationHandler {
    address public trustedSigner;

    constructor(address initialSigner) {
        trustedSigner = initialSigner;
    }

    function updateTrustedSigner(address newSigner) public {
        require(msg.sender == trustedSigner, "Not authorized");
        trustedSigner = newSigner;
    }
}
```

This simple pattern allows you to change the trusted signer as needed, reflecting real-world operational needs such as periodic key rotation to mitigate security risks.

***

### Additional References and Resources

* [**Hedera SDKs**](../../../../sdks-and-apis/sdks/)
* [**Hedera Account Service**](../../system-smart-contracts/hedera-account-service.md)
* [**HIP-632 Specification**](https://hips.hedera.com/hip/hip-632)
* [**Hedera Account Service System Contract**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-account-service)
