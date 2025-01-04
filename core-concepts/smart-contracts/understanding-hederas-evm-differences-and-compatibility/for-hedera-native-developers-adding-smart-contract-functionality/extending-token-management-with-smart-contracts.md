# Extending Token Management with Smart Contracts

## **Overview**

As a Hedera developer, you’re familiar with managing token supply through the Hedera Token Service (HTS). By integrating smart contracts, you can add programmable logic to your tokens, enabling conditional minting, burning, or transferring based on on-chain criteria. This approach allows you to design advanced tokenomics mechanisms tailored to your application’s needs.

### **Key Considerations for Tokenomics on Hedera**

* Hedera does not support native HBAR burning; custom tokenomics strategies rely on HTS for minting and burning tokens.
* The supply key grants critical permissions for token management, and its secure handling is essential.

**Recommended Practices**

* **Combine HTS and Smart Contracts**:
  * Use HTS system contract functions (`mintToken`, `burnToken`) to manage token supply programmatically within smart contracts.
  * Securely assign a supply key to your HTS token.
* **Implement Access Control**:
  * Use multi-sig accounts or role-based permissions to secure supply modifications.
  * Validate input parameters in smart contract functions to prevent misuse.

***

### **Example: Minting and Burning HTS Tokens**

The following smart contract demonstrates how to mint and burn HTS tokens using system contracts:

{% code overflow="wrap" %}
```solidity
pragma solidity ^0.8.0;

interface HederaTokenService {
    function mintToken(address token, int64 amount, bytes[] calldata metadata) external returns (int64 newTotalSupply);
    function burnToken(address token, int64 amount, bytes[] calldata metadata) external returns (int64 newTotalSupply);
}

contract TokenManager {
    HederaTokenService constant hts = HederaTokenService(0x167);
    address public tokenAddress; // HTS token with a supply key

    constructor(address _tokenAddress) {
        tokenAddress = _tokenAddress;
    }

    function mintTokens(int64 amount) external {
        hts.mintToken(tokenAddress, amount, new bytes[](0));
    }

    function burnTokens(int64 amount) external {
        hts.burnToken(tokenAddress, amount, new bytes );
    }
}
```
{% endcode %}

***

## Additional Resources

* [**HTS System Contract Functions**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-token-service)
* [**Tokens Managed by Smart Contracts**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/tokens-managed-by-smart-contracts)
* [**Accessing HTS Tokens Through the EVM**](https://docs.hedera.com/hedera/core-concepts/tokens/access-hts-tokens-through-the-evm)
