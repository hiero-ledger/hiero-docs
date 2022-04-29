# Create an account

### **Create an account using the account create API**

A transaction that creates a Hedera account. A Hedera account is required to interact with any of the Hedera network services as you need an account to pay for all associated transaction/query fees. You can visit the [Hedera Developer Portal](https://portal.hedera.com/register) to create a previewnet or testnet account. You can also use third-party wallets to generate free [mainnet accounts](../../../mainnet/mainnet-access.md). To process an account create transaction, you will need an existing account to pay for the transaction fee. To obtain the new account ID, request the [receipt](../transactions/get-a-transaction-receipt.md) of the transaction.

{% hint style="info" %}
When creating a **new account** using the **** <mark style="color:purple;">`AccountCreateTransaction()`</mark>API you will need an existing account to pay for the associated transaction fee.&#x20;
{% endhint %}

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

* The account paying for the transaction fee is required to sign the transaction&#x20;

**Transaction Properties**

| Field                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Key**                              | The key for the new account.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Initial Balance**                  | The initial balance of the account, transferred from the operator account, in Hbar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **Receiver Signature Required**      | <p>If true, all the account keys must sign any transaction depositing into this account (in addition to all withdrawals).</p><p><em>default: false</em></p>                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **Max Automatic Token Associations** | <p>Accounts can optionally automatically associate tokens to this account if this property is set. You do not need to associate a token prior to transferring it to this account. The max automatic token association is 1,000 token IDs.</p><p></p><p>Update: There is no limit on how many tokens you can associate in the 0.25 mainnet release. Currently, live on previewnet and testnet Reference <a href="https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md">HIP-23</a> and <a href="https://hips.hedera.com/hip/hip-367">HIP-367</a>.</p> |
| **Memo**                             | Set a note or description that should be recorded with the state of the account entity (maximum length of 100 characters). Anyone can view this memo on the network.                                                                                                                                                                                                                                                                                                                                                                                                              |
| **Auto Renew Period**                | <p>The period of time in which the account will auto-renew in seconds. The account is charged tinybars for every auto-renew period. Duration type is in seconds. For example, one hour would result in an input value of 3,600 seconds.<br><br><strong>NOTE:</strong> This is fixed to approximately 3 months (7890000 seconds). Any other value will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</p><p><em>default: 2,592,000 seconds</em> (DISABLED)</p>                                                                                                       |
| **Proxy Account**                    | ID of the account to which this account is proxy staked **(**DISABLED).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new AccountCreateTransaction()` | Initializes the AccountCreateTransaction object |

```java
new AccountCreateTransaction()
```

#### Methods

{% tabs %}
{% tab title="V2" %}
| Method                                         | Type                                           | Requirement |
| ---------------------------------------------- | ---------------------------------------------- | ----------- |
| `setKey(<key>)`                                | Key                                            | Required    |
| `setInitialBalance(<initialBalance>)`          | HBar                                           | Optional    |
| `setReceiverSignatureRequired(<booleanValue>)` | boolean                                        | Optional    |
| `setMaxAutomaticTokenAssociations(<amount>)`   | int                                            | Optional    |
| `setAccountMemo(<memo>)`                       | String                                         | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`        | Duration                                       | Disabled    |
| `setProxyAccount(<accountId>)`                 | [AccountId](../specialized-types.md#accountid) | Disabled    |

{% code title="Java" %}
```java
//Create the transaction
AccountCreateTransaction transaction = new AccountCreateTransaction()
    .setKey(privateKey.getPublicKey())
    .setInitialBalance(new Hbar(1000));

//Submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the account ID
AccountId newAccountId = receipt.accountId;

System.out.println("The new account ID is " +newAccountId);

//Version 2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new AccountCreateTransaction()
    .setKey(privateKey.publicKey)
    .setInitialBalance(new Hbar(1000));

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the account ID
const newAccountId = receipt.accountId;

console.log("The new account ID is " +newAccountId);

//v2.0.5
```
{% endcode %}

{% code title="Go" %}
```go
//Create the transaction
transaction := hedera.NewAccountCreateTransaction().
        SetKey(privateKey.PublicKey()).
        SetInitialBalance(hedera.NewHbar(1000))

//Sign the transaction with the client operator private key and submit to a Hedera network
txResponse, err := AccountCreateTransaction.Execute(client)
if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the account ID
newAccountId := *receipt.AccountID

fmt.Printf("The new account ID is %v\n", newAccountId)

//Version 2.0.0
```
{% endcode %}
{% endtab %}

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

{% tabs %}
{% tab title="V2" %}
| Method                           | Type     | Description                                               |
| -------------------------------- | -------- | --------------------------------------------------------- |
| `getKey()`                       | Key      | Returns the public key on the account                     |
| `getInitialBalance()`            | Hbar     | Returns the initial balance of the account                |
| `getAutoRenewPeriod()`           | Duration | Returns the auto renew period on the account              |
| `getReceiverSignatureRequired()` | boolean  | Returns whether the receiver signature is required or not |

{% code title="Java" %}
```java
//Create an account with 1,000 hbar
AccountCreateTransaction transaction = new AccountCreateTransaction()
    // The only _required_ property here is `key`
    .setKey(newKey.getPublicKey())
    .setInitialBalance(new Hbar(1000));

//Return the key on the account
Key accountKey = transaction.getKey();
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create an account with 1,000 hbar
const transaction = new AccountCreateTransaction()
    // The only _required_ property here is `key`
    .setKey(newKey.getPublicKey())
    .setInitialBalance(new Hbar(1000));

//Return the key on the account
const accountKey = transaction.getKey();
```
{% endcode %}

{% code title="Go" %}
```go
//Create an account with 1,000 hbar
AccountCreateTransaction := hedera.NewAccountCreateTransaction().
    SetKey(newKey.PublicKey()).
        SetInitialBalance(hedera.NewHbar(1000))

//Return the key on the account
accountKey, err := AccountCreateTransaction.GetKey()
```
{% endcode %}
{% endtab %}
{% endtabs %}

### **Create an account via an account alias**

You can create an account via an **account alias**. An account alias is a single public key address that is used to create the Hedera account. Hedera supports aliases generated using [Ed25519](../keys/generate-a-new-key-pair.md#ed25519) or [ECDSA](../keys/generate-a-new-key-pair.md#ecdsa-secp256k1) (secp256k1) algorithms. The account alias is immutable and cannot be modified.

The account is officially registered with Hedera when hbars are initially deposited to the account alias. The transaction fee to create the account is deducted from the initial hbar transfer. The remaining balance minus the transaction fee to create the account is the initial balance of the new account. The account create transaction is executed first to register the new account and following the transfer transaction to transfer the remaining hbar balance to the new account.&#x20;

The consensus timestamp for the create account transaction is one nanosecond before the transfer transaction. The parent transfer transaction and the child account create transaction share the same payer account and timestamp in the transaction ID except that the child transaction has an added nonce value. You will also notice the account and transaction memo set to `auto-created account`.

You can return the new account ID from one of the following ways:

* Requesting the [child records](../transactions/get-a-transaction-record.md) of the parent transfer transaction using the parent transfer transaction ID. The child transaction ID in the child transaction record will display a nonce value (`0.0.2252@1640119571.329880313/1`).
* Requesting the [child receipts](../transactions/get-a-transaction-receipt.md) of the parent transfer transaction using the parent transfer transaction ID. The account ID field will be populated with the new account ID.
* Requesting the receipt or record of the account create transaction by using the transaction ID of the parent transfer transaction and setting the nonce value to 1
* Requesting an [account info](get-account-info.md) using the account alias&#x20;
* Looking at the parent transfer transaction record transfer list for the account that has a transfer that equals the transfer value minus the transaction fee

{% hint style="info" %}
**Note:** You cannot get the new account ID from the receipt of the transfer transaction like you would expect from a normal account create transaction that was not triggered by another transaction. You can use the alias in transfer transactions, account info and balance queries. The feature will be enabled to support all other transactions and queries in a future release.
{% endhint %}

**Transaction Signing Requirements**

* The account key paying for the transfer transaction fee
* The account the hbars are being debited from the transfer transaction
