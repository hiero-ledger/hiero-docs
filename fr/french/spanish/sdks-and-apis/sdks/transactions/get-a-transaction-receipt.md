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

<table><thead><tr><th width="390">Method</th><th width="138.33333333333331">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setTransactionId(<transactionId>)</code></td><td><a href="transaction-id.md">TransactionID</a></td><td>Required</td></tr><tr><td><code>setIncludeDuplicates(<value>)</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>setIncludeChildren(<value>)</code></td><td>boolean</td><td>Optional</td></tr></tbody></table>

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

<table data-header-hidden><thead><tr><th width="337"></th><th width="188.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code><TransactionResponse>.getReceipt(<client>)</code></td><td>TransactionReceipt</td><td>Returns the receipt of a transaction</td></tr><tr><td><code><TransactionResponse>.getReceipt(<client, timeout>)</code></td><td>Client, Duration</td><td>Request the receipt from the network for this duration</td></tr><tr><td><code><TransactionResponse>.getReceiptQuery()</code></td><td>TransactionReceiptQuery</td><td>Returns the TransactionReceiptQuery response for a transaction. This will not error on bad status like <code>RECEIPT_NOT_FOUND</code> and will return information about a failed transaction if necessary.</td></tr><tr><td><code><TransactionResponse>.getReceiptAsync(<client, timeout>)</code></td><td>Client, Duration</td><td>Request receipt asynchronously for the provided duration</td></tr><tr><td><code><TransactionReceipt>.status</code></td><td>Status</td><td>Whether the transaction reached consensus or not</td></tr><tr><td><code><TransactionReceipt>.accountId</code></td><td>AccountId</td><td>The newly generated account ID</td></tr><tr><td><code><TransactionReceipt>.topicId</code></td><td>TopicId</td><td>The newly generated topic ID</td></tr><tr><td><code><TransactionReceipt>).fileId</code></td><td>FileId</td><td>The newly generated file ID</td></tr><tr><td><code><TransactionReceipt>).contractId</code></td><td>ContractId</td><td>The newly generated contract ID</td></tr><tr><td><code><TransactionReceipt>).tokenId</code></td><td>TokenId</td><td>The newly generated token ID</td></tr><tr><td><code><TransactionReceipt>).scheduleId</code></td><td>ScheduleId</td><td>The newly generated schedule ID</td></tr><tr><td><code><TransactionReceipt>).scheduledTransactionId</code></td><td>TransactionId</td><td>The generated scheduled transaction ID</td></tr><tr><td><code><TransactionReceipt>).exchangeRate</code></td><td>ExchangeRate</td><td>The exchange rate in hbar, cents, and expiration time</td></tr><tr><td><code><TransactionReceipt>.topicRunningHash</code></td><td>ByteString</td><td>The topic running hash</td></tr><tr><td><code><TransactionReceipt>.topicSequenceNumber</code></td><td>long</td><td>The topic sequence number</td></tr><tr><td><code><TransactionReceipt>.totalSupply</code></td><td>long</td><td>The total supply of a token</td></tr><tr><td><code><TransactionReceipt>.serials</code></td><td>List<long></td><td>The list of newly created serial numbers upon execution of a token mint transaction.</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}

```java
//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

System.out.println("The transaction receipt: " +receipt);

//v2.0.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

console.log("The transaction receipt: " +receipt);

//v2.0.0
```

{% endtab %}

{% tab title="Go" %}

```java
//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

fmt.Printf("The transaction receipt %v\n", receipt)

//v2.0.0
```

{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Sample Output:" %}

```bash
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

{% endtab %}
{% endtabs %}
