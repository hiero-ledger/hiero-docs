# Integrating ED25519 Accounts and Advanced Features Into Smart Contracts

## Overview

Hedera-native developers can leverage Hedera’s advanced account and key management features, including ED25519 accounts, multi-sig configurations, and threshold keys. To integrate seamlessly with EVM-compatible chains and applications, you’ll need to work with ECDSA key pairs.

Hedera’s [HIP-632](https://hips.hedera.com/hip/hip-632) system contract functions—`isAuthorized` and `isAuthorizedRaw`—bridge this gap by enabling on-chain verification of both ED25519 and ECDSA signatures. This ensures you can extend Hedera-native security features into EVM-compatible smart contracts without compromising trust boundaries or functionality.

***

### **Bridging ED25519 Accounts with Solidity**

Hedera’s ED25519 accounts are incompatible with Solidity’s `ECRECOVER` function, which supports ECDSA. To enable seamless integration, HIP-632 introduces two key system contract functions:

* `isAuthorizedRaw`: Validates a single raw ED25519 signature.
* `isAuthorized`: Validates multiple signatures or threshold key configurations, supporting multi-sig and advanced key schemes.

These functions allow you to enforce the same account security models on-chain within smart contracts.

**Basic Example: Validating a Single ED25519 Signature**

Here’s a Solidity example for validating ED25519 signatures using the isAuthorizedRaw function. The function calls the system contract function (`isAuthorizedRaw`) to verify a raw signature on-chain.

{% code overflow="wrap" %}
```solidity
function verifyED25519Signature(
    address accountAlias,
    bytes32 messageHash,
    bytes memory signatureBlob
) public returns (bool) {
    (bool success, ) = address(0x167).call(
        abi.encodeWithSignature(
            "isAuthorizedRaw(address,bytes32,bytes)",
            accountAlias,
            messageHash,
            signatureBlob
        )
    );
    return success;
}
```
{% endcode %}

**Use Case**: Validate ED25519 signatures on-chain to ensure that only authorized accounts execute sensitive operations.

***

### Integrating Multi-Sig and Threshold Keys On-Chain

Hedera’s account model supports multi-sig and threshold key configurations. You can replicate these models on-chain with `isAuthorized` for robust access control.

**Example: On-Chain Multi-Sig Verification**

This example demonstrates requiring multiple valid signatures for critical contract actions:

{% code overflow="wrap" %}
```solidity
function validateMultiSig(address accountAlias, bytes memory proposalData, bytes memory signatureBlob) public returns (bool) {
    (bool success, ) = address(0x167).call(
        abi.encodeWithSignature("isAuthorized(address,bytes,bytes)", accountAlias, proposalData, signatureBlob)
    );
    return success;
}
```
{% endcode %}

**Use Case**: Ideal for governance scenarios like DAOs where multiple stakeholders must approve actions.

**Advanced Example: Executing a DAO Proposal with Threshold Keys**

In more complex scenarios, like DAOs, you can combine multi-sig verification with actionable contract logic to enforce threshold-based governance processes:

{% code overflow="wrap" %}
```solidity
function executeProposal(address daoAccount, bytes memory proposalData, bytes memory signatures) public {
    require(validateMultiSig(daoAccount, proposalData, signatures), "Invalid signatures");
    // Execute the proposal logic here
}
```
{% endcode %}

This ensures that even on-chain actions that modify state or issue tokens adhere to your established threshold-based governance processes.

***

### Supporting Dynamic Key Rotation

Hedera’s dynamic key rotation allows you to update an account’s keys without changing its alias. By integrating `isAuthorized` checks into your contracts, your on-chain logic automatically remains in sync with the current authorized keys. Even as keys change over time to improve security or operational flexibility, your contracts don’t need to be redeployed or modified—`isAuthorized` will always reflect the latest configuration.

***

## **References**

* [**HIP-632 Documentation**](https://hips.hedera.com/HIP/hip-632)
* [**Hedera Account Service**](https://docs.hedera.com)
