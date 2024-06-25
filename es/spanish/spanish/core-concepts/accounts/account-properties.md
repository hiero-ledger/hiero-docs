# Account Properties

## Account ID

The account ID is the ID of the account entity on the Hedera network. The account ID includes the **shard number**, **realm number**, and an **account** <mark style="color:purple;">`<shardNum>.<realmNum>.<account>`</mark>**.** The account ID is used to specify the account in all Hedera transactions and queries. There can be more than one account ID that can represent an account.\\

<details>

<summary>Shard Number</summary>

Format: **`shardNum`**`.realmNum.account`

The shard number is the number of the shard the account exists in. A shard is a partition of the data received by the nodes participating in a given shard. Today, Hedera operates in only one shard. This value will remain zero until Hedera operates in more than one shard. This value is non-negative and is 8 bytes.\
\
Default: `0`

</details>

<details>

<summary>Realm Number</summary>

Format: `shardNum.`**`realmNum`**`.account`\
\
The realm number is the number of the realm the account exists within a given shard. Today, Hedera operates in only one realm. This value will remain zero until Hedera operates in more than one shard. This value is non-negative and is 8 bytes. The account can only belong to precisely one realm. The realm ID can be reused in other shards.\
\
Default: `0`

</details>

<details>

<summary><strong>Account</strong></summary>

Format: `shardNum.realmNum.`**`account`**

The `account` can be one of the following:\
\
:black\_circle: [Account Number](account-properties.md#account-number)\
:black\_circle: [Account Alias](account-properties.md#account-alias)

</details>

### **Account Number**

Each Hedera account has a system-provided **account number** when the account is created. An account number is a non-negative number of 8 bytes. You can use the account number to specify the account in all Hedera transactions and query requests. Account numbers are unique and immutable. The account number for a newly created account is returned in the transaction receipt or transaction record for the transaction ID that created the account. The account number ID has the following format <mark style="color:purple;">`<shardNum>.<realmNum>.<accountNum>`</mark><mark style="color:blue;">.</mark>

<table><thead><tr><th width="199">Account Number ID</th><th width="523.3333333333333">Description</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>The account number 10 in account number ID format.</td></tr></tbody></table>

#### Account Number Alias

All accounts can have an **account number alias.** An account number alias is a hex-encoded form of the account number prefixed with 20 bytes of zeros. It is an EVM-compatible address that references the Hedera account. The account number alias does not contain the shard ID and realm ID.

This account property is not stored in consensus node state. You will not see this value returned when querying the consensus nodes for the account object and inspecting the account alias field.\
\
The mirror node will calculate the account number alias from the account number. The account number alias is calculated and returned in account REST APIs only when the account does not have an existing account alias. For example, if the account was created through the [auto account creation](auto-account-creation.md) flow using an account alias the account number alias will not be populated. If the account was normally created then the account alias field will store the account number alias.

<table><thead><tr><th width="175">Account ID</th><th>Account Number Alias Example</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>The hex encoding value for 10 is "0a."<br><br><code>000000000000000000000000000000000000000a</code></td></tr></tbody></table>

### Account Alias

Some Hedera accounts will have an **account alias**. Account aliases are a pointer to the account object in addition to being identified by the account number. Account aliases are assigned to the account when the account is created via the [auto account creation](./#auto-account-creation) flow. The network does not generate the account alias; instead, the user specifies the account alias upon account creation. This property will be null if an account is created through the normal account creation flow. The account aliases are unique and immutable. The account alias ID has the following format <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark><mark style="color:blue;">.</mark>

This format is only acceptable when specified in the `TransferTransaction`, `AccountInfoQuery` and `AccountBalanceQuery`. If this format is used to reference an account in any other transaction type the transaction will not succeed.

The `alias` can be one of the following alias types:

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>Public Key</td><td><a href="account-properties.md#public-key-account-alias">#public-key-account-alias</a></td></tr><tr><td>EVM Address</td><td><a href="account-properties.md#public-key-account-alias">#public-key-account-alias</a></td></tr></tbody></table>

#### Public Key Account Alias

The account alias public key is the public key of an ECDSA secp256k1 or ED25519 key type. The public key account ID format is <mark style="color:blue;">`<shardNum>.<realmNum>.<alias>`</mark> where <mark style="color:blue;">`alias`</mark> is the public key. This format is created using the bytes of a simple cryptographic public key supported by Hedera. The public key account alias is not required to match the account's [public key](account-properties.md#keys) used to determine the cryptographic signature needed to sign transactions for the account.

<details>

<summary>Example Public Key Alias Account ID</summary>

`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`

</details>

#### **EVM Address Account Alias**

An EVM address account alias is the rightmost 20 bytes of the 32-byte `Keccak-256` hash of the `ECDSA` public key of the account. This calculation is in the manner described by the [Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf). Note that the recovery id is not formally part of the public key and is not included in the hash. This is calculated on the consensus nodes using the `ECDSA` key provided in the [auto account creation](auto-account-creation.md) flow. The EVM address is also commonly known as the public address. The EVM address account ID format is <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> where <mark style="color:purple;">`alias`</mark> is the EVM address.

The EVM address account and the [account number alias](account-properties.md#account-number-alias) are 20-byte values. They can be differentiated because the account number alias is always prefixed with 12 bytes. The EVM address account alias is commonly used in wallets and tools to represent account addresses.

<details>

<summary>EVM Address Account Alias Account ID Example</summary>

The shard number and realm number are set to 0 followed by the EVM address.

\
**Example**\
\
EVM Address: `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Encoded EVM Address: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
EVM Address Account Alias Account ID:\
\
`0.0.b794f5ea0ba39494ce839613fffba74279579268`

</details>

Reference Hedera Improvement Proposal: [HIP-583](https://hips.hedera.com/hip/hip-583)

## Account Memo

A memo is like a short note that lives with the account object in the ledger state and can be viewed on a network explorer when looking up the account. This account memo is limited to 100 characters. The account memo is mutable and can be updated or removed from the account at any time. The account key is required to sign the transaction to facilitate any changes to this property.

{% hint style="warning" %}
Do not post any private information in the account memo field. This field is visible to all participants in the network.
{% endhint %}

## Account Nonce

Accounts on Hedera can submit `EthereumTransaction` types processed by the Ethereum Virtual Machine (EVM) on a consensus node. The nonce on the account represents a sequentially incrementing count of the transactions submitted by an account through the `EthereumTransaction` type. The default account nonce value is set to zero.

Reference Hedera Improvement Proposal: [HIP-410](https://hips.hedera.com/hip/hip-410)

## Automatic Token Associations

Hedera accounts must generally approve custom tokens before transferring them into the receiving account. The receiving account must sign the transaction that will associate the tokens, allowing the specified tokens to be deposited into their account. The automatic token association feature allows the account to bypass manually associating the custom token before transferring it into the account.

Accounts can automatically approve up to 5,000 tokens without manually preauthorizing each custom token. Suppose an account needs to hold a balance for custom tokens greater than 5,000. In that case, the account must manually approve each additional token using the transaction to associate the tokens. There is no limit on the total number of tokens an account can hold. This property is mutable and can be changed after it is set.

Reference Hedera Improvement Proposal: [HIP-23](https://hips.hedera.com/hip/hip-23)

## Balances

When a new account is created, you can specify an initial HBAR balance for the account. The initial HBAR balance for the token is deducted from the account that is paying to create the new account. Creating an account with an initial balance is optional.

A Hedera account can hold a balance of HBAR and custom fungible and non-fungible tokens (NFTs). Account balances can be viewed on a [Network Explorer](../../networks/community-mirror-nodes.md) and queried from mirror node REST APIs or consensus nodes.

| Token Type                                       | Description                                                                                               | Token ID Example                                                                                          |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **HBAR**                                         | The native Hedera fungible token used to pay for transaction fees and secure the network. | None                                                                                                      |
| **Fungible Token**                               | Custom fungible tokens created on Hedera.                                                 | <p>The fungible token ID is represented as <code>0.0.tokenNum</code><br><br>ex: <code>0.0.100</code></p>  |
| **Non-Fungible Token (NFTs)** | Custom non-fungible tokens (NFTs) created on Hedera.                   | <p>NFT ID is represented as <code>0.0.tokenNum-serialNum</code>.<br></p><p>ex: <code>0.0.101-1</code></p> |

## Keys

Each account is required to have at least one key upon creation. If a key is not supplied at the time of account creation, the network will reject the transaction. The individual(s) that have access to the account's private key(s) have access to authorize the transfer of tokens into or out of the account and are required to sign transactions that modify the account. Modifying the account includes changing any property, like the balance, keys, memo, etc.

Accounts can optionally have more than one key associated with them. These kinds of accounts are multi-signature accounts meaning you will require more than one key to sign the transaction to change a property on an account or debit HBARs. The signing requirements for a multi-signature account depend on the account's key chosen key structure. For support of key structures and key types, follow the link below.

{% content-ref url="../keys-and-signatures.md" %}
[keys-and-signatures.md](../keys-and-signatures.md)
{% endcontent-ref %}

{% hint style="danger" %}
Warning: The private key(s) associated with the account is not to be shared with anyone as it will allow others to authorize transactions from your account on your behalf. Sharing your private key is like sharing your bank account password. Please make sure your private keys are stored in a secure wallet.
{% endhint %}

## Receiver Signature Required

Accounts can optionally require the account to sign any transactions depositing tokens into the account. This feature is set to false by default. If this feature is set to true, the account will be required to sign all transactions that deposit tokens into the account. This property is mutable and can be updated after the account is created.

## Staking

Staking in Hedera is taking an account and associating the HBAR balance to a node in the network. Custom fungible or non-fungible token balances an account holds do not contribute to staking on the network. The purpose of staking accounts to a node on the network is to strengthen the security of the network. To contribute to the security of the network, staked accounts can earn rewards in HBAR. Please see this [guide](https://docs.hedera.com/hedera/core-concepts/staking) for additional information about the staking rewards program. Contracts can also stake their accounts to earn rewards.

{% hint style="info" %}
An account can only stake to one node or one account at any given time.
{% endhint %}

<details>

<summary>Staked Node ID</summary>

An account can optionally elect to stake its HBAR to a node in the Hedera network. The staked node ID is the node an account can stake to. The full balance of the account is staked to the node. Do not confuse the node ID with the node's account ID. If you stake to the node's account ID, your account will not earn staking rewards.\
\
The staked account balance is liquid at all times. This means you can transfer HBAR tokens in and out of the account, and your account will continue to be staked to the node without disruption.\
\
There is no lock-up period. This means the HBAR tokens in your account are not held for a period of time before you can use them.\
\
The node ID for a node can be found [here](https://docs.hedera.com/hedera/networks/mainnet/mainnet-nodes) or can be queried from the [nodes REST API](https://testnet.mirrornode.hedera.com/api/v1/docs/#/network/getNetworkNodes).\
\
Example:\
\
Node ID: `1`\\

</details>

<details>

<summary>Staked Account ID</summary>

An account can optionally elect to stake its HBAR to another account in the Hedera network. This is known as **indirect staking**. The staked account ID is the ID of the account to stake to. The full balance of the account is staked to the specified account.\
\
There is no lock-up period and the balance is always liquid just like staking to a node.\
\
Accounts that stake to another account do not earn the staking rewards. For example, If account A is staked to account B, account B will need to be staked to a node in order to contribute to network security and earn staking rewards. Account B will earn the rewards for staking when staked to a node for both the HBAR balances in both Account A + Account B. Account A will not earn rewards for staking.\
\
Example:\
\
Account ID: `0.0.10`

</details>

<details>

<summary>Decline to Earn Staking Rewards</summary>

Accounts can decline to earn staking rewards when they stake to a node or an account. The staked account still contributes to the staking weight of the node, but does not earn rewards or is calculated as part of the payment of the rewards to the other accounts that have elected to earn rewards. By default, all staked accounts will earn rewards unless this boolean flag is set to true. This election can be changed by updating the account properties. Hedera treasury accounts enable this flag to decline earning staking rewards.\
\
Default: `true` (all accounts accept earning staking rewards if the account is staked)

</details>

### Staking Information

The network stores the staking metadata for an account and contract accounts. This information is returned in account information query requests (`AccountInfoQuery` or`ContractInfoQuery`). The staking metadata for an account includes the following information:

- **decline\_reward:** whether or not the account declined to earn staking rewards
- **stake\_period\_start:** The staking period during which either the staking settings for this account or contract changed (such as starting staking or changing staked\_node\_id) or the most recent reward was earned, whichever is later. If this account or contract is not currently staked to a node, then this field is not set. The stake period is 24 hours, starting UTC midnight.
- **pending\_reward:** The amount in tinybars that will be received in the next staking reward payment
- **staked\_to\_me:** The total tinybar balance of all accounts staked to this account
- **staked\_id:** ID of the account or node to which this account or contract is staking
  - **staked\_account\_id:** The account to which this account or contract is staking to
  - **staked\_node\_id:** The ID of the node this account or contract is staked to

Reference Hedera Improvement Proposal: [HIP-406](https://hips.hedera.com/hip/hip-406)

## Auto Renewals & **Expiration**

{% hint style="warning" %}
Auto-renewals and expiration (rent) are currently not enabled.
{% endhint %}

Like the other Hedera entities, accounts take up network storage. To cover the cost of storing an account, a renewal fee will be charged for the storage utilized on the network. This feature is not enabled on the network today; however, in the future, when it is enabled, the account must have sufficient funds to pay for the renewal fees.

The amount charged for renewal will be charged every pre-determined period in seconds. The interval of time the account will be charged is the auto-renew period. The system will automatically charge the account renewal fees. If the account does not have an HBAR balance, it will be suspended for one week before it is deleted from the ledger. You can renew an account during the suspension period.

<details>

<summary>Expiration Time</summary>

The effective consensus timestamp at (and after) which the entity is set to expire.

</details>

<details>

<summary><strong>Auto Renew Account</strong></summary>

The auto renew account is the account that will be used to pay for the auto renewal fees. If there is no auto renew account specified, the auto renewal fees will be charged to the account.

</details>

<details>

<summary><strong>Auto Renew Period</strong></summary>

The interval at which this account will be charged the auto renewal fees. The maximum auto renew period for an account is be limited to 3 months (8000001 sec seconds). The minimum auto renew period is 30 days. The auto renew period is mutable and can be updated at any time. If there are insufficient funds, then it extends as long as possible. If it is empty when it expires, then it is deleted.

</details>

Reference Hedera Improvement Proposal: [HIP-16](https://hips.hedera.com/hip/hip-16)
