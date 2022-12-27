# Mint a token

Minting fungible token allows you to increase the total supply of the token. Minting a non-fungible token creates an NFT with its unique metadata for the class of NFTs defined by the token ID. The Supply Key must sign the transaction.

* If no Supply Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_SUPPLY\_KEY. The maximum total supply a token can have is 2^63-1.
* The amount provided must be in the lowest denomination possible.
  * Example: Token A has 2 decimals. In order to mint 100 tokens, one must provide an amount of 10000. In order to mint 100.55 tokens, one must provide an amount of 10055.
* The metadata field is specific to NFTs. Once an NFT is minted, the metadata cannot be changed and is immutable.
  * You can use the metadata field to add a URI that contains additional information about the token. You can view the metadata schema [here](https://hips.hedera.com/hip/hip-412). The metadata field has a 100-character limit.
* The serial number for the NFT is returned in the receipt of the transaction.
* When minting NFTs, do not set the amount. The amount is used for minting fungible tokens only.
* This transaction accepts zero unit minting operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564))

**Transaction Signing Requirements**

* Supply key
* Transaction fee payer account key

**Transaction Fees**

* Please see the transaction and query [fees](broken-reference) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                               |
| ---------------------------- | ----------------------------------------- |
| `new TokenMintTransaction()` | Initializes a TokenMintTransaction object |

```java
new TokenMintTransaction()
```

### Methods

| Method                     | Type           | Description                                                                                                                                                                                                                               | Requirement |
| -------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`    | TokenId        | The token ID for which to mint additional tokens                                                                                                                                                                                          | Required    |
| `setAmount(<amount>)`      | long           | Applicable to tokens of type `FUNGIBLE_COMMON`.The amount to mint to the Treasury Account. The amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than `2^63-1`. | Optional    |
| `setMetadata(<metaDatas>)` | List\<byte\[]> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes and is immutable.                                                                   | Optional    |
| `addMetadata(<metaData>)`  | byte \[]       | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes and is immutable.                                                                   | Optional    |

{% tabs %}
{% tab title="Java" %}
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
{% endtab %}

{% tab title="JavaScript" %}
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
{% endtab %}

{% tab title="Go" %}
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
{% endtab %}
{% endtabs %}
