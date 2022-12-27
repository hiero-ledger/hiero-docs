# Update a smart contract

A transaction that allows you to modify the smart contract entity state like admin keys, proxy account, auto renew period and memo. This transaction does not update the contract that is tied to the smart contract entity. The contract tied to the entity is immutable. The contract entity is immutable if an admin key is not specified. Once the transaction has been successfully executed on a Hedera network the previous field values will be updated with the new ones. To get a previous state of a smart contract instance, you can query a mirror node for that data. Any null field is ignored (left unchanged)

**Transaction Signing Requirements**

* If only the expiration time is being modified, then no signature is needed on this transaction other than for the account paying for the transaction itself.
* If any other smart contract entity property is being modified, the transaction must be signed by the admin key.
* If the admin key is being updated, the new key must sign

**Transaction Fees**

* Please see the transaction and query [fees](../../../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Smart Contract Properties**

| Field                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin Key**                    | Sets the new admin key and its fields can be modified arbitrarily if this key signs a transaction to modify it. If this is null, then such modifications are not possible, and there is no administrator that can override the normal operation of this smart contract instance. Note that if it is created with no admin keys, then there is no administrator to authorize changing the admin keys, so there can never be any admin keys for that instance. The bytecode will also be immutable. |
| **Expiration Time**              | The new expiry of the contract, no earlier than the current expiry (resolves to EXPIRATION\_REDUCTION\_NOT\_ALLOWED otherwise).                                                                                                                                                                                                                                                                                                                                                                   |
| **Staked ID**                    | <p>The new node account ID or node ID this contract is being staked to. If set to the sentinel 1, this removes the contract's staked node ID. See <a href="https://hips.hedera.com/hip/hip-406">HIP-406.</a><br><br><strong>Note:</strong> Accounts cannot stake to contract accounts. This will fixed in a future release.</p>                                                                                                                                                                   |
| **Decline Rewards**              | Updates the contract to decline receiving staking rewards if set to true. The default value is false. See [HIP-406.](https://hips.hedera.com/hip/hip-406)                                                                                                                                                                                                                                                                                                                                         |
| **Auto Renew Period**            | The new period that the instance will charge its account every this many seconds to renew. (not enabled)                                                                                                                                                                                                                                                                                                                                                                                          |
| **Auto Renew Account ID**        | If set to the sentinel 0.0.0 AccountID, this field removes the contract's auto-renew account. Otherwise it updates the contract's auto-renew account to the referenced account. (not enabled)                                                                                                                                                                                                                                                                                                     |
| **Automatic Token Associations** | The maximum number of tokens that this contract can be automatically associated with (i.e., receive air-drops from).                                                                                                                                                                                                                                                                                                                                                                              |
| **Memo**                         | The new memo to be associated with this contract.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |

| Constructor                       | Description                                      |
| --------------------------------- | ------------------------------------------------ |
| `new ContractUpdateTransaction()` | Initializes the ContractUpdateTransaction object |

```java
new ContractUpdateTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                                       | Type            | Requirement          |
| -------------------------------------------- | --------------- | -------------------- |
| `setContractId(<contractId>)`                | ContractId      | Required             |
| `setAdminKey(<publicKey>)`                   | Ed25519PubicKey | Optional             |
| `setBytecodeFileId(<fileId>)`                | FileId          | Deprecated \[0.20.0] |
| `setProxyAccountId(<accountId>`)             | AccountId       | Optional             |
| `setContractMemo(<memo>)`                    | String          | Optional             |
| `setContractExpirationTime(<expirationTime)` | Instant         | Optional             |
| `setAutoRenewPeriod(<autoRenewPeriod>)`      | Duration        | Optional             |

{% code title="Java" %}
```java
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
     .setContractId(newContractId)
     .setContractMemo("Update the memo of the smart contract");

TransactionId txId = transaction.execute(client);

TransactionReceipt receipt = txId.getReceipt(client);

Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus)

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const transaction = new ContractUpdateTransaction()
     .setContractId(newContractId)
     .setContractMemo("Update the memo of the smart contract");

const txId = await transaction.execute(client);

const receipt = await txId.getReceipt(client);

const transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus)

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}
| Method                      | Type                                                   | Requirement |
| --------------------------- | ------------------------------------------------------ | ----------- |
| `getContractId()`           | [ContractId](../../../specialized-types.md#contractid) | Required    |
| `getAdminKey()`             | Key                                                    | Optional    |
| `getBytecodeFileId()`       | [FileId](../../../specialized-types.md#fileid)         | Optional    |
| `getProxyAccountId()`       | [AccountId](../../../specialized-types.md#accountid)   | Optional    |
| `getContractMemo()`         | String                                                 | Optional    |
| `getStakedAccountId()`      | AccountId                                              | Optional    |
| `getStakedNodeId()`         | long                                                   | Optional    |
| `getDeclineStakingReward()` | boolean                                                | Optional    |
| `getAutoRenewPeriod()`      | Duration                                               | Optional    |

{% code title="Java" %}
```java
//Create the transaction with an admin key
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```java
//Create the transaction with an admin key
const transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewContractUpdateTransaction().
        SetContractID(contractId).
        SetBytecodeFileID(byteCodeFileID).
        SetAdminKey(adminKey)

//Get admin key
transaction.GetAdminKey()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}
