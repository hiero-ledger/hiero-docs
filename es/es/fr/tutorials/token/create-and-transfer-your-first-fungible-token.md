# Create and Transfer Your First Fungible Token

## Summary

Fungible tokens share a single set of properties and have interchangeable value with one another. Use cases for fungible tokens include applications like stablecoins, in-game rewards systems, crypto tokens, loyalty program points, and much more.

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

- Get a [Hedera testnet account](../../getting-started/introduction.md).
- Set up your environment [here](../../getting-started/environment-set-up.md).

***

## 1. Create a Fungible Token

Use _<mark style="color:purple;">**`TokenCreateTransaction()`**</mark>_ to create a fungible token and configure its properties. At a minimum, this constructor requires setting a name, symbol, and treasury account ID. All other fields are optional, so if they’re not specified then default values are used. For instance, not specifying an _admin key_, makes a token immutable (can’t change or add properties); not specifying a _supply key_, makes a token supply fixed (can’t mint new or burn existing tokens); not specifying a _token type_, makes a token fungible.

After submitting the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt.

Unlike NFTs, fungible tokens do not require the decimals and initial supply to be set to zero during creation. In this case, we set the initial supply to 100 units, which is shown in the code as 10000 to account to 2 decimals.

{% tabs %}
{% tab title="Java" %}

```java
// CREATE FUNGIBLE TOKEN (STABLECOIN)
TokenCreateTransaction tokenCreateTx = new TokenCreateTransaction()
        .setTokenName("USD Bar")
        .setTokenSymbol("USDB")
        .setTokenType(TokenType.FUNGIBLE_COMMON)
        .setDecimals(2)
        .setInitialSupply(10000)
        .setTreasuryAccountId(treasuryId)
        .setSupplyType(TokenSupplyType.INFINITE)
        .setSupplyKey(supplyKey)
        .freezeWith(client);

//SIGN WITH TREASURY KEY
TokenCreateTransaction tokenCreateSign = tokenCreateTx.sign(treasuryKey);

//SUBMIT THE TRANSACTION
TransactionResponse tokenCreateSubmit = tokenCreateSign.execute(client);

//GET THE TRANSACTION RECEIPT
TransactionReceipt tokenCreateRx = tokenCreateSubmit.getReceipt(client);

//GET THE TOKEN ID
TokenId tokenId = tokenCreateRx.tokenId;

//LOG THE TOKEN ID TO THE CONSOLE
System.out.println("Created token with ID: " +tokenId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// CREATE FUNGIBLE TOKEN (STABLECOIN)
let tokenCreateTx = await new TokenCreateTransaction()
	.setTokenName("USD Bar")
	.setTokenSymbol("USDB")
	.setTokenType(TokenType.FungibleCommon)
	.setDecimals(2)
	.setInitialSupply(10000)
	.setTreasuryAccountId(treasuryId)
	.setSupplyType(TokenSupplyType.Infinite)
	.setSupplyKey(supplyKey)
	.freezeWith(client);

//SIGN WITH TREASURY KEY
let tokenCreateSign = await tokenCreateTx.sign(treasuryKey);

//SUBMIT THE TRANSACTION
let tokenCreateSubmit = await tokenCreateSign.execute(client);

//GET THE TRANSACTION RECEIPT
let tokenCreateRx = await tokenCreateSubmit.getReceipt(client);

//GET THE TOKEN ID
let tokenId = tokenCreateRx.tokenId;

//LOG THE TOKEN ID TO THE CONSOLE
console.log(`- Created token with ID: ${tokenId} \n`);
```

{% endtab %}

{% tab title="Go" %}

```go
//CREATE FUNGIBLE TOKEN (STABLECOIN)
tokenCreateTx, err := hedera.NewTokenCreateTransaction().
	SetTokenName("USD Bar").
	SetTokenSymbol("USDB").
	SetTokenType(hedera.TokenTypeFungibleCommon).
	SetDecimals(2).
	SetInitialSupply(10000).
	SetTreasuryAccountID(treasuryAccountId).
	SetSupplyType(hedera.TokenSupplyTypeInfinite).
	SetSupplyKey(supplyKey).
	FreezeWith(client)

//SIGN WITH TREASURY KEY
tokenCreateSign := tokenCreateTx.Sign(treasuryKey)

//SUBMIT THE TRANSACTION
tokenCreateSubmit, err := tokenCreateSign.Execute(client)

//GET THE TRANSACTION RECEIPT
tokenCreateRx, err := tokenCreateSubmit.GetReceipt(client)

//GET THE TOKEN ID
tokenId := *tokenCreateRx.TokenID

//LOG THE TOKEN ID TO THE CONSOLE
fmt.Println("Created fungible token with token ID", tokenId)
```

{% endtab %}
{% endtabs %}

***

## 2. Associate User Accounts with Token

Before an account that is not the treasury for a token can receive or send this specific token ID, the account must become “associated” with the token.

{% tabs %}
{% tab title="Java" %}

```java
// TOKEN ASSOCIATION WITH ALICE's ACCOUNT
TokenAssociateTransaction associateAliceTx = new TokenAssociateTransaction()
        .setAccountId(aliceAccountId)
        .setTokenIds(Collections.singletonList(tokenId))
	.freezeWith(client)
        .sign(aliceKey);
        
//SUBMIT THE TRANSACTION
TransactionResponse associateAliceTxSubmit = associateAliceTx.execute(client);
        
//GET THE RECEIPT OF THE TRANSACTION
TransactionReceipt associateAliceRx = associateAliceTxSubmit.getReceipt(client);
        
//LOG THE TRANSACTION STATUS
System.out.println("Token association with Alice's account: " +associateAliceRx.status);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// TOKEN ASSOCIATION WITH ALICE's ACCOUNT
let associateAliceTx = await new TokenAssociateTransaction()
	.setAccountId(aliceId)
	.setTokenIds([tokenId])
	.freezeWith(client)
	.sign(aliceKey);

//SUBMIT THE TRANSACTION
let associateAliceTxSubmit = await associateAliceTx.execute(client);

//GET THE RECEIPT OF THE TRANSACTION
let associateAliceRx = await associateAliceTxSubmit.getReceipt(client);

//LOG THE TRANSACTION STATUS
console.log(`- Token association with Alice's account: ${associateAliceRx.status} \n`);
```

{% endtab %}

{% tab title="Go" %}

```go
//TOKEN ASSOCIATION WITH ALICE's ACCOUNT
associateAliceTx, err := hedera.NewTokenAssociateTransaction().
	SetAccountID(aliceAccountId).
	SetTokenIDs(tokenId).
	FreezeWith(client)

//SIGN WITH ALICE'S KEY TO AUTHORIZE THE ASSOCIATION
signTx := associateAliceTx.Sign(aliceKey)

//SUBMIT THE TRANSACTION
associateAliceTxSubmit, err := signTx.Execute(client)

//GET THE RECEIPT OF THE TRANSACTION
associateAliceRx, err := associateAliceTxSubmit.GetReceipt(client)

//LOG THE TRANSACTION STATUS
fmt.Println("Non-fungible token association with Alice's account:", associateAliceRx.Status)
```

{% endtab %}
{% endtabs %}

***

## 3. Transfer the Token

Now, transfer 25 units of the token from the Treasury to Alice and check the account balances before and after the transfer.

{% tabs %}
{% tab title="Java" %}

```java
//BALANCE CHECK
AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println(" Treasury balance: " +balanceCheckTreasury.tokens + " units of token ID" +tokenId);
AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
System.out.println("Alice's balance: " + balanceCheckAlice.tokens + " units of token ID " + tokenId);

//TRANSFER STABLECOIN FROM TREASURY TO ALICE
TransferTransaction tokenTransferTx = new TransferTransaction()
        .addTokenTransfer(tokenId, treasuryId, -5)
        .addTokenTransfer(tokenId, aliceAccountId, 5)
        .freezeWith(client)
        .sign(treasuryKey);
        
//SUBMIT THE TRANSACTION
TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);
        
//GET THE RECEIPT OF THE TRANSACTION
TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);
        
//LOG THE TRANSACTION STATUS
System.out.println("Stablecoin transfer from Treasury to Alice: " + tokenTransferRx.status );

//BALANCE CHECK
AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
System.out.println("Treasury balance " + balanceCheckTreasury2.tokens +  " units of token ID " + tokenId);
AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
System.out.println("Alice's balance: " +balanceCheckAlice2.tokens + " units of token ID " + tokenId);
        
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//BALANCE CHECK
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);

// TRANSFER STABLECOIN FROM TREASURY TO ALICE
let tokenTransferTx = await new TransferTransaction()
	.addTokenTransfer(tokenId, treasuryId, -5)
	.addTokenTransfer(tokenId, aliceId, 5)
	.freezeWith(client)
	.sign(treasuryKey);

//SUBMIT THE TRANSACTION
let tokenTransferSubmit = await tokenTransferTx.execute(client);

//GET THE RECEIPT OF THE TRANSACTION
let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);

//LOG THE TRANSACTION STATUS
console.log(`\n- Stablecoin transfer from Treasury to Alice: ${tokenTransferRx.status} \n`);

// BALANCE CHECK
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
```

{% endtab %}

{% tab title="Go" %}

```go
//CHECK THE BALANCE BEFORE THE TRANSFER FROM THE TREASURY ACCOUNT
balanceCheckTreasury, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
fmt.Println("Treasury balance:", balanceCheckTreasury.Tokens, "units of token ID", tokenId)

//CHECK THE BALANCE BEFORE THE TRANSFER FOR ALICE'S ACCOUNT
balanceCheckAlice, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
fmt.Println("Alice's balance:", balanceCheckAlice.Tokens, "units of token ID", tokenId)

//TRANSFER THE STABLECOIN TO ALICE
tokenTransferTx, err := hedera.NewTransferTransaction().
	AddTokenTransfer(tokenId, treasuryAccountId, -5).
	AddTokenTransfer(tokenId, aliceAccountId, 5).
	FreezeWith(client)

//SIGN WITH THE TREASURY KEY TO AUTHORIZE THE TRANSFER
signTransferTx := tokenTransferTx.Sign(treasuryKey)

//SUBMIT THE TRANSACTION
tokenTransferSubmit, err := signTransferTx.Execute(client)

//GET THE TRANSACTION RECEIPT
tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

fmt.Println("Token transfer from Treasury to Alice:", tokenTransferRx.Status)

//CHECK THE BALANCE AFTER THE TRANSFER FOR THE TREASURY ACCOUNT
balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
fmt.Println("Treasury balance:", balanceCheckTreasury2.Tokens, "units of token", tokenId)

//CHECK THE BALANCE AFTER THE TRANSFER FOR ALICE'S ACCOUNT
balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
gofmt.Println("Alice's balance:", balanceCheckAlice2.Tokens, "units of token", tokenId)
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

import java.util.Collections;
import java.util.Objects;
import java.util.concurrent.TimeoutException;

public class CreateFungibleTutorial {
    public static void main(String[] args) throws TimeoutException, PrecheckStatusException, ReceiptStatusException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Objects.requireNonNull(Dotenv.load().get("MY_ACCOUNT_ID")));
        PrivateKey myPrivateKey = PrivateKey.fromString(Objects.requireNonNull(Dotenv.load().get("MY_PRIVATE_KEY")));

        //Create your Hedera testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

        //Treasury Key
        PrivateKey treasuryKey = PrivateKey.generate();
        PublicKey treasuryPublicKey = treasuryKey.getPublicKey();

        //Create treasury account
        TransactionResponse treasuryAccount = new AccountCreateTransaction()
                .setKey(treasuryPublicKey)
                .setInitialBalance(new Hbar(5))
                .execute(client);

        AccountId treasuryId = treasuryAccount.getReceipt(client).accountId;

        //Alice Key
        PrivateKey aliceKey = PrivateKey.generate();
        PublicKey alicePublicKey = aliceKey.getPublicKey();

        //Create Alice's account
        TransactionResponse aliceAccount = new AccountCreateTransaction()
                .setKey(alicePublicKey)
                .setInitialBalance(new Hbar(5))
                .execute(client);

        AccountId aliceAccountId = aliceAccount.getReceipt(client).accountId;

        //Alice Key
        PrivateKey supplyKey = PrivateKey.generate();
        PublicKey supplyPublicKey = supplyKey.getPublicKey();

        // CREATE FUNGIBLE TOKEN (STABLECOIN)
        TokenCreateTransaction tokenCreateTx = new TokenCreateTransaction()
                .setTokenName("USD Bar")
                .setTokenSymbol("USDB")
                .setTokenType(TokenType.FUNGIBLE_COMMON)
                .setDecimals(2)
                .setInitialSupply(10000)
                .setTreasuryAccountId(treasuryId)
                .setSupplyType(TokenSupplyType.INFINITE)
                .setSupplyKey(supplyKey)
                .freezeWith(client);

        //Sign with the treasury key
        TokenCreateTransaction tokenCreateSign = tokenCreateTx.sign(treasuryKey);

        //Submit the transaction
        TransactionResponse tokenCreateSubmit = tokenCreateSign.execute(client);

        //Get the transaction receipt
        TransactionReceipt tokenCreateRx = tokenCreateSubmit.getReceipt(client);

        //Get the token ID
        TokenId tokenId = tokenCreateRx.tokenId;

        //Log the token ID to the console
        System.out.println("Created token with ID: " +tokenId);

        // TOKEN ASSOCIATION WITH ALICE's ACCOUNT
        TokenAssociateTransaction associateAliceTx = new TokenAssociateTransaction()
                .setAccountId(aliceAccountId)
                .setTokenIds(Collections.singletonList(tokenId))
	        .freezeWith(client)
                .sign(aliceKey);

        //Submit the transaction
        TransactionResponse associateAliceTxSubmit = associateAliceTx.execute(client);

        //Get the receipt of the transaction
        TransactionReceipt associateAliceRx = associateAliceTxSubmit.getReceipt(client);

        //Get the transaction status
        System.out.println("Token association with Alice's account: " +associateAliceRx.status);

        // BALANCE CHECK
        AccountBalance balanceCheckTreasury = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println(" Treasury balance: " +balanceCheckTreasury.tokens + " units of token ID" +tokenId);
        AccountBalance balanceCheckAlice = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println("Alice's balance: " + balanceCheckAlice.tokens + " units of token ID " + tokenId);

        // TRANSFER STABLECOIN FROM TREASURY TO ALICE
        TransferTransaction tokenTransferTx = new TransferTransaction()
                .addTokenTransfer(tokenId, treasuryId, -5)
                .addTokenTransfer(tokenId, aliceAccountId, 5)
                .freezeWith(client)
                .sign(treasuryKey);

        //SUBMIT THE TRANSACTION
        TransactionResponse tokenTransferSubmit = tokenTransferTx.execute(client);

        //GET THE RECEIPT OF THE TRANSACTION
        TransactionReceipt tokenTransferRx = tokenTransferSubmit.getReceipt(client);

        //LOG THE TRANSACTION STATUS
        System.out.println("Stablecoin transfer from Treasury to Alice: " + tokenTransferRx.status );

        // BALANCE CHECK
        AccountBalance balanceCheckTreasury2 = new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
        System.out.println("Treasury balance " + balanceCheckTreasury2.tokens +  " units of token ID " + tokenId);
        AccountBalance balanceCheckAlice2 = new AccountBalanceQuery().setAccountId(aliceAccountId).execute(client);
        System.out.println("Alice's balance: " +balanceCheckAlice2.tokens + " units of token ID " + tokenId);

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
	AccountId,
	PrivateKey,
	Client,
	TokenCreateTransaction,
	TokenType,
	TokenSupplyType,
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

async function createFungibleToken() {
	//CREATE FUNGIBLE TOKEN (STABLECOIN)
	let tokenCreateTx = await new TokenCreateTransaction()
		.setTokenName("USD Bar")
		.setTokenSymbol("USDB")
		.setTokenType(TokenType.FungibleCommon)
		.setDecimals(2)
		.setInitialSupply(10000)
		.setTreasuryAccountId(treasuryId)
		.setSupplyType(TokenSupplyType.Infinite)
		.setSupplyKey(supplyKey)
		.freezeWith(client);

	let tokenCreateSign = await tokenCreateTx.sign(treasuryKey);
	let tokenCreateSubmit = await tokenCreateSign.execute(client);
	let tokenCreateRx = await tokenCreateSubmit.getReceipt(client);
	let tokenId = tokenCreateRx.tokenId;
	console.log(`- Created token with ID: ${tokenId} \n`);

	//TOKEN ASSOCIATION WITH ALICE's ACCOUNT
	let associateAliceTx = await new TokenAssociateTransaction()
		.setAccountId(aliceId)
		.setTokenIds([tokenId])
		.freezeWith(client)
		.sign(aliceKey);
	let associateAliceTxSubmit = await associateAliceTx.execute(client);
	let associateAliceRx = await associateAliceTxSubmit.getReceipt(client);
	console.log(`- Token association with Alice's account: ${associateAliceRx.status} \n`);

	//BALANCE CHECK
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
	console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
	console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);

	//TRANSFER STABLECOIN FROM TREASURY TO ALICE
	let tokenTransferTx = await new TransferTransaction()
		.addTokenTransfer(tokenId, treasuryId, -5)
		.addTokenTransfer(tokenId, aliceId, 5)
		.freezeWith(client)
		.sign(treasuryKey);
	let tokenTransferSubmit = await tokenTransferTx.execute(client);
	let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);
	console.log(`\n- Stablecoin transfer from Treasury to Alice: ${tokenTransferRx.status} \n`);

	//BALANCE CHECK
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(treasuryId).execute(client);
	console.log(`- Treasury balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
	var balanceCheckTx = await new AccountBalanceQuery().setAccountId(aliceId).execute(client);
	console.log(`- Alice's balance: ${balanceCheckTx.tokens._map.get(tokenId.toString())} units of token ID ${tokenId}`);
}
createFungibleToken();
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

	//LOADS THE .ENV FILE AND THROWS AN EROOR IF IT CANNOT LOAD THE VARIABLES
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
	}

	//GRAB YOUR TESTNET ACCOUNT ID AND KEY FROMZ THE .ENV FILE
	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	//PRINT ACCOUNT ID AND KEY TO MAKE SURE THERE WASN'T AN ERROR READING FROM THE .ENV FILE
	fmt.Printf("The account ID is = %v\n", myAccountId)
	fmt.Printf("The private key is = %v\n", myPrivateKey)

	//CREATE TESTNET CLIENT
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	//CREATE TREASURY KEY
	treasuryKey, err := hedera.GeneratePrivateKey()
	treasuryPublicKey := treasuryKey.PublicKey()

	//CREATE TREASURY ACCOUNT
	treasuryAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(treasuryPublicKey).
		SetInitialBalance(hedera.NewHbar(5)).
		Execute(client)
	
	//GET THE RECEIPT OF THE TRANSACTION
	receipt, err := treasuryAccount.GetReceipt(client)

	//GET THE ACCOUNT ID
	treasuryAccountId := *receipt.AccountID

	//ALICE'S KEY
	aliceKey, err := hedera.GeneratePrivateKey()
	alicePublicKey := aliceKey.PublicKey()

	//CREATE AILICE'S ACCOUNT
	aliceAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(alicePublicKey).
		SetInitialBalance(hedera.NewHbar(5)).
		Execute(client)

	//GET THE RECEIPT OF THE TRANSACTION
	receipt2, err := aliceAccount.GetReceipt(client)

	//GET ALICE'S ACCOUNT ID
	aliceAccountId := *receipt2.AccountID

	//CREATE SUPPLY KEY
	supplyKey, err := hedera.GeneratePrivateKey()

	//CREATE FUNGIBLE TOKEN (STABLECOIN)
	tokenCreateTx, err := hedera.NewTokenCreateTransaction().
		SetTokenName("USD Bar").
		SetTokenSymbol("USDB").
		SetTokenType(hedera.TokenTypeFungibleCommon).
		SetDecimals(2).
		SetInitialSupply(10000).
		SetTreasuryAccountID(treasuryAccountId).
		SetSupplyType(hedera.TokenSupplyTypeInfinite).
		SetSupplyKey(supplyKey).
		FreezeWith(client)

	//SIGN WITH TREASURY KEY
	tokenCreateSign := tokenCreateTx.Sign(treasuryKey)

	//SUBMIT THE TRANSACTION
	tokenCreateSubmit, err := tokenCreateSign.Execute(client)

	//GET THE TRANSACTION RECEIPT
	tokenCreateRx, err := tokenCreateSubmit.GetReceipt(client)

	//GET THE TOKEN ID
	tokenId := *tokenCreateRx.TokenID

	//LOG THE TOKEN ID TO THE CONSOLE
	fmt.Println("Created fungible token with token ID", tokenId)

	//TOKEN ASSOCIATION WITH ALICE's ACCOUNT
	associateAliceTx, err := hedera.NewTokenAssociateTransaction().
		SetAccountID(aliceAccountId).
		SetTokenIDs(tokenId).
		FreezeWith(client)

	//SIGN WITH ALICE'S KEY TO AUTHORIZE THE ASSOCIATION
	signTx := associateAliceTx.Sign(aliceKey)

	//SUBMIT THE TRANSACTION
	associateAliceTxSubmit, err := signTx.Execute(client)

	//GET THE RECEIPT OF THE TRANSACTION
	associateAliceRx, err := associateAliceTxSubmit.GetReceipt(client)

	//LOG THE TRANSACTION STATUS
	fmt.Println("STABLECOIN token association with Alice's account:", associateAliceRx.Status)

	//Check the balance before the transfer for the treasury account
	balanceCheckTreasury, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
	fmt.Println("Treasury balance:", balanceCheckTreasury.Tokens, "units of token ID", tokenId)

	//Check the balance before the transfer for Alice's account
	balanceCheckAlice, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
	fmt.Println("Alice's balance:", balanceCheckAlice.Tokens, "units of token ID", tokenId)

	//Transfer the STABLECOIN from treasury to Alice
	tokenTransferTx, err := hedera.NewTransferTransaction().
		AddTokenTransfer(tokenId, treasuryAccountId, -5).
		AddTokenTransfer(tokenId, aliceAccountId, 5).
		FreezeWith(client)

	//SIGN WITH THE TREASURY KEY TO AUTHORIZE THE TRANSFER
	signTransferTx := tokenTransferTx.Sign(treasuryKey)

	//SUBMIT THE TRANSACTION
	tokenTransferSubmit, err := signTransferTx.Execute(client)

	//GET THE TRANSACTION RECEIPT
	tokenTransferRx, err := tokenTransferSubmit.GetReceipt(client)

	fmt.Println("Token transfer from Treasury to Alice:", tokenTransferRx.Status)

	//CHECK THE BALANCE AFTER THE TRANSFER FOR THE TREASURY ACCOUNT
	balanceCheckTreasury2, err := hedera.NewAccountBalanceQuery().SetAccountID(treasuryAccountId).Execute(client)
	fmt.Println("Treasury balance:", balanceCheckTreasury2.Tokens, "units of token", tokenId)

	//CHECK THE BALANCE AFTER THE TRANSFER FOR ALICE'S ACCOUNT
	balanceCheckAlice2, err := hedera.NewAccountBalanceQuery().SetAccountID(aliceAccountId).Execute(client)
	fmt.Println("Alice's balance:", balanceCheckAlice2.Tokens, "units of token", tokenId)

}
```

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
