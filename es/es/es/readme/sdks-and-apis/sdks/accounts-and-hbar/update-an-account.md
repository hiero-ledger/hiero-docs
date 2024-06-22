# Update an account

A transaction that updates the properties of an existing account. The network will store the latest updates on the account. If you would like to retrieve the state of an account in the past, you can query a mirror node.

**Account Properties**

{% content-ref url="../../../core-concepts/accounts/account-properties.md" %}
[account-properties.md](../../../core-concepts/accounts/account-properties.md)
{% endcontent-ref %}

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

- The account key(s) are required to sign the transaction
- If you are updating the keys on the account the OLD KEY and NEW KEY must sign
  - If either is a key list then the key list keys are all required to sign
  - If either is a threshold key, the threshold value is required to sign
- If you do not have the required signatures, the network will throw an INVALID\_SIGNATURE error

### Methods

| Method                                            | Type      | Requirement |
| ------------------------------------------------- | --------- | ----------- |
| `setAccountId(<accountId>)`                       | AccountId | Required    |
| `setKey(<key>)`                                   | Key       | Optional    |
| `setReceiverSignatureRequired(<boolean>)`         | Boolean   | Optional    |
| `setMaxAutomaticTokenAssociations(<amount>)`      | int       | Optional    |
| `setAccountMemo(<memo>)`                          | String    | Optional    |
| `setAutoRenewPeriod(<duration>)`                  | Duration  | Optional    |
| `setStakedAccountId(<stakedAccountId>)`           | AccountId | Optional    |
| `setStakedNodeId(<stakedNodeId>)`                 | long      | Optional    |
| `setDeclineStakingReward(<declineStakingReward>)` | boolean   | Optional    |
| `setExpirationTime(<expirationTime>)`             | Instant   | Disabled    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction to update the key on the account
AccountUpdateTransaction transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(updateKey);

//Sign the transaction with the old key and new key, submit to a Hedera network   
TransactionResponse txResponse = transaction.freezeWith(client).sign(oldKey).sign(newKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//Version 2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction to update the key on the account
const transaction = await new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(updateKey)
    .freezeWith(client);

//Sign the transaction with the old key and new key
const signTx = await (await transaction.sign(oldKey)).sign(newKey);

//SIgn the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());

//v2.0.5
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the transaction to update the key on the account
transaction, err := hedera.NewAccountUpdateTransaction().
        SetAccountID(newAccountId).
        SetKey(updateKey.PublicKey()).
        FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign the transaction with the old key and new key, submit to a Hedera network   
txResponse, err := transaction.Sign(newKey).Sign(updateKey).Execute(client)

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

println("The transaction consensus status is ", transactionStatus)

//Version 2.0.0
```

{% endtab %}
{% endtabs %}

## Get transaction values

Return the properties of an account create transaction.

<table><thead><tr><th width="362.3333333333333">Method</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>getKey()</code></td><td>Key</td><td>Returns the public key on the account</td></tr><tr><td><code>getInitialBalance()</code></td><td>Hbar</td><td>Returns the initial balance of the account</td></tr><tr><td><code>getReceiverSignatureRequired()</code></td><td>boolean</td><td>Returns whether the receiver signature is required or not</td></tr><tr><td><code>getExpirationTime()</code></td><td>Instant</td><td>Returns the expiration time</td></tr><tr><td><code>getAccountMemo()</code></td><td>String</td><td>Returns the account memo</td></tr><tr><td><code>getDeclineStakingReward()</code></td><td>boolean</td><td>Returns whether or not the account is declining rewards</td></tr><tr><td><code>getStakedNodeId()</code></td><td>long</td><td>Returns the node ID the account is staked to</td></tr><tr><td><code>getStakedAccountId()</code></td><td>AccountId</td><td>Returns the account ID the node is staked to</td></tr><tr><td><code>getAutoRenewPeriod()</code></td><td>Duration</td><td>Returns the auto renew period on the account</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

```java
//Create a transaction
AccountUpdateTransaction transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(newKeyUpdate);

//Get the key on the account
Key accountKey = transaction.getKey();

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create a transaction
const transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(newKeyUpdate);

//Get the key of an account
const accountKey = transaction.getKey();

//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the transaction 
transaction, err := hedera.NewAccountUpdateTransaction().
        SetAccountID(newAccountId).
        SetKey(updateKey.PublicKey())

//Get the key of an account
accountKey := transaction.GetKey()

//v2.0.0
```

{% endtab %}
{% endtabs %}
