# Delete an account

A transaction that deletes an existing account from the Hedera network. Before deleting an account, the existing hbars must be transferred to another account. If you fail to transfer the hbars, you will receive an error message "setTransferAccountId() required." Transfers cannot be made into a deleted account. A record of the deleted account will remain in the ledger until it expires.The expiration of a deleted account can be extended. The account that is being deleted is required to sign the transaction.

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

- The account the is being deleted is required to sign the transaction

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new AccountDeleteTransaction()` | Initializes the AccountDeleteTransaction object |

```java
new AccountDeleteTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                                      | Type      | Description                                                               | Requirement |
| ------------------------------------------- | --------- | ------------------------------------------------------------------------- | ----------- |
| `setAccountId(<accountId>)`                 | AccountId | The ID of the account to delete.                          | Required    |
| `setTransferAccountId(<transferAccountId>)` | AccountId | The ID of the account to transfer the remaining funds to. | Optional    |

{% code title="Java" %}

```java
//Create the transaction
AccountDeleteTransaction transaction = new AccountDeleteTransaction()
    .setDeleteAccountId(accountId)
    .setTransferAccountId(OPERATOR_ID);

//Build the unsigned transaction, sign with account key, sign with the client operator account private key and submit to a Hedera network
TransactionId txId = transaction.build(client).sign(newKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = await new AccountDeleteTransaction()
    .setDeleteAccountId(accountId)
    .setTransferAccountId(OPERATOR_ID)
    .build(client);

//Sign the transaction with the account key
const signTx = await transaction.sign(accountKey);

//Sign with the client operator private key and submit to a Hedera network
const txId = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);
```

{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}

| Method                                      | Type      | Description                                    |
| ------------------------------------------- | --------- | ---------------------------------------------- |
| `getAccountId(<accountId>)`                 | AccountId | The account to delete                          |
| `getTransferAccountId(<transferAccountId>)` | AccountId | The account to transfer the remaining funds to |

{% code title="Java" %}

```java
//Create the transaction to delete an account
AccountDeleteTransaction transaction = new AccountDeleteTransaction()
    .setAccountId(newAccountId)
    .setTransferAccountId(OPERATOR_ID);

//Get the account ID from the transaction
AccountId transactionAccountId = transaction.getAccountId()

System.out.println("The account to be deleted in this transaction is " +transactionAccountId)

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```java
//Create the transaction to delete an account
const transaction = new AccountDeleteTransaction()
    .setAccountId(newAccountId)
    .setTransferAccountId(OPERATOR_ID);

//Get the account ID from the transaction
const transactionAccountId = transaction.getAccountId()

console.log("The account to be deleted in this transaction is " +transactionAccountId)
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction to delete an account
transaction, err := hedera.NewAccountDeleteTransaction().
        SetAccountID(newAccountID).
        SetTransferAccountID(operatorAccountID)

//Get the account ID from the transaction
transactionAccountId := transaction.GetAccountID()

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}
