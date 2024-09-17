# Update an account

A transaction that updates the properties of an existing account. The network will store the latest updates on the account. If you would like to retrieve the state of an account in the past, you can query a mirror node.

**Account Properties**

{% content-ref url="../../../core-concepts/accounts/account-properties.md" %}
[account-properties.md](../../../core-concepts/accounts/account-properties.md)
{% endcontent-ref %}

**Transaction Fees**

* The sender pays for the token association fee and the rent for the first auto-renewal period.
* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee.
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate the cost of your transaction fee.

**Transaction Signing Requirements**

* The account key(s) are required to sign the transaction.
* If you are updating the keys on the account, the OLD KEY and NEW KEY must sign.
  * If either is a key list, the key list keys are all required to sign.
  * If either is a threshold key, the threshold value is required to sign.
* If you do not have the required signatures, the network will throw an `INVALID_SIGNATURE` error.

#### Maximum Auto-Associations and Fees

Accounts have a property, `maxAutoAssociations`, and the property's value determines the maximum number of automatic token associations allowed.

<table><thead><tr><th width="159" align="center">Property Value</th><th>Description</th></tr></thead><tbody><tr><td align="center"><code>0</code></td><td>Automatic <strong>token</strong> associations or <a data-footnote-ref href="#user-content-fn-1"><strong>token airdrops</strong></a> are not allowed, and the account must be manually associated with a token. This also applies if the value is less than or equal to <code>usedAutoAssociations</code>.</td></tr><tr><td align="center"><code>-1</code></td><td>The number of automatic <strong>token</strong> associations an account can have is unlimited. <code>-1</code> is the default value for new automatically-created accounts. </td></tr><tr><td align="center"><code>> 0</code></td><td>If the value is a positive number (number greater than 0), the number of automatic token associations an account can have is limited to that number. </td></tr></tbody></table>

{% hint style="info" %}
The sender pays the `maxAutoAssociations` fee and the rent for the first auto-renewal period for the association. This is in addition to the typical transfer fees. This ensures the receiver can receive tokens without association and makes it a smoother transfer process.
{% endhint %}

Reference: [HIP-904](https://hips.hedera.com/hip/hip-904)

### Methods

<table><thead><tr><th width="507.3333333333333">Method</th><th width="114">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setAccountId(&#x3C;accountId>)</code></td><td>AccountId</td><td>Required</td></tr><tr><td><code>setKey(&#x3C;key>)</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>setReceiverSignatureRequired(&#x3C;boolean>)</code></td><td>Boolean</td><td>Optional</td></tr><tr><td><code>setMaxAutomaticTokenAssociations(&#x3C;amount>)</code></td><td>int</td><td>Optional</td></tr><tr><td><code>setAccountMemo(&#x3C;memo>)</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setAutoRenewPeriod(&#x3C;duration>)</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code>setStakedAccountId(&#x3C;stakedAccountId>)</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>setStakedNodeId(&#x3C;stakedNodeId>)</code></td><td>long</td><td>Optional</td></tr><tr><td><code>setDeclineStakingReward(&#x3C;declineStakingReward>)</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>setExpirationTime(&#x3C;expirationTime>)</code></td><td>Instant</td><td>Disabled</td></tr></tbody></table>

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

<table><thead><tr><th width="362.3333333333333">Method</th><th width="113">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>getKey()</code></td><td>Key</td><td>Returns the public key on the account</td></tr><tr><td><code>getInitialBalance()</code></td><td>Hbar</td><td>Returns the initial balance of the account</td></tr><tr><td><code>getReceiverSignatureRequired()</code></td><td>boolean</td><td>Returns whether the receiver signature is required or not</td></tr><tr><td><code>getExpirationTime()</code></td><td>Instant</td><td>Returns the expiration time</td></tr><tr><td><code>getAccountMemo()</code></td><td>String</td><td>Returns the account memo</td></tr><tr><td><code>getDeclineStakingReward()</code></td><td>boolean</td><td>Returns whether or not the account is declining rewards</td></tr><tr><td><code>getStakedNodeId()</code></td><td>long</td><td>Returns the node ID the account is staked to</td></tr><tr><td><code>getStakedAccountId()</code></td><td>AccountId</td><td>Returns the account ID the node is staked to</td></tr><tr><td><code>getAutoRenewPeriod()</code></td><td>Duration</td><td>Returns the auto renew period on the account</td></tr></tbody></table>

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

[^1]: 
