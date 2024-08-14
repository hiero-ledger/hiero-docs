# Environment Setup

## Summary

This environment setup guide will provide you with the necessary steps to get your development environment ready for building applications on the Hedera Network. You will set up a new project directory, establish a `.env` environment variable file to store your Hedera Testnet account ID and private keys and configure your Hedera Testnet client.

***

## Prerequisites

* Completed the [Introduction](introduction.md) step.

{% hint style="info" %}
_**Note:** You can always check the "_[_Code Check ✅_](environment-set-up.md#code-check) _" section at the bottom of each page to view the entire code if you run into issues. You can also post your issue to the respective SDK channel in our Discord community_ [_here_](http://hedera.com/discord) _or on the GitHub repository_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## **Step 1: Create your project directory**

Open your IDE of choice and follow the below steps to create your new project directory.

{% tabs %}
{% tab title="Java Gradle" %}
Create a new Gradle project and name it `HederaExamples`. Add the following dependencies to your `build.gradle` file.

{% code title="build.gradle " %}
```gradle
dependencies {

    implementation 'com.hedera.hashgraph:sdk:2.32.0'
    implementation 'io.grpc:grpc-netty-shaded:1.57.2'
    implementation 'io.github.cdimascio:dotenv-java:2.3.2'
    implementation 'org.slf4j:slf4j-nop:2.0.9'
    implementation 'com.google.code.gson:gson:2.8.8'
}
```
{% endcode %}
{% endtab %}

{% tab title="Java Maven" %}
Create a new Maven project and name it `HederaExamples`. Add the following dependencies to your `pom.xml` file.

{% code title="pom.xml " %}
```xml
<dependencies>
        <dependency>
            <groupId>com.hedera.hashgraph</groupId>
            <artifactId>sdk</artifactId>
            <version>2.32.0</version>
        </dependency>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-netty-shaded</artifactId>
            <version>1.57.2</version>
        </dependency>
        <dependency>
            <groupId>io.github.cdimascio</groupId>
            <artifactId>dotenv-java</artifactId>
            <version>2.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>2.0.9</version>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.8</version>
        </dependency>
</dependencies>
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
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
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

***

## Step 2: Install Dependencies and SDKs

{% tabs %}
{% tab title="Java" %}
Create a new Java class and name it something like _`HederaExamples`_. Import the following classes to use in your example:

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
```

_**Note:** You may install the latest version of the Java SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-java)_._
{% endtab %}

{% tab title="JavaScript" %}
Install the [JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) with your favorite package manager _`npm`_ or _`yarn`_ by running the following command:

```bash
// Install Hedera's JS SDK with NPM
npm install --save @hashgraph/sdk

// Install with Yarn
yarn add @hashgraph/sdk
```

Install _`dotenv`_ with your favorite package manager. This will allow our node environment to use your testnet _**account ID**_ and the _**private key**_ we will store in a _`.env`_ file next.

```bash
// Install with NPM
npm install dotenv

// Install with Yarn
yarn add dotenv
```

Create a _`index.js`_ file by running the following command:

```bash
touch index.js
```

Your project structure should look something like this:

![](../.gitbook/assets/project\_directory.png)
{% endtab %}

{% tab title="Go" %}
Create a `hedera_examples.go` file in `hedera-go-examples` root directory. You will write all of your code in this file.

```bash
touch hedera_examples.go
```

Create the Go "module" file by running the below command. The `go.mod` file defines the module's properties and dependencies and provides a way to manage versioning for Go projects.

```go
go mod init hedera_examples.go
```

Install the [Go SDK](https://github.com/hashgraph/hedera-sdk-go):

```go-module
go get github.com/hashgraph/hedera-sdk-go/v2@latest
```

And the [DotEnv package](https://github.com/joho/godotenv):&#x20;

```go-module
go get github.com/joho/godotenv
```

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
{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Note:** Testnet **HBAR** is required for this next step. Please follow the instructions to create a Hedera account on the_ [_portal_](https://docs.hedera.com/guides/getting-started/introduction) _before you move on to the next step._
{% endhint %}

***

## Step 3: **Create your .env File**

Create the `.env` file in your project's root directory. The `.env` file stores your environment variables, such as your account ID and private key.&#x20;

_**📣 Note**: If you have not created an account, please do so_ [_here_](introduction.md) _before this step._

{% tabs %}
{% tab title="Hedera Developer Portal" %}
If you created your testnet account through the developer portal, grab the Hedera Testnet account ID and DER-encoded private key from your [Hedera portal profile](https://portal.hedera.com/) (see screenshot below) and assign them to the `MY_ACCOUNT_ID` and `MY_PRIVATE_KEY` environment variables in your `.env` file:&#x20;

<figure><img src="../.gitbook/assets/DER portal (1).png" alt="" width="563"><figcaption><p>Hedera Developer Portal</p></figcaption></figure>

```markdown
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e020100300506032b657004220420ed5a93073.....
```
{% endtab %}

{% tab title="Hedera Faucet" %}
Alternatively, if you used the faucet to create a testnet account, grab your faucet account ID and the private key (how to export a private key from MetaMask [here](https://support.metamask.io/hc/en-us/articles/360015289632-How-to-export-an-account-s-private-key))  and assign them to the `MY_ACCOUNT_ID` and `MY_PRIVATE_KEY` environment variables in your `.env` file:

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

```
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=0xfd154395435c81233b2fc906486f35e068...
```
{% endtab %}
{% endtabs %}

Next, you will load your account ID and private key variables from the `.env` file created in the previous step.

{% tabs %}
{% tab title="Java" %}
Within the _`main`_ method, add your testnet account ID and private key from the environment file.

{% code title="HederaExamples.java" %}
```java
public class HederaExamples {

    public static void main(String[] args) {

        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));  
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="index.js" %}
```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar, TransferTransaction } = require("@hashgraph/sdk");
require('dotenv').config();

async function environmentSetup() {

    //Grab your Hedera testnet account ID and private key from your .env file
    const myAccountId = process.env.MY_ACCOUNT_ID;
    const myPrivateKey = process.env.MY_PRIVATE_KEY;

    // If we weren't able to grab it, we should throw a new error
    if (!myAccountId || !myPrivateKey) {
        throw new Error("Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present");
    }
}
environmentSetup();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code title="hedera_examples.go" %}
```go
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
{% endcode %}

In your terminal, enter the following command to create your `go.mod` file. This module is used for tracking dependencies and is required.

```go-module
go mod init hedera_examples.go
```

Run your code to see your testnet account ID and private key printed to the console.

```go-module
go run hedera_examples.go
```
{% endtab %}
{% endtabs %}

***

## Step 4: Create your Hedera Testnet client

Create a Hedera Testnet [client](../support-and-community/glossary.md#client) and set the operator information using the testnet account ID and private key for transaction and query fee authorization. The _operator_ is the default account that will pay for the transaction and query fees in HBAR. You will need to sign the transaction or query with the private key of that account to authorize the payment. In this case, the operator ID is your testnet account ID, and the operator private key is the corresponding testnet account private key.

{% hint style="warning" %}
To avoid encountering the **`INSUFFICIENT_TX_FEE`** error while conducting transactions, you can adjust the maximum transaction fee limit through the **`.setDefaultMaxTransactionFee()`** method. Similarly, the maximum query payment can be adjusted using the **`.setDefaultMaxQueryPayment()`** method.
{% endhint %}

<details>

<summary>🚨 How to resolve the <em>INSUFFIENT_TX_FEE</em> error</summary>

To resolve this error, you must adjust the max transaction fee to a higher value suitable for your needs.

Here is a simple example addition to your code:

```javascript
const maxTransactionFee = new Hbar(XX); // replace XX with desired fee in Hbar
```

In this example, you can set `maxTransactionFee` to any value greater than 5 HBAR (or 500,000,000 tinybars) to avoid the "_INSUFFICIENT\_TX\_FEE_" error for transactions greater than 5 HBAR. Please replace `XX` with the desired value.

To implement this new max transaction fee, you use the `setDefaultMaxTransactionFee()` method as shown below:

```javascript
client.setDefaultMaxTransactionFee(maxTransactionFee);
```

</details>

{% tabs %}
{% tab title="Java" %}
```java
//Create your Hedera Testnet client
Client client = Client.forTestnet();

//Set your account as the client's operator
client.setOperator(myAccountId, myPrivateKey);

//Set the default maximum transaction fee (in Hbar)
client.setDefaultMaxTransactionFee(new Hbar(100));

//Set the maximum payment for queries (in Hbar)
client.setDefaultMaxQueryPayment(new Hbar(50));
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create your Hedera Testnet client
const client = Client.forTestnet();

//Set your account as the client's operator
client.setOperator(myAccountId, myPrivateKey);

//Set the default maximum transaction fee (in Hbar)
client.setDefaultMaxTransactionFee(new Hbar(100));

//Set the maximum payment for queries (in Hbar)
client.setDefaultMaxQueryPayment(new Hbar(50));
```
{% endtab %}

{% tab title="Go" %}
```go
//Create your testnet client
client := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)

// Set default max transaction fee
client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))

// Set max query payment
client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
```
{% endtab %}
{% endtabs %}

**Your project environment is now set up to submit transactions and queries to the Hedera test network successfully!**

Next, you will learn how to [create an account](create-an-account.md).

## Code Check :white\_check\_mark:

***

<details>

<summary>Java</summary>

<pre class="language-java" data-title="HederaExamples.java"><code class="lang-java">import com.hedera.hashgraph.sdk.Hbar;
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

        public static void main(String[] args) {
                
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));+
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        //Create your Hedera Testnet client
        
<strong>        Client client = Client.forTestnet();
</strong>        
        client.setOperator(myAccountId, myPrivateKey);
        // Set default max transaction fee &#x26; max query payment
        client.setDefaultMaxTransactionFee(new Hbar(100)); 
        client.setDefaultMaxQueryPayment(new Hbar(50)); 
        
        System.out.println("Client setup complete.");
    }
}
</code></pre>

</details>

<details>

<summary>JavaScript</summary>

{% code title="index.js" %}
```javascript
const {
  Hbar,
  Client,
} = require("@hashgraph/sdk");

require("dotenv").config();

async function environmentSetup() {
  //Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (!myAccountId || !myPrivateKey) {
    throw new Error(
      "Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present"
    );
  }
  
  //Create your Hedera Testnet client
  const client = Client.forTestnet();

  //Set your account as the client's operator
  client.setOperator(myAccountId, myPrivateKey);

  //Set the default maximum transaction fee (in Hbar)
  client.setDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client.setDefaultMaxQueryPayment(new Hbar(50));
  
  console.log("Client setup complete.");
}
environmentSetup();
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

	//Create your testnet client
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	// Set default max transaction fee & max query payment
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
	
	fmt.Println(“Client setup complete.”)
}
```
{% endcode %}

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

***

**Contributors:** [fabianstraubinger99](https://github.com/fabianstraubinger99)
