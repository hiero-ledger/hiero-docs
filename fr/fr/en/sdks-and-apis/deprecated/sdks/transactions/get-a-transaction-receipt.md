# Get a transaction receipt

The transaction receipt gives you information about a transaction including whether or not the transaction reached consensus on the network. You request the receipt for every transaction type and there is currently no transaction fee associated with this network request.

{% hint style="info" %}
Receipts can be requested from a Hedera network for up to 3 minutes.
{% endhint %}

#### Transaction Receipt Contents

The transaction receipt returns the following information about a transaction:

- Whether the transaction reached consensus or not (success or fail)
- The newly generated account ID, topic ID, token ID, file ID, schedule ID, scheduled transaction ID or smart contract ID
- The exchange rate
- The topic running hash
- The topic sequence number
- The total supply of token
- The serial numbers for of the newly created NFTs after a token mint transaction was executed

**Transaction Signing Requirements**

- Transaction receipt requests do not have an associated fee at this time so there is no signature requirement

| **Constructor**                 | **Description**                                  |
| ------------------------------- | ------------------------------------------------ |
| `new TransactionReceiptQuery()` | Initializes the `TransactionReceiptQuery` Object |

- Transaction ID: The ID of the transaction to return the receipt for
- Include Duplicates: Whether or not to include the receipts for duplicate transactions
- Include Children: Whether or not to include the receipt for children transactions triggered by a parent transaction

|                                     |                                                               |                 |
| ----------------------------------- | ------------------------------------------------------------- | --------------- |
| **Method**                          | **Type**                                                      | **Requirement** |
| `setTransactionId(<transactionId>)` | [TransactionID](../../../sdks/transactions/transaction-id.md) | Required        |
| `setIncludeDuplicates(<value>)`     | boolean                                                       | Optional        |
| `setIncludeChildren(<value>)`       | boolean                                                       | Optional        |

{% tabs %}
{% tab title="Java" %}

```java
new TransactionReceiptQuery()
    .setTransactionId(transactionId)
    .execute(client)
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
new TransactionReceiptQuery()
    .setTransactionId(transactionId)
    .execute(client)
```

{% endtab %}

{% tab title="Go" %}

```go
hedera.NewTransactionReceiptQuery().
    SetTransactionId(transactionId).
    Execute(client)
```

{% endtab %}
{% endtabs %}

### Helper Methods

{% tabs %}
{% tab title="V2" %}

| **Method**                                                 | **Type**                | **Description**                                                                                                                                                                                                                 |
| ---------------------------------------------------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<TransactionResponse>.getReceipt(<client>)`               | TransactionReceipt      | Returns the receipt of a transaction                                                                                                                                                                                            |
| `<TransactionResponse>.getReceipt(<client, timeout>)`      | Client, Duration        | Request the receipt from the network for this duration                                                                                                                                                                          |
| `<TransactionResponse>.getReceiptQuery()`                  | TransactionReceiptQuery | Returns the TransactionReceiptQuery response for a transaction. This will not error on bad status like `RECEIPT_NOT_FOUND` and will return information about a failed transaction if necessary. |
| `<TransactionResponse>.getReceiptAsync(<client, timeout>)` | Client, Duration        | Request receipt asynchronously for the provided duration                                                                                                                                                                        |
| `<TransactionReceipt>.status`                              | Status                  | Whether the transaction reached consensus or not                                                                                                                                                                                |
| `<TransactionReceipt>.accountId`                           | AccountId               | The newly generated account ID                                                                                                                                                                                                  |
| `<TransactionReceipt>.topicId`                             | TopicId                 | The newly generated topic ID                                                                                                                                                                                                    |
| `<TransactionReceipt>).fileId`                             | FileId                  | The newly generated file ID                                                                                                                                                                                                     |
| `<TransactionReceipt>).contractId`                         | ContractId              | The newly generated contract ID                                                                                                                                                                                                 |
| `<TransactionReceipt>).tokenId`                            | TokenId                 | The newly generated token ID                                                                                                                                                                                                    |
| `<TransactionReceipt>).scheduleId`                         | ScheduleId              | The newly generated schedule ID                                                                                                                                                                                                 |
| `<TransactionReceipt>).scheduledTransactionId`             | TransactionId           | The generated scheduled transaction ID                                                                                                                                                                                          |
| `<TransactionReceipt>).exchangeRate`                       | ExchangeRate            | The exchange rate in hbar, cents, and expiration time                                                                                                                                                                           |
| `<TransactionReceipt>.topicRunningHash`                    | ByteString              | The topic running hash                                                                                                                                                                                                          |
| `<TransactionReceipt>.topicSequenceNumber`                 | long                    | The topic sequence number                                                                                                                                                                                                       |
| `<TransactionReceipt>.totalSupply`                         | long                    | The total supply of a token                                                                                                                                                                                                     |
| `<TransactionReceipt>.transactionId`                       | TransactionId           | The transaction ID of the transaction the receipt is being requested for                                                                                                                                                        |
| `<TransactionReceipt>.serials`                             | List\<long>            | The list of newly created serial numbers upon execution of a token mint transaction.                                                                                                                            |

{% code title="Java" %}

```java
//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

System.out.println("The transaction receipt: " +receipt);

//v2.0.0
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

console.log("The transaction receipt: " +receipt);

//v2.0.0
```

{% endcode %}

{% code title="Go" %}

```java
//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

fmt.Printf("The transaction receipt %v\n", receipt)

//v2.0.0
```

{% endcode %}

**Sample Output:**

```
TransactionReceipt{
     status=SUCCESS,
     exchangeRate=ExchangeRate{
          hbars=1,
          cents=12, 
          expirationTime=2100-01-01T00:00:00Z
     }, 
     accountId=null,
     fileId=null, 
     contractId=null, 
     topicId=null, 
     tokenId=null, 
     topicSequenceNumber=null, 
     topicRunningHash=null, 
     totalSupply=0, 
     scheduleId=0.0.2531
     schdeduledTransactionId=null,
     serials=[]
    }
```

\`\`
{% endtab %}
{% endtabs %}
