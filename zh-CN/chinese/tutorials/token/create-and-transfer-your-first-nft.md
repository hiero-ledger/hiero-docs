# Create and Transfer Your First NFT

## Summary

Using the Hedera Token Service, you can create non-fungible tokens (NFTs). NFTs are uniquely identifiable. On the Hedera network, the token ID represents a collection of NFTs of the same class, and the serial number of each token uniquely identifies each NFT in the class.

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

- Get a [Hedera testnet account](../../getting-started/introduction.md).
- Set up your environment [here](../../getting-started/environment-set-up.md).

***

## 1. Create a Non-Fungible Token (NFT)

Use _<mark style="color:blue;">**`TokenCreateTransaction()`**</mark>_ to configure and set the token properties. At a minimum, this constructor requires setting a name, symbol, and treasury account ID. All other fields are optional, so if they’re not specified then default values are used. For instance, not specifying an _admin key_, makes a token immutable (can’t change or add properties); not specifying a _supply key_, makes a token supply fixed (can’t mint new or burn existing tokens); not specifying a _token type_, makes a token fungible.

After submitting the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt. This token ID represents an NFT class.

{% tabs %}
{% tab title="Java" %}

```java
//Create the NFT
TokenCreateTransaction nftCreate = new TokenCreateTransaction()
        .setTokenName("diploma")
        .setTokenSymbol("GRAD")
        .setTokenType(TokenType.NON_FUNGIBLE_UNIQUE)
        .setDecimals(0)
        .setInitialSupply(0)
        .setTreasuryAccountId(treasuryId)
        .setSupplyType(TokenSupplyType.FINITE)
        .setMaxSupply(250)
        .setSupplyKey(supplyKey)
        .freezeWith(client);

//Sign the transaction with the treasury key
TokenCreateTransaction nftCreateTxSign = nftCreate.sign(treasuryKey);

//Submit the transaction to a Hedera network
TransactionResponse nftCreateSubmit = nftCreateTxSign.execute(client);

//Get the transaction receipt
TransactionReceipt nftCreateRx = nftCreateSubmit.getReceipt(client);

//Get the token ID
TokenId tokenId = nftCreateRx.tokenId;

//Log the token ID
System.out.println("Created NFT with token ID " + tokenId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the NFT
const nftCreate = await new TokenCreateTransaction()
	.setTokenName("diploma")
	.setTokenSymbol("GRAD")
	.setTokenType(TokenType.NonFungibleUnique)
	.setDecimals(0)
	.setInitialSupply(0)
	.setTreasuryAccountId(treasuryId)
	.setSupplyType(TokenSupplyType.Finite)
	.setMaxSupply(250)
	.setSupplyKey(supplyKey)
	.freezeWith(client);

//Sign the transaction with the treasury key
const nftCreateTxSign = await nftCreate.sign(treasuryKey);

//Submit the transaction to a Hedera network
const nftCreateSubmit = await nftCreateTxSign.execute(client);

//Get the transaction receipt
const nftCreateRx = await nftCreateSubmit.getReceipt(client);

//Get the token ID
const tokenId = nftCreateRx.tokenId;

//Log the token ID
console.log("Created NFT with Token ID: " + tokenId);
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the NFT
nftCreate, err := hedera.NewTokenCreateTransaction().
     SetTokenName("diploma").
     SetTokenSymbol("GRAD").
     SetTokenType(hedera.TokenTypeNonFungibleUnique).
     SetDecimals(0). 
     SetInitialSupply(0).
     SetTreasuryAccountID(treasuryAccountId).
     SetSupplyType(hedera.TokenSupplyTypeFinite).
     SetMaxSupply(250).
     SetSupplyKey(supplyKey).
     FreezeWith(client)

//Sign the transaction with the treasury key
nftCreateTxSign := nftCreate.Sign(treasuryKey)

//Submit the transaction to a Hedera network
nftCreateSubmit, err := nftCreateTxSign.Execute(client)

//Get the transaction receipt
nftCreateRx, err := nftCreateSubmit.GetReceipt(client)

//Get the token ID
tokenId := *nftCreateRx.TokenID

//Log the token ID
fmt.Println("Created NFT with token ID ", tokenId)
```

{% endtab %}
{% endtabs %}

***

## 2. Mint a New NFT

When creating an NFT, the decimals and initial supply must be set to zero. After the token is created, you mint each NFT using the token mint operation. Specifying a _supply key_ during token creation is a requirement to be able to [mint](../../sdks-and-apis/sdks/token-service/mint-a-token.md) and [burn](../../sdks-and-apis/sdks/token-service/burn-a-token.md) tokens. The supply key is required to sign mint and burn transactions.

Both the NFT image and metadata live in the InterPlanetary File System (IPFS), which provides decentralized storage. The file diploma\_metadata.json contains the metadata for the NFT. A content identifier (CID) pointing to the metadata file is used during minting of the new NFT. Notice that the metadata file contains a URI pointing to the NFT image.

{% tabs %}
{% tab title="Java" %}

```java
// Max transaction fee as a constant
final int MAX_TRANSACTION_FEE = 20;

// IPFS content identifiers for which we will create a NFT
String[] CID = {
        "ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json",
        "ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json",
        "ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json",
        "ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json",
        "ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"
    };

// Mint a new NFT
TokenMintTransaction mintTx = new TokenMintTransaction().setTokenId(tokenId)
        .setMaxTransactionFee(new Hbar(MAX_TRANSACTION_FEE));

for (String cid : CID) {
        mintTx.addMetadata(cid.getBytes());
}

// Freeze the transaction
mintTx.freezeWith(client);

// Sign transaction with the supply key
TokenMintTransaction mintTxSign = mintTx.sign(supplyKey);

// Submit the transaction to a Hedera network
TransactionResponse mintTxSubmit = mintTxSign.execute(client);

// Get the transaction receipt
TransactionReceipt mintRx = mintTxSubmit.getReceipt(client);

// Log the serial number
System.out.println("Created NFT " + tokenId + " with serial: " + mintRx.serials);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Max transaction fee as a constant
const maxTransactionFee = new Hbar(20);

//IPFS content identifiers for which we will create a NFT
const CID = [
  Buffer.from(
    "ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"
  ),
  Buffer.from(
    "ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"
  ),
  Buffer.from(
    "ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"
  ),
  Buffer.from(
    "ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"
  ),
  Buffer.from(
    "ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"
  )
];
	
// MINT NEW BATCH OF NFTs
const mintTx = new TokenMintTransaction()
	.setTokenId(tokenId)
	.setMetadata(CID) //Batch minting - UP TO 10 NFTs in single tx
	.setMaxTransactionFee(maxTransactionFee)
	.freezeWith(client);

//Sign the transaction with the supply key
const mintTxSign = await mintTx.sign(supplyKey);

//Submit the transaction to a Hedera network
const mintTxSubmit = await mintTxSign.execute(client);

//Get the transaction receipt
const mintRx = await mintTxSubmit.getReceipt(client);

//Log the serial number
console.log("Created NFT " + tokenId + " with serial number: " + mintRx.serials);
```

{% endtab %}

{% tab title="Go" %}

```go
// Max transaction fee as a constant
const maxTransactionFee = 20 // in tinybars

//IPFS content identifiers for which we will create a NFT
CID := [][]byte{
    []byte("ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"),
    []byte("ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"),
    []byte("ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"),
    []byte("ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"),
    []byte("ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"),
}

// Mint new NFT
mintTx, err := hedera.NewTokenMintTransaction().
    SetTokenID(tokenId).
    SetMetadata(CID).
    SetMaxTransactionFee(hedera.HbarFromTinybars(maxTransactionFee)).
    FreezeWith(client)

// Sign the transaction with the supply key
mintTxSign := mintTx.Sign(supplyKey)

// Submit the transaction to a Hedera network
mintTxSubmit, err := mintTxSign.Execute(client)

// Get the transaction receipt
mintRx, err := mintTxSubmit.GetReceipt(client)

// Log the serial number
fmt.Printf("Created NFT %s with serial: %d\n", tokenId, mintRx.SerialNumbers[0])
```

{% endtab %}
{% endtabs %}

{% code title="diploma_metadata.json" %}

```json
{
	"name": "Diploma",
	"description": "Certificate of Graduation",
	"image": "https://ipfs.io/ipfs/QmdTNJDYePd4EUUzYPuhc1GsDELjab6ypgvDQAp4visoM9?filename=diploma.jpg",
	"properties": {
		"university": "H State University",
		"college": "Engineering and Applied Sciences",
		"level": "Masters",
		"field": "Mechanical Engineering",
		"honors": "yes",
		"honorsType": "Summa Cum Laude",
		"gpa": "3.84",
		"student": "Alice",
		"date": "2021-03-18"
	}
}
```

{% endcode %}

### **🚨 Throttle cap warning**&#x20

Batch minting of 10+ NFTs can run into throughput issues and potentially hit the [transaction limit (throttle cap)](../../networks/mainnet/#network-throttles), causing the SDK to throw `BUSY` exceptions. Adding an application-level retry loop can help manage these exceptions for the batch-minting process.

The following are examples of retry loops:

{% tabs %}
{% tab title="Java" %}

```java
private static final int MAX_RETRIES = 5;

private static TransactionReceipt executeTransaction
(Transaction<?> transaction, PrivateKey key) 
throws Exception {
    int retries = 0;a

    while (retries < MAX_RETRIES) {
        try {
            TransactionResponse txResponse = transaction.sign(key).execute(client);
            TransactionReceipt txReceipt = txResponse.getReceipt(client);

            return txReceipt;
        } catch (PrecheckStatusException e) {
            if (e.status == Status.BUSY) {
                retries++;
                System.out.println("Retry attempt: " + retries);
            } else {
                throw e;
            }
        }
    }
    
    throw new Exception("Transaction failed after " + MAX_RETRIES + " attempts");
}
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
async function executeTransaction(transaction, key) {
  let retries = 0;
  while (retries < MAX_RETRIES) {
    try {
      const txSign = await transaction.sign(key);
      const txSubmit = await txSign.execute(client);
      const txReceipt = await txSubmit.getReceipt(client);

      // If the transaction succeeded, return the receipt
      return txReceipt;
    } catch (err) {
      // If the error is BUSY, retry the transaction
      if (err.toString().includes('BUSY')) {
        retries++;
        console.log(`Retry attempt: ${retries}`);
      } else {
        // If the error is not BUSY, throw the error
        throw err;
      }
    }
  }
  throw new Error(`Transaction failed after ${MAX_RETRIES} attempts`);
}
```

{% endtab %}

{% tab title="Go" %}

```go
func retry(attempts int, sleep time.Duration, fn func() error) error {
	err := fn()
	if err != nil {
		if attempts--; attempts > 0 {
			// Exponential backoff
			time.Sleep(sleep)
			return retry(attempts, 2*sleep, fn)
		}
	}
	return err
}

func executeWithRetry(fn func() error) error {
	return retry(2, 1*time.Second, fn)
}
```

{% endtab %}
{% endtabs %}

***

## 3. Associate User Accounts with the NFT

Before an account that is not the treasury for a token can receive or send this specific token ID, the account must become “associated” with the token. To associate a token to an account, the account owner must sign the associate transaction. If you have an account with the automatic token association property set, you do not need to associate the token before transferring it to the receiving account.

{% tabs %}
{% tab title="Java" %}

```java
//Create the associate transaction and sign with Alice's key 
TokenAssociateTransaction associateAliceTx = new TokenAssociateTransaction()
        .setAccountId(aliceAccountId)
        .setTokenIds(Collections.singletonList(tokenId))
	.freezeWith(client)
        .sign(aliceKey);

//Submit the transaction to a Hedera network
TransactionResponse associateAliceTxSubmit = associateAliceTx.execute(client);

//Get the transaction receipt
TransactionReceipt associateAliceRx = associateAliceTxSubmit.getReceipt(client);

//Confirm the transaction was successful
System.out.println("\nNFT association with Alice's account: " + associateAliceRx.status + " ✅");
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create the associate transaction and sign with Alice's key 
const associateAliceTx = await new TokenAssociateTransaction()
	.setAccountId(aliceId)
	.setTokenIds([tokenId])
	.freezeWith(client)
	.sign(aliceKey);

//Submit the transaction to a Hedera network
const associateAliceTxSubmit = await associateAliceTx.execute(client);

//Get the transaction receipt
const associateAliceRx = await associateAliceTxSubmit.getReceipt(client);

//Confirm the transaction was successful
console.log(`NFT association with Alice's account: ${associateAliceRx.status}\n`);
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the associate transaction
associateAliceTx, err := hedera.NewTokenAssociateTransaction().
	SetAccountID(aliceAccountId).
	SetTokenIDs(tokenId).
	FreezeWith(client)

//Sign with Alice's key
signTx := associateAliceTx.Sign(aliceKey)

//Submit the transaction to a Hedera network
associateAliceTxSubmit, err := signTx.Execute(client)//Get the transaction receipt

//Get the transaction receipt
associateAliceRx, err := associateAliceTxSubmit.GetReceipt(client)

//Confirm the transaction was successful
fmt.Println("NFT association with Alice's account:", associateAliceRx.Status)
```

{% endtab %}
{% endtabs %}

***

## 4. Transfer the NFT

Now, transfer the NFT and check the account balances before and after the send! After the transfer, you should expect 1 NFT to be removed from the treasury account and available in Alice's account. The treasury account key has to sign the transfer transaction to authorize the transfer to Alice's account.

{% tabs %}
{% tab title="Java" %}

```java
// Check the balance before the NFT transfer for the treasury account
AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println(
        "- Treasury balance: " + balanceCheckTreasury.tokens.getOrDefault(tokenId, 0L)
                + " NFTs of ID " + tokenId);


// Check the balance before the NFT transfer for Alice's account
AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId)
                        .execute(client);
System.out.println("Alice's balance: " + balanceCheckAlice.tokens.getOrDefault(tokenId, 0L) + " NFTs of ID " + tokenId);

// Transfer NFT from treasury to Alice
// Sign with the treasury key to authorize the transfer
TransferTransaction tokenTransferTx = new TransferTransaction()
        .addNftTransfer(new NftId(tokenId, 1), treasuryId, aliceAccountId)
        .freezeWith(client)
        .sign(treasuryKey);

TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);
TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);

System.out.println("\nNFT transfer from Treasury to Alice: " + tokenTransferRx.status);

// Check the balance for the treasury account after the transfer
AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println("\nTreasury balance: " + balanceCheckTreasury2.tokens.getOrDefault(tokenId, 0L) + " NFTs of ID " + tokenId);

// Check the balance for Alice's account after the transfer
AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
System.out.println(
        "- Alice's balance: " + balanceCheckAlice2.tokens.getOrDefault(tokenId, 0L)
                + " NFTs of ID " + tokenId + "\n");
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Check the balance before the transfer for the treasury account
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Check the balance before the transfer for Alice's account
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Transfer the NFT from treasury to Alice
// Sign with the treasury key to authorize the transfer
const tokenTransferTx = await new TransferTransaction()
	.addNftTransfer(tokenId, 1, treasuryId, aliceId)
	.freezeWith(client)
	.sign(treasuryKey);

const tokenTransferSubmit = await tokenTransferTx.execute(client);
const tokenTransferRx = await tokenTransferSubmit.getReceipt(client);

console.log(`\nNFT transfer from Treasury to Alice: ${tokenTransferRx.status} \n`);

// Check the balance of the treasury account after the transfer
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Check the balance of Alice's account after the transfer
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);
```

{% endtab %}

{% tab title="Go" %}

```go
// Transfer the NFT from treasury to Alice
	tokenTransferTx, err := hedera.NewTransferTransaction().
			AddNftTransfer(hedera.NftID{TokenID: tokenId, SerialNumber: 1}, treasuryAccountId, aliceAccountId).
			FreezeWith(client)
		if err != nil {
			panic(err)
		}

		// Sign with the treasury key to authorize the transfer
		signTransferTx := tokenTransferTx.Sign(treasuryKey)

		//Submit the transaction
		tokenTransferSubmit, err := signTransferTx.Execute(client)

		// Get the transaction receipt
		tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

		fmt.Println("\nNFT transfer from Treasury to Alice:", tokenTransferRx.Status, "✅")

		// Check the balance of the treasury account after the transfer
		balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		treasuryNftBalance2 := balanceCheckTreasury2.Tokens.Get(tokenId)
		fmt.Println("Treasury balance:", treasuryNftBalance2, "NFTs of ID", tokenId)

		// Check the balance of Alice's account after the transfer
		balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		aliceNftBalance2 := balanceCheckAlice2.Tokens.Get(tokenId)
		fmt.Println("Alice's balance:", aliceNftBalance2, "NFTs of ID", tokenId, "\n")
```

{% endtab %}
{% endtabs %}

***

## Code Check ✅

<details>

<summary>Java</summary>

```java
import com.hedera.hashgraph.sdk.*;
import io.github.cdimascio.dotenv.Dotenv;

import java.util.*;
import java.util.concurrent.TimeoutException;

public class CreateYourFirstNft {

    public static void main(String[] args)
            throws TimeoutException, PrecheckStatusException, ReceiptStatusException {

        // Load environment variables
        Dotenv dotenv = Dotenv.load();

        // Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(dotenv.get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromStringDER(dotenv.get("MY_PRIVATE_KEY"));

        // Create your Hedera testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

        // Treasury Key
        PrivateKey treasuryKey = PrivateKey.generateED25519();
        PublicKey treasuryPublicKey = treasuryKey.getPublicKey();

        // Create treasury account
        TransactionResponse treasuryAccount = new AccountCreateTransaction()
                .setKey(treasuryPublicKey)
                .setInitialBalance(new Hbar(10))
                .execute(client);

        AccountId treasuryId = treasuryAccount.getReceipt(client).accountId;

        // Alice Key
        PrivateKey aliceKey = PrivateKey.generateED25519();
        PublicKey alicePublicKey = aliceKey.getPublicKey();

        // Create Alice's account
        TransactionResponse aliceAccount = new AccountCreateTransaction()
                .setKey(alicePublicKey)
                .setInitialBalance(new Hbar(10))
                .execute(client);

        AccountId aliceAccountId = aliceAccount.getReceipt(client).accountId;

        // Generate the upply Key
        PrivateKey supplyKey = PrivateKey.generateED25519();
        PublicKey supplyPublicKey = supplyKey.getPublicKey();

        // Create the NFT
        TokenCreateTransaction nftCreate = new TokenCreateTransaction()
                .setTokenName("diploma")
                .setTokenSymbol("GRAD")
                .setTokenType(TokenType.NON_FUNGIBLE_UNIQUE)
                .setDecimals(0)
                .setInitialSupply(0)
                .setTreasuryAccountId(treasuryId)
                .setSupplyType(TokenSupplyType.FINITE)
                .setMaxSupply(250)
                .setSupplyKey(supplyKey)
                .freezeWith(client);

        // Sign the transaction with the treasury key
        TokenCreateTransaction nftCreateTxSign = nftCreate.sign(treasuryKey);

        // Submit the transaction to a Hedera network
        TransactionResponse nftCreateSubmit = nftCreateTxSign.execute(client);

        // Get the transaction receipt
        TransactionReceipt nftCreateRx = nftCreateSubmit.getReceipt(client);

        // Get the token ID
        TokenId tokenId = nftCreateRx.tokenId;

        // Log the token ID
        System.out.println("\nCreated NFT with token ID " + tokenId);

        // Max transaction fee as a constant
        final int MAX_TRANSACTION_FEE = 20;

        // IPFS content identifiers for which we will create a NFT
        String[] CID = {
                "ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json",
                "ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json",
                "ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json",
                "ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json",
                "ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"
        };

        // Mint a new NFT
        TokenMintTransaction mintTx = new TokenMintTransaction().setTokenId(tokenId)
                .setMaxTransactionFee(new Hbar(MAX_TRANSACTION_FEE));

        for (String cid : CID) {
            mintTx.addMetadata(cid.getBytes());
        }

        // Freeze the transaction
        mintTx.freezeWith(client);

        // Sign transaction with the supply key
        TokenMintTransaction mintTxSign = mintTx.sign(supplyKey);

        // Submit the transaction to a Hedera network
        TransactionResponse mintTxSubmit = mintTxSign.execute(client);

        // Get the transaction receipt
        TransactionReceipt mintRx = mintTxSubmit.getReceipt(client);

        // Log the serial number
        System.out.println("Created NFT " + tokenId + " with serial: " + mintRx.serials);

        // Create the associate transaction and sign with Alice's key
        TokenAssociateTransaction associateAliceTx = new TokenAssociateTransaction()
                .setAccountId(aliceAccountId)
                .setTokenIds(Collections.singletonList(tokenId))
                .freezeWith(client)
                .sign(aliceKey);

        // Submit the transaction to a Hedera network
        TransactionResponse associateAliceTxSubmit = associateAliceTx.execute(client);

        // Get the transaction receipt
        TransactionReceipt associateAliceRx = associateAliceTxSubmit.getReceipt(client);

        // Confirm the transaction was successful
        System.out.println("\nNFT association with Alice's account: " + associateAliceRx.status + " ✅");

        // Check the balance before the NFT transfer for the treasury account
        AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println(
                "- Treasury balance: " + balanceCheckTreasury.tokens.get(tokenId)
                        + " NFTs of ID " + tokenId);

        // Check the balance before the NFT transfer for Alice's account
        AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println(
                "- Alice's balance: " + balanceCheckAlice.tokens.get(tokenId)
                        + " NFTs of ID " + tokenId);

        // Transfer NFT from treasury to Alice
        // Sign with the treasury key to authorize the transfer
        TransferTransaction tokenTransferTx = new TransferTransaction()
                .addNftTransfer(new NftId(tokenId, 1), treasuryId, aliceAccountId)
                .freezeWith(client).sign(treasuryKey);

        // Submit the transaction to a Hedera network
        TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);

        // Get the transaction receipt
        TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);

        // Confirm the transfer was successful
        System.out.println("\nNFT transfer from Treasury to Alice: " + tokenTransferRx.status + " ✅");

        // Check the balance for the treasury account after the transfer
        AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println(
                "- Treasury balance: " + balanceCheckTreasury2.tokens.get(tokenId)
                        + " NFTs of ID " + tokenId);

        // Check the balance for Alice's account after the transfer
        AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println(
                "- Alice's balance: " + balanceCheckAlice2.tokens.get(tokenId)
                        + " NFTs of ID " + tokenId + "\n");
    }
}
```

</details>

<details>

<summary>JavaScript</summary>

```javascript
console.clear();
require("dotenv").config();
const {
  Hbar,
  Client,
  AccountId,
  PrivateKey,
  TokenType,
  TokenSupplyType,
  TokenMintTransaction,
  TransferTransaction,
  AccountBalanceQuery,
  TokenCreateTransaction,
  TokenAssociateTransaction,
} = require("@hashgraph/sdk");

// Configure accounts and client, and generate needed keys
const operatorId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorKey = PrivateKey.fromStringDer(process.env.OPERATOR_PVKEY);
const treasuryId = AccountId.fromString(process.env.TREASURY_ID);
const treasuryKey = PrivateKey.fromStringDer(process.env.TREASURY_PVKEY);
const aliceId = AccountId.fromString(process.env.ALICE_ID);
const aliceKey = PrivateKey.fromStringDer(process.env.ALICE_PVKEY);

const client = Client.forTestnet().setOperator(operatorId, operatorKey);

const supplyKey = PrivateKey.generate();

async function createFirstNft() {
  //Create the NFT
  const nftCreate = await new TokenCreateTransaction()
    .setTokenName("diploma")
    .setTokenSymbol("GRAD")
    .setTokenType(TokenType.NonFungibleUnique)
    .setDecimals(0)
    .setInitialSupply(0)
    .setTreasuryAccountId(treasuryId)
    .setSupplyType(TokenSupplyType.Finite)
    .setMaxSupply(250)
    .setSupplyKey(supplyKey)
    .freezeWith(client);

  //Sign the transaction with the treasury key
  const nftCreateTxSign = await nftCreate.sign(treasuryKey);

  //Submit the transaction to a Hedera network
  const nftCreateSubmit = await nftCreateTxSign.execute(client);

  //Get the transaction receipt
  const nftCreateRx = await nftCreateSubmit.getReceipt(client);

  //Get the token ID
  const tokenId = nftCreateRx.tokenId;

  //Log the token ID
  console.log(`\nCreated NFT with Token ID: ` + tokenId);

  // Max transaction fee as a constant
  const maxTransactionFee = new Hbar(20);

  //IPFS content identifiers for which we will create a NFT
  const CID = [
    Buffer.from(
      "ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"
    ),
    Buffer.from(
      "ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"
    ),
    Buffer.from(
      "ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"
    ),
    Buffer.from(
      "ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"
    ),
    Buffer.from(
      "ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"
    ),
  ];

  // MINT NEW BATCH OF NFTs
  const mintTx = new TokenMintTransaction()
    .setTokenId(tokenId)
    .setMetadata(CID) //Batch minting - UP TO 10 NFTs in single tx
    .setMaxTransactionFee(maxTransactionFee)
    .freezeWith(client);

  //Sign the transaction with the supply key
  const mintTxSign = await mintTx.sign(supplyKey);

  //Submit the transaction to a Hedera network
  const mintTxSubmit = await mintTxSign.execute(client);

  //Get the transaction receipt
  const mintRx = await mintTxSubmit.getReceipt(client);

  //Log the serial number
  console.log(
    "Created NFT " + tokenId + " with serial number: " + mintRx.serials + "\n"
  );

  //Create the associate transaction and sign with Alice's key
  const associateAliceTx = await new TokenAssociateTransaction()
    .setAccountId(aliceId)
    .setTokenIds([tokenId])
    .freezeWith(client)
    .sign(aliceKey);

  //Submit the transaction to a Hedera network
  const associateAliceTxSubmit = await associateAliceTx.execute(client);

  //Get the transaction receipt
  const associateAliceRx = await associateAliceTxSubmit.getReceipt(client);

  //Confirm the transaction was successful
  console.log(
    `NFT association with Alice's account: ${associateAliceRx.status}\n`
  );

  // Check the balance before the transfer for the treasury account
  var balanceCheckTx = await new AccountBalanceQuery()
    .setAccountId(treasuryId)
    .execute(client);
  console.log(
    `Treasury balance: ${balanceCheckTx.tokens._map.get(
      tokenId.toString()
    )} NFTs of ID ${tokenId}`
  );

  // Check the balance before the transfer for Alice's account
  var balanceCheckTx = await new AccountBalanceQuery()
    .setAccountId(aliceId)
    .execute(client);
  console.log(
    `Alice's balance: ${balanceCheckTx.tokens._map.get(
      tokenId.toString()
    )} NFTs of ID ${tokenId}`
  );

  // Transfer the NFT from treasury to Alice
  // Sign with the treasury key to authorize the transfer
  const tokenTransferTx = await new TransferTransaction()
    .addNftTransfer(tokenId, 1, treasuryId, aliceId)
    .freezeWith(client)
    .sign(treasuryKey);

  const tokenTransferSubmit = await tokenTransferTx.execute(client);
  const tokenTransferRx = await tokenTransferSubmit.getReceipt(client);

  console.log(
    `\nNFT transfer from Treasury to Alice: ${tokenTransferRx.status} \n`
  );

  // Check the balance of the treasury account after the transfer
  var balanceCheckTx = await new AccountBalanceQuery()
    .setAccountId(treasuryId)
    .execute(client);
  console.log(
    `Treasury balance: ${balanceCheckTx.tokens._map.get(
      tokenId.toString()
    )} NFTs of ID ${tokenId}`
  );

  // Check the balance of Alice's account after the transfer
  var balanceCheckTx = await new AccountBalanceQuery()
    .setAccountId(aliceId)
    .execute(client);
  console.log(
    `Alice's balance: ${balanceCheckTx.tokens._map.get(
      tokenId.toString()
    )} NFTs of ID ${tokenId}`
  );
}
createFirstNft();
```

</details>

<details>

<summary>Go</summary>

```go
package main

import (
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {
	// Load environment variables
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
	}

	// Grab your testnet account ID and private key from the .env file
	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	// Create your testnet client
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	// Create a treasury Key
	treasuryKey, err := hedera.GeneratePrivateKey()
	if err != nil {
		panic(err)
	}
	treasuryPublicKey := treasuryKey.PublicKey()

	// Create treasury account
	treasuryAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(treasuryPublicKey).
		SetInitialBalance(hedera.NewHbar(10)).
		Execute(client)
	if err != nil {
		panic(err)
	}

	// Get the receipt of the transaction
	receipt, err := treasuryAccount.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	// Get the account ID
	treasuryAccountId := *receipt.AccountID

	// Alice Key
	aliceKey, err := hedera.GeneratePrivateKey()
	alicePublicKey := aliceKey.PublicKey()

	// Create Alice's account
	aliceAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(alicePublicKey).
		SetInitialBalance(hedera.NewHbar(10)).
		Execute(client)

	// Get the receipt of the transaction
	receipt2, err := aliceAccount.GetReceipt(client)

	// Get the account ID
	aliceAccountId := *receipt2.AccountID

	// Create a supply key
	supplyKey, err := hedera.GeneratePrivateKey()

	// Create the NFT
	nftCreate, err := hedera.NewTokenCreateTransaction().
		SetTokenName("diploma").
		SetTokenSymbol("GRAD").
		SetTokenType(hedera.TokenTypeNonFungibleUnique).
		SetDecimals(0).
		SetInitialSupply(0).
		SetTreasuryAccountID(treasuryAccountId).
		SetSupplyType(hedera.TokenSupplyTypeFinite).
		SetMaxSupply(250).
		SetSupplyKey(supplyKey).
		FreezeWith(client)

	// Sign the transaction with the treasury key
	nftCreateTxSign := nftCreate.Sign(treasuryKey)

	// Submit the transaction to a Hedera network
	nftCreateSubmit, err := nftCreateTxSign.Execute(client)

	// Get the transaction receipt
	nftCreateRx, err := nftCreateSubmit.GetReceipt(client)

	// Get the token ID
	tokenId := *nftCreateRx.TokenID

	// Log the token ID
	fmt.Println("\nCreated NFT with token ID", tokenId)

	// Max transaction fee as a constant
	const maxTransactionFee = 20 // in tinybars

	// IPFS content identifiers for which we will create a NFT
	CID := [][]byte{
		[]byte("ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"),
		[]byte("ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"),
		[]byte("ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"),
		[]byte("ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"),
		[]byte("ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"),
	}

	for _, singleCID := range CID {
		mintTx, err := hedera.NewTokenMintTransaction().
			SetTokenID(tokenId).
			SetMetadata(singleCID).
			SetMaxTransactionFee(hedera.NewHbar(maxTransactionFee)).
			FreezeWith(client)
		if err != nil {
			fmt.Println("Error while creating mint transaction:", err)
			continue
		}

		mintTxSign := mintTx.Sign(supplyKey)
		mintTxSubmit, err := mintTxSign.Execute(client)
		if err != nil {
			fmt.Println("Error while executing mint transaction:", err)
			continue
		}

		mintRx, err := mintTxSubmit.GetReceipt(client)
		if err != nil {
			fmt.Println("Error while getting mint transaction receipt:", err)
			continue
		}

		fmt.Printf("Created NFT %s with serial: %d\n", tokenId, mintRx.SerialNumbers[0])

		// Create the associate transaction
		associateAliceTx, err := hedera.NewTokenAssociateTransaction().
			SetAccountID(aliceAccountId).
			SetTokenIDs(tokenId).
			FreezeWith(client)
		if err != nil {
			panic(err)
		}

		// Sign with Alice's key
		signTx := associateAliceTx.Sign(aliceKey)

		// Submit the transaction to a Hedera network
		associateAliceTxSubmit, err := signTx.Execute(client)

		// Get the transaction receipt
		associateAliceRx, err := associateAliceTxSubmit.GetReceipt(client)

		// Confirm the transaction was successful
		fmt.Println("\nNFT association with Alice's account:", associateAliceRx.Status, "✅")

		// Check the balance before the transfer for the treasury account
		balanceCheckTreasury, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		treasuryNftBalance := balanceCheckTreasury.Tokens.Get(tokenId)
		fmt.Println("- Treasury balance:", treasuryNftBalance, "NFTs of ID", tokenId)

		// Check the balance before the transfer for Alice's account
		balanceCheckAlice, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		aliceNftBalance := balanceCheckAlice.Tokens.Get(tokenId)
		fmt.Println("- Alice's balance:", aliceNftBalance, "NFTs of ID", tokenId)

		// Transfer the NFT from treasury to Alice
		tokenTransferTx, err := hedera.NewTransferTransaction().
			AddNftTransfer(hedera.NftID{TokenID: tokenId, SerialNumber: 1}, treasuryAccountId, aliceAccountId).
			FreezeWith(client)
		if err != nil {
			panic(err)
		}

		// Sign with the treasury key to authorize the transfer
		signTransferTx := tokenTransferTx.Sign(treasuryKey)

		tokenTransferSubmit, err := signTransferTx.Execute(client)
		tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

		fmt.Println("\nNFT transfer from Treasury to Alice:", tokenTransferRx.Status, "✅")

		// Check the balance of the treasury account after the transfer
		balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		treasuryNftBalance2 := balanceCheckTreasury2.Tokens.Get(tokenId)
		fmt.Println("- Treasury balance:", treasuryNftBalance2, "NFTs of ID ", tokenId)

		// Check the balance of Alice's account after the transfer
		balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
		if err != nil {
			panic(err)
		}
		aliceNftBalance2 := balanceCheckAlice2.Tokens.Get(tokenId)
		fmt.Println("- Alice's balance:", aliceNftBalance2, "NFTs of ID ", tokenId, "\n")
	}
}

```

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
