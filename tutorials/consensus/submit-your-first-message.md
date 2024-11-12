# Submit Your First Message

## Summary

With the Hedera Consensus Service (HCS), you can develop applications like stock markets, audit logs, stablecoins, or new network services that require high throughput and decentralized trust. This is made possible by having direct access to the native speed, security, and fair ordering guarantees of the Hashgraph consensus algorithm, with the full trust of the Hedera ledger.

In short, HCS offers the validity of the order of events and transparency into the history of events without requiring a persistent history of transactions. To achieve this, [Mirror nodes](../../core-concepts/mirror-nodes/) store all transaction data so you can retrieve it to audit events.

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](https://portal.hedera.com/register).
* Set up your environment [here](../../getting-started/environment-set-up.md).

✅ _You can find a full_ [_code check_](submit-your-first-message.md#code-check) _for this tutorial at the bottom of this page._

***

## Table of Contents

1. [Create Topic](submit-your-first-message.md#id-1.-create-your-first-topic)
2. [Subscribe to Topic](submit-your-first-message.md#id-2.-subscribe-to-a-topic)
3. [Submit Message](submit-your-first-message.md#id-3.-submit-a-message)
4. [Code Check](submit-your-first-message.md#code-check)

***

## 1. Create your first topic

To create your first topic, you will use the _<mark style="color:blue;">**`TopicCreateTransaction()`**</mark>_, set its properties, and submit it to the Hedera network. In this tutorial, you will create a **public topic** by not setting any properties on the topic. This means that anyone can send messages to your topic.

If you would like to create a **private topic,** you can optionally set a topic key ([_`setSubmitKey()`_](https://docs.hedera.com/guides/docs/sdks/consensus/create-a-topic#methods)). This means that messages submitted to this topic require the topic key to sign. If the topic key does not sign a message, the message will not be submitted to the topic.

After submitting the transaction to the Hedera network, you can obtain the new topic ID by requesting the receipt. Creating a topic only costs you [**$0.01**](https://docs.hedera.com/hedera/networks/mainnet/fees#consensus-service).

{% tabs %}
{% tab title="Java" %}
```java
// Create a new topic
TransactionResponse txResponse = new TopicCreateTransaction()
   .execute(client);

// Get the receipt
TransactionReceipt receipt = txResponse.getReceipt(client);
        
// Get the topic ID
TopicId topicId = receipt.topicId;

// Log the topic ID
System.out.println("Your topic ID is: " +topicId);

// Wait 5 seconds between consensus topic creation and subscription creation
Thread.sleep(5000);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Create a new topic
let txResponse = await new TopicCreateTransaction().execute(client);

// Grab the newly generated topic ID
let receipt = await txResponse.getReceipt(client);
let topicId = receipt.topicId;
console.log(`Your topic ID is: ${topicId}`);

// Wait 5 seconds between consensus topic creation and subscription creation
await new Promise((resolve) => setTimeout(resolve, 5000));
```
{% endtab %}

{% tab title="Go" %}
```go
// Create a new topic
transactionResponse, err := hedera.NewTopicCreateTransaction().
	Execute(client)

if err != nil {
	println(err.Error(), ": error creating topic")
	return
}

// Get the topic create transaction receipt
transactionReceipt, err := transactionResponse.GetReceipt(client)

if err != nil {
	println(err.Error(), ": error getting topic create receipt")
	return
}

// Get the topic ID from the transaction receipt
topicID := *transactionReceipt.TopicID

//Log the topic ID to the console
fmt.Printf("topicID: %v\n", topicID)
```
{% endtab %}
{% endtabs %}

***

## 2. Subscribe to a topic

After you create the topic, you will want to subscribe to the topic via a Hedera mirror node. Subscribing to a topic via a Hedera mirror node allows you to receive the stream of messages that are being submitted to it.

{% hint style="info" %}
The _**Hedera Testnet**_ client already establishes a connection to a Hedera mirror node. You can set a custom mirror node by calling _<mark style="color:blue;">**`client.SetMirrorNetwork()`**</mark>_. Please note that you can subscribe to Hedera Consensus Service (HCS) topics via [gRPC API](https://docs.hedera.com/guides/docs/mirror-node-api/hedera-consensus-service-api-1) only. Remember to set the mirror node's host and port accordingly when dealing with another mirror node provider.
{% endhint %}

To subscribe to a topic, you will use [_<mark style="color:purple;">**`TopicMessageQuery()`**</mark>_](../../sdks-and-apis/sdks/consensus-service/get-topic-message.md). You will provide it with the topic ID to subscribe to, the Hedera mirror node client information, and the topic message contents to return.

{% tabs %}
{% tab title="Java" %}
```java
// Subscribe to the topic
new TopicMessageQuery()
    .setTopicId(topicId)
    .subscribe(client, resp -> {
            String messageAsString = new String(resp.contents, StandardCharsets.UTF_8);
            System.out.println(resp.consensusTimestamp + " received topic message: " + messageAsString);
    });
```
{% endtab %}

{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript"><strong>// Subscribe to the topic
</strong><strong>new TopicMessageQuery()
</strong>  .setTopicId(topicId)
  .subscribe(client, null, (message) => {
    let messageAsString = Buffer.from(message.contents, "utf8").toString();
    console.log(
      `${message.consensusTimestamp.toDate()} Received: ${messageAsString}`
    );
  });
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
// Subscribe to the topic
_, err = hedera.NewTopicMessageQuery().
	SetTopicID(topicID).
	Subscribe(client, func(message hedera.TopicMessage) {
		fmt.Println(message.ConsensusTimestamp.String(), "received topic message ", string(message.Contents), "\r")
   	})
```
{% endtab %}
{% endtabs %}

***

## 3. Submit a message

Now you are ready to submit your first message to the topic. To do this, you will use [_<mark style="color:purple;">**`TopicMessageSubmitTransaction()`**</mark>_](../../sdks-and-apis/sdks/consensus-service/submit-a-message.md). For this transaction, you will provide the topic ID and the message to submit to it. Each message you send to a topic costs you [**$0.0001**](https://docs.hedera.com/hedera/networks/mainnet/fees#consensus-service). In other words, you can send 10,000 messages for $1 on the Hedera Network.

{% tabs %}
{% tab title="Java" %}
```java
// Send message to the topic
TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
      .setTopicId(topicId)
      .setMessage("Hello, HCS!")
      .execute(client);

// Get the receipt of the transaction
 TransactionReceipt receipt2 = submitMessage.getReceipt(client);

// Prevent the main thread from exiting so the topic message can be returned and printed to the console
Thread.sleep(30000);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Send message to the topic
let sendResponse = await new TopicMessageSubmitTransaction({
	topicId: topicId,
	message: "Hello, HCS!",
}).execute(client);

// Get the receipt of the transaction
const getReceipt = await sendResponse.getReceipt(client);

// Get the status of the transaction
const transactionStatus = getReceipt.status
console.log("The message transaction status " + transactionStatus.toString())
```
{% endtab %}

{% tab title="Go" %}
```go
// Send message to the topic
submitMessage, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Hello, HCS!")).
	SetTopicID(topicID).
	Execute(client)

if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}

// Get the receipt of the transaction
receipt, err := submitMessage.GetReceipt(client)

// Get the transaction status
transactionStatus := receipt.Status
fmt.Println("The message transaction status " + transactionStatus.String())

// Prevent the program from exiting to display the message from the mirror node to the console
time.Sleep(30000)
```
{% endtab %}
{% endtabs %}

To conclude: The total cost to create a topic and send a message to it is **$0.0101.**

***

## Code Check ✅

<details>

<summary>Java</summary>

```java
import com.hedera.hashgraph.sdk.*;
import io.github.cdimascio.dotenv.Dotenv;

import java.nio.charset.StandardCharsets;
import java.util.concurrent.TimeoutException;

public class CreateTopicTutorial {
    public static void main(String[] args) throws TimeoutException, PrecheckStatusException, ReceiptStatusException, InterruptedException {

        // Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));

        // Build your Hedera client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

        // Create a new topic
        TransactionResponse txResponse = new TopicCreateTransaction()
                .execute(client);

        // Get the receipt
        TransactionReceipt receipt = txResponse.getReceipt(client);

        // Get the topic ID
        TopicId topicId = receipt.topicId;

        // Log the topic ID
        System.out.println("Your topic ID is: " +topicId);

        // Wait 5 seconds between consensus topic creation and subscription creation
        Thread.sleep(5000);

        // Subscribe to the topic
        new TopicMessageQuery()
                .setTopicId(topicId)
                .subscribe(client, resp -> {
                    String messageAsString = new String(resp.contents, StandardCharsets.UTF_8);
                    System.out.println(resp.consensusTimestamp + " received topic message: " + messageAsString);
                });

        // Send message to topic
        TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
                .setTopicId(topicId)
                .setMessage("Hello, HCS!")
                .execute(client);

        // Get the receipt of the transaction
        TransactionReceipt receipt2 = submitMessage.getReceipt(client);

        // Wait before the main thread exits to return the topic message to the console
        Thread.sleep(30000);

    }
}
```

</details>

<details>

<summary>JavaScript</summary>

<pre class="language-javascript"><code class="lang-javascript">console.clear();
require("dotenv").config();
const {
  AccountId,
  PrivateKey,
  Client,
  TopicCreateTransaction,
<strong>  TopicMessageQuery,
</strong>  TopicMessageSubmitTransaction,
} = require("@hashgraph/sdk");

// Grab the OPERATOR_ID and OPERATOR_KEY from the .env file
const myAccountId = process.env.MY_ACCOUNT_ID;
const myPrivateKey = process.env.MY_PRIVATE_KEY;

// Build Hedera testnet and mirror node client
const client = Client.forTestnet();

// Set the operator account ID and operator private key
client.setOperator(myAccountId, myPrivateKey);

async function submitFirstMessage() {
  // Create a new topic
  let txResponse = await new TopicCreateTransaction().execute(client);

  // Grab the newly generated topic ID
  let receipt = await txResponse.getReceipt(client);
  let topicId = receipt.topicId;
  console.log(`Your topic ID is: ${topicId}`);

  // Wait 5 seconds between consensus topic creation and subscription creation
  await new Promise((resolve) => setTimeout(resolve, 5000));

  // Create the topic
  new TopicMessageQuery()
    .setTopicId(topicId)
    .subscribe(client, null, (message) => {
      let messageAsString = Buffer.from(message.contents, "utf8").toString();
      console.log(
        `${message.consensusTimestamp.toDate()} Received: ${messageAsString}`
      );
    });

  // Send message to topic
  let sendResponse = await new TopicMessageSubmitTransaction({
    topicId: topicId,
    message: "Hello, HCS!",
  }).execute(client);
  const getReceipt = await sendResponse.getReceipt(client);

  // Get the status of the transaction
  const transactionStatus = getReceipt.status;
  console.log("The message transaction status: " + transactionStatus.toString());
}

submitFirstMessage();
</code></pre>

</details>

<details>

<summary>Go</summary>

```go
package main

import (
	"fmt"
	"os"
	"time"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {

	// Loads the .env file and throws an error if it cannot load the variables from that file corectly
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load enviroment variables from .env file. Error:\n%v\n", err))
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

	// Create a new topic
	transactionResponse, err := hedera.NewTopicCreateTransaction().
		Execute(client)

	if err != nil {
		println(err.Error(), ": error creating topic")
		return
	}

	// Get the topic create transaction receipt
	transactionReceipt, err := transactionResponse.GetReceipt(client)

	if err != nil {
		println(err.Error(), ": error getting topic create receipt")
		return
	}

	// Get the topic ID from the transaction receipt
	topicID := *transactionReceipt.TopicID

	// Log the topic ID to the console
	fmt.Printf("topicID: %v\n", topicID)

	// Create the query to subscribe to a topic
	_, err = hedera.NewTopicMessageQuery().
		SetTopicID(topicID).
		Subscribe(client, func(message hedera.TopicMessage) {
			fmt.Println(message.ConsensusTimestamp.String(), "received topic message ", string(message.Contents), "\r")
		})
        
        // Submit message to topic
	submitMessage, err := hedera.NewTopicMessageSubmitTransaction().
		SetMessage([]byte("Hello, HCS!")).
		SetTopicID(topicID).
		Execute(client)

	if err != nil {
		println(err.Error(), ": error submitting to topic")
		return
	}
        
        // Get the transaction receipt
	receipt, err := submitMessage.GetReceipt(client)
        
        // Log the transaction status
	transactionStatus := receipt.Status
	fmt.Println("The transaction message status " + transactionStatus.String())
    
        // Prevent the program from exiting to display the message from the mirror to the console
	time.Sleep(30 * time.Second)
    }
```

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Simi, Sr. Software Manager </p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan">LinkedIn</a></p></td><td></td><td></td><td><a href="https://www.linkedin.com/in/shunjan">https://www.linkedin.com/in/shunjan </a></td></tr><tr><td align="center"><p>Editor: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td></td><td></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr></tbody></table>
