# Pseudorandom Number Generator

A transaction that generates a pseudorandom number. When the pseudorandom number generate transaction executes, its transaction record will contain the 384-bit array of pseudorandom bytes. The transaction has an optional `range` parameter. If the parameter is given and is positive, then the record will contain a 32-bit pseudorandom integer `r`, where `0 <= r < range` instead of containing the 384 pseudorandom bits.

When the `n`th transaction needs a pseudorandom number, it is given the running hash of all records up to and including the record for transaction `n-3`. If it needs 384 bits, then it uses the entire hash. If it needs 256 bits, it uses the first 256 bits of the hash. If it needs a random number `r` that is in the range `0 <= r < range`, then it lets `x` be the first 32 bits of the hash (interpreted as a signed integer).

The choice of using the hash up to transaction `n-3` rather than `n-1` is to ensure the transactions can be processed quickly. Because the thread calculating the hash will have more time to complete it before it is needed. The use of `n-3` rather than `n-1000000` is to make it hard to predict the pseudorandom number in advance.\\

Reference: [HIP-351](https://hips.hedera.com/hip/hip-351)

| Field     | Description                                                                 |
| --------- | --------------------------------------------------------------------------- |
| **Range** | The specified range to return the pseudorandom number from. |

#### Methods

| Method              | Type    | Requirement |
| ------------------- | ------- | ----------- |
| `setRange(<range>)` | integer | Optional    |
| `getRange(<range>)` | integer | Optional    |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction with range set
TransactionResponse transaction = new PrngTransaction()
        //Set the range
        .setRange(250)                
        .execute(client);

//Get the record
TransactionRecord transactionRecord = transaction.getRecord(client);

//Get the number
int prngNumber = transactionRecord.prngNumber;

System.out.println(prngNumber);

//SDK version 2.17.0
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the transaction with range set
const transaction = await new PrngTransaction()
        //Set the range
        .setRange(250)                
        .execute(client);

//Get the record
const transactionRecord = await transaction.getRecord(client);

//Get the number
const prngNumber = transactionRecord.prngNumber;

console.log(prngNumber);

//SDK version 2.17.0
```

{% endtab %}

{% tab title="Go" %}

```go
transaction, err := hedera.NewPrngTransaction().
	// Set the range
	SetRange(250).
	Execute(client)
if err != nil {
	println(err.Error(), ": error executing rng transaction")
	return
}

transactionRecord, err := createResponse.GetRecord(client)
if err != nil {
	println(err.Error(), ": error getting receipt")
	return
}

if transactionRecord.PrngNumber != nil {
	println(err.Error(), ": error, pseudo-random number is nil")
	return
}

println("The pseudorandom number is:", *transactionRecord.PrngNumber)

//Version 2.17.0
```

{% endtab %}
{% endtabs %}
