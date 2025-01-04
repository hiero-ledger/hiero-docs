# ERC-3643 Real World Assets (RWA)

The [ERC-3643](https://docs.erc3643.org/erc-3643) token standard is designed to turn [real world assets (RWAs)](../../../support-and-community/glossary.md#real-world-asset-rwa), like company shares, loans, or real estate, into digital tokens that can be traded on the blockchain. Unlike regular tokens, it follow strict rules to make sure they meet legal requirements. With this standard, every token holder’s identity is verified to comply with regulations like [Know Your Customer (KYC)](../../../support-and-community/glossary.md#know-your-customer-kyc) and [Anti-Money Laundering (AML)](../../../support-and-community/glossary.md#anti-money-laundering-aml) laws, making it ideal for assets that need extra security and regulatory approval.

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
