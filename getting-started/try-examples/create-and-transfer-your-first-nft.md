# Create and Transfer Your First NFT

![](<../../.gitbook/assets/Screen Shot 2021-12-01 at 2.22.52 PM.png>)

## Summary

Using the Hedera Token Service you can create non-fungible tokens (NFTs). NFTs are uniquely identifiable. On the Hedera network, the token ID represents a collection of NFTs of the same class, and the serial number of each token uniquely identifies each NFT in the class.

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

{% content-ref url="../environment-set-up.md" %}
[environment-set-up.md](../environment-set-up.md)
{% endcontent-ref %}

## 1. Create a Non-Fungible Token (NFT)

Use _<mark style="color:purple;">**`TokenCreateTransaction()`**</mark>_ to configure and set the token properties. At a minimum, this constructor requires setting a name, symbol, and treasury account ID. All other fields are optional, so if they’re not specified then default values are used. For instance, not specifying an _admin key_, makes a token immutable (can’t change or add properties); not specifying a _supply key_, makes a token supply fixed (can’t mint new or burn existing tokens); not specifying a _token type_, makes a token fungible.

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
System.out.println("Created NFT with token ID " +tokenId);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the NFT
let nftCreate = await new TokenCreateTransaction()
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
let nftCreateTxSign = await nftCreate.sign(treasuryKey);

//Submit the transaction to a Hedera network
let nftCreateSubmit = await nftCreateTxSign.execute(client);

//Get the transaction receipt
let nftCreateRx = await nftCreateSubmit.getReceipt(client);

//Get the token ID
let tokenId = nftCreateRx.tokenId;

//Log the token ID
console.log(`- Created NFT with Token ID: ${tokenId} \n`);
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

## 2. Mint a New NFT

When creating an NFT, the decimals and initial supply must be set to zero. After the token is created, you mint each NFT using the token mint operation. Specifying a _supply key_ during token creation is a requirement to be able to [mint](https://docs.hedera.com/guides/docs/sdks/tokens/mint-a-token) and [burn](https://docs.hedera.com/guides/docs/sdks/tokens/burn-a-token) tokens. The supply key is required to sign mint and burn transactions.

Both the NFT image and metadata live in the InterPlanetary File System (IPFS), which provides decentralized storage. The file diploma\_metadata.json contains the metadata for the NFT. A content identifier (CID) pointing to the metadata file is used during minting of the new NFT. Notice that the metadata file contains a URI pointing to the NFT image.

{% tabs %}
{% tab title="Java" %}
```java
// IPFS content identifiers for which we will create a NFT
String CID = ("QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa") ;

// Mint a new NFT
TokenMintTransaction mintTx = new TokenMintTransaction()
        .setTokenId(tokenId)
        .addMetadata(CID.getBytes())
	.freezeWith(client);

//Sign transaction with the supply key
TokenMintTransaction mintTxSign = mintTx.sign(supplyKey);

//Submit the transaction to a Hedera network
TransactionResponse mintTxSubmit = mintTxSign.execute(client);

//Get the transaction receipt
TransactionReceipt mintRx = mintTxSubmit.getReceipt(client);

//Log the serial number
System.out.println("Created NFT " +tokenId + "with serial: " +mintRx.serials);
   
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//IPFS content identifiers for which we will create a NFT
CID = "ipfs://QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa";

// Mint new NFT
let mintTx = await new TokenMintTransaction()
	.setTokenId(tokenId)
	.setMetadata([Buffer.from(CID)])
	.freezeWith(client);

//Sign the transaction with the supply key
let mintTxSign = await mintTx.sign(supplyKey);

//Submit the transaction to a Hedera network
let mintTxSubmit = await mintTxSign.execute(client);

//Get the transaction receipt
let mintRx = await mintTxSubmit.getReceipt(client);

//Log the serial number
console.log(`- Created NFT ${tokenId} with serial: ${mintRx.serials[0].low} \n`);
```
{% endtab %}

{% tab title="Go" %}
```go
//IPFS content identifiers for which we will create a NFT
CID := "QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa"

//Mint new NFT
mintTx, err := hedera.NewTokenMintTransaction().
	SetTokenID(tokenId).
	SetMetadata([]byte(CID)).
	FreezeWith(client)

//Sign the transaction with the supply key
mintTxSign := mintTx.Sign(supplyKey)

//Submit the transaction to a Hedera network
mintTxSubmit, err := mintTxSign.Execute(client)

//Get the transaction receipt
mintRx, err := mintTxSubmit.GetReceipt(client)

//Log the serial number
fmt.Print("Created NFT ", tokenId, " with serial: ", mintRx.SerialNumbers)o
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

## 3. Associate User Accounts with the NFT

Before an account that is not the treasury for a token can receive or send this specific token ID, the account must become “associated” with the token. To associate a token to an account the account owner must sign the associate transaction.

If you have an account with the automatic token association property set you do not need to associate the token before transferring it the receiving account.

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
System.out.println("NFT association with Alice's account: " +associateAliceRx.status);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the associate transaction and sign with Alice's key 
let associateAliceTx = await new TokenAssociateTransaction()
	.setAccountId(aliceId)
	.setTokenIds([tokenId])
	.freezeWith(client)
	.sign(aliceKey);

//Submit the transaction to a Hedera network
let associateAliceTxSubmit = await associateAliceTx.execute(client);

//Get the transaction receipt
let associateAliceRx = await associateAliceTxSubmit.getReceipt(client);

//Confirm the transaction was successful
console.log(`- NFT association with Alice's account: ${associateAliceRx.status}\n`);
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

## 4. Transfer the NFT

Now, transfer the NFT and check the account balances before and after the send! After the transfer you should expect to the NFT to be removed from the treasury account and available in Alice's account. The treasury account key has to sign the transfer transaction to authorize the transfer to Alice's account.

{% tabs %}
{% tab title="Java" %}
```java
// Check the balance before the transfer for the treasury account
AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println("Treasury balance: " +balanceCheckTreasury.tokens + "NFTs of ID " +tokenId);

// Check the balance before the transfer for Alice's account
AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
System.out.println("Alice's balance: " +balanceCheckAlice.tokens + "NFTs of ID " +tokenId);

// Transfer the NFT from treasury to Alice
// Sign with the treasury key to authorize the transfer
TransferTransaction tokenTransferTx = new TransferTransaction()
        .addNftTransfer( new NftId(tokenId, 1), treasuryId, aliceAccountId)
        .freezeWith(client)
        .sign(treasuryKey);

TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);
TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);

System.out.println("NFT transfer from Treasury to Alice: " +tokenTransferRx.status);

// Check the balance of the treasury account after the transfer
AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println("Treasury balance: " +balanceCheckTreasury2.tokens + "NFTs of ID " + tokenId);

// Check the balance of Alice's account after the transfer
AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
System.out.println("Alice's balance: " +balanceCheckAlice2.tokens +  "NFTs of ID " +tokenId);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Check the balance before the transfer for the treasury account
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Check the balance before the transfer for Alice's account
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Transfer the NFT from treasury to Alice
// Sign with the treasury key to authorize the transfer
let tokenTransferTx = await new TransferTransaction()
	.addNftTransfer(tokenId, 1, treasuryId, aliceId)
	.freezeWith(client)
	.sign(treasuryKey);

let tokenTransferSubmit = await tokenTransferTx.execute(client);
let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);

console.log(`\n- NFT transfer from Treasury to Alice: ${tokenTransferRx.status} \n`);

// Check the balance of the treasury account after the transfer
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

// Check the balance of Alice's account after the transfer
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);
```
{% endtab %}

{% tab title="Go" %}
```go
//Transfer the NFT from treasury to Alice
tokenTransferTx, err := hedera.NewTransferTransaction().
	AddNftTransfer(hedera.NftID{TokenID: tokenId, SerialNumber: 1}, treasuryAccountId, aliceAccountId).
	FreezeWith(client)

//Sign with the treasury key to authorize the transfer
signTransferTx := tokenTransferTx.Sign(treasuryKey)

//Submit the transaction
tokenTransferSubmit, err := signTransferTx.Execute(client)

//Get the transaction receipt
tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

//Log the transaction status
fmt.Println("NFT transfer from Treasury to Alice:", tokenTransferRx.Status)

// Check the balance of the treasury account after the transfer
balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
fmt.Println("Treasury balance:", balanceCheckTreasury2.Tokens, "NFTs of ID", tokenId)

// Check the balance of Alice's account after the transfer
balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
fmt.Println("Alice's balance:", balanceCheckAlice2.Tokens, "NFTs of ID", tokenId)
```
{% endtab %}
{% endtabs %}

## Code Check ✅

{% tabs %}
{% tab title="Java" %}
```java
import com.hedera.hashgraph.sdk.*;
import io.github.cdimascio.dotenv.Dotenv;

import java.util.Collections;
import java.util.List;
import java.util.Objects;
import java.util.concurrent.TimeoutException;

public class CreateNFTTutorial {

    public static void main(String[] args) throws TimeoutException, PrecheckStatusException, ReceiptStatusException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Objects.requireNonNull(Dotenv.load().get("MY_ACCOUNT_ID")));
        PrivateKey myPrivateKey = PrivateKey.fromString(Objects.requireNonNull(Dotenv.load().get("MY_PRIVATE_KEY")));

        //Create your Hedera testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

        //Treasury Key
        PrivateKey treasuryKey = PrivateKey.generateED25519();
        PublicKey treasuryPublicKey = treasuryKey.getPublicKey();

        //Create treasury account
        TransactionResponse treasuryAccount = new AccountCreateTransaction()
                .setKey(treasuryPublicKey)
                .setInitialBalance(new Hbar(10))
                .execute(client);

        AccountId treasuryId = treasuryAccount.getReceipt(client).accountId;

        //Alice Key
        PrivateKey aliceKey = PrivateKey.generateED25519();
        PublicKey alicePublicKey = aliceKey.getPublicKey();

        //Create Alice's account
        TransactionResponse aliceAccount = new AccountCreateTransaction()
                .setKey(alicePublicKey)
                .setInitialBalance(new Hbar(10))
                .execute(client);

        AccountId aliceAccountId = aliceAccount.getReceipt(client).accountId;

        //Supply Key
        PrivateKey supplyKey = PrivateKey.generateED25519();
        PublicKey supplyPublicKey = supplyKey.getPublicKey();

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
        System.out.println("Created NFT with token ID " +tokenId);

        // IPFS CONTENT IDENTIFIERS FOR WHICH WE WILL CREATE NFT
        String CID = ("QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa") ;

        // MINT NEW NFT
        TokenMintTransaction mintTx = new TokenMintTransaction()
                .setTokenId(tokenId)
                .addMetadata(CID.getBytes())
	        .freezeWith(client);

        //Sign with the supply key
        TokenMintTransaction mintTxSign = mintTx.sign(supplyKey);

        //Submit the transaction to a Hedera network
        TransactionResponse mintTxSubmit = mintTxSign.execute(client);

        //Get the transaction receipt
        TransactionReceipt mintRx = mintTxSubmit.getReceipt(client);

        //Log the serial number
        System.out.println("Created NFT " +tokenId + "with serial: " +mintRx.serials);

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
        System.out.println("NFT association with Alice's account: " +associateAliceRx.status);

        // Check the balance before the NFT transfer for the treasury account
        AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println("Treasury balance: " +balanceCheckTreasury.tokens + "NFTs of ID " +tokenId);

        // Check the balance before the NFT transfer for Alice's account
        AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println("Alice's balance: " +balanceCheckAlice.tokens + "NFTs of ID " +tokenId);

        // Transfer NFT from treasury to Alice
        // Sign with the treasury key to authorize the transfer
        TransferTransaction tokenTransferTx = new TransferTransaction()
                .addNftTransfer( new NftId(tokenId, 1), treasuryId, aliceAccountId)
                .freezeWith(client)
                .sign(treasuryKey);

        TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);
        TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);

        System.out.println("NFT transfer from Treasury to Alice: " +tokenTransferRx.status);

        // Check the balance for the treasury account after the transfer
        AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println("Treasury balance: " +balanceCheckTreasury2.tokens + "NFTs of ID " + tokenId);

        // Check the balance for Alice's account after the transfer
        AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println("Alice's balance: " +balanceCheckAlice2.tokens +  "NFTs of ID " +tokenId);

    }
}
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
console.clear();
require("dotenv").config();
const {
	AccountId,
	PrivateKey,
	Client,
	TokenCreateTransaction,
	TokenType,
	TokenSupplyType,
	TokenMintTransaction,
	TransferTransaction,
	AccountBalanceQuery,
	TokenAssociateTransaction,
} = require("@hashgraph/sdk");

// Configure accounts and client, and generate needed keys
const operatorId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorKey = PrivateKey.fromString(process.env.OPERATOR_PVKEY);
const treasuryId = AccountId.fromString(process.env.TREASURY_ID);
const treasuryKey = PrivateKey.fromString(process.env.TREASURY_PVKEY);
const aliceId = AccountId.fromString(process.env.ALICE_ID);
const aliceKey = PrivateKey.fromString(process.env.ALICE_PVKEY);

const client = Client.forTestnet().setOperator(operatorId, operatorKey);

const supplyKey = PrivateKey.generate();

async function main() {
	//Create the NFT
	let nftCreate = await new TokenCreateTransaction()
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
	let nftCreateTxSign = await nftCreate.sign(treasuryKey);

	//Submit the transaction to a Hedera network
	let nftCreateSubmit = await nftCreateTxSign.execute(client);

	//Get the transaction receipt
	let nftCreateRx = await nftCreateSubmit.getReceipt(client);

	//Get the token ID
	let tokenId = nftCreateRx.tokenId;

	//Log the token ID
	console.log(`- Created NFT with Token ID: ${tokenId} \n`);

	//IPFS content identifiers for which we will create a NFT
	CID = ["QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa"];

	// Mint new NFT
	let mintTx = await new TokenMintTransaction()
		.setTokenId(tokenId)
		.setMetadata([Buffer.from(CID)])
		.freezeWith(client);

	//Sign the transaction with the supply key
	let mintTxSign = await mintTx.sign(supplyKey);

	//Submit the transaction to a Hedera network
	let mintTxSubmit = await mintTxSign.execute(client);

	//Get the transaction receipt
	let mintRx = await mintTxSubmit.getReceipt(client);

	//Log the serial number
	console.log(`- Created NFT ${tokenId} with serial: ${mintRx.serials[0].low} \n`);
	
	//Create the associate transaction and sign with Alice's key 
	let associateAliceTx = await new TokenAssociateTransaction()
		.setAccountId(aliceId)
		.setTokenIds([tokenId])
		.freezeWith(client)
		.sign(aliceKey);

	//Submit the transaction to a Hedera network
	let associateAliceTxSubmit = await associateAliceTx.execute(client);

	//Get the transaction receipt
	let associateAliceRx = await associateAliceTxSubmit.getReceipt(client);

	//Confirm the transaction was successful
	console.log(`- NFT association with Alice's account: ${associateAliceRx.status}\n`);


	// Check the balance before the transfer for the treasury account
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
	console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

	// Check the balance before the transfer for Alice's account
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
	console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

	// Transfer the NFT from treasury to Alice
	// Sign with the treasury key to authorize the transfer
	let tokenTransferTx = await new TransferTransaction()
		.addNftTransfer(tokenId, 1, treasuryId, aliceId)
		.freezeWith(client)
		.sign(treasuryKey);

	let tokenTransferSubmit = await tokenTransferTx.execute(client);
	let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);

	console.log(`\n- NFT transfer from Treasury to Alice: ${tokenTransferRx.status} \n`);

	// Check the balance of the treasury account after the transfer
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
	console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);

	// Check the balance of Alice's account after the transfer
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
	console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} NFTs of ID ${tokenId}`);
}
main();
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {

	//Loads the .env file and throws an error if it cannot load the variables from that file corectly
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load enviroment variables from .env file. Error:\n%v\n", err))
	}

	//Grab your testnet account ID and private key from the .env file
	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	//Print your testnet account ID and private key to the console to make sure there was no error
	fmt.Printf("The account ID is = %v\n", myAccountId)
	fmt.Printf("The private key is = %v\n", myPrivateKey)

	//Create your testnet client
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	//Create a treasury Key
	treasuryKey, err := hedera.GeneratePrivateKey()
	treasuryPublicKey := treasuryKey.PublicKey()

	//Create treasury account
	treasuryAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(treasuryPublicKey).
		SetInitialBalance(hedera.NewHbar(10)).
		Execute(client)
	
	//Get the receipt of the transaction
	receipt, err := treasuryAccount.GetReceipt(client)

	//Get the account ID
	treasuryAccountId := *receipt.AccountID

	//Alice Key
	aliceKey, err := hedera.GeneratePrivateKey()
	alicePublicKey := aliceKey.PublicKey()

	//Create Alice's account
	aliceAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(alicePublicKey).
		SetInitialBalance(hedera.NewHbar(10)).
		Execute(client)
	
	//Get the receipt of the transaction
	receipt2, err := aliceAccount.GetReceipt(client)

	//Get the account ID
	aliceAccountId := *receipt2.AccountID

	//Create a supply key
	supplyKey, err := hedera.GeneratePrivateKey()

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
	fmt.Println("Created NFT with token ID", tokenId)

	// IPFS content identifiers for which we will create a NFT
	CID := "QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa"

	// Minet new NFT
	mintTx, err := hedera.NewTokenMintTransaction().
		SetTokenID(tokenId).
		SetMetadata([]byte(CID)).
		FreezeWith(client)

	//Sign the transaction with the supply key
	mintTxSign := mintTx.Sign(supplyKey)

	//Submit the transaction to a Hedera network
	mintTxSubmit, err := mintTxSign.Execute(client)

	//Get the transaction receipt
	mintRx, err := mintTxSubmit.GetReceipt(client)

	//Log the serial number
	fmt.Println("Created NFT", tokenId, "with serial:", mintRx.SerialNumbers)

	//Create the associate transaction
	associateAliceTx, err := hedera.NewTokenAssociateTransaction().
		SetAccountID(aliceAccountId).
		SetTokenIDs(tokenId).
		FreezeWith(client)

	//Sign with Alice's key
	signTx := associateAliceTx.Sign(aliceKey)

	//Submit the transaction to a Hedera network
	associateAliceTxSubmit, err := signTx.Execute(client)

	//Get the transaction receipt
	associateAliceRx, err := associateAliceTxSubmit.GetReceipt(client)

	//Confirm the transaction was successful
	fmt.Println("NFT association with Alice's account:", associateAliceRx.Status)

	// Check the balance before the transfer for the treasury account
	balanceCheckTreasury, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
	fmt.Println("Treasury balance:", balanceCheckTreasury.Tokens, "NFTs of ID", tokenId)

	// Check the balance before the transfer for Alice's account
	balanceCheckAlice, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
	fmt.Println("Alice's balance:", balanceCheckAlice.Tokens, "NFTs of ID", tokenId)

	// Transfer the NFT from treasury to Alice
	tokenTransferTx, err := hedera.NewTransferTransaction().
		AddNftTransfer(hedera.NftID{TokenID: tokenId, SerialNumber: 1}, treasuryAccountId, aliceAccountId).
		FreezeWith(client)
	
	// Sign with the treasury key to authorize the transfer
	signTransferTx := tokenTransferTx.Sign(treasuryKey)

	tokenTransferSubmit, err := signTransferTx.Execute(client)
	tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

	fmt.Println("NFT transfer from Treasury to Alice:", tokenTransferRx.Status)

	// Check the balance of the treasury account after the transfer
	balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
	fmt.Println("Treasury balance:", balanceCheckTreasury2.Tokens, "NFTs of ID", tokenId)

	// Check the balance of Alice's account after the transfer
	balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
	fmt.Println("Alice's balance:", balanceCheckAlice2.Tokens, "NFTs of ID", tokenId)

}
	
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
