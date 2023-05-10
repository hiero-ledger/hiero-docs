# Auto Account Creation

Auto account creation is a unique flow in which applications, like wallets and exchanges, can create free user "accounts" instantly, even without an internet connection. Applications can make these by generating an **account alias.** The alias account ID format used to specify the account alias in Hedera transactions comprises the shard ID, realm ID, and account alias <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark>. This is an alternative account identifier compared to the standard account number format <mark style="color:purple;">`<shardId>.<realmId>.<accountNum>`</mark><mark style="color:blue;">.</mark>

The account alias can be either one of the supported types:

<details>

<summary>Public Key</summary>

The public key alias can be an ED25519 or ECDSA secp256k1 public key type. \
\
**Example**\
\
DER Encoded ED25519 Public Key Alias:\
\
`302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
ED25519 Public Key Alias Account ID: \
\
`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`

</details>

<details>

<summary>EVM Address</summary>

The EVM address alias is created by using the rightmost 20 bytes of the 32 byte `Keccak-256` hash of an `ECDSA secp256k1` public key. This calculation is in the manner described by the [Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf). The EVM address is not equivalent to the ECDSA public key. \
\
The acceptable format for Hedera transactions is the EVM Address Alias Account ID. The acceptable format for Ethereum public addresses to denote an account address is the hex encoded public address. \
\
**Example**\
\
EVM Address: `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Encoded EVM Address: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
EVM Address Alias Account ID: `0.0.b794f5ea0ba39494ce839613fffba74279579268`

</details>

The <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> format is only acceptable when specified in the `TransferTransaction`, `AccountInfoQuery`, and `AccountBalanceQuery` transaction types. If this format is used to specify an account in any other transaction type, the transaction will not succeed.&#x20;

Reference Hedera Improvement Proposal: [HIP-583](https://hips.hedera.com/hip/hip-583)

## **Auto Account Creation Flow**

### **1. Create an account alias**

Create an account alias and convert it to the alias account ID format. The alias account ID format requires appending the shard number and realm numbers to the account alias. This form of account is purely a local account, i.e., not registered with the Hedera network.&#x20;

### **2. Deposit tokens to the account alias account ID**

Once the alias account ID is created, applications can create a transaction to transfer tokens to the alias account ID for users. Users can transfer HBAR, custom fungible or non-fungible tokens to the alias account ID. This will trigger the creation of the official Hedera account. When using the auto account creation flow, the first token transferred to the alias account ID is automatically associated to the account.

The initial transfer of tokens to the alias account ID will do a few things:

1. The system will first create a system-initiated transaction to create a new account on Hedera. This is to create a new account on Hedera officially. This transaction occurs one nanosecond before the subsequent token transfer transaction.&#x20;
2. After the new account is created, the system will assign a new account number, and the account alias will be stored with the account in the alias field in the state. The new account will have an "auto-created account" set in the account memo field.
   * For accounts created using the public key alias, the account will be assigned the account public key that matches the alias public key.&#x20;
   * For an account created via an EVM address alias, the account will not have an account public key, creating a [hollow account](auto-account-creation.md#auto-account-creation-evm-address-alias).
3. Once the new account is officially created, the token transfer transaction instantiated by the user will transfer the tokens to the new account.&#x20;
4. The account specified to pay for the token transfer transaction fees will also be charged the account creation transaction fees in tinybar.&#x20;

The above interactions introduce the concept of [parent and child transactions](../transactions-and-queries.md#nested-transactions). The parent transaction is the transaction that represents the transfer of tokens from the sender account to the destination account. The child transaction is the transaction the system initiated to create the account. This concept is important since the parent transaction record or receipt will not return the new account number ID. You must get the transaction record or receipt of the child transaction. The parent and child transactions will share the same transaction ID, except the child transaction has an added nonce value. &#x20;

{% hint style="info" %}
**parent transaction**: the transaction responsible for transferring the tokens to the alias account ID destination account.

**child transaction**: the transaction that created the new account
{% endhint %}

### **3. Get the new account number**

You can obtain the new account number in any of the following ways:

* Request the parent transaction record or receipt and set the child transaction record boolean flag equal to true.&#x20;
* Request the transaction receipt or record of the account create transaction by using the transaction ID of the parent transfer transaction and incrementing the nonce value from 0 to 1.
* Specify the account alias account ID in an `AccountInfoQuery` transaction request. The response will return the account's account number account ID.
* Inspect the parent transfer transaction record transfer list for the account with a transfer equal to the token transfer value.

## Auto Account Creation: EVM Address Alias

Accounts created with the auto account creation flow using an [EVM address alias](account-properties.md#account-alias-evm-address) result in creating a **hollow account**. Hollow accounts are incomplete accounts with an account number and alias but not an account key. The hollow account can accept token transfers into the account in this state. It cannot transfer tokens from the account or modify any account properties until the account key has been added and the account is complete.

To update the hollow account into a complete account, the hollow account needs to be specified as a transaction fee payer account for a Hedera transaction. It must also sign the transaction with the ECDSA private key corresponding to the EVM address alias. When the hollow account becomes a complete account, you can use the account to pay for transaction fees or update account properties as you would with regular Hedera accounts.

## Examples

<details>

<summary>Auto-create an account using a public key alias</summary>

:black\_circle: [Java](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AccountAliasExample.java) \
:black\_circle: [JavaScript](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/account-alias.js) \
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/alias\_id\_example/main.go) &#x20;

</details>

<details>

<summary>Auto-create an account using an EVM address (public address) alias</summary>

:black\_circle: [Java ](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AutoCreateAccountTransferTransactionExample.java)\
:black\_circle: [JavaScript ](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/transfer-using-evm-address.js)\
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/account\_create\_token\_transfer/main.go)

</details>
