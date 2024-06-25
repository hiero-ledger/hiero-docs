# Delete a smart contract

A transaction that deletes a smart contract from a Hedera network. Once a smart contract is marked deleted, you will not be able to modify any of the contract's properties. \*\*\*\* If a smart contract did not have an admin key defined, you cannot delete the smart contract. You can verify the smart contract was deleted by submitting a smart contract info query to the network. If a smart contract has an associated hbar balance, you will need to transfer the balance to another Hedera account.

**Transaction Signing Requirements**

- If the admin key was defined for the smart contract it is required to sign the transaction.
- The client operator's (fee payer account) private key is required to sign the transaction.

**Transaction Fees**

- Please see the transaction and query [NextCall a smart contract function](https://app.gitbook.com/s/3UlLhrwSBZKwLvX6vlUX/docs/sdks/smart-contracts/call-a-smart-contract-function) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

### Methods

| Method                                      | Type                                             | Description                                                                                                                             | Requirement |
| ------------------------------------------- | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setContractId(<contractId>)`               | [ContractId](../specialized-types.md#contractid) | Sets the contract ID (x.z.y) which should be deleted.                | Required    |
| `setTransferAccountId(<transferAccountId>)` | [AccountId](../specialized-types.md#accountid)   | Sets the account ID (x.z.y) which will receive all remaining hbars                   | Optional    |
| `setTransferContractId(<contractId>)`       | [ContractId](../specialized-types.md#contractid) | Sets the contract ID (x.z.y) which will receive all remaining hbars. | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
ContractDeleteTransaction transaction = new ContractDeleteTransaction()
    .setContractId(contractId);

//Freeze the transaction for signing, sign with the admin key on the contract, sign with the client operator private key and submit to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(adminKey).execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction
const transaction = await new ContractDeleteTransaction()
    .setContractId(contractId)
    .freezeWith(client);

//Sign with the admin key on the contract
const signTx = await transaction.sign(adminKey)

//Sign the transaction with the client operator's private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v2.0.5
```

{% endtab %}

{% tab title="Go" %}

```java
//Create and freeze the transaction
transaction := hedera.NewContractDeleteTransaction().
	  SetContractID(contractID)
	  FreezeWith(client)
	
//Sign with the admin key on the contract, sign with the client operator private key and submit to a Hedera network
txResponse. err := transaction.Sign(adminKey).Execute(client)
if err != nil {
		panic(err)
}

//Get the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
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
