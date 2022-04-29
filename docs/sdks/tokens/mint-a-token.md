# Mint a token

Minting fungible token allows you to increase the total supply of the token. Minting a non-fungible token creates an NFT with its unique metadata for the class of NFTs defined by the token ID. The Supply Key must sign the transaction.

* If no Supply Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_SUPPLY\_KEY. The maximum total supply a token can have is 2^63-1.
* The amount provided must be in the lowest denomination possible.
  * Example: Token A has 2 decimals. In order to mint 100 tokens, one must provide an amount of 10000. In order to mint 100.55 tokens, one must provide an amount of 10055.
* The metadata field is specific to NFTs. Once an NFT is minted, the metadata cannot be changed and is immutable.
  * You can use the metadata field to add a URI that contains additional information about the token. You can view the metadata schema [here](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-10.md). The metadata field has a 100 character limit.
* The serial number for the NFT is returned in the receipt of the transaction.
* When minting NFTs, do not set the amount. The amount is used for minting fungible tokens only.

**Transaction Signing Requirements**

* Supply key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                               |
| ---------------------------- | ----------------------------------------- |
| `new TokenMintTransaction()` | Initializes a TokenMintTransaction object |

```java
new TokenMintTransaction()
```

### Methods

{% tabs %}
{% tab title="V2" %}
| Method                     | Type           | Description                                                                                                                                                                                                                               | Requirement |
| -------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`    | TokenId        | The token ID for which to mint additional tokens                                                                                                                                                                                          | Required    |
| `setAmount(<amount>)`      | long           | Applicable to tokens of type `FUNGIBLE_COMMON`.The amount to mint to the Treasury Account. The amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than `2^63-1`. | Optional    |
| `setMetadata(<metaDatas>)` | List\<byte\[]> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes and is immutable.                                                                   | Optional    |
| `addMetadata(<metaData>)`  | byte \[]       | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes and is immutable.                                                                   | Optional    |

{% code title="Java" %}
```java
//Mint another 1,000 tokens
TokenMintTransaction transaction = new TokenMintTransaction()
     .setTokenId(tokenId)
     .setAmount(1000);

//Freeze the unsigned transaction, sign with the supply private key of the token, submit the transaction to a Hedera network
TransactionResponse txResponse = transaction.freezeWith(client).sign(supplyKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Obtain the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus;

//v2.0.1
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Mint another 1,000 tokens and freeze the unsigned transaction for manual signing
const transaction = await new TokenMintTransaction()
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
//Mint another 1,000 tokens and freeze the unsigned transaction for manual signing
transaction, err = hedera.NewTokenMintTransaction().
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
| Method                     | Type           | Description                                                                                                                                                                        | Requirement |
| -------------------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`    | TokenId        | The token for which to mint tokens                                                                                                                                                 | Required    |
| `setAmount(<amount>)`      | long           | The amount to mint to the Treasury Account. Amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than 2^63. | Required    |
| `addMetadata(<metadata>)`  | byte\[]        | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes.                             | Optional    |
| `addMetadata(<metadatas>)` | List\<byte\[]> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created.The maximum allowed size of each metadata is 100 bytes.                              | Optional    |
| `addMetadata(<metadatas>)` | String         | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes.                             | Optional    |

{% code title="Java" %}
```java
//Mint another 1,000 tokens
TokenMintTransaction transaction = new TokenMintTransaction()
    .setTokenId(newTokenId)
    .setAmount(1000);

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
//Mint another 1,000 tokens
const transaction = new TokenMintTransaction()
    .setTokenId(newTokenId)
    .setAmount(1000);

//Build the unsigned transaction, sign with supply private key of the token, submit the transaction to a Hedera network
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
