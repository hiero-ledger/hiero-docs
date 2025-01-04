# Create a Token

## Introduction to Creating a Token&#x20;

This tutorial will walk you through a `TokenCreateTransaction` and create a fungible [Hedera Token Service (HTS)](../../support-and-community/glossary.md#hedera-token-service-hts) token. You will learn how to configure essential token properties, set up necessary keys and permissions, and submit your transaction to the Hedera network.&#x20;

#### **What you will accomplish**

By the end of this tutorial, you will be able to:

* Create a new fungible token using HTS.
* Query the transaction via Mirror Node API.
* View your transaction on a Mirror Node Explorer.

***

## Prerequisites

Before you begin, you should have **completed** the following tutorials:

* [x] [Create and Fund Your Hedera Testnet Account](../../tutorials/more-tutorials/create-and-fund-your-hedera-testnet-account.md)
* [x] [Environment Setup](../environment-setup.md)&#x20;

***

## Step 1: Navigate to the `hts` example in the project directory

From the root directory of the `hedera-future-world` project CD (change directories) to the token create transaction example.&#x20;

```bash
cd hts
```

If you completed a previous example in the series you can use to go back to the root directory and cd into this example.

```bash
cd ../hts
```

If you want to get back to the root directory, you can CD out from any directory with this command.&#x20;

```bash
cd ../
```

You can follow along through the code walkthrough or skip ahead to execute the program [here](create-a-token.md#step-3-run-the-token-create-transaction-script).&#x20;

***

## Step 2: Guided Code Walkthrough

Open the HTS token script (`/hts/script-hts-ft...`) in a code editor like [VS Code](https://code.visualstudio.com/), [IntelliJ](https://www.jetbrains.com/idea/), or a [Gitpod](https://gitpod.io/) instance. The imports at the top include modules for interacting with the Hedera network via the SDK. The `@hashgraph/sdk` enables account management and transactions like creating a token while the `dotenv` package loads environment variables from the `.env` file, such as the operator account ID, private key, and name variables.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript" data-title="script-hts-ft.js"><code class="lang-javascript"><strong>import {
</strong>    Client,
    PrivateKey,
    AccountId,
    TokenCreateTransaction,
    TokenType,
<strong>} from '@hashgraph/sdk';
</strong><strong>import dotenv from 'dotenv';
</strong>import {
    createLogger,
} from '../util/util.js';

const logger = await createLogger({
    scriptId: 'htsFt',
    scriptCategory: 'task',
});
let client;

async function scriptHtsFungibleToken() {
    logger.logStart('Hello Future World - HTS Fungible Token - start');

    // Read in environment variables from `.env` file in parent directory
    dotenv.config({ path: '../.env' });
    logger.log('Read .env file');

    // Initialize the operator account
    const operatorIdStr = process.env.OPERATOR_ACCOUNT_ID;
    const operatorKeyStr = process.env.OPERATOR_ACCOUNT_PRIVATE_KEY;
    if (!operatorIdStr || !operatorKeyStr) {
        throw new Error('Must set OPERATOR_ACCOUNT_ID and OPERATOR_ACCOUNT_PRIVATE_KEY environment variables');
    }
    const operatorId = AccountId.fromString(operatorIdStr);
    const operatorKey = PrivateKey.fromStringECDSA(operatorKeyStr);
    client = Client.forTestnet().setOperator(operatorId, operatorKey);
    logger.log('Using account:', operatorIdStr);
}
</code></pre>
{% endtab %}

{% tab title="Java" %}
<pre class="language-java" data-title="ScriptHtsFt.java"><code class="lang-java">package hts;

import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.URI;

import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

<strong>import com.hedera.hashgraph.sdk.*;
</strong>
<strong>import io.github.cdimascio.dotenv.Dotenv;
</strong>
public class ScriptHtsFt {
    public static void main(String[] args) throws Exception {
        System.out.println("🏁 Hello Future World - HTS Fungible Token - start");

	    // Load environment variables from .env file
        Dotenv dotenv = Dotenv.configure().directory("../").load();
        String operatorIdStr = dotenv.get("OPERATOR_ACCOUNT_ID");
        String operatorKeyStr = dotenv.get("OPERATOR_ACCOUNT_PRIVATE_KEY");
        if (operatorIdStr == null || operatorKeyStr == null) {
            throw new RuntimeException("Must set OPERATOR_ACCOUNT_ID, OPERATOR_ACCOUNT_PRIVATE_KEY");
        }
        if (operatorKeyStr.startsWith("0x")) {
            operatorKeyStr = operatorKeyStr.substring(2);
        }

	 // Initialize the operator account
        AccountId operatorId = AccountId.fromString(operatorIdStr);
        PrivateKey operatorKey = PrivateKey.fromStringECDSA(operatorKeyStr);
        System.out.println("Using account: " + operatorIdStr);]
}
</code></pre>
{% endtab %}

{% tab title="Go" %}
<pre class="language-go" data-title="script-hts-ft.go"><code class="lang-go">package main

<strong>import (
</strong>	"encoding/json"
	"fmt"
	"log"
	"os"
	"strings"
	"time"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/imroc/req/v3"
	"github.com/joho/godotenv"
)

type TokenMNAPIResponse struct {
	Name        string `json:"name"`
	TotalSupply string `json:"total_supply"`
}

func main() {
	fmt.Println("🏁 Hello Future World - HTS Fungible Token - start")

	// Load environment variables from .env file
	err := godotenv.Load("../.env")
	if err != nil {
		log.Fatal("Error loading .env file")
	}

	// Initialize the operator account
	operatorIdStr := os.Getenv("OPERATOR_ACCOUNT_ID")
	operatorKeyStr := os.Getenv("OPERATOR_ACCOUNT_PRIVATE_KEY")
	if operatorIdStr == "" || operatorKeyStr == "" {
		log.Fatal("Must set OPERATOR_ACCOUNT_ID, OPERATOR_ACCOUNT_PRIVATE_KEY")
	}

	operatorId, _ := hedera.AccountIDFromString(operatorIdStr)
	// Necessary because Go SDK v2.37.0 does not handle the `0x` prefix automatically
	// Ref: https://github.com/hashgraph/hedera-sdk-go/issues/1057
	operatorKeyStr = strings.TrimPrefix(operatorKeyStr, "0x")
	operatorKey, _ := hedera.PrivateKeyFromStringECDSA(operatorKeyStr)
	fmt.Printf("Using account: %s\n", operatorId)
	fmt.Printf("Using operatorKey: %s\n", operatorKeyStr)
}
</code></pre>
{% endtab %}
{% endtabs %}

### Create a Hedera Testnet Client

To set up your Hedera Testnet client, create the client and configure the operator using your Testnet account ID and private key. The operator account covers transaction and query fees in HBAR, with all transactions requiring a signature from the operator's private key for authorization.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="script-hts-ft.js" %}
```javascript
// The client operator ID and key is the account that will be automatically set to pay for the transaction fees for each transaction
client = Client.forTestnet().setOperator(operatorId, operatorKey);

//Set the default maximum transaction fee (in Hbar)
client.setDefaultMaxTransactionFee(new Hbar(100));

//Set the maximum payment for queries (in Hbar)
client.setDefaultMaxQueryPayment(new Hbar(50));
```
{% endcode %}
{% endtab %}

{% tab title="Java" %}
{% code title="ScriptHtsFt.java" %}
```java
// The client operator ID and key is the account that will be automatically set to pay for the transaction fees for each transaction
Client client = Client.forTestnet().setOperator(operatorId, operatorKey);

//Set the default maximum transaction fee (in HBAR)
client.setDefaultMaxTransactionFee(new Hbar(100));
        
//Set the default maximum payment for queries (in HBAR)
client.setDefaultMaxQueryPayment(new Hbar(50));
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code title="" %}
```go
// The client operator ID and key is the account that will be automatically set to pay for the transaction fees for each transaction
client := hedera.ClientForTestnet()
client.SetOperator(operatorId, operatorKey)

// Set the default maximum transaction fee (in HBAR)
client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	
// Set the default maximum payment for queries (in HBAR)
client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
To avoid encountering the **`INSUFFICIENT_TX_FEE`** error while executing transactions, you can also specify the maximum transaction fee limit through the **`.setDefaultMaxTransactionFee()`** method and the maximum query payment through the **`.setDefaultMaxQueryPayment()`** method to control costs, ensuring your client operates within your desired financial limits on the Hedera Testnet.
{% endhint %}

<details>

<summary><strong>🚨 How to resolve the <code>INSUFFICIENT_TX_FEE</code> error</strong></summary>

To resolve this error, you must adjust the max transaction fee to a higher value suitable for your needs.

Here is a simple example addition to your code:

Copy

```javascript
const maxTransactionFee = new Hbar(XX); // replace XX with desired fee in Hbar
```

In this example, you can set `maxTransactionFee` to any value greater than 5 HBAR (or 500,000,000 tinybars) to avoid the "_INSUFFICIENT\_TX\_FEE_" error for transactions greater than 5 HBAR. Please replace `XX` with the desired value.

To implement this new max transaction fee, you use the `setDefaultMaxTransactionFee()` method as shown below:

Copy

```javascript
client.setDefaultMaxTransactionFee(maxTransactionFee);
```

</details>

### Create a Token Create Transaction

To create a fungible token using the HTS, start by instantiating a `TokenCreateTransaction`. Set the token type to `TokenType.FungibleCommon`, which functions similarly to [ERC-20 tokens](../../core-concepts/smart-contracts/tokens-managed-by-smart-contracts/erc-20-fungible-tokens.md) on Ethereum, meaning all token units are interchangeable.

Configure the token with the required properties:

* **Token Name**: The name of the token.
* **Token Symbol**: A publicly visible symbol for the token (e.g., hBARK).
* **Treasury Account ID**: The account holding the initial token supply.

<details>

<summary>Other optional fields</summary>

Default values will apply if left unspecified:

* No **admin key** makes the token immutable (unchangeable).
* No **supply key** fixes the token's supply (no minting or burning).
* No **token type** defaults to fungible.

</details>

Unlike NFTs, fungible tokens don’t require decimals or an initial supply of zero. For example, an initial supply of `10,000` units is represented as `1000000` in code to account for two decimals.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="script-hts-ft.js" %}
```javascript
// Create the token create transaction
const tokenCreateTx = await new TokenCreateTransaction()
    //Set the transaction memo
    .setTransactionMemo(`Hello Future World token - ${logger.version}`)
    // HTS `TokenType.FungibleCommon` behaves similarly to ERC20
    .setTokenType(TokenType.FungibleCommon)
    // Configure token options: name, symbol, decimals, initial supply
    .setTokenName(`${yourName} coin`)
    //Set the token symbol
    .setTokenSymbol(logger.scriptId)
    //Set the token decimals to 2
    .setDecimals(2)
    //Set the initial supply of the token to 10,000
    .setInitialSupply(1000000)
    // Configure token access permissions: treasury account, admin, freezing
    .setTreasuryAccountId(operatorId)
    //Set the admin key of the the token to the operator account
    .setAdminKey(operatorKey) 
    //Set the freeze default value to false
    .setFreezeDefault(false)
    // Freeze the transaction to prepare for signing
    .freezeWith(client);
    
// Get the transaction ID of the transaction. 
// The SDK automatically generates and assigns a transaction ID when the transaction is created
const tokenCreateTxId = tokenCreateTx.transactionId;
logger.log('The token create transaction ID: ', tokenCreateTxId.toString());
```
{% endcode %}
{% endtab %}

{% tab title="Java" %}
{% code title="ScriptHtsFt.java" %}
```java
// Create a HTS token create transaction
TokenCreateTransaction tokenCreateTx = new TokenCreateTransaction()
     //Set the transaction memo
     .setTransactionMemo("Hello Future World token - xyz")
     // HTS "TokenType.FungibleCommon" behaves similarly to ERC20
     .setTokenType(TokenType.FUNGIBLE_COMMON)
     // Configure token options: name, symbol, decimals, initial supply
     .setTokenName("htsFt coin")
     // Set the token symbol
     .setTokenSymbol("HTSFT")
     // Set the token decimals to 2
     .setDecimals(2)
     // Set the initial supply of the token to 1,000,000
     .setInitialSupply(1_000_000)
     // Configure token access permissions: treasury account, admin, freezing
     .setTreasuryAccountId(operatorId)
     // Set the freeze default value to false
     .setFreezeDefault(false)
     //Freeze the transaction and prepare for signing
     .freezeWith(client);

// Get the transaction ID of the transaction. The SDK automatically generates and assigns a transaction ID when the transaction is created
TransactionId tokenCreateTxId = tokenCreateTx.getTransactionId();
System.out.println("The token create transaction ID: " + tokenCreateTxId.toString());
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```go
// Create the token create transaction
tokenCreateTx, _ := hedera.NewTokenCreateTransaction().
     //Set the transaction memo
     SetTransactionMemo("Hello Future World token - xyz").
     // HTS `TokenType.FungibleCommon` behaves similarly to ERC20
     SetTokenType(hedera.TokenTypeFungibleCommon).
     // Configure token options: name, symbol, decimals, initial supply
     SetTokenName(`htsFt coin`).
     // Set the token symbol
     SetTokenSymbol("HTSFT").
     // Set the token decimals to 2
     SetDecimals(2).
     // Set the initial supply of the token to 1,000,000
     SetInitialSupply(1_000_000).
     // Configure token access permissions: treasury account, admin, freezing
     SetTreasuryAccountID(operatorId).
     // Set the freeze default value to false
     SetFreezeDefault(false).
     //Freeze the transaction and prepare for signing
     FreezeWith(client)

// Get the transaction ID of the transaction. The SDK automatically generates and assigns a transaction ID when the transaction is created
tokenCreateTxId := tokenCreateTx.GetTransactionID()
fmt.Printf("The token create transaction ID: %s\n", tokenCreateTxId.String())
```
{% endtab %}
{% endtabs %}

<details>

<summary><strong>Key terminology for HTS token create transaction</strong></summary>

* [**Token Type**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/token-service/token-types): Fungible tokens, declared using `TokenType.FungibleCommon`, may be thought of as analogous to _ERC20_ tokens. Note that HTS also supports another token type, `TokenType.NonFungibleUnique`, which may be thought of as analogous to _ERC721_ tokens.
* **Token Name**: This is the full name of the token. For example, "Singapore Dollar".
* **Token Symbol**: This is the abbreviation of the token's name. For example, "SGD".
* **Decimals**: This is the number of decimal places the currency uses. For example, `2` mimics "cents", where the smallest unit of the token is 0.01 (1/100) of a single token.
* **Initial Supply**: This is the number of units of the token to "mint" when first creating the token. Note that this is specified in the smallest units, so `1_000_000` initial supply when decimals is 2, results in `10_000` full units of the token being minted. It might be easier to think about it as "one million cents equals ten thousand dollars".
* **Treasury Account ID**: This is the account for which the initial supply of the token is credited. For example, using `operatorId` in this examplewould mean that your specified testnet account receives all the tokens when they are minted.
* [**Admin Key**](https://docs.hedera.com/hedera/sdks-and-apis/sdks/token-service/define-a-token#token-properties): This is the key that is authorized to administrate this token. For example, using `operatorKey` would mean that your testnet account key would authorize (required to sign related transactions) to perform actions such as minting additional supply.

</details>

### Sign and Submit the Token Create Transaction

The transaction must be signed using the operator's private key. This step ensures that the transaction is authenticated and authorized by the operator. Once signed, the transaction is submitted to the Hedera Testnet, where it will be processed and validated.

After submission, get the transaction receipt. The receipt contains important information about the transaction, such as its status and any resulting data. The receipt is used to confirm that the transaction was successfully processed and to get the new token ID. The token ID uniquely identifies the newly created token on the Hedera network and is required for any subsequent operations with the token.

{% tabs %}
{% tab title="JavaScript" %}
{% code title="script-hts-ft.js" %}
```javascript
// Sign the transaction with the operator's private key
const tokenCreateTxSigned = await tokenCreateTx.sign(operatorKey);

// Submit the signed transaction to the Hedera network
const tokenCreateTxSubmitted = await tokenCreateTxSigned.execute(client);

// Get the transaction receipt
const tokenCreateTxReceipt = await tokenCreateTxSubmitted.getReceipt(client);

// Get and log the newly created token ID to the console
const tokenId = tokenCreateTxReceipt.tokenId;
logger.log('tokenId:', tokenId.toString());
```
{% endcode %}
{% endtab %}

{% tab title="Java" %}
{% code title="ScriptHtsFt.java" %}
```java
// Sign the transaction with the operator's private key
TokenCreateTransaction tokenCreateTxSigned = tokenCreateTx.sign(operatorKey);

// Submit the transaction to the Hedera network
TransactionResponse tokenCreateTxSubmitted = tokenCreateTxSigned.execute(client);

// Get the transaction receipt
TransactionReceipt tokenCreateTxReceipt = tokenCreateTxSubmitted.getReceipt(client);

// Get the token ID
TokenId tokenId = tokenCreateTxReceipt.tokenId;
System.out.println("Token ID: " + tokenId.toString());
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code title="script-hts-ft.go" %}
```go
// Sign the transaction with the operator's private key
tokenCreateTxSigned := tokenCreateTx.Sign(operatorKey)

// Submit the transaction to the Hedera network
tokenCreateTxSubmitted, _ := tokenCreateTxSigned.Execute(client)

// Get the transaction receipt
tokenCreateTxReceipt, _ := tokenCreateTxSubmitted.GetReceipt(client)

// Get the newly created token ID
tokenId := tokenCreateTxReceipt.TokenID
fmt.Printf("Token ID: %s\n", tokenId.String())
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Query the Account Token Balance Mirror Node API&#x20;

Mirror nodes store the history of transactions that took place on the network. To query the token balance of your account, use the Mirror Node API with the path `/api/v1/tokens/{tokenId}`. This API endpoint allows you to get the balance information about a specific token by replacing `{tokenId}` with your actual token ID. Since the treasury account was configured as your own account, it will hold the entire initial supply of the token.&#x20;

* Specify `tokenId` within the URL path

The constructed `tokenVerifyMirrorNodeApiUrl` string should look like this:

{% tabs %}
{% tab title="JavaScript" %}
{% code title="script-hts-ft.js" %}
```javascript
const tokenVerifyMirrorNodeApiUrl =
    `https://testnet.mirrornode.hedera.com/api/v1/tokens/${tokenId.toString()}`;
```
{% endcode %}
{% endtab %}

{% tab title="Java" %}
{% code title="ScriptHtsFt.java" %}
```java
String tokenMirrorNodeApiUrl = 
    "https://testnet.mirrornode.hedera.com/api/v1/tokens/" + tokenId.toString();
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code title="script-hts-ft.go" %}
```go
tokenMirrorNodeApiUrl :=
    fmt.Sprintf(
    "https://testnet.mirrornode.hedera.com/api/v1/tokens/%s", tokenId.String())
```
{% endcode %}
{% endtab %}
{% endtabs %}

<details>

<summary>Learn more about Mirror Node APIs</summary>

You can explore the Mirror Node APIs interactively via its Swagger page: [Hedera Testnet Mirror Node REST API](https://testnet.mirrornode.hedera.com/api/v1/docs/#/).

You can perform the same Mirror Node API query as `tokenVerifyMirrorNodeApiUrl` above. This is what the relevant part of the Swagger page would look like when doing so:

![](../../.gitbook/assets/token-verify-mirror-node-api.png)

➡  You can learn more about the Mirror Nodes via its documentation: [REST API](https://docs.hedera.com/hedera/sdks-and-apis/rest-api).

</details>

***

## Step 3: Run the Token Create Transaction Script

In the terminal, `cd` into the `./hts` directory and run the token create transaction script:

{% tabs %}
{% tab title="JavaScript" %}
```bash
node script-hts-ft.js
```
{% endtab %}

{% tab title="Java" %}
```bash
gradle run
```
{% endtab %}

{% tab title="Go" %}
```bash
go mod tidy
go run script-hts-ft.go
```
{% endtab %}
{% endtabs %}

Sample output:

<pre><code>🏁 Hello Future World - HTS Fungible Token - start  …
Read .env file
Using account: 0.0.1455

🟣 Creating new HTS token  …
↪️ file:///workspace/hello-future-world-x/hts/script-hts-ft...
The token create transaction ID:  0.0.46495@1722971043.397070956
tokenId: 0.0.5878530 

🟣 View the token on HashScan  …
↪️ file:///workspace/hello-future-world-x/hts/script-hts-ft...
Paste URL in browser:
<strong> https://hashscan.io/testnet/token/0.0.46599 
</strong>
🟣 Get token data from the Hedera Mirror Node  …
↪️ file:///workspace/hello-future-world-x/hts/script-hts-ft...
The token Hedera Mirror Node API URL:
 https://testnet.mirrornode.hedera.com/api/v1/tokens/0.0.46599
The name of this token: bguiz coin
The total supply of this token: 1000000 

🎉 Hello Future World - HTS Fungible Token - complete  …
</code></pre>

Copy and paste the HashScan URL in your browser to view the token creation transaction details and verify that:

<figure><img src="https://72010995-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-LsUmP_7NoX2exvbv_Ps%2Fuploads%2Fgit-blob-d8c70bc0885d4c3566babd044f76ca2972484667%2Fhello-world--hts--token.drawing.svg?alt=media" alt=""><figcaption><p>HTS transaction in Hashscan, with annotated items to check.</p></figcaption></figure>

* The token should exist, and its "token ID" should match `tokenId`. **(1)**
* The "name" and "symbol" should be shown as the same values derived from your name (or nickname) that you chose earlier. **(2)**
* The "treasury account" should match `operatorId`. **(3)**
* Both the "total supply" and "initial supply" should be `10,000`. **(4)**

{% hint style="info" %}
**Note**: "total supply" and "initial supply" are not displayed as `1,000,000` because of the two decimal places configured. Instead, these are displayed as `10,000.00`.
{% endhint %}

***

## **Code Check ✅**

* [**Java**](https://github.com/hedera-dev/hello-future-world-java/blob/main/hts/src/main/java/hts/ScriptHtsFt.java)
* [**JavaScript**](https://github.com/hedera-dev/hello-future-world-js/blob/main/hts/script-hts-ft.js)
* [**Go**](https://github.com/hedera-dev/hello-future-world-go/blob/main/hts/script-hts-ft.go)

***

## Complete <a href="#complete" id="complete"></a>

Congratulations, you have completed the **Create a Token** tutorial in the Getting Started series for the Web2 Developers learning path!  🎉🎉🎉!

You have learned how to:

* [x] Create a new fungible token using HTS.
* [x] Query the transaction via Mirror Node API.
* [x] View your transaction on a Mirror Node Explorer.

***

## Next Steps

Continue building on Hedera with another tutorial in the series to explore more Hedera services.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th align="center"></th><th data-hidden><select multiple><option value="MB66z3fRYAlS" label="Accounts" color="blue"></option><option value="ZtnVYE29eArB" label="Topics" color="blue"></option><option value="TDPtoF12LPsm" label="Consensus Service" color="blue"></option><option value="FA8tkDPySmBL" label="Smart Contract Service" color="blue"></option><option value="VB4WYIKreZrZ" label="Token Service" color="blue"></option><option value="ILNVVpFoQpVk" label="Transactions" color="blue"></option><option value="CyJdTxHORHj7" label="Mirror Node" color="blue"></option><option value="6rVFgdKhOqrP" label="Keys &#x26; Signatures" color="blue"></option><option value="1ea0c60597qU" label="Queries" color="blue"></option><option value="iVX52i9gmTP4" label="Cryptocurrency" color="blue"></option></select></th><th data-hidden><select multiple><option value="WZtQiM8yq8qW" label="Accounts" color="blue"></option><option value="pr6gYjjHGJYO" label="Tokens" color="blue"></option><option value="0Vfu6ryyddVV" label="Smart Contracts" color="blue"></option><option value="paNqgxe1BUOW" label="EVM" color="blue"></option><option value="TtBMmEvGoSry" label="Transactions &#x26; Queries" color="blue"></option><option value="aNiFPpmkJ3uC" label="Keys &#x26; Signatures" color="blue"></option><option value="iq5mK7i8IkXM" label="Cryptocurrency" color="blue"></option><option value="RMY8nJInTzMv" label="Topics" color="blue"></option><option value="LPy3Gd1S3ilQ" label="Consensus Service" color="blue"></option><option value="zC34wTqgyTq8" label="Token Service" color="blue"></option><option value="dJ5dfPXSBQaX" label="Smart Contracts Service" color="blue"></option></select></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Create a Topic</strong>  </td><td>Learn how to create topics and publish messages using the <a href="../../support-and-community/glossary.md#hedera-consensus-service-hcs">Hedera Consensus Service</a> (HCS).</td><td align="center"><a href="create-a-topic.md"><strong>LEARN MORE</strong></a></td><td><span data-option="ZtnVYE29eArB">Topics, </span><span data-option="TDPtoF12LPsm">Consensus Service</span></td><td></td><td><a href="broken-reference">Broken link</a></td></tr><tr><td><strong>Transfer HBAR</strong> </td><td>Learn how to transfer HBAR, Hedera's native cryptocurrency, between accounts. </td><td align="center"><p></p><p><a href="transfer-hbar.md"><strong>LEARN MORE</strong></a></p></td><td><span data-option="MB66z3fRYAlS">Accounts, </span><span data-option="6rVFgdKhOqrP">Keys &#x26; Signatures, </span><span data-option="ILNVVpFoQpVk">Transactions, </span><span data-option="1ea0c60597qU">Queries</span></td><td></td><td><a href="broken-reference">Broken link</a></td></tr></tbody></table>

{% hint style="info" %}
**Have questions?** Join the [Hedera Discord](https://hedera.com/discord) and post them in the [`developer-general`](https://discord.com/channels/373889138199494658/373889138199494660) channel or ask on [Stack Overflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph).
{% endhint %}
