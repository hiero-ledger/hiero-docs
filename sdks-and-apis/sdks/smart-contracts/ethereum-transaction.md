# Ethereum transaction

The raw Ethereum transaction (RLP encoded type 0, 1, and 2) will hold signed Ethereum transactions and execute them as Hedera transactions in a prescribed manner.

{% hint style="warning" %}
**Important**\
Hedera interprets hbar decimals differently depending on the context.\
\
The lowest denomination of HBAR when used within a the `value` field in the `EthereumTransaction` is `weibars` meaning with `18` decimals\
\
The lowest denomination of hbar when used within `data` or the Hedera EVM is `tinybars` meaning with `8` decimals.\
\
Gas Price information is in `weibars` denomination.\
\
Reference: [HIP-410](https://hips.hedera.com/hip/hip-410)
{% endhint %}



| Field                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Call Data File ID** | For large transactions (for example contract creation) this should be used to set the FileId of an HFS file containing the callData f the `ethereumData`. The data in the `ethereumData` will be re-written with the callData element as a zero-length string with the original contents in the referenced file at the time of execution. The `ethereumData` will need to be "rehydrated" with the callData for signature validation to pass.                                                                                                                                                                                                                                                                                                                                     |
| **Ethereum Data**     | The raw Ethereum transaction (RLP encoded type 0, 1, and 2). Complete unless the `callDataFileId` is set.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **Max Allowance**     | <p>The maximum amount that the payer of the Hedera transaction is willing to pay in hbar to complete the transaction.<br><br>Ordinarily, the account with the ECDSA alias corresponding to the public key that is extracted from the <code>ethereum_data</code> signature is responsible for fees that result from the execution of the transaction. If that amount of authorized fees is not sufficient then the payer of the transaction can be charged, up to but not exceeding this amount. If the <code>ethereum_data</code> transaction authorized an amount that was insufficient then the payer will only be charged the amount needed to make up the difference. If the gas price in the transaction was set to zero then the payer will be assessed the entire fee.</p> |

**Transaction Signing Requirements**

* The key of the transaction fee-paying account

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee

| Method                                          | Type     |
| ----------------------------------------------- | -------- |
| `setCallDataFileId(<fileId>)`                   | FileID   |
| `setEthereumData(<ethereumData>)`               | byte \[] |
| `setMaxGasAllowanceHbar(<maxGasAllowanceHbar>)` | Hbar     |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
EthereumTransaction transaction = new EthereumTransaction()
     .setEthereumData(ethereumData)
     .setMaxGasAllowanceHbar(allowance);

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.14
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new EthereumTransaction()
     .setEthereumData(ethereumData)
     .setMaxGasAllowance(allowance);

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
const txResponse = await transaction.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus);

//v2.14
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction, err := hedera.NewEthereumTransaction().
     SetEthereumData(ethereumData)
     SetGasAllowed(allowance)

//Sign with the client operator private key to pay for the transaction and submit the query to a Hedera network
txResponse, err := transaction.Execute(client)
if err != nil {
	panic(err)
}

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {the 
	panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status %v\n", transactionStatus)

//v2.14
```
{% endtab %}
{% endtabs %}
