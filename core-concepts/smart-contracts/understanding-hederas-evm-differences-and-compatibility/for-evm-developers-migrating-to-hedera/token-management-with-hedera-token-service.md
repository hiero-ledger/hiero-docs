# Token Management with Hedera Token Service

## Overview

Ethereum supports native ETH burning via mechanisms like [EIP-1559](https://ethereum.github.io/abm1559/notebooks/eip1559.html), but Hedera takes a different approach. The native HBAR token cannot be burned. Instead, developers can use the Hedera Token Service (HTS) to create and manage custom tokens with built-in minting and burning capabilities.

### Key Features for EVM Developers

* **Supply Key**
  * Controls minting and burning of tokens.
  * Must be securely managed to prevent unauthorized actions.
* **HTS System Contract**
  * System contract functions accessible via reserved address `0x167`.
  * Enable token creation, minting, and burning directly from Solidity contracts.
* **Access Control**
  * Only addresses authorized by the supply key can mint or burn tokens.
  * Multi-signature (multi-sig) or threshold key configurations can enhance security.

***

### Minting and Burning Tokens with HTS

* **Minting Tokens**: Introduce new tokens for incentives, rewards, or liquidity.
* **Burning Tokens**: Remove tokens to increase scarcity or meet regulatory requirements.

#### Code Example: HTS Mint/Burn

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: Apache-2.0
pragma solidity ^0.8.0;

interface HederaTokenService {
    function createFungibleToken(
        address treasury,
        uint64 initialSupply,
        string memory tokenName,
        string memory tokenSymbol,
        uint32 decimals
    ) external returns (address tokenAddress);

    function mintToken(address token, int64 amount, bytes[] calldata metadata) external returns (int64 newTotalSupply);

    function burnToken(address token, int64 amount, bytes[] calldata metadata) external returns (int64 newTotalSupply);
}

contract TokenManager {
    HederaTokenService constant hts = HederaTokenService(0x167);
    address public token;

    constructor(address treasury) {
        // Create a fungible token with an initial supply of 1,000 units
        // Token parameters: name = "MyHederaToken", symbol = "MHT", decimals = 8
        token = hts.createFungibleToken(treasury, 1000, "MyHederaToken", "MHT", 8);
    }

    // Mint additional tokens. Ensure that msg.sender holds the supply key or is authorized.
    function mintMoreTokens(int64 amount) external {
        // Metadata array left empty, but can be used for NFT-like functionality or extra data
        hts.mintToken(token, amount, new bytes[](0));
    }

    // Burn existing tokens. Ensure the caller is authorized via supply key management.
    function burnSomeTokens(int64 amount) external {
        hts.burnToken(token, amount, new bytes[](0));
    }
}
```
{% endcode %}

**Important Notes**

* Ensure the caller holds the supply key.
* Associate the treasury account with the token for successful operations.
* Minting and burning fail without proper key authorization.

***

## Additional Resources

* [**Access HTS Tokens Through the EVM**](https://docs.hedera.com/hedera/core-concepts/tokens/access-hts-tokens-through-the-evm)
* [**HTS System Contract Functions**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-token-service)
