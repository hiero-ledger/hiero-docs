# Create an account

### **Create an account using the account create API**

A transaction that creates a Hedera account. A Hedera account is required to interact with any of the Hedera network services as you need an account to pay for all associated transaction/query fees. You can visit the [Hedera Developer Portal](https://portal.hedera.com/register) to create a previewnet or testnet account. You can also use third-party wallets to generate free [mainnet accounts](../../../networks/mainnet/mainnet-access.md). To process an account create transaction, you will need an existing account to pay for the transaction fee. To obtain the new account ID, request the [receipt](../transactions/get-a-transaction-receipt.md) of the transaction.

{% hint style="info" %}
When creating a **new account** using the<mark style="color:purple;">`AccountCreateTransaction()`</mark>API you will need an existing account to pay for the associated transaction fee.
{% endhint %}

**Account Properties**

{% content-ref url="../../../core-concepts/accounts/account-properties.md" %}
[account-properties.md](../../../core-concepts/accounts/account-properties.md)
{% endcontent-ref %}

**Transaction Fees**

- Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

- The account paying for the transaction fee is required to sign the transaction

#### Methods

<table><thead><tr><th width="514">Method</th><th width="129.33333333333331">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setKey()</code></td><td>Key</td><td>Required</td></tr><tr><td><code>setAlias()</code></td><td>EvmAddress</td><td>Optional</td></tr><tr><td><code>setInitialBalance()</code></td><td>HBar</td><td>Optional</td></tr><tr><td><code>setReceiverSignatureRequired()</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>setMaxAutomaticTokenAssociations()</code></td><td>int</td><td>Optional</td></tr><tr><td><code>setStakedAccountId()</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>setStakedNodeId()</code></td><td>long</td><td>Optional</td></tr><tr><td><code>setDeclineStakingReward()</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>setAccountMemo()</code></td><td>String</td><td>Optional</td></tr><tr><td><code>setAutoRenewPeriod()</code></td><td>Duration</td><td>Disabled</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

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

{% endtab %}

{% tab title="JavaScript" %}

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

{% endtab %}

{% tab title="Go" %}

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

{% endtab %}
{% endtabs %}

#### Get transaction values

| Method                           | Type      | Description                                               |
| -------------------------------- | --------- | --------------------------------------------------------- |
| `getKey()`                       | Key       | Returns the public key on the account                     |
| `getInitialBalance()`            | Hbar      | Returns the initial balance of the account                |
| `getAutoRenewPeriod()`           | Duration  | Returns the auto renew period on the account              |
| `getDeclineStakingReward()`      | boolean   | Returns whether or not the account declined rewards       |
| `getStakedNodeId()`              | long      | Returns the node ID                                       |
| `getStakedAccountId()`           | AccountId | Returns the node account ID                               |
| `getReceiverSignatureRequired()` | boolean   | Returns whether the receiver signature is required or not |

{% tabs %}
{% tab title="Java" %}

```java
//Create an account with 1,000 hbar
AccountCreateTransaction transaction = new AccountCreateTransaction()
    // The only _required_ property here is `key`
    .setKey(newKey.getPublicKey())
    .setInitialBalance(new Hbar(1000));

//Return the key on the account
Key accountKey = transaction.getKey();
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create an account with 1,000 hbar
const transaction = new AccountCreateTransaction()
    // The only _required_ property here is `key`
    .setKey(newKey.getPublicKey())
    .setInitialBalance(new Hbar(1000));

//Return the key on the account
const accountKey = transaction.getKey();
```

{% endtab %}

{% tab title="Go" %}

```go
//Create an account with 1,000 hbar
AccountCreateTransaction := hedera.NewAccountCreateTransaction().
    SetKey(newKey.PublicKey()).
        SetInitialBalance(hedera.NewHbar(1000))

//Return the key on the account
accountKey, err := AccountCreateTransaction.GetKey()
```

{% endtab %}
{% endtabs %}
