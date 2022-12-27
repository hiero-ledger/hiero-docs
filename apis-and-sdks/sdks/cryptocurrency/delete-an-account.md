# Delete an account

A transaction that deletes an existing account from the Hedera network. Before deleting an account, the existing HBARs must be transferred to another account. If you fail to transfer the HBARs, you will receive an error message "setTransferAccountId() required." Transfers cannot be made into a deleted account. A record of the deleted account will remain in the ledger until it expires. The expiration of a deleted account can be extended. The account that is being deleted is required to sign the transaction.

**Transaction Fees**

* Please see the transaction and query [fees](broken-reference) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

* The account that is being deleted is required to sign the transaction

| Constructor                      | Description                                     |
| -------------------------------- | ----------------------------------------------- |
| `new AccountDeleteTransaction()` | Initializes the AccountDeleteTransaction object |

```java
new AccountDeleteTransaction()
```

### Methods

| Method                                      | Type      | Description                                               | Requirement |
| ------------------------------------------- | --------- | --------------------------------------------------------- | ----------- |
| `setAccountId(<accountId>)`                 | AccountId | The ID of the account to delete.                          | Required    |
| `setTransferAccountId(<transferAccountId>)` | AccountId | The ID of the account to transfer the remaining funds to. | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction to delete an account
AccountDeleteTransaction transaction = new AccountDeleteTransaction()
    .setAccountId(accountId)
    .setTransferAccountId(OPERATOR_ID);

//Freeze the transaction for signing, sign with the private key of the account that will be deleted, sign with the operator key and submit to a Hedera network
TransactionResponse  txResponse = transaction.freezeWith(client).sign(newKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction to delete an account
const transaction = await new AccountDeleteTransaction()
    .setAccountId(accountId)
    .setTransferAccountId(OPERATOR_ID)
    .freezeWith(client);

//Sign the transaction with the account key
const signTx = await transaction.sign(accountKey);

//Sign with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//2.0.5
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the transaction to delete an account, freeze the transaction for signing
transaction, err := hedera.NewAccountDeleteTransaction().
        SetAccountID(newAccountID).
        SetTransferAccountID(operatorAccountID).
        FreezeWith(client)
if err != nil {
    panic(err)
}

//Sign with the private key of the account that will be deleted, sign with the operator key and submit to a Hedera network
txResponse, err := transaction.Sign(accountKey).Execute(client)
if err != nil {
    panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", transactionStatus)

//v2.0.0
```
{% endtab %}
{% endtabs %}

## Get transaction values

| Method                                      | Type      | Description                                    |
| ------------------------------------------- | --------- | ---------------------------------------------- |
| `getAccountId(<accountId>)`                 | AccountId | The account to delete                          |
| `getTransferAccountId(<transferAccountId>)` | AccountId | The account to transfer the remaining funds to |

{% tabs %}
{% tab title="Java" %}
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
{% endtab %}

{% tab title="JavaScript" %}
```java
//Create the transaction to delete an account
const transaction = new AccountDeleteTransaction()
    .setAccountId(newAccountId)
    .setTransferAccountId(OPERATOR_ID);

//Get the account ID from the transaction
const transactionAccountId = transaction.getAccountId()

console.log("The account to be deleted in this transaction is " +transactionAccountId)
```
{% endtab %}

{% tab title="JavaScript" %}
```java
//Create the transaction to delete an account
transaction, err := hedera.NewAccountDeleteTransaction().
        SetAccountID(newAccountID).
        SetTransferAccountID(operatorAccountID)

//Get the account ID from the transaction
transactionAccountId := transaction.GetAccountID()

//v2.0.0
```
{% endtab %}
{% endtabs %}
