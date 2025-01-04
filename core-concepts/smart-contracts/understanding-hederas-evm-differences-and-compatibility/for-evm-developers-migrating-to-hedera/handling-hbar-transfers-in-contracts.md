# Handling HBAR Transfers in Contracts

## Overview

On Ethereum, sending ETH to a contract address automatically triggers the `receive()` or `fallback()` functions, allowing contracts to process incoming funds. On Hedera, these functions also exist but require HBAR to be explicitly sent via `contractCall` for them to execute. Direct HBAR transfers to a contract’s Hedera account won’t trigger any logic unless additional steps are taken.

Fortunately, the core Solidity patterns—like using `transfer()`, `send()`, or `call()`—work the same way on Hedera, making it easy for developers familiar with EVM. This guide highlights these mechanisms, details the key Hedera-specific considerations, and provides examples to help you handle HBAR transfers in your smart contracts

### **Sending to Contract**

In Solidity, there are three ways to transfer value to and from contracts:

* `transfer()`: Sends a fixed amount of gas and reverts on failure.
* `send()`: Sends a fixed amount of gas and returns `false` on failure instead of reverting.
* `call()`: A low-level function for sending value that allows specifying gas and includes additional data payloads.

These methods are supported on Hedera, ensuring compatibility with existing Solidity patterns. For more information on the supported functions, refer to the [Hedera smart contracts repo](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/solidity).

### **Key Considerations**

* **Fallback and Receive Functions**: When sending HBAR to a contract address via `contractCall`, Hedera behaves like Ethereum. If `receive()` or `fallback()` functions are defined in the contract, they will be triggered upon receipt of HBAR.
* **Important Note**: Directly transferring HBAR to a contract’s Hedera account (not via `contractCall`) will not trigger these functions. To execute logic upon receipt, ensure transfers occur within the EVM environment.

***

## Example Contract Functions for HBAR Transfers

Below is an example of contract functions and how HBAR transfers are handled using Solidity. These patterns are identical to those used for ETH on the EVM:

```solidity
// Handle incoming HBAR transfers
receive() external payable {
    // Example logic for received HBAR
    emit HbarReceived(msg.sender, msg.value);
}

// Transfer HBAR using different methods
function transferHbar(address payable _receiverAddress, uint _amount) public {
    _receiverAddress.transfer(_amount);
}

function sendHbar(address payable _receiverAddress, uint _amount) public {
    require(_receiverAddress.send(_amount), "Failed to send HBAR");
}

function callHbar(address payable _receiverAddress, uint _amount) public {
    (bool sent, ) = _receiverAddress.call{value: _amount}("");
    require(sent, "Failed to send HBAR");
}

// Event for logging received HBAR
event HbarReceived(address sender, uint256 amount);
```

{% hint style="info" %}
**Suggested Tutorial**

For developers newer to Solidity, we recommend exploring [Solidity courses](https://solidity-by-example.org/) to gain a deeper understanding of handling value transfers. A detailed tutorial on sending and receiving HBAR using Solidity smart contracts on Hedera can be found [here](https://docs.hedera.com/hedera/tutorials/smart-contracts/send-and-receive-hbar-using-solidity-smart-contracts).
{% endhint %}

***

### Additional Resources

* [**Hedera Smart Contracts Repo**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/solidity)
* [**Solidity Documentation**](https://docs.soliditylang.org/)
* [**Solidity by Example**](https://solidity-by-example.org/)
