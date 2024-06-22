# Mint a token

Minting fungible token allows you to increase the total supply of the token. Minting a non-fungible token creates an NFT with its unique metadata for the class of NFTs defined by the token ID. The Supply Key must sign the transaction.

- If no Supply Key is defined, the transaction will resolve to TOKEN\_HAS\_NO\_SUPPLY\_KEY. The maximum total supply a token can have is 2^63-1.
- The amount provided must be in the lowest denomination possible.
  - Example: Token A has 2 decimals. In order to mint 100 tokens, one must provide an amount of 10000. In order to mint 100.55 tokens, one must provide an amount of 10055.
- The metadata field is specific to NFTs. Once an NFT is minted, the metadata cannot be changed and is immutable.
  - You can use the metadata field to add a URI that contains additional information about the token. You can view the metadata schema [here](https://hips.hedera.com/hip/hip-412). The metadata field has a 100 character limit.
- The serial number for the NFT is returned in the receipt of the transaction.
- When minting NFTs, do not set the amount. The amount is used for minting fungible tokens only.
- This transaction accepts zero unit minting operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564))

**Transaction Signing Requirements**

- Supply key
- Transaction fee payer account key

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

| Constructor                  | Description                               |
| ---------------------------- | ----------------------------------------- |
| `new TokenMintTransaction()` | Initializes a TokenMintTransaction object |

```java
new TokenMintTransaction()
```

### Methods

{% tabs %}
{% tab title="V1" %}

| Method                     | Type                                                                      | Description                                                                                                                                                                                                                        | Requirement |
| -------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| `setTokenId(<tokenId>)`    | TokenId                                                                   | The token for which to mint tokens                                                                                                                                                                                                 | Required    |
| `setAmount(<amount>)`      | long                                                                      | The amount to mint to the Treasury Account. Amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than 2^63. | Required    |
| `addMetadata(<metadata>)`  | byte\[]              | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes.                             | Optional    |
| `addMetadata(<metadatas>)` | List\\\\<byte\[]> | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created.The maximum allowed size of each metadata is 100 bytes.                              | Optional    |
| `addMetadata(<metadatas>)` | String                                                                    | Applicable to tokens of type `NON_FUNGIBLE_UNIQUE`. A list of metadata that are being created. The maximum allowed size of each metadata is 100 bytes.                             | Optional    |

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
