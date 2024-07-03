# Creating Smart Contracts

A [smart contract](../../support-and-community/glossary.md#smart-contract) is an immutable program consisting of a set of logic (state variables, functions, event handlers, etc.) or rules that can be deployed, stored, and accessed on a [distributed ledger technology](../../support-and-community/glossary.md#distributed-ledger-technology-dlt) such as Hedera. The functions contained within a smart contract can update and manage the state of the contract and read data from the deployed contract. They may also create and call other smart contracts functions on the network. Smart contracts are secure, tamper-proof, and transparent, offering a new level of trust and efficiency.

Hedera supports any language that compiles to the Ethereum Mainnet. This includes [Solidity](../../support-and-community/glossary.md#solidity) and [Vyper](../../support-and-community/glossary.md#vyper). These programming languages compile code and produce [bytecode](../../support-and-community/glossary.md#bytecode) that the [Ethereum Virtual Machine (EVM)](../../support-and-community/glossary.md#ethereum-virtual-machine-evm) can interpret and understand.

- To learn more about the Solidity programming language, check out the documentation maintained by the Solidity team [here](https://docs.soliditylang.org/en/v0.8.19/).
- To learn more about Vyper, check out the documentation maintained by the Vyper team [here](https://docs.vyperlang.org/en/stable/).

In addition, many tools are available to write and compile smart contracts, including the popular [Remix IDE](../../support-and-community/glossary.md#remix-ide) and [Hardhat](../../support-and-community/glossary.md#hardhat). The Remix IDE is a user-friendly platform that allows you to easily write and compile your smart contracts and perform other tasks such as debugging and testing. Using these tools, you can create powerful and secure smart contracts that can be used for various purposes, from simple token transfers to complex financial instruments.

**Example**

The following is a very simple example of a smart contract written in the Solidity programming language. The smart contract defines the `owner` and `message` state variables, along with functions like `set_message` (which modifies state details by writing) and `get_message`(which reads state details).

```solidity
pragma solidity >=0.7.0 <0.8.9;

contract HelloHedera {
    // the contract's owner, set in the constructor
    address owner;

    // the message we're storing
    string message;

    constructor(string memory message_) {
        // set the owner of the contract for `kill()`
        owner = msg.sender;
        message = message_;
    }

    function set_message(string memory message_) public {
        // only allow the owner to update the message
        if (msg.sender != owner) return;
        message = message_;
    }

    // return a string
    function get_message() public view returns (string memory) {
        return message;
    }
}
```

***

## Things you should consider when creating a contract

**Automatic Token Associations**

An auto association slot is one or more slots you approve that allow tokens to be sent to your contract without explicit authorization for each token type. If this property is not set, you must approve each token before it is transferred to the contract for the transfer to be successful via the `TokenAssociateTransaction` in the SDKs. Learn more about auto-token associations [here](../accounts/account-properties.md#automatic-token-associations).

This functionality is exclusively accessible when configuring a `ContractCreateTransaction` API through the Hedera SDKs. If you are deploying a contract on Hedera using EVM tools such as Hardhat and the Hedera JSON RPC Relay, please note that this property cannot be configured, as EVM tools lack compatibility with Hedera's unique features.

**Admin Key**

Contracts have the option to have an [admin key](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_create.proto#L117). This concept is native to Hedera contracts and allows the contract account properties to be updated. Note that this does not impact the contract [bytecode](../../support-and-community/glossary.md#bytecode) and does not relate to upgradability. If the admin key is not set, you will not be able to update the following Hedera native properties (noted in [ContractUpdateTransactionBody](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto) protobuf) for your contract once it is deployed:

- [`autoRenewPeriod`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L78)
- [`memoField`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L88)
- [`max_automatic_token_associations`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L105)
- [`auto_renew_account_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L111)
- [`staked_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L116)
- [`decline_reward`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L134)

You cannot set the admin key field if you deploy a contract via tools like Hardhat. This field can be set if desired by deploying a contract using one of the Hedera [SDKs](../../sdks-and-apis/sdks/).&#x20

**Max Contract Storage Size**

Contracts on Hedera have  a storage size limit of 16,384,000 key value pairs (\~100MB).&#x20

**Rent**

While rent is not enabled for contracts deployed on Hedera today, you will want to be familiar with the concept of rent, as it may potentially impact the costs of maintaining your contract state on the network. Please refer to the Smart Contract Rent documentation [here](smart-contract-rent.md).

**Transaction and Gas Fees**

There are Hedera transaction fees and EVM fees associated with deploying a contract. To view the list of base fees, check out the fees page [here](../../networks/mainnet/fees/) and the fee estimator calculator [here](https://hedera.com/fees).

***

## Smart Contract FAQs

<details>

<summary>What is a smart contract?</summary>

A smart contract is a program that is written in a language that can be interpreted by the EVM. Please refer to the [glossary](../../support-and-community/glossary.md) for more keywords and definitions.

</details>

<details>

<summary>What programming language does Hedera support for smart contracts?</summary>

Hedera supports Solidity and Vyper.

</details>

<details>

<summary>Can I write and compile my smart contracts using Remix IDE or other Ethereum ecosystem tools? </summary>

You can use Remix IDE or other Ethereum ecosystem tools to write, compile, and deploy your smart contract on Hedera. Check out our EVM-compatible tools [here](../../#evm-compatible-tools).&#x20

</details>

<details>

<summary>Where can I find the smart contracts that are deployed to each Hedera network (previewnet, testnet, mainnet)?</summary>

On your favorite trusted Block Explorer (also called Mirror Node Explorer on Hedera). To view community-hosted explorers check out the network explorer tools page [here](../../networks/community-mirror-nodes.md).&#x20

</details>

<details>

<summary>Which ERC token standards are supported on Hedera?</summary>

Hedera supports ERC-20 and ERC-721 token standards and can find the full list of supported standards [here](tokens-managed-by-smart-contracts/).

</details>
