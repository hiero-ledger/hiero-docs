# Burn a token

Burns fungible and non-fungible tokens owned by the Treasury Account. If no Supply Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_SUPPLY\_KEY.&#x20;

* The operation decreases the Total Supply of the Token.&#x20;
* Total supply cannot go below zero.&#x20;
* The amount provided must be in the lowest denomination possible.&#x20;
  * Example: Token A has 2 decimals. In order to burn 100 tokens, one must provide an amount of 10000. In order to burn 100.55 tokens, one must provide an amount of 10055.

**Transaction Signing Requirements**

* Supply key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                                     |
| ---------------------------- | ----------------------------------------------- |
| `new TokenBurnTransaction()` |     Initializes the TokenBurnTransaction object |

```java
new TokenBurnTransaction()
```

### Methods

{% tabs %}
{% tab title="V2" %}


| Method                  | Type        | Description                                                                             | Requirement |
| ----------------------- | ----------- | --------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)` | TokenId     | The ID of the token to burn supply                                                      | Required    |
| `setAmount(<amount>)`   | long        | The number of tokens to burn (fungible tokens)                                          | Optional    |
| `setSerials(<serials>)` | List\<long> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The  list of NFT serial IDs to burn. | Optional    |
| `addSerial(<serial>)`   | long        | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`.The serial ID to burn.               | Optional    |

{% code title="Java" %}
```java
//Burn 1,000 tokens
TokenBurnTransaction transaction = new TokenBurnTransaction()
     .setTokenId(tokenId)
     .setAmount(1000);

//Freeze the unsigned transaction, sign with the supply private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(supplyKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.0.1
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Burn 1,000 tokens and freeze the unsigned transaction for manual signing
const transaction = await new TokenBurnTransaction()
     .setTokenId(tokenId)
     .setAmount(1000)
     .freezeWith(client);

//Sign with the supply private key of the token 
const signTx = await transaction.sign(supplyKey);

//Submit the transaction to a Hedera network    
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);
    
//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status " +transactionStatus.toString());

//v2.0.7
```
{% endcode %}

{% code title="Go" %}
```go
//Burn 1,000 tokens and freeze the unsigned transaction for manual signing
transaction, err = hedera.NewTokenBurnTransaction().
		SetTokenID(tokenId).
		SetAmount(1000).
		FreezeWith(client)

if err != nil {
		panic(err)
}

//Sign with the supply private key of the token, submit the transaction to a Hedera network
txResponse, err := transaction.Sign(supplyKey).Execute(client)

if err != nil {
		panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)

if err != nil {
		panic(err)
}

//Get the transaction consensus status
status := receipt.Status

fmt.Printf("The transaction consensus status is %v\n", status)

//v2.1.0
```
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method                     | Type        | Description                                                                        | Requirement |
| -------------------------- | ----------- | ---------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`    | TokenId     | The ID of the token to burn supply                                                 | Required    |
| `setTokenAmount(<amount>)` | long        | The number of tokens to burn                                                       | Optional    |
| `addSerial(<serial>)`      | long        | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. Serial ID to burn.             | Optional    |
| `setSerials(<serials>)`    | List\<Long> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE.` A list of NFT serials to burn. | Optional    |

{% code title="Java" %}
```java
//Burn 1,000 tokens
TokenBurnTransaction transaction = new TokenBurnTransaction()
    .setTokenId(newTokenId)
    .setAmount(1000)

//Build the unsigned transaction, sign with the supply private key of the token, submit the transaction to a Hedera network
TransactionId transactionId = transaction.build(client).sign(supplyKey).execute(client);
    
//Request the receipt of the transaction
TransactionReceipt getReceipt = transactionId.getReceipt(client);
    
//Obtain the transaction consensus status
Status transactionStatus = getReceipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);
//Version: 1.2.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Burn 1,000 tokens
const transaction = new TokenBurnTransaction()
    .setTokenId(newTokenId)
    .setAmount(1000)

//Build the unsigned transaction, sign with the supply private key of the token, submit the transaction to a Hedera network
const transactionId = await transaction.build(client).sign(supplyKey).execute(client);
    
//Request the receipt of the transaction
const getReceipt = await transactionId.getReceipt(client);
    
//Obtain the transaction consensus status
const transactionStatus = getReceipt.status;

console.log("The transaction consensus status is " +transactionStatus);
//Version 1.4.2
```
{% endcode %}
{% endtab %}
{% endtabs %}

