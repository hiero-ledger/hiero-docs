# Decimal Handling (8 vs. 18 Decimals)

## **Overview**

Managing token decimals is critical when working with HBAR, HTS tokens, and ERC tokens on Hedera, as each system has distinct precision standards. These differences impact how token balances are calculated, displayed, and transferred across various tools and environments.

***

## Token Decimal Comparison and API Context

The table below compares the decimal handling of HBAR, HTS tokens, and ERC tokens on Hedera, incorporating details about their representation across APIs and services. This overview highlights differences in precision and context.

<table><thead><tr><th width="186">API/Service</th><th width="130">Decimals</th><th>Explanation</th></tr></thead><tbody><tr><td><strong>Hedera API (HAPI)</strong></td><td>8 decimals</td><td>HBAR is represented with 8 decimal places, aligning with its native smallest unit tinybar.</td></tr><tr><td><strong>Hedera Smart Contract Service</strong></td><td>8 decimals</td><td>Within the EVM environment, HBAR maintains 8 decimal places, consistent with its native representation.</td></tr><tr><td><strong>JSON-RPC Relay (Arguments)</strong></td><td>8 decimals</td><td>When HBAR values are passed as arguments in JSON-RPC calls, they are represented with 8 decimal places.</td></tr><tr><td><strong>JSON-RPC Relay (<code>msg.value</code>)</strong></td><td>18 decimals</td><td>For compatibility with EVM tooling, <code>msg.value</code> in JSON-RPC Relay represents HBAR with 18 decimal places. Consequently, <code>gasPrice</code> also uses 18 decimal places in this context.</td></tr><tr><td><strong>HTS Tokens</strong></td><td>Configurable (up to 8 decimals)</td><td>HTS tokens allow token creators to define precision at token creation, offering flexibility for various use cases.</td></tr><tr><td><strong>ERC Tokens</strong></td><td>Default 18 decimals</td><td>ERC tokens on Hedera follow Ethereum token standards, with 18 decimals as the default unless specified otherwise.</td></tr></tbody></table>

**Key Impacts**:

* Account for scaling differences when converting HBAR between APIs, especially when using JSON-RPC.
* HBAR fees are always calculated in tinybars, regardless of the API or service used.
* JSON-RPC’s use of 18 decimals ensures smooth integration with EVM tools and libraries.

***

## Conversion Helpers

Utility functions are essential for managing discrepancies between HBAR (measured in tinybars, 8 decimals), HTS tokens (which can have configurable decimal places), and ERC tokens (measured in wei, 18 decimals). These conversions ensure consistency across your smart contracts, front-end applications, and APIs.

**Code Example: Decimal Conversion Helpers**

{% code overflow="wrap" %}
```solidity
// Convert from 18 decimals (weibar/wei) to 8 decimals (tinybar)
function convertToTinybar(uint256 weiAmount) public pure returns (uint256) {
    // 1 tinybar = 10^10 weibar
    return weiAmount / (10 ** 10);
}

// Convert from 8 decimals (tinybar) to 18 decimals (weibar/wei)
function convertToWei(uint256 tinybarAmount) public pure returns (uint256) {
    return tinybarAmount * (10 ** 10);
}
```
{% endcode %}

Reference: [**Smart Contracts Gas and Fees**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/gas-and-fees)

***

### **Additional Resources**

* [**ERC-20 Token Standard**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/tokens-managed-by-smart-contracts/erc-20-fungible-tokens)
* [**Hedera Token Service Documentation**](https://docs.hedera.com/hedera/core-concepts/tokens)
* [**HBAR Decimal Places Documentation**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/hbars#hbar-decimal-places)
* [**Token Managed by Smart Contracts**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/tokens-managed-by-smart-contracts)
