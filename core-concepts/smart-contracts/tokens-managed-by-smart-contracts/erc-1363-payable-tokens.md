# ERC-1363 (Payable Tokens)

The [ERC-1363](https://erc1363.org/) standard, also known as the payable token standard, is an upgrade to [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) tokens. While ERC-20 tokens can only be sent from one person to another, ERC-1363 tokens can trigger actions in a smart contract immediately after being sent or approved for spending.&#x20;

For example, if you use one of these tokens to pay for a subscription, the contract can instantly recognize the payment and activate your subscription with no extra steps. This makes the standard useful for quick transactions like buying services, paying invoices, or managing subscriptions, all in one easy step.

{% hint style="info" %}
**Note:** Hedera’s system contract functions do not natively support `ERC-1363` functionalities on HTS tokens. However, standard `ERC-1363` functions can still be implemented within a smart contract and deployed on the network, similar to other EVM-compatible chains.
{% endhint %}

### **Interface `ERC-1363` Functions**

<details>

<summary><strong>transferAndCall</strong></summary>

{% code overflow="wrap" %}
```solidity
function transferAndCall(address to, uint256 value, bytes calldata data) external returns (bool);
```
{% endcode %}

Transfers tokens and calls a function on the recipient contract in a single transaction.

</details>

<details>

<summary><strong>approveAndCall</strong></summary>

{% code overflow="wrap" %}
```solidity
function approveAndCall(address spender, uint256 value, bytes calldata data) external returns (bool);
```
{% endcode %}

</details>

***

### Additional References

* [ERC-1363](https://erc1363.org/)&#x20;
* [ERC-165](https://eips.ethereum.org/EIPS/eip-165)
* [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)
