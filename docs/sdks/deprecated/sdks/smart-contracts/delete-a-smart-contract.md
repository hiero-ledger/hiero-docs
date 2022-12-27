# Delete a smart contract

A transaction that deletes a smart contract from a Hedera network. Once a smart contract is marked deleted, you will not be able to modify any of the contract's properties. \*\*\*\* If a smart contract did not have an admin key defined, you cannot delete the smart contract. You can verify the smart contract was deleted by submitting a smart contract info query to the network. If a smart contract has an associated hbar balance, you will need to transfer the balance to another Hedera account.

**Transaction Signing Requirements**

* If the admin key was defined for the smart contract it is required to sign the transaction.
* The client operator's (fee payer account) private key is required to sign the transaction.

**Transaction Fees**

* Please see the transaction and query [fees](../../../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                       | Description                                    |
| --------------------------------- | ---------------------------------------------- |
| `new ContractDeleteTransaction()` | Initializes a ContractDeleteTransaction object |

```java
new ContractDeleteTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}
| Method                                      | Type       | Description                                                          | Requirement |
| ------------------------------------------- | ---------- | -------------------------------------------------------------------- | ----------- |
| `setContractId(<contractId>)`               | ContractId | Sets the contract ID (x.z.y) which should be deleted.                | Required    |
| `setTransferAccountId(<transferAccountId>)` | AccountId  | Sets the account ID (x.z.y) which will receive all remaining hbars   | Optional    |
| `setTransferContractId(<contractId>)`       | ContractId | Sets the contract ID (x.z.y) which will receive all remaining hbars. | Optional    |

{% code title="Java" %}
```java
//Create the transaction
ContractDeleteTransaction transaction = new ContractDeleteTransaction()
     .setContractId(newContractId);

//Sign the transaction with the admin key, client operator private key and submit the transaction to a Hedera network
TransactionId txResponse = transaction.build(client).sign(newAdminKey).execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const transaction = new ContractDeleteTransaction()
     .setContractId(newContractId);

//Sign the transaction with the admin key, client operator private key and submit the transaction to a Hedera network
const txResponse = await transaction.build(client).sign(newAdminKey).execute(client);

//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}
