# Environment Setup

## Summary

In this section, you will create your new project directory, your _**`.env`**_ file to store your, Hedera Testnet **account ID** and **private keys** and set up your _Hedera Testnet_ client.

## Prerequisites: <a href="#pre-requisites" id="pre-requisites"></a>

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center">➡ <mark style="color:purple;"><strong>INTRODUCTION</strong></mark></td><td><a href="introduction.md">introduction.md</a></td></tr></tbody></table>

{% hint style="info" %}
_**Note:** You can always check the "_[_Code Check ✅_](environment-set-up.md#code-check) _" section at the bottom of each page to view the entire code if you run into issues. You can also post your issue to the respective SDK channel in our Discord community_ [_here_](http://hedera.com/discord) _or on the GitHub repository_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

## **Step 1: Create your project directory**

Open your IDE of choice and follow the below steps to create your new project directory.

{% tabs %}
{% tab title="Java" %}
Create a new Gradle project and name it `HederaExamples`. Add the following dependencies to your `build.gradle` file.

{% code title="build.gradle " %}
```java
dependencies {

    implementation 'com.hedera.hashgraph:sdk:2.19.0'
    implementation 'io.grpc:grpc-netty-shaded:1.46.0'
    implementation 'io.github.cdimascio:dotenv-java:2.3.2'
    implementation 'org.slf4j:slf4j-nop:2.0.3'
    implementation 'com.google.code.gson:gson:2.8.8'

}
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
Open your terminal and create a directory called _`hello-hedera-js-sdk`_. After you create the project directory navigate to the directory by running the following command:

```bash
mkdir hello-hedera-js-sdk && cd hello-hedera-js-sdk
```

Initialize a _`node.js`_ project in this new directory by running the following command:

```bash
npm init -y
```

This is what your console should look like after running the command:

```bash
{
  "name": "hello-hedera-js-sdk",
  "version": "",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```
{% endtab %}

{% tab title="Go" %}
Open your terminal and create a project directory called something like `hedera-go-examples` to store your Go source code.

```bash
mkdir hedera-go-examples && cd hedera-go-examples
```
{% endtab %}
{% endtabs %}

## Step 2: Install Dependencies and SDKs

{% tabs %}
{% tab title="Java" %}
Create a new Java class and name it something like _`HederaExamples`_. Import the following classes to use in your example:

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.PrivateKey;
import io.github.cdimascio.dotenv.Dotenv;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;

import java.util.concurrent.TimeoutException;
```

Within the _`main`_ method, grab your testnet **account ID** and **private key** from the `.env` file.

```java
public class HederaExamples {

    public static void main(String[] args) {

        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));  
    }
}
```

_**Note:** You may choose to install the latest version of the SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-java)_._
{% endtab %}

{% tab title="JavaScript" %}
Open this project in your favorite IDE/text editor like [Visual Studio Code](https://code.visualstudio.com/Download).

Install the [JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) with your favorite package manager _`npm`_ or _`yarn`_ by running the following command:

```bash
// install Hedera's JS SDK with NPM
npm install --save @hashgraph/sdk

// Install with Yarn
yarn add @hashgraph/sdk
```

Install _`dotenv`_ with your favorite package manager. This will allow our node environment to use your testnet _**account ID**_ and the _**private key**_ that we will store in a _`.env`_ file next.

```bash
// install with NPM
npm install dotenv

// Install with Yarn
yarn add dotenv
```

Navigate to the project root directory and create a _`index.js`_ file by running the following command:

```bash
touch index.js
```

Your project structure should look something like this:

![](<../.gitbook/assets/image105 (1) (1).png>)

Grab your Hedera Testnet _**account ID**_ and _**private key**_ from the _`.env`_ file.

{% code title="index.js" %}
```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar, TransferTransaction } = require("@hashgraph/sdk");

async function main() {

    //Grab your Hedera testnet account ID and private key from your .env file
    const myAccountId = process.env.MY_ACCOUNT_ID;
    const myPrivateKey = process.env.MY_PRIVATE_KEY;

    // If we weren't able to grab it, we should throw a new error
    if (!myAccountId || !myPrivateKey) {
        throw new Error("Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present");
    }
}
main();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
Create a `hedera_examples.go` file in `hedera-go-examples` directory. You will write all of your code in this file.

{% hint style="info" %}
_**Note:** Install the Go SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-go) _and the DotEnv package_ [_here_](https://github.com/joho/godotenv) _before moving forward._
{% endhint %}

Import the following packages to your `hedera_examples.go` file:

```go
package main

import (
    "fmt"
    "os"

    "github.com/joho/godotenv"
    "github.com/hashgraph/hedera-sdk-go/v2"
)
```

Next, you will load your account ID and private key variables from the `.env` file created in the previous step.

```go
package main

import (
    "fmt"
    "os"

    "github.com/joho/godotenv"
    "github.com/hashgraph/hedera-sdk-go/v2"
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
}
```

In your terminal, enter the following command to create your `go.mod` file. This module is used for tracking dependencies and is required.

```shell
go mod init hedera_examples.go
```

Run your code to see your testnet account ID and private key printed to the console.

```shell
go run hedera_examples.go
```
{% endtab %}
{% endtabs %}

## Step 3: **Create your .env File**

The _`.env`_ file stores your environment variables, _**account ID**_ and _**private key (DER encoded)**_**.** Create the file in your project's root directory.

{% hint style="info" %}
_**Note:** Testnet **HBAR** is required for this next step. Please follow the instructions to create a Hedera account on the_ [_portal_](https://docs.hedera.com/guides/getting-started/introduction) _before you move on to the next step._
{% endhint %}

![](<../.gitbook/assets/DER portal (1).png>)

Grab the Hedera Testnet _**account ID**_ and _**DER encoded**_ _**private key**_ from your [Hedera portal profile](https://portal.hedera.com/)(see screenshot above) and assign them to the `MY_ACCOUNT_ID` and `MY_PRIVATE_KEY` environment variables in your `.env` file:

{% tabs %}
{% tab title="Java" %}
```
MY_ACCOUNT_ID=ENTER TESTNET ACCOUNT ID 
MY_PRIVATE_KEY=ENTER TESTNET PRIVATE KEY
```
{% endtab %}

{% tab title="JavaScript" %}
```
MY_ACCOUNT_ID=ENTER TESTNET ACCOUNT ID 
MY_PRIVATE_KEY=ENTER TESTNET PRIVATE KEY
```
{% endtab %}

{% tab title="Go" %}
```
MY_ACCOUNT_ID=ENTER TESTNET ACCOUNT ID 
MY_PRIVATE_KEY=ENTER TESTNET PRIVATE KEY
```
{% endtab %}
{% endtabs %}

## Step 4: Create your Hedera Testnet client

Create a _Hedera Testnet_ client and set the operator information using the testnet account ID and private key for transaction and query fee authorization. The operator is the default account that will pay for the transaction and query fees in HBAR. You will need to sign the transaction or query with the private key of that account to authorize the payment. In this case, the operator ID is your testnet **account ID** and the operator private key is the corresponding testnet account **private key**.

{% tabs %}
{% tab title="Java" %}
```java
//Create your Hedera Testnet client
Client client = Client.forTestnet();
client.setOperator(myAccountId, myPrivateKey);
```

{% hint style="info" %}
_**Note:** The client has a default **max transaction fee** of 100,000,000 tinybars (1 HBAR) and default **max query payment** of 100,000,000 tinybars (1 HBAR). If you need to change these values, you can use`.setDefaultMaxTransactionFee()` for a transaction and `.setDefaultMaxQueryPayment()` for queries. You are only charged the actual cost of the transaction or query._
{% endhint %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="index.js" %}
```javascript
// Create our connection to the Hedera network
// The Hedera JS SDK makes this really easy!
const client = Client.forTestnet();

client.setOperator(myAccountId, myPrivateKey);
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```go
//Create your testnet client
client := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)
```

{% hint style="info" %}
**Note:** The client has a default **max transaction fee** of 100,000,000 tinybars (1 HBAR) and default **max query payment** of 100,000,000 tinybars (1 HBAR). If you need to change these values, you can use`.setDefaultMaxTransactionFee()` for transactions and `.setDefaultMaxQueryPayment()` for queries. You are only charged the actual cost of the transaction or query.
{% endhint %}
{% endtab %}
{% endtabs %}

{% hint style="success" %}
_**Your project environment is now set up to submit transactions and queries to the Hedera test network successfully!**_

_Next, you will learn how to create an account._
{% endhint %}

## Code Check :white\_check\_mark:

This is what your code should look like:

<details>

<summary>Java</summary>

{% code title="HederaExamples.java" %}
```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.PrivateKey;
import io.github.cdimascio.dotenv.Dotenv;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;

import java.util.concurrent.TimeoutException;
​
public class HederaExamples {
​
    public static void main(String[] args) {
​
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));+
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
​
        //Create your Hedera Testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);
​
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
  Client,
  PrivateKey,
  AccountCreateTransaction,
  AccountBalanceQuery,
  Hbar,
  TransferTransaction,
} = require("@hashgraph/sdk");

require("dotenv").config();

async function main() {
  //Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (!myAccountId || !myPrivateKey) {
    throw new Error(
      "Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present"
    );
  }

  // Create our connection to the Hedera network
  // The Hedera JS SDK makes this really easy!
  const client = Client.forTestnet();
  client.setOperator(myAccountId, myPrivateKey);
}
main();
```
{% endcode %}

</details>

<details>

<summary>Go</summary>

{% code title="hedera_examples.go" %}
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
    fmt.Printf("The account ID is = %v\n", myAccountId)
    fmt.Printf("The private key is = %v\n", myPrivateKey)

    //Create your testnet client
    client := hedera.ClientForTestnet()
    client.SetOperator(myAccountId, myPrivateKey)
}
```
{% endcode %}

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
