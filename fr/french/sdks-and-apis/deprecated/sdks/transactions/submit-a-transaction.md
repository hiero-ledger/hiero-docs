# Submit a transaction

The `execute()` method submits a transaction to a Hedera network. This method will create the transaction ID from the client operator account ID, sign with the client operator private key, and pick a node from the defined network on the client to submit the transaction to. The transaction is also automatically signed with the client operator account private key. You do not need to manually sign transactions if this key is the required key on any given transaction. Once you submit the transaction, the response will include the following:

- The transaction ID of the transaction
- The node ID of the node the transaction was submitted to
- The transaction hash

**Transaction Signing Requirements**

- Please refer to the specific transaction type and defined key structure of the account, topic, token, file or smart contract to understand the signing requirements

{% tabs %}
{% tab title="V2" %}

| Method                                                      | Type                                                           | Description                                                                                                                                        |
| ----------------------------------------------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `execute(<client>)`                                         | Client                                                         | Sign with the client operator and submit to a Hedera network                                                                                       |
| `execute(<client, timeout>)`                                | Client, Duration                                               | The duration of times the client will try to submit the transaction upon the network being busy                                                    |
| `executeWithSigner(<signer>)`                               |                                                                | Sign the transaction with a local wallet. This feature is available in the Hedera JavaScript SDK only. >=`v2.11.0` |
| `<transactionResponse>.transactionId`                       | TransactionId                                                  | Returns the transaction ID of the transaction                                                                                                      |
| `<transactionResponse>.nodeId`                              | AccountId                                                      | Returns the node ID of the node that processed the transaction                                                                                     |
| `<transactionResponse>.transactionHash`                     | byte \[ ] | Returns the hash of the transaction                                                                                                                |
| `<transactionResponse>.setValidateStatus(<validateStatus>)` | boolean                                                        | Whether getReceipt() or getRecord() will throw an exception if the receipt status is not SUCCESS             |
| `<transactionResponse>.getValidateStatus`                   | boolean                                                        | Return whether getReceipt() or getRecord() will throw an exception if the receipt status is not SUCCESS      |

{% code title="Java" %}

```java
//Create the transaction
AccountCreateTransaction transaction = new AccountCreateTransaction()
        .setKey(newPublicKey)
        .setInitialBalance(new Hbar(1000));

//Sign with client operator private key and submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Get the transaction ID
TransactionId transactionId = txResponse.transactionId;

//Get the account ID of the node that processed the transaction
AccountId nodeId = txResponse.nodeId;

//Get the transaction hash
byte [] transactionHash = txResponse.transactionHash;

System.out.println("The transaction ID is " +transactionId);
System.out.println("The transaction hash is " +transactionHash);
System.out.println("The node ID is " +nodeId);

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create the transaction
const transaction = new AccountCreateTransaction()
        .setKey(newPublicKey)
        .setInitialBalance(new Hbar(1000));

//Sign with client operator private key and submit the transaction to a Hedera network
const txResponse = await transaction.execute(client);

//Get the transaction ID
const transactionId = txResponse.transactionId;

//Get the account ID of the node that processed the transaction
const nodeId = txResponse.nodeId;

//Get the transaction hash
const transactionHash = txResponse.transactionHash;

console.log("The transaction ID is " +transactionId);
console.log("The transaction hash is " +transactionHash);
console.log("The node ID is " +nodeId);

//v2.0.0
```

{% endcode %}

{% code title="Go" %}

```java
//Create the transaction
transaction := hedera.NewAccountCreateTransaction().
        SetKey(publicKey).
        SetInitialBalance(hedera.NewHbar(1000))

//Sign with client operator private key and submit the transaction to a Hedera network
txResponse, err := transaction.Execute(client)

if err != nil {
        panic(err)
}

//Get the transaction ID
transactionId := txResponse.TransactionID

//Get the account ID of the node that processed the transaction
transactionNodeId := txResponse.NodeID

//Get the transaction hash
transactionHash := txResponse.Hash

fmt.Printf("The transaction id is %v\n", transactionId)
fmt.Printf("The transaction hash is %v\n", transactionHash)
fmt.Printf("The node id is %v\n", transactionNodeId)

//v2.0.0
```

{% endcode %}
{% endtab %}
{% endtabs %}
