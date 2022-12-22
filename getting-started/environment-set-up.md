# Environment Setup

## Summary

In this section you will complete the following:

* Create your project directory
* Create a `.env` file and store your Hedera Testnet account ID and keys
* Set-up your Hedera Testnet client

## Pre-requisites: <a href="#pre-requisites" id="pre-requisites"></a>

{% content-ref url="introduction.md" %}
[introduction.md](introduction.md)
{% endcontent-ref %}

You can always check the "Code Check ✅ " section at the bottom of each page to view the entire code if you run into issues. You can also post your issue to the respective SDK channel in our Discord community [here](http://hedera.com/discord) or on the GitHub repository.

{% tabs %}
{% tab title="Java" %}
**Step 1: Create a new Gradle project in your favorite IDE**

Open your favorite IDE and create a new gradle project. Add the following dependencies to your build.gradle file. You may choose to install the latest version of the SDK [here](https://github.com/hashgraph/hedera-sdk-java).

{% code title="build.gradle " %}
```java
dependencies {

    implementation 'com.hedera.hashgraph:sdk:2.14.0'
    implementation 'io.grpc:grpc-netty-shaded:1.40.0'
    compile 'io.github.cdimascio:java-dotenv:5.2.1' // Module that stores your environment variables from a .env file
    implementation 'org.slf4j:slf4j-nop:1.7.29'
    implementation 'com.google.code.gson:gson:2.8.8'
    
}
```
{% endcode %}

**Step 2: Create a .env file in your project**

Create a **.env** file in the root directory of your project. Grab the the Hedera Testnet **account ID** and **private key** from your [Hedera portal profile](https://portal.hedera.com/) and enter them in the `MY_ACCOUNT_ID` and `MY_PRIVATE_KEY` fields.

```
MY_ACCOUNT_ID=ENTER TESTNET ACCOUNT ID 
MY_PRIVATE_KEY=ENTER TESTNET PRIVATE KEY
```

**Step 3: Create a new class**

Create a new java class and title it something like `HederaExamples`. Import the following classes to use in your example.

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.PrivateKey;
import io.github.cdimascio.dotenv.Dotenv;
```

Within the `main` method, grab your testnet account ID and private key from the .env file.

```java
public class HederaExamples {

    public static void main(String[] args) {

        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));  
    }
}
```

**Step 4: Create your Hedera Testnet client**

You have the option to create a client for the Hedera Previewnet, Testnet or Mainnet. Since we are using a Hedera Testnet account ID and private key, we will create a client for the Hedera Testnet. You can view all the client configurations here.

After you create your Hedera Testnet client, you will need to set the operator information. The operator is the account that will pay for the transaction and query fees in HBAR. You will need to sign the transaction or query with the private key of that account to authorize the payment. In this case, the operator ID is your testnet account ID and the operator private key is the corresponding testnet account private key.

```java
//Create your Hedera Testnet client
Client client = Client.forTestnet();
client.setOperator(myAccountId, myPrivateKey);
```

{% hint style="info" %}
The client has a default **max transaction fee** of 100,000,000 tinybars (1 hbar) and default **max query payment** of 100,000,000 tinybars (1 hbar). If you need to change these values, you can use`.setDefaultMaxTransactionFee()` for a transaction and `.setDefaultMaxQueryPayment()` for queries. You are only charged the actual cost of the transaction or query.
{% endhint %}

Your project environment is now set up to successfully submit transactions and queries to the Hedera test network!

Next, you will learn how to create an account. Click the link at the bottom to get started.

**Code Check** :white\_check\_mark:

What your code should look like at this point:

{% code title="HederaExamples.java" %}
```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.PrivateKey;
import io.github.cdimascio.dotenv.Dotenv;
​
public class HederaExamples {
​
    public static void main(String[] args) {
​
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
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
{% endtab %}

{% tab title="JavaScript" %}
**Step 1: Set up your node.js environment**

**Create a new directory for our sample & move into it**

Open your terminal and create a directory called `hello-hedera-js-sdk`. After you create the project directory navigate into the directory.

```bash
mkdir hello-hedera-js-sdk && cd hello-hedera-js-sdk
```

**Initialize a node.js project in this new directory**

```bash
npm init
```

Note: you can just say “yes” to all of the defaults and/or plugin what makes sense. It’s an example!

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

**Step 2: Install the Hedera JavaScript SDK**

Now that you have your node environment setup, we can install the Hedera’s JS SDK! You can open this project in your favorite text editor like [Visual Studio Code](https://code.visualstudio.com/Download).

* Install the JS SDK with your favorite package manager, `npm` or `yarn`.

```
// install Hedera's JS SDK with NPM
npm install --save @hashgraph/sdk

// Install with Yarn
yarn add @hashgraph/sdk
```

* Install `dotenv` with your favorite package manager. This will allow our node environment to use your testnet account ID and the private key that we will store in a `.env` file next.

```bash
// install with NPM
npm install dotenv

// Install with Yarn
yarn add dotenv
```

**Step 3: Create a .env file in your project**

The .env file will store your Hedera testnet **account ID** and **private key.** Create this file in the root directory of your project and save it as **.env** file.

Now you can add your testnet account ID and private key provided from your [Hedera Portal](https://portal.hedera.com/) Account.

```
MY_ACCOUNT_ID = ENTER YOUR ACCOUNT ID
MY_PRIVATE_KEY = ENTER YOUR PRIVATE KEY
```

**Step 4: Create an index.js file in the 'root' directory**

This file will contain the code we will write in the following samples.

```shell
touch index.js
```

Your project structure should look something like this after:

![](../.gitbook/assets/image105.png)

Grab your Hedera testnet account ID and private key from the .env file.

{% code title="index.js" %}
```javascript
const { Client } = require("@hashgraph/sdk");
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
}
main();
```
{% endcode %}

**Step 5: Create your Hedera testnet client**

You have the option to create a client for the Hedera mainnet or testnet. Since we are using a Hedera testnet account ID and private key, we will create a client for the Hedera testnet. You can find all the client configurations here.

After you create your Hedera testnet client, you will need to set the operator information. The operator is the account that will pay for the transaction and query fees in hbar.

{% code title="index.js" %}
```javascript
// Create our connection to the Hedera network
// The Hedera JS SDK makes this really easy!
const client = Client.forTestnet();

client.setOperator(myAccountId, myPrivateKey);
```
{% endcode %}

Your project environment is now set up to successfully submit transactions/queries to the Hedera test network!

Next, you will learn how to create an account. Click the link at the bottom to get started.

**Code Check ✅**

What your `index.js` file should look like at this point:

{% code title="index.js" %}
```javascript
const { Client } = require("@hashgraph/sdk");
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
}
main();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
**Step 1: Create your Go project**

Open your terminal and create a project directory called something like `hedera-go-examples` to store your Go source code.

```bash
mkdir hedera-go-examples && cd hedera-go-examples
```

**Step 2: Create a .env file in your project**

Open the project in your favorite IDE and create a **.env** file in the root directory of your project. Enter your Hedera testnet account ID and private key provided to you from your Hedera portal account.

```
MY_ACCOUNT_ID = ENTER TESTNET ACCOUNT ID
MY_PRIVATE_KEY = ENTER TESTNET PRIVATE KEY
```

**Step 3: Install the Hedera Go SDK**

Create a `hedera_examples.go` file in `hedera-go-examples` directory. You will write all of your code in this file.

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

Run your code to see your testnet account ID and private key are printed to the console.

```shell
go run hedera_examples.go
```

**Step 4: Create your Hedera testnet client**

You have the option to create a client for the Hedera previewnet, testnet, and mainnet. Since we are using a Hedera testnet account ID and private key, we will create a client for the Hedera testnet. This allows you to submit transactions and queries to the test network.

After you create your Hedera testnet client, you will need to set the operator account ID and private key. The operator is the default account that will pay for the transaction and query fees. The operator in this example will be your testnet account ID and private key.

```go
//Create your testnet client
client := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)
```

{% hint style="info" %}
The client has a default **max transaction fee** of 100,000,000 tinybars (1 hbar) and default **max query payment** of 100,000,000 tinybars (1 hbar). If you need to change these values, you can use`.setDefaultMaxTransactionFee()` for transactions and `.setDefaultMaxQueryPayment()` for queries. You are only charged the actual cost of the transaction or query.
{% endhint %}

Your project environment is now set up to successfully submit transactions/queries to the Hedera test network!

Next, you will learn how to create a Hedera testnet account. Click the link at the bottom to get started.

**Code Check ✅**

What your code should look like at this point:

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
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
