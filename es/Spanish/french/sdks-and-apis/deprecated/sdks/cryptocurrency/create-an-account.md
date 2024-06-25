# Create an account

### **Create an account using the account create API**

A transaction that creates a Hedera account. A Hedera account is required to interact with any of the Hedera network services as you need an account to pay for all associated transaction/query fees. You can visit the [Hedera Developer Portal](https://portal.hedera.com/register) to create a previewnet or testnet account. You can also use third-party wallets to generate free [mainnet accounts](../../../../networks/mainnet/mainnet-access.md). To process an account create transaction, you will need an existing account to pay for the transaction fee. To obtain the new account ID, request the [receipt](../../../sdks/transactions/get-a-transaction-receipt.md) of the transaction.

{% hint style="info" %}
When creating a **new account** using the<mark style="color:purple;">`AccountCreateTransaction()`</mark>API you will need an existing account to pay for the associated transaction fee.
{% endhint %}

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

- The account paying for the transaction fee is required to sign the transaction

**Transaction Properties**

| Field                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Key**                              | The key for the new account.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **Initial Balance**                  | The initial balance of the account, transferred from the operator account, in Hbar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Receiver Signature Required**      | <p>If true, all the account keys must sign any transaction depositing into this account (in addition to all withdrawals).</p><p><em>default: false</em></p>                                                                                                                                                                                                                                                                                                                                                                                                                |
| **Max Automatic Token Associations** | <p>Accounts can optionally automatically associate tokens to this account if this property is set. You do not need to associate a token prior to transferring it to this account. The max automatic token association is 1,000 token IDs.</p><p>Update: There is no limit on how many tokens you can associate in the 0.25 mainnet release. Currently, live on previewnet and testnet Reference <a href="https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md">HIP-23</a> and <a href="https://hips.hedera.com/hip/hip-367">HIP-367</a>.</p> |
| **Staked ID**                        | <p>The staked ID is the node ID this account is staking to or the account ID of the account this account is staking to. An account can be staked to only one node or one account at a time. See <a href="https://hips.hedera.com/hip/hip-406">HIP-406.</a><br><br><strong>Note:</strong> Accounts cannot stake to contract accounts. This will fixed in a future release.</p>                                                                                                                                                                                              |
| **Decline Rewards**                  | Some accounts may stake to a node and decline to earn rewards. If set to true, the account will not receive staking rewards. The default value is false. See [HIP-406.](https://hips.hedera.com/hip/hip-406)                                                                                                                                                                                                                                                                                               |
| **Memo**                             | Set a note or description that should be recorded with the state of the account entity (maximum length of 100 characters). Anyone can view this memo on the network.                                                                                                                                                                                                                                                                                                                                                    |
| **Auto Renew Period**                | <p>The period of time in which the account will auto-renew in seconds. The account is charged tinybars for every auto-renew period. Duration type is in seconds. For example, one hour would result in an input value of 3,600 seconds.</p><p><em>default: 7,890,000 seconds</em> (DISABLED)</p>                                                                                                                                                                                                                                                                           |

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new AccountCreateTransaction()` | Initializes the AccountCreateTransaction object |

```java
new AccountCreateTransaction()
```

#### Methods

{% tabs %}
{% tab title="V1" %}

| Method                                         | Type            | Requirement |
| ---------------------------------------------- | --------------- | ----------- |
| `setKey(<key>)`                                | Ed25519PubicKey | Required    |
| `setInitialBalance(<initialBalance>)`          | Hbar/long       | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`        | Duration        | Disabled    |
| `setReceiverSignatureRequired(<booleanValue>)` | boolean         | Optional    |
| `setProxyAccount(<accountId>)`                 | AccountId       | Disabled    |

{% code title="Java" %}

```java
//Create the transaction
AccountCreateTransaction transaction = new AccountCreateTransaction()
     .setKey(newPublicKey)
     .setInitialBalance(new Hbar(1));

//Sign with the client operator account private key and submit to a Hedera network
TransactionId txId = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the new account ID
AccountId newAccountId = receipt.getAccountId();

System.out.println("The new account ID is " +newAccountId);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new AccountCreateTransaction()
     .setKey(newPublicKey)
     .setInitialBalance(new Hbar(1));

//Sign with the client operator account private key and submit to a Hedera network
const txId = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the new account ID
const newAccountId =  receipt.getAccountId();

console.log("The new account ID is " +newAccountId);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

#### Get transaction values

### **Create an account via an account alias**

Since the introduction of [HIP-32](https://hips.hedera.com/hip/hip-32), you can create an account via an **account alias**. An account alias is a single public key address that is used to create the Hedera account. Hedera supports aliases generated using [ED25519](../../../sdks/keys/generate-a-new-key-pair.md#ed25519) or [ECDSA](../../../sdks/keys/generate-a-new-key-pair.md#ecdsa-secp256k1) (secp256k1) algorithms.

Wallet software can allow the user to create an "account" instantly, for free, even without an internet connection. In this case, it will not create an actual account on Hedera. Instead, it will simply create a public/private key pair for the user. The software will then display this as an "account" with a zero balance, with a "long account ID". This long-form doesn't look like `0.0.123`, but instead is an alias consisting of `<shard>.<realm>.<bytes>`, where the `bytes` is a [base32url](https://datatracker.ietf.org/doc/html/rfc4648#section-6) representation of the bytes of a serialized HAPI primitive `Key`, with the trailing `=` padding characters removed. For example, `0.0.CIQNOWUYAGBLCCVX2VF75U6JMQDTUDXBOLZ5VJRDEWXQEGTI64DVCGQ` is the alias address of shard 0 realm 0 with the serialization of a HAPI `Key` for the ed25519 public key `0xd75a980182b10ab7d54bfed3c964073a0ee172f3daa62325af021a68f707511a`.

The account alias is immutable, cannot be modified, and it will continue to be associated with that account for as long as it exists. Still, if the account is deleted and removed from Hedera, the alias can be associated with a new account.

The account is officially registered with Hedera when HBARs, fungible or non-fungible tokens are initially deposited to the account alias. The fee to create the accounts is charged in the transaction fee of the transfer transaction used to send the token to the account alias. The account creation transaction is executed first to register the new account and following the transfer transaction to transfer the tokens to the new account.

The consensus timestamp for the create account transaction is one nanosecond before the transfer transaction. The parent transfer transaction and the child account create transaction share the same payer account and timestamp in the transaction ID except that the child transaction has an added nonce value. You will also notice the account and transaction memo set to `auto-created account` indicating this account was created by using an account alias.

You can return the new account ID in one of the following ways:

- Requesting the [child records](../../../sdks/transactions/get-a-transaction-record.md) of the parent transfer transaction using the parent transfer transaction ID. The child transaction ID in the child transaction record will display a nonce value (`0.0.2252@1640119571.329880313/1`).
- Requesting the [child receipts](../../../sdks/transactions/get-a-transaction-receipt.md) of the parent transfer transaction using the parent transfer transaction ID. The account ID field will be populated with the new account ID.
- Requesting the receipt or record of the account create transaction by using the transaction ID of the parent transfer transaction and setting the nonce value to 1
- Requesting [account info](../../../sdks/accounts-and-hbar/get-account-info.md) using the account alias
- Looking at the parent transfer transaction record transfer list for the account that has a transfer that equals the transfer value minus the transaction fee

{% hint style="info" %}
**Note:** You cannot get the new account ID from the receipt of the transfer transaction like you would expect from a normal account create transaction that was not triggered by another transaction. You can use the alias in transfer transactions, account info and balance queries. The feature will be enabled to support all other transactions and queries in a future release.
{% endhint %}

Follow the steps below to create an account using an account alias:

- Create an ED25519 or ECDSA (secp256k1) key pair
  - The public key will be the account alias
  - The private key of the key pair you created will be the private key associated with the new Hedera account that will be created in the following step
- Convert the public key (account alias) to a Hedera account ID format (0.0.publicKey)
- Use the `TransferTransaction` and transfer HBARs, fungible, or your non-fungible token to the public key address in the Hedera account ID format
  - The transfer will trigger an `AccountCreateTransaction` and create the Hedera account for you
  - Following the transaction that creates your account, the tokens will be transferred to that account
- Get the new Hedera account ID (0.0.accountNum) by requesting the child record, child receipt, or account info (see above)

**Transaction Signing Requirements**

- The account key paying for the transfer transaction fee

**Reference HIPs**

- [HIP-32](https://hips.hedera.com/hip/hip-32)
- [HIP-542](https://hips.hedera.com/hip/hip-542)
