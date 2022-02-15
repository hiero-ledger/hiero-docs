# Update an Account

A transaction that updates the properties of an existing account. The network will store the latest updates on the account. If you would like to retrieve the state of an account in the past, you can query a mirror node.

**Transaction Signing Requirements**

* The account key\(s\) are required to sign the transaction
* If you are updating the keys on the account the OLD KEY and NEW KEY must sign
  * If either is a key list then the key list keys are all required to sign
  * If either is a threshold key, the threshold value is required to sign
* If you do not have the required signatures, the network will throw an INVALID\_SIGNATURE error

**Account Properties**

<table>
  <thead>
    <tr>
      <th style="text-align:left">Field</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>Key</b>
      </td>
      <td style="text-align:left">The new key for the account. The old key and new key must sign the transaction.
        If the old key or new key is a threshold key then only the threshold of
        the old key and new key are required to sign the transaction.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Expiration</b>
      </td>
      <td style="text-align:left">The new expiration for the account</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Receiver Signature Required</b>
      </td>
      <td style="text-align:left">
        <p>If true, all the account keys must sign any transaction depositing into
          this account (in addition to all withdrawals).</p>
        <p><em>default: false</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Max Automatic Token Associations</b>
      </td>
      <td style="text-align:left">Setting this account property will modify the existing automatic token
        association value. The maximum number of token auto associations is 1,000.
        If you are trying to reduce the number of auto token association slots,
        please make sure those slots are empty. If those slots are not empty, please
        dissociate tokens that were auto associated.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Memo</b>
      </td>
      <td style="text-align:left">Add or update a short description that should be recorded with the state
        of the account entity (maximum length of 100 characters). Anyone can view
        this memo on the network.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Auto Renew Period</b>
      </td>
      <td style="text-align:left">
        <p>The new period of time in which the account will auto-renew in seconds.
          The account is charged tinybars for every auto-renew period. Duration type
          is in seconds. For example, one hour would result in an input value of
          3,600 seconds.
          <br />
          <br /><b>NOTE:</b> This is fixed to approximately 3 months (7890000 seconds).
          Any other value will return the following error: AUTORENEW_DURATION_NOT_IN_RANGE.</p>
        <p><em>default: 2,592,000 seconds</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Proxy Account</b>
      </td>
      <td style="text-align:left">The new account ID to which this account is proxy staked.</td>
    </tr>
  </tbody>
</table>

| Constructor | Description |
| :--- | :--- |
| `new AccountUpdateTransaction()` | Initializes the AccountUpdateTransaction object |

```java
new AccountUpdateTransaction()
```

### Methods

{% tabs %}
{% tab title="V2" %}
| Method | Type | Requirement |
| :--- | :--- | :--- |
| `setAccountId(<accountId>)` | AccountId | Required |
| `setKey(<key>)` | Key | Optional |
| `setReceiverSignatureRequired(<boolean>)` | Boolean | Optional |
| `setMaxAutomaticTokenAssociations(<amount>)` | int | Optional |
| `setAccountMemo(<memo>)` | String | Optional |
| `setAutoRenewPeriod(<duration>)` | Duration | Disabled |
| `setExpirationTime(<expirationTime>)` | Instant | Disabled |
| `setProxyAccountId(<accountId>)` | AccountId | Disabled |

{% code title="Java" %}
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
{% endcode %}

{% code title="JavaScript" %}
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
{% endcode %}

{% code title="Go" %}
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
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method | Type | Requirement |
| :--- | :--- | :--- |
| `setAccountId(<accountId>)` | AccountId | Required |
| `setKey(<key>)` | PublicKey | Optional |
| `setAutoRenewPeriod(<duration>)` | Duration | Optional |
| `setExpirationTime(<expirationTime>)` | Instant | Optional |
| `setReceiverSignatureRequired(<boolean>)` | Boolean | Optional |
| `setProxyAccountId(<accountId>)` | AccountId | Optional |

{% code title="Java" %}
```java
//Create the transaction to update the key on the account
AccountUpdateTransaction transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(updateKey);

//Sign the transaction with the old key and new key, submit to a Hedera network   
TransactionId txId = transaction.build(client).sign(oldKey).sign(newKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//Version 1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction to update the key on the account
const transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(newKey);

//Sign the transaction with the old key and new key, submit to a Hedera network   
const txId = await transaction.build(client).sign(oldKey).sign(newKey).execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

Return the properties of an account create transaction.

{% tabs %}
{% tab title="V2" %}
| Method | Type | Description |
| :--- | :--- | :--- |
| `getKey()` | Key | Returns the public key on the account |
| `getInitialBalance()` | Hbar | Returns the initial balance of the account |
| `getReceiverSignatureRequired()` | boolean | Returns whether the receiver signature is required or not |
| `getExpirationTime()` | Instant | Returns the expiration time |
| `getAutoRenewPeriod()` | Duration | Returns the auto renew period on the account |

{% code title="Java" %}
```java
//Create a transaction
AccountUpdateTransaction transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(newKeyUpdate);

//Get the key on the account
Key accountKey = transaction.getKey();

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create a transaction
const transaction = new AccountUpdateTransaction()
    .setAccountId(accountId)
    .setKey(newKeyUpdate);

//Get the key of an account
const accountKey = transaction.getKey();

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction 
transaction, err := hedera.NewAccountUpdateTransaction().
        SetAccountID(newAccountId).
        SetKey(updateKey.PublicKey())

//Get the key of an account
accountKey := transaction.GetKey()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}

