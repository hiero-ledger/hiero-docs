# ERC-3643 Real World Assets (RWA)

The [ERC-3643](https://docs.erc3643.org/erc-3643) token standard is variously known as "permissioned tokens", "real world asset tokens" or "T-REX (Token for Regulated EXchanges)". As the names suggest, ERC-3643 is designed to turn [real world assets (RWAs)](../../../support-and-community/glossary.md#real-world-asset-rwa), like company shares, loans, or real estate, into digital tokens that can be traded on the blockchain. Unlike regular tokens, it follow strict rules to make sure they meet legal requirements. With this standard, every token holder’s identity is verified to comply with regulations like [Know Your Customer (KYC)](../../../support-and-community/glossary.md#know-your-customer-kyc) and [Anti-Money Laundering (AML)](../../../support-and-community/glossary.md#anti-money-laundering-aml) laws, making it ideal for assets that need extra security and regulatory approval.

ERC-3643 tokens integrate identity management through ONCHAINID, where verified participant identities are securely stored on-chain. Token transfers follow strict compliance rules, ensuring regulatory requirements are met before execution. While enhancing security and compliance, ERC-3643 remains interoperable with existing ERC-20 platforms, enabling seamless integration into blockchain ecosystems.

{% hint style="info" %}
**Note**: Hedera’s system contract functions do not natively support `ERC-3643` functionalities on HTS tokens. However, standard `ERC-3643` functions can still be implemented within a smart contract and deployed on the network, similar to other EVM-compatible chains.
{% endhint %}

### **Interface `ERC-3643` Functions**

<details>

<summary><strong>setOnchainID</strong></summary>

```solidity
function setOnchainID(address _onchainID) external;
```

Sets the token's onchain ID. Only the owner of the token contract can call this function.

</details>

<details>

<summary><strong>setIdentityRegistry</strong></summary>

```solidity
function setIdentityRegistry(address _identityRegistry) external; 
```

RWA tokens link to verified identities on-chain managed through a decentralized identity system.

</details>

<details>

<summary><strong>setIdentityRegistry</strong></summary>

```solidity
function setIdentityRegistry(address _identityRegistry) external
```

`setIdentityRegistry` allow contract owners additional administrative functions to manage compliance and identity registry settings.

</details>

<details>

<summary><strong>setComplianceContract</strong></summary>

```solidity
function setComplianceContract(address _compliance) external
```

`setComplianceContract` allow contract owners additional administrative functions to manage compliance and identity registry settings.

</details>

<details>

<summary><strong>forcedTransfer</strong></summary>

```solidity
function forcedTransfer(
    address _from,
    address _to,
    uint256 _amount
) external returns (bool);
```

Forces a transfer of tokens between two whitelisted addresses. Only an agent of the token can call this function.

</details>

***

### **Additional References**

* [ERC-3643](https://docs.erc3643.org/erc-3643)
* [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)
* The original EIP can be found [here](https://eips.ethereum.org/EIPS/eip-3643)

To get a deeper understanding of ERC-3643, see the following:

#### 1. Implementation Details and Function Interactions

For comprehensive implementations of functions like `setIdentityRegistry` and their interactions with compliance modules or identity registries, refer to the [Tokens Interface](https://docs.erc3643.org/erc-3643/smart-contracts-library/permissioned-tokens/tokens-interface) section.\
This section provides detailed function definitions and their roles within the ERC-3643 framework.

#### 2. Supporting Contracts

* **Identity Registry:**\
  The [Identity Registry Interface](https://docs.erc3643.org/erc-3643/smart-contracts-library/onchain-identities/identity-registry/identity-registry-interface) section details the contract responsible for managing and verifying investor identities, ensuring compliance with KYC/AML regulations.
* **Compliance Management:**\
  The [Compliance Interface](https://docs.erc3643.org/erc-3643/smart-contracts-library/compliance-management/compliance-interface) section outlines the contract that enforces compliance rules during token transfers, ensuring adherence to regulatory requirements.

#### 3. Token Logic Examples

For insights into token operations:

* Refer to the [Tokens Interface](https://docs.erc3643.org/erc-3643/smart-contracts-library/permissioned-tokens/tokens-interface) section for details on the `transfer` function implementation.\
  This demonstrates how compliance rules are integrated into standard ERC-20-like operations.

#### 4. Forced Transfer Logic

For details on the `forcedTransfer` function, including permission checks and enforcement of whitelisting, refer to the [Tokens Interface](https://docs.erc3643.org/erc-3643/smart-contracts-library/permissioned-tokens/tokens-interface) section.

***

**Contributor**: [**@sumanair** ](https://github.com/sumanair)
