# Smart Contract Addresses

After a smart contract is deployed on Hedera, it is associated with a unique smart contract address. There are two types of addresses a smart contract can be referenced by in the system:

**➡** [**Smart Contract EVM Address**](smart-contract-addresses.md#evm-address)

**➡** [**Smart Contract ID**](smart-contract-addresses.md#contract-id)

***

### EVM Address

The standard smart contract EVM address is the address that is compatible with EVM. The EVM contract address is returned by the system once the contract is deployed. This is the address format that is commonly used in the Ethereum ecosystem. You can use the smart contract EVM address to reference smart contracts in Ethereum Ecosystem tools like [Hardhat](../../support-and-community/glossary.md#hardhat) and [MetaMask](../../support-and-community/glossary.md#metamask).

Example Contract EVM Address hex encoded contract ID: `0x00000000000000000000000000000000002cd37f`

{% hint style="info" %}
_**Note:** Contracts deployed using the `ContractCreate` Hedera API transactions will have this form (For example, using ContractCreateTransaction in the SDKs). All other deployment cases will be in the standard EVM address, post_ [_HIP-729_](https://hips.hedera.com/hip/hip-729)_._
{% endhint %}

Example Contract EVM Address: [`0x86ecca95fecdb515d068975b75eac4357contractd6e86c5`](https://hashscan.io/mainnet/contract/0.0.2958097?p=1\&k=1685819177.474035003)

***

### Contract ID

In the Hedera Network, smart contracts can also be identified by a smart contract ID. A smart contract ID is a contract identifier native to the Hedera network. Both the smart contract EVM address and smart contract ID are accepted identifiers for a smart contract when interacting with the contract on Hedera using the Hedera transactions.

Example Contract ID: `0.0.123`

In some cases, the EVM address is the hex-encoded format of the contract ID.

The smart contract ID is **not a compatible** address format accepted or known in the Ethereum ecosystem. For example, if you use MetaMask, you will not specify the contract by its contract ID and instead use its EVM address.

When viewing the contract information, you may see both types of addresses noted in Hedera Network Explorers like [HashScan](https://hashscan.io/).

<figure><img src="../../.gitbook/assets/contract ID.png" alt=""><figcaption><p>EVM address &#x26; contract ID example on HashScan</p></figcaption></figure>

***

### Smart Contract Accounts

Similar to [Ethereum](../../support-and-community/glossary.md#ethereum), Smart Contract entities are also a type of account. A smart contract deployed on Hedera can hold [HBAR](../../support-and-community/glossary.md#hbar), [fungible](../../support-and-community/glossary.md#fungible-token), and [non-fungible tokens](../../support-and-community/glossary.md#non-fungible-token-nft).

<table><thead><tr><th width="289">Smart Contract Property</th><th>Example</th></tr></thead><tbody><tr><td><strong>Smart Contract ID</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0.0.2940467</a></td></tr><tr><td><strong>Smart Contract EVM Address</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0xde2b7414e2918a393b59fc130bceb75c3ee52493</a></td></tr><tr><td><strong>Smart Contract Hex Encoded Contract ID</strong></td><td>0x00000000000000000000000000000000002cff73<br>*<em>This is only present if the contract was NOT deployed via an EVM tool and instead the Hedera SDKs.</em></td></tr><tr><td><strong>Smart Contract Account ID</strong></td><td><a href="https://hashscan.io/mainnet/account/0.0.2940467?app=false&#x26;ph=1&#x26;pt=1&#x26;p2=1&#x26;p1=1">0.0.2940467</a></td></tr></tbody></table>
