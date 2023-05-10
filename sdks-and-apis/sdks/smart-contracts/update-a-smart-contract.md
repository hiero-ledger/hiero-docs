# Update a smart contract

A transaction that allows you to modify the smart contract entity state like admin keys, proxy account, auto-renew period, and memo. This transaction does not update the contract that is tied to the smart contract entity. The contract tied to the entity is immutable. The contract entity is immutable if an admin key is not specified. Once the transaction has been successfully executed on a Hedera network the previous field values will be updated with the new ones. To get a previous state of a smart contract instance, you can query a mirror node for that data. Any null field is ignored (left unchanged)

**Transaction Signing Requirements**

* If only the expiration time is being modified, then no signature is needed on this transaction other than for the account paying for the transaction itself.
* If any other smart contract entity property is being modified, the transaction must be signed by the admin key.
* If the admin key is being updated, the new key must sign

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Smart Contract Properties**

| Field                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin Key**                    | Sets the new admin key and its fields can be modified arbitrarily if this key signs a transaction to modify it. If this is null, then such modifications are not possible, and there is no administrator that can override the normal operation of this smart contract instance. Note that if it is created with no admin keys, then there is no administrator to authorize changing the admin keys, so there can never be any admin keys for that instance. The bytecode will also be immutable. |
| **Expiration Time**              | The new expiry of the contract, no earlier than the current expiry (resolves to EXPIRATION\_REDUCTION\_NOT\_ALLOWED otherwise).                                                                                                                                                                                                                                                                                                                                                                   |
| **Staked ID**                    | <p>The new node account ID or node ID this contract is being staked to. If set to the sentinel 1, this removes the contract's staked node ID. See <a href="https://hips.hedera.com/hip/hip-406">HIP-406.</a><br><br><strong>Note:</strong> Accounts cannot stake to contract accounts. This will fixed in a future release.</p>                                                                                                                                                                   |
| **Decline Rewards**              | Updates the contract to decline receiving staking rewards if set to true. The default value is false. See [HIP-406.](https://hips.hedera.com/hip/hip-406)                                                                                                                                                                                                                                                                                                                                         |
| **Auto Renew Period**            | The new period that the instance will charge its account every this many seconds to renew.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Auto Renew Account ID**        | Indicates the account that will pay the contract's auto-renew fee. If the Auto Renew account id is cleared, then the smart contract's account will be charged the auto-renew fee                                                                                                                                                                                                                                                                                                                  |
| **Automatic Token Associations** | The maximum number of tokens that this contract can be automatically associated with (i.e., receive air-drops from).                                                                                                                                                                                                                                                                                                                                                                              |
| **Memo**                         | The new memo to be associated with this contract.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |

### Methods

| Method                                            | Type                                                                | Requirement |
| ------------------------------------------------- | ------------------------------------------------------------------- | ----------- |
| `setContractId(<contractId>)`                     | [ContractId](../../deprecated/sdks/specialized-types.md#contractid) | Required    |
| `setAdminKey(<keys>)`                             | Key                                                                 | Optional    |
| `setContractMemo(<memo>)`                         | String                                                              | Optional    |
| `setExpirationTime(<expirationTime)`              | Instant                                                             | Optional    |
| `setMaxAutomaticTokenAssociations()`              | int                                                                 | Optional    |
| `setContractMemo(<memo>)`                         | String                                                              | Optional    |
| `setStakedAccountId(<stakedAccountId>)`           | [AccountId](../../deprecated/sdks/specialized-types.md#accountid)   | Optional    |
| `setStakedNodeId(<stakedNodeId>)`                 | long                                                                | Optional    |
| `setDeclineStakingReward(<declineStakingReward>)` | boolean                                                             | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`           | Duration                                                            | Optional    |
| `setAutoRenewAccountId(<accountId>)`              | [AccountId](../../deprecated/sdks/specialized-types.md#accountid)   | Optional    |
| `clearAutoRenewAccountId()`                       |                                                                     | Optional    |

{% hint style="info" %}
_**Note:** The new expiration time must be an instance of type **`Timestamp`**, thus, the **`Timestamp`** object has to be imported from the SDK package. The new expiration time has to be initialized as a new instance of that type._
{% endhint %}

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction 
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Modify the default transaction fee
ContractUpdateTransaction modifyTransactionFee = transaction.setMaxTransactionFee(new Hbar(20));

//sign with the new admin key, sign with the old admin key, sign with the client operator account and submit to a Hedera network
TransactionResponse txResponse = modifyTransactionFee.freezeWith(client).sign(newAdminKey).sign(adminKey).execute(client);

//Get the consensus status of the transaction
Status transactionStatus = receipt.status;

System.out.println("The consensus status of the transaction is " +transactionStatus);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey)
    .setMaxTransactionFee(new Hbar(20))
    .freezeWith(client);

//Sign the transaction with the old admin key and new admin key
const signTx = await (await transaction.sign(newAdminKey)).sign(adminKey);

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client)

//Get the consensus status of the transaction
const transactionStatus = receipt.status;

console.log("The consensus status of the transaction is " +transactionStatus);

//v2.0.5
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction := hedera.NewContractUpdateTransaction().
        SetContractID(contractId).
        SetBytecodeFileID(byteCodeFileID).
        SetAdminKey(adminKey)

//Change the default max transaction fee to 20 HBAR
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(20, hedera.HbarUnits.Hbar))

//Freeze the transaction
freezeTransaction, err := modifyMaxTransactionFee.FreezeWith(client)
if err != nil {
        panic(err)
}

//Sign with the old admin key, sign with the new admin key, sign with the client operator account and submit to a Hedera network
txResponse, err := freezeTransaction.Sign(newAdminKey).Sign(adminKey).Execute(client)
if err != nil {
        panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)
if err != nil {
        panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status %v\n", transactionStatus)

//v2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method                      | Type                                                                | Requirement |
| --------------------------- | ------------------------------------------------------------------- | ----------- |
| `getContractId()`           | [ContractId](../../deprecated/sdks/specialized-types.md#contractid) | Required    |
| `getAdminKey()`             | Key                                                                 | Optional    |
| `getBytecodeFileId()`       | [FileId](../../deprecated/sdks/specialized-types.md#fileid)         | Optional    |
| `getProxyAccountId()`       | [AccountId](../../deprecated/sdks/specialized-types.md#accountid)   | Optional    |
| `getContractMemo()`         | String                                                              | Optional    |
| `getStakedAccountId()`      | [AccountId](../../deprecated/sdks/specialized-types.md#accountid)   | Optional    |
| `getStakedNodeId()`         | long                                                                | Optional    |
| `getDeclineStakingReward()` | boolean                                                             | Optional    |
| `getAutoRenewAccountId()`   | [AccountId](../../deprecated/sdks/specialized-types.md#accountid)   | Required    |
| `getAutoRenewPeriod()`      | Duration                                                            | Required    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction with an admin key
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```java
//Create the transaction with an admin key
const transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
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
{% endtab %}
{% endtabs %}
