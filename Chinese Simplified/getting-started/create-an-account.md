# Create an Account

## Summary

In this section, you will learn how to make a simple Hedera account. Hedera accounts are the entry point by which you can interact with the [Hedera APIs](../sdks-and-apis/hedera-api/). Accounts hold a balance of HBAR used to pay for API calls for the various transaction and query types.

***

## Prerequisites

- Completed the [Introduction](introduction.md) step.
- Completed the [Environment Setup](environment-set-up.md) step.

***

## Step 1: Import modules

Import the following modules to your code file.

{% tabs %}
{% tab title="Java" %}

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;
import io.github.cdimascio.dotenv.Dotenv;
import java.util.concurrent.TimeoutException;
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar } = require("@hashgraph/sdk");
require("dotenv").config();
```

{% endtab %}

{% tab title="Go" %}

```go
import (
    "fmt"
    "os"

    "github.com/hashgraph/hedera-sdk-go/v2"
    "github.com/joho/godotenv"
)
```

{% endtab %}
{% endtabs %}

***

## Step 2: Generate keys for the new account

Generate a private and public key to associate with the account you will create.

{% tabs %}
{% tab title="Java" %}

```java
//Create your Hedera Testnet client
//Client client = Client.forTestnet();
//client.setOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

// Generate a new key pair
PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//const client = Client.forTestnet();
//client.setOperator(myAccountId, myPrivateKey);
//-----------------------<enter code below>--------------------------------------

//Create new keys
const newAccountPrivateKey = PrivateKey.generateED25519(); 
const newAccountPublicKey = newAccountPrivateKey.publicKey;
```

{% endtab %}

{% tab title="Go" %}

```go
//client := hedera.ClientForTestnet()
//client.SetOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

//Generate new keys for the account you will create
newAccountPrivateKey, err := hedera.PrivateKeyGenerateEd25519()

if err != nil {
  panic(err)
}

newAccountPublicKey := newAccountPrivateKey.PublicKey()
```

{% endtab %}
{% endtabs %}

***

## Step 3: Create a new account

Create a new account using _`AccountCreateTransaction()`_. Use the public key created in the previous step to enter in the _`setKey()`_ field. This will associate the key pair generated in the previous step with the new account. The public key of the account is visible to the public and can be viewed in a mirror node explorer. The private key is used to authorize account-related transactions like transferring _HBAR_ or tokens from that account to another account. The account will have an initial balance of _1,000 tinybars_ funded from your testnet account created by the Hedera portal.

You can view transactions successfully submitted to the network by getting the transaction ID and searching for it in a mirror node explorer. The transaction ID is composed of the account ID that paid for the transaction and the transaction's valid start time e.g. _`0.0.1234@1609348302`_<mark style="color:blue;">.</mark> The transaction's valid start time is the time the transaction begins to be valid on the network. The SDK automatically generates a transaction ID for each transaction behind the scenes.

{% tabs %}
{% tab title="Java" %}

```java
//Create new account and assign the public key
TransactionResponse newAccount = new AccountCreateTransaction()
     .setKey(newAccountPublicKey)
     .setInitialBalance(Hbar.fromTinybars(1000))
     .execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create a new account with 1,000 tinybar starting balance
const newAccount = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);
```

{% endtab %}

{% tab title="Go" %}

```go
//Create new account and assign the public key
newAccount, err := hedera.NewAccountCreateTransaction().
    SetKey(newAccountPublicKey).
    SetInitialBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
    Execute(client)
```

{% endtab %}
{% endtabs %}

## Step 4: Get the new account ID

The _account ID_ for the new account is returned in the receipt of the transaction that created the account. The receipt provides information about the transaction like whether it was successful or not and any new entity IDs that were created. Entities include accounts, smart contracts, tokens, files, topics, and scheduled transactions. The _account ID_ is in x.y.z format where z is the account number. The preceding values (x and y) default to zero today and represent the shard and realm number respectively. Your new _account ID_ should result in something like _`0.0.1234`_.

{% tabs %}
{% tab title="Java" %}

```java
// Get the new account ID
AccountId newAccountId = newAccount.getReceipt(client).accountId;

//Log the account ID
System.out.println("New account ID is: " +newAccountId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Get the new account ID
const getReceipt = await newAccount.getReceipt(client);
const newAccountId = getReceipt.accountId;

//Log the account ID
console.log("The new account ID is: " +newAccountId);
```

{% endtab %}

{% tab title="Go" %}

```go
//Request the receipt of the transaction
receipt, err := newAccount.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the new account ID from the receipt
newAccountId := *receipt.AccountID

//Log the account ID
fmt.Printf("The new account ID is %v\n", newAccountId)
```

{% endtab %}
{% endtabs %}

***

## Step 5: Verify the new account balance

Next, you will submit a query to the Hedera test network to return the balance of the new account using the new _account ID_. The current account balance for the new account should be 1,000 _tinybars_. Getting the balance of an account is free today.

{% tabs %}
{% tab title="Java" %}

```java
//Check the new account's balance
AccountBalance accountBalance = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("New account balance: " +accountBalance.hbars);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Verify the account balance
const accountBalance = await new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

console.log("The new account balance is: " +accountBalance.hbars.toTinybars() +" tinybar.");
```

{% endtab %}

{% tab title="Go" %}

```go
//Create the account balance query
query := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Sign with client operator private key and submit the query to a Hedera network
accountBalance, err := query.Execute(client)
if err != nil {
    panic(err)
}

//Print the balance of tinybars
fmt.Println("The account balance for the new account is ", accountBalance.Hbars.AsTinybar())
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
:star: **Congratulations! You have successfully completed the following:**

- Created a new Hedera account with an initial balance of 1,000 tinybars.
- Obtained the new account ID by requesting the receipt of the transaction.
- Verified the starting balance of the new account by submitting a query to the network.

You are now ready to transfer some HBAR to the new account :money\_mouth:!
{% endhint %}

***

## Code Check :white\_check\_mark:

<details>

<summary>Java</summary>

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;
import io.github.cdimascio.dotenv.Dotenv;
​
import java.util.concurrent.TimeoutException;
​
public class HederaExamples {
​
    public static void main(String[] args) throws TimeoutException, HederaPreCheckStatusException, HederaReceiptStatusException {​
        
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        
        //Create your Hedera Testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);
        
        // Set default max transaction fee & max query payment
        client.setDefaultMaxTransactionFee(new Hbar(100)); 
        client.setDefaultMaxQueryPayment(new Hbar(50)); 
        
        // Generate a new key pair
        PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
        PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();
​
        //Create new account and assign the public key
        TransactionResponse newAccount = new AccountCreateTransaction()
                .setKey(newAccountPublicKey)
                .setInitialBalance( Hbar.fromTinybars(1000))
                .execute(client);
​
        // Get the new account ID
        AccountId newAccountId = newAccount.getReceipt(client).accountId;
​
        System.out.println("\nNew account ID: " +newAccountId);
        
        //Check the new account's balance
        AccountBalance accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);
​
        System.out.println("New account balance: " +accountBalance.hbars);
​
    }
}
```

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
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
require("dotenv").config();

async function environmentSetup() {
  // Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Environment variables myAccountId and myPrivateKey must be present"
    );
  }

  // Create your connection to the Hedera Network
  const client = Client.forTestnet();
  client.setOperator(myAccountId, myPrivateKey);

  //Set the default maximum transaction fee (in Hbar)
  client.setDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client.setDefaultMaxQueryPayment(new Hbar(50));

  // Create new keys
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey.publicKey;

  // Create a new account with 1,000 tinybar starting balance
  const newAccount = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);

  // Get the new account ID
  const getReceipt = await newAccount.getReceipt(client);
  const newAccountId = getReceipt.accountId;
  
  console.log("\nNew account ID: " + newAccountId);

  // Verify the account balance
  const accountBalance = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .execute(client);

  console.log(
    "The new account balance is: " +
      accountBalance.hbars.toTinybars() +
      " tinybar."
  );

  return newAccountId;
}
environmentSetup();
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

	//Print your testnet account ID and private key to the console to make sure there was no error
	fmt.Printf("\nThe account ID is = %v\n", myAccountId)
	fmt.Printf("The private key is = %v", myPrivateKey)

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
	fmt.Println("\n")
	fmt.Printf("New account ID: %v\n", newAccountId)

	//Create the account balance query
	query := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign with client operator private key and submit the query to a Hedera network
	accountBalance, err := query.Execute(client)
	if err != nil {
		panic(err)
	}

	//Print the balance of tinybars
	fmt.Println("New account balance for the new account is", accountBalance.Hbars.AsTinybar())
}

```

</details>

#### Sample output:

```bash
New account ID: 0.0.13724748
New account balance: 1000 tinybars.
```

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
