# Transfer HBAR

## Summary

In this section, you will learn how to transfer **HBAR** from your account to another account on the Hedera test network.

## Pre-requisites: <a href="#pre-requisites" id="pre-requisites"></a>

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="introduction.md"><strong>Introduction</strong></a><strong></strong></td><td></td><td></td><td><a href="../.gitbook/assets/black hedera.jpeg">black hedera.jpeg</a></td><td><a href="introduction.md">introduction.md</a></td></tr><tr><td align="center"><strong></strong><a href="environment-set-up.md"><strong>Environment Setup</strong></a><strong></strong></td><td></td><td></td><td><a href="../.gitbook/assets/black hedera.jpeg">black hedera.jpeg</a></td><td><a href="environment-set-up.md">environment-set-up.md</a></td></tr><tr><td align="center"><strong></strong><a href="create-an-account.md"><strong>Create an Account</strong></a><strong></strong></td><td></td><td></td><td><a href="../.gitbook/assets/black hedera.jpeg">black hedera.jpeg</a></td><td><a href="create-an-account.md">create-an-account.md</a></td></tr></tbody></table>

You can always check the "[Code Check ✅](transfer-hbar.md#code-check) " section at the bottom of each page to view the entire code if you run into issues. You can also post your issue to the respective SDK channel in our Discord community [here](http://hedera.com/discord) or on the GitHub repository [here](https://github.com/hashgraph/hedera-docs).

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
**Note:** The net value of the transfer must equal zero (the total number of **HBAR** sent by the sender must equal the total number of **HBAR** received by the recipient).
{% endhint %}

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

## Step 3. Get the account balance

### **Get the cost of requesting the query**

You can request the cost of a query prior to submitting the query to the Hedera network. Checking an account balance is free of charge today. You can verify that by the method below.

{% tabs %}
{% tab title="Java" %}
```java
//Request the cost of the query
Hbar queryCost = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

System.out.println("The cost of this query is: " +queryCost);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Request the cost of the query
const queryCost = await new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

console.log("The cost of query is: " +queryCost);
```
{% endtab %}

{% tab title="Go" %}
```java
//Create the query that you want to submit
balanceQuery := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Get the cost of the query
cost, err := balanceQuery.GetCost(client)

if err != nil {
        panic(err)
}

println("The account balance query cost is:", cost.String())
```
{% endtab %}
{% endtabs %}

### **Get the account balance**

You will verify the account balance was updated for the new account by requesting a get account balance query. The current account balance should be the sum of the initial balance (1,000 **tinybars**) plus the transfer amount (1,000 **tinybars**) and equal to 2,000 **tinybars**.

{% tabs %}
{% tab title="Java" %}
```java
//Check the new account's balance
AccountBalance accountBalanceNew = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("The new account balance is: " +accountBalanceNew.hbars);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Check the new account's balance
const getNewBalance = await new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

console.log("The account balance after the transfer is: " +getNewBalance.hbars.toTinybars() +" tinybar.")
```
{% endtab %}

{% tab title="Go" %}
```go
//Check the new account's balance
newAccountBalancequery := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Sign with client operator private key and submit the query to a Hedera network
newAccountBalance, err := newAccountBalancequery.Execute(client)
if err != nil {
    panic(err)
}

//Print the balance of tinybars
fmt.Println("The hbar account balance for this account is", newAccountBalance.Hbars.AsTinybar())
```
{% endtab %}
{% endtabs %}

:star: Congratulations! You have successfully transferred **HBAR** to another account on the Hedera Testnet! If you have followed the tutorial from the beginning, you have completed the following thus far:&#x20;

* Set up your Hedera environment to submit transactions and queries.
* Created an account.
* Transferred **HBAR** to another account.

Do you want to keep learning? Visit our "[Resources](../resources/starter-projects.md)" and "[Documentation](../docs/sdks/)" sections to take your learning experience to the next level. You can also find additional Java SDK examples [here](https://github.com/hashgraph/hedera-sdk-java/tree/main/examples/src/main/java).

{% content-ref url="try-examples/" %}
[try-examples](try-examples/)
{% endcontent-ref %}

## Code Check ✅

Your complete code file should look something like this:

{% tabs %}
{% tab title="Java" %}
{% code title="HederaExamples.java" %}
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
import com.hedera.hashgraph.sdk.TransferTransaction;
import io.github.cdimascio.dotenv.Dotenv;

import java.util.concurrent.TimeoutException;

public class HederaExamples {

    public static void main(String[] args) throws TimeoutException, HederaPreCheckStatusException, HederaReceiptStatusException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));

        //Create your Hedera testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

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

        System.out.println("The new account ID is: " +newAccountId);

        //Check the new account's balance
        AccountBalance accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);

        System.out.println("The new account balance is: " +accountBalance.hbars);

        //Transfer HBAR
        TransactionResponse sendHbar = new TransferTransaction()
                .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
                .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
                .execute(client);

        System.out.println("The transfer transaction was: " +sendHbar.getReceipt(client).status);

        //Request the cost of the query
        Hbar queryCost = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .getCost(client);

        System.out.println("The cost of this query is: " +queryCost);

        //Check the new account's balance
        AccountBalance accountBalanceNew = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);

        System.out.println("The new account balance is: " +accountBalanceNew.hbars);

    }
}
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="index.js" %}
```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar, TransferTransaction} = require("@hashgraph/sdk");
require("dotenv").config();

async function main() {

    //Grab your Hedera testnet account ID and private key from your .env file
    const myAccountId = process.env.MY_ACCOUNT_ID;
    const myPrivateKey = process.env.MY_PRIVATE_KEY;

    // If we weren't able to grab it, we should throw a new error
    if (myAccountId == null ||
        myPrivateKey == null ) {
        throw new Error("Environment variables myAccountId and myPrivateKey must be present");
    }

    // Create our connection to the Hedera network
    // The Hedera JS SDK makes this really easy!
    const client = Client.forTestnet();

    client.setOperator(myAccountId, myPrivateKey);

    //Create new keys
    const newAccountPrivateKey = PrivateKey.generateED25519(); 
    const newAccountPublicKey = newAccountPrivateKey.publicKey;

    //Create a new account with 1,000 tinybar starting balance
    const newAccountTransactionResponse = await new AccountCreateTransaction()
        .setKey(newAccountPublicKey)
        .setInitialBalance(Hbar.fromTinybars(1000))
        .execute(client);

    // Get the new account ID
    const getReceipt = await newAccountTransactionResponse.getReceipt(client);
    const newAccountId = getReceipt.accountId;

    console.log("The new account ID is: " +newAccountId);

    //Verify the account balance
    const accountBalance = await new AccountBalanceQuery()
        .setAccountId(newAccountId)
        .execute(client);

    console.log("The new account balance is: " +accountBalance.hbars.toTinybars() +" tinybar.");

    //Create the transfer transaction
    const sendHbar = await new TransferTransaction()
        .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
        .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
        .execute(client);

    //Verify the transaction reached consensus
    const transactionReceipt = await sendHbar.getReceipt(client);
    console.log("The transfer transaction from my account to the new account was: " + transactionReceipt.status.toString());
    
    //Request the cost of the query
    const queryCost = await new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

    console.log("The cost of query is: " +queryCost);

    //Check the new account's balance
    const getNewBalance = await new AccountBalanceQuery()
        .setAccountId(newAccountId)
        .execute(client);

    console.log("The account balance after the transfer is: " +getNewBalance.hbars.toTinybars() +" tinybar.")

}
main();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```java
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
    fmt.Printf("The account ID is = %v\n", myAccountId)
    fmt.Printf("The private key is = %v\n", myPrivateKey)

    //Create your testnet client
    client := hedera.ClientForTestnet()
    client.SetOperator(myAccountId, myPrivateKey)

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

    //Create the query that you want to submit
    balanceQuery := hedera.NewAccountBalanceQuery().
        SetAccountID(newAccountId)

    //Get the cost of the query
    cost, err := balanceQuery.GetCost(client)

    if err != nil {
        panic(err)
    }

    fmt.Println("The account balance query cost is:", cost.String())

    //Check the new account's balance
    newAccountBalancequery := hedera.NewAccountBalanceQuery().
        SetAccountID(newAccountId)

    //Sign with client operator private key and submit the query to a Hedera network
    newAccountBalance, err := newAccountBalancequery.Execute(client)
    if err != nil {
        panic(err)
    }

    //Print the balance of tinybars
    fmt.Println("The hbar account balance for this account is", newAccountBalance.Hbars.AsTinybar())
}
```
{% endtab %}
{% endtabs %}

## Sample Output:

```
The new account ID is: 0.0.215975 
The new account balance is: 1000 tℏ 
The transfer transaction was: SUCCESS The cost of this query is: 0 
The new account balance is: 2000 tℏ
```

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
