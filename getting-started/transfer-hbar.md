# Transfer HBAR

## Summary

In this section, you will learn how to transfer **HBAR** from your account to another on the Hedera test network.

***

## Prerequisites <a href="#pre-requisites" id="pre-requisites"></a>

* Completed the [Introduction](introduction.md) step.
* Completed the [Environment Setup](environment-set-up.md) step.
* Completed the [Created an Account](create-an-account.md) step.

{% hint style="info" %}
_**Note:** You can always check the "_[_Code Check ✅_](transfer-hbar.md#code-check) _" section at the bottom of each page to view the entire code if you run into issues. You can also post your issue to the respective SDK channel in our Discord community_ [_here_](http://hedera.com/discord) _or on the GitHub repository_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## Step 1. Create a transfer transaction

Use your new account created in the "[Create an account](create-an-account.md)" section and transfer 1,000 **tinybars** from your account to the new account. The account sending the **HBAR** needs to sign the transaction using its private keys to authorize the transfer. Since you are transferring from the account associated with the client, you do not need to explicitly sign the transaction as the operator account(account transferring the **HBAR**) signs all transactions to authorize the payment of the transaction fee.

{% tabs %}
{% tab title="Java" %}
```java
//System.out.println("The new account balance is: " +accountBalance.hbars);
//-----------------------<enter code below>--------------------------------------

//Transfer HBAR
TransactionResponse sendHbar = new TransferTransaction()
     .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Sending account
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Receiving account
     .execute(client);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//console.log("The new account balance is: " +accountBalance.hbars.toTinybars() +" tinybar.");
//-----------------------<enter code below>--------------------------------------

//Create the transfer transaction
const sendHbar = await new TransferTransaction()
     .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Sending account
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Receiving account
     .execute(client);
```
{% endtab %}

{% tab title="Go" %}
```java
//Print the balance of tinybars
//fmt.Println("The account balance for the new account is ", accountBalance.Hbars.AsTinybar())
//-----------------------<enter code below>--------------------------------------

//Transfer hbar from your testnet account to the new account
transaction := hedera.NewTransferTransaction().
        AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
        AddHbarTransfer(newAccountId,hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar))

//Submit the transaction to a Hedera network
txResponse, err := transaction.Execute(client)

if err != nil {
    panic(err)
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Note:** The net value of the transfer must equal zero (the total number of_ **HBAR** _sent by the sender must equal the total number of_ **HBAR** _received by the recipient)._
{% endhint %}

***

## Step 2. Verify the transfer transaction reached consensus

To verify the transfer transaction reached consensus by the network, you will submit a request to obtain the receipt of the transaction. The receipt status will let you know if the transaction was successful (reached consensus) or not.

{% tabs %}
{% tab title="Java" %}
```java
System.out.println("The transfer transaction was: " +sendHbar.getReceipt(client).status);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Verify the transaction reached consensus
const transactionReceipt = await sendHbar.getReceipt(client);
console.log("The transfer transaction from my account to the new account was: " + transactionReceipt.status.toString());
```
{% endtab %}

{% tab title="Go" %}
```java
//Request the receipt of the transaction
transferReceipt, err := txResponse.GetReceipt(client)

if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := transferReceipt.Status

fmt.Printf("The transaction consensus status is %v\n", transactionStatus)
```
{% endtab %}
{% endtabs %}

***

## Code Check ✅

Your complete code file should look something like this:

<details>

<summary>Java</summary>

{% code title="HederaExamples.java" %}
```java
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.Client;
import io.github.cdimascio.dotenv.Dotenv;
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.AccountBalance;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.ReceiptStatusException;
import com.hedera.hashgraph.sdk.PrecheckStatusException;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;

import java.util.concurrent.TimeoutException;

public class HederaExamples {

    public static void main(String[] args) throws TimeoutException, HederaPreCheckStatusException, HederaReceiptStatusException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        
        // Create your connection to the Hedera network
        const client = Client.forTestnet();

        //Set your account as the client's operator
        client.setOperator(myAccountId, myPrivateKey);
  
        // Set default max transaction fee & max query payment
        client.setMaxTransactionFee(new Hbar(100));
        client.setMaxQueryPayment(new Hbar(50));

        // Generate a new key pair
        PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
        PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();

        //Create new account and assign the public key
        TransactionResponse newAccount = new AccountCreateTransaction()
                .setKey(newAccountPublicKey)
                .setInitialBalance( Hbar.fromTinybars(1000))
                .execute(client);

        // Get the new account ID
        AccountId newAccountId = newAccount.getReceipt(client).accountId;
        
        System.out.println("\nNew account ID: " +newAccountId);
        
        //Check the new account's balance
        AccountBalance accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);

        //Transfer HBAR
        TransactionResponse sendHbar = new TransferTransaction()
                .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
                .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
                .execute(client);

        System.out.println("The transfer transaction was: " +sendHbar.getReceipt(client).status);

    }
}
```
{% endcode %}

</details>

<details>

<summary>JavaScript</summary>

{% code title="index.js" %}
```javascript
const {
  Hbar,
  Client,
  PrivateKey,
  AccountBalanceQuery,
  TransferTransaction,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
require("dotenv").config();

async function transferHbar() {
  // Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Environment variables myAccountId and myPrivateKey must be present"
    );
  }

  // Create your connection to the Hedera network
  const client = Client.forTestnet();

  //Set your account as the client's operator
  client.setOperator(myAccountId, myPrivateKey);

  // Set default max transaction fee & max query payment
  client.setDefaultMaxTransactionFee(new Hbar(100));
  client.setDefaultMaxQueryPayment(new Hbar(50));

  // Create new keys
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey.publicKey;

  // Create a new account with 1,000 tinybar starting balance
  const newAccountTransactionResponse = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);

  // Get the new account ID
  const getReceipt = await newAccountTransactionResponse.getReceipt(client);
  const newAccountId = getReceipt.accountId;

  console.log("\nNew account ID: " + newAccountId);

  // Verify the account balance
  const accountBalance = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .execute(client);

  console.log(
    "\nNew account balance is: " +
      accountBalance.hbars.toTinybars() +
      " tinybars."
  );

  // Create the transfer transaction
  const sendHbar = await new TransferTransaction()
    .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
    .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
    .execute(client);

  // Verify the transaction reached consensus
  const transactionReceipt = await sendHbar.getReceipt(client);
  console.log(
    "\nThe transfer transaction from my account to the new account was: " +
      transactionReceipt.status.toString()
  );
}
transferHbar();
```
{% endcode %}

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

	//Loads the .env file and throws an error if it cannot load the variables from that file correctly
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
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

	//Create your testnet client
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	// Set default max transaction fee & max query payment
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))

	//Generate new keys for the account you will create
	newAccountPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		panic(err)
	}

	newAccountPublicKey := newAccountPrivateKey.PublicKey()

	//Create new account and assign the public key
	newAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(newAccountPublicKey).
		SetInitialBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
		Execute(client)

	//Request the receipt of the transaction
	receipt, err := newAccount.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	//Get the new account ID from the receipt
	newAccountId := *receipt.AccountID

	//Print the new account ID to the console
	fmt.Printf("The new account ID is %v\n", newAccountId)

	//Create the account balance query
	query := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign with client operator private key and submit the query to a Hedera network
	accountBalance, err := query.Execute(client)
	if err != nil {
		panic(err)
	}

	//Print the balance of tinybars
	fmt.Println("The account balance for the new account is", accountBalance.Hbars.AsTinybar())

	//Transfer hbar from your testnet account to the new account
	transaction := hedera.NewTransferTransaction().
		AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
		AddHbarTransfer(newAccountId, hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar))

	//Submit the transaction to a Hedera network
	txResponse, err := transaction.Execute(client)

	if err != nil {
		panic(err)
	}

	//Request the receipt of the transaction
	transferReceipt, err := txResponse.GetReceipt(client)

	if err != nil {
		panic(err)
	}

	//Get the transaction consensus status
	transactionStatus := transferReceipt.Status

	fmt.Printf("The transaction consensus status is %v\n", transactionStatus)
}
```

</details>

#### Sample output:

```bash
New account ID: 0.0.4382765

New account balance is: 1000 tinybars.

The transfer transaction from my account to the new account was: SUCCESS
```

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
