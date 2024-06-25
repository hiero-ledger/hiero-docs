# Submit Message to Private Topic

## Summary

In the previous tutorial, "Submit Your First Message," you have learned how to submit a message to a **public topic**. It means anyone can send a message to the topic you created because you didn't set a [Submit Key](https://docs.hedera.com/hedera/sdks-and-apis/sdks/consensus-service/create-a-topic#private-topic).

When setting a _Submit Key,_ your topic becomes a **private topic** because each message needs to be signed by the Submit Key. Therefore, you can control who can submit messages to your topic. Of course, the data is still public, as is all data on a public ledger, but we say the topic is private because the topic is restricted by who can submit messages to it.

***

## Prerequisites

We recommend you complete the "Submit Your First Message" tutorial [here](submit-your-first-message.md) to get a basic understanding of the Hedera Consensus Service. This example does not build upon the previous examples.

✅ _You can find a full_ [_code check_](submit-message-to-private-topic.md#code-check) _for this tutorial at the bottom of this page._

***

## Table of Contents

1. [Create Private Topic](submit-message-to-private-topic.md#1.-create-a-private-topic)
2. [Subscribe to Topic](submit-message-to-private-topic.md#2.-subscribe-to-a-topic)
3. [Submit Message](submit-message-to-private-topic.md#3.-submit-a-message)
4. [Code Check](submit-message-to-private-topic.md#code-check)

***

## 1. Create a private topic

To create a private topic, you will use [_<mark style="color:purple;">**`setSubmitKey()`**</mark>_](https://docs.hedera.com/hedera/sdks-and-apis/sdks/consensus-service/create-a-topic#methods) to set a Submit Key. This key needs to sign all messages someone sends to the topic. A message will be rejected if you don't sign the message or sign with an incorrect key. The cost of creating a private topic is the same as a public topic: [**$0.01**](https://docs.hedera.com/hedera/networks/mainnet/fees#consensus-service).

{% tabs %}
{% tab title="Java" %}

```java
// Create a new topic
TransactionResponse txResponse = new TopicCreateTransaction()
   .setSubmitKey(myPrivateKey.getPublicKey())
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
let txResponse = await new TopicCreateTransaction()
    .setSubmitKey(myPrivateKey.publicKey)
    .execute(client);

// Grab the newly generated topic ID
let receipt = await txResponse.getReceipt(client);
console.log(`Your topic ID is: ${receipt.topicId}`);

// Wait 5 seconds between consensus topic creation and subscription creation
await new Promise((resolve) => setTimeout(resolve, 5000));
```

{% endtab %}

{% tab title="Go" %}

```go
// Create a new topic
transactionResponse, err := hedera.NewTopicCreateTransaction().
	SetSubmitKey(myPrivateKey.PublicKey()).
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

The code used to subscribe to a public or private topic doesn't change. Anyone can listen to the messages you send to your private topic. You need to provide the [_<mark style="color:purple;">**`TopicMessageQuery()`**</mark>_](../../sdks-and-apis/sdks/consensus-service/get-topic-message.md) with your topic ID to subscribe to it.

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

```javascript
// Subscribe to the topic
new TopicMessageQuery()
  .setTopicId(topicId)
  .subscribe(client, null, (message) => {
    let messageAsString = Buffer.from(message.contents, "utf8").toString();
    console.log(
      `${message.consensusTimestamp.toDate()} Received: ${messageAsString}`
    );
  });
```

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

Now you are ready to submit a message to your private topic. To do this, you will use [_<mark style="color:purple;">**`TopicMessageSubmitTransaction()`**</mark>_](../../sdks-and-apis/sdks/consensus-service/submit-a-message.md). However, you need to sign this transaction with your Submit Key. The cost for sending a message to a private topic is the same as a public topic: [**$0.0001**](https://docs.hedera.com/hedera/networks/mainnet/fees#consensus-service).

{% tabs %}
{% tab title="Java" %}

```java
// Send message to private topic
TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
      .setTopicId(topicId)
      .setMessage("Submitkey set!")
      .freezeWith(client)
      .sign(myPrivateKey)
      .execute(client)

// Get the receipt of the transaction
TransactionReceipt receipt2 = submitMessage.getReceipt(client);

// Prevent the main thread from exiting so the topic message can be returned and printed to the console
Thread.sleep(30000);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Send message to private topic
let submitMsgTx = await new TopicMessageSubmitTransaction({
  topicId: topicId,
  message: "Submitkey set!",
})
.freezeWith(client)
.sign(myPrivateKey);

let submitMsgTxSubmit = await submitMsgTx.execute(client);

// Get the receipt of the transaction
let getReceipt = await submitMsgTxSubmit.getReceipt(client);

// Get the status of the transaction
const transactionStatus = getReceipt.status;
console.log("The message transaction status " + transactionStatus.toString());
```

{% endtab %}

{% tab title="Go" %}

```go
// Prepare message to send to private topic
submitMessageTx, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Submitkey set!")).
	SetTopicID(topicID).
	FreezeWith(client)

if err != nil {
	println(err.Error(), ": error freezing topic message submit transaction")
	return
}

// Sign message with submit key
submitMessageTx.Sign(myPrivateKey)

// Submit message
submitTxResponse, err := submitMessageTx.Execute(client)
if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}

// Get the receipt of the transaction
receipt, err := submitTxResponse.GetReceipt(client)

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

<pre class="language-java"><code class="lang-java">import com.hedera.hashgraph.sdk.*;
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
                .setSubmitKey(myPrivateKey.getPublicKey())
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

        // Send message to private topic
<strong>        TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
</strong>              .setTopicId(topicId)
              .setMessage("Submitkey set!")
              .freezeWith(client)
              .sign(myPrivateKey)
              .execute(client)

        // Get the receipt of the transaction
        TransactionReceipt receipt2 = submitMessage.getReceipt(client);

        // Wait before the main thread exits to return the topic message to the console
        Thread.sleep(30000);
    }
}
</code></pre>

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
  TopicCreateTransaction,
  TopicMessageQuery,
  TopicMessageSubmitTransaction,
} = require("@hashgraph/sdk");

// Grab the OPERATOR_ID and OPERATOR_KEY from the .env file
const myAccountId = process.env.MY_ACCOUNT_ID;
const myPrivateKey = process.env.MY_PRIVATE_KEY;

// Build Hedera testnet and mirror node client
const client = Client.forTestnet();

// Set the operator account ID and operator private key
client.setOperator(myAccountId, myPrivateKey);

async function submitPrivateMessage() {
  // Create a new topic
  let txResponse = await new TopicCreateTransaction()
    .setSubmitKey(myPrivateKey.publicKey)
    .execute(client);

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

  // Send message to private topic
  let submitMsgTx = await new TopicMessageSubmitTransaction({
    topicId: topicId,
    message: "Submitkey set!",
  })
  .freezeWith(client)
  .sign(myPrivateKey);

  let submitMsgTxSubmit = await submitMsgTx.execute(client);
  let getReceipt = await submitMsgTxSubmit.getReceipt(client);

  // Get the status of the transaction
  const transactionStatus = getReceipt.status;
  console.log("The message transaction status: " + transactionStatus.toString());
}

submitPrivateMessage();
```

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
		SetSubmitKey(myPrivateKey.PublicKey()).
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

	//Create the query to subscribe to a topic
	_, err = hedera.NewTopicMessageQuery().
		SetTopicID(topicID).
		Subscribe(client, func(message hedera.TopicMessage) {
			fmt.Println(message.ConsensusTimestamp.String(), "received topic message ", string(message.Contents), "\r")
		})
        
        // Prepare message to send to private topic
	submitMessageTx, err := hedera.NewTopicMessageSubmitTransaction().
		SetMessage([]byte("Submitkey set!")).
		SetTopicID(topicID).
		FreezeWith(client)
	
	if err != nil {
		println(err.Error(), ": error freezing topic message submit transaction")
		return
	}
	
	// Sign message with submit key
	submitMessageTx.Sign(myPrivateKey)
	
	// Submit message
	submitTxResponse, err := submitMessageTx.Execute(client)
	if err != nil {
		println(err.Error(), ": error submitting to topic")
		return
	}
	
	// Get the receipt of the transaction
	receipt, err := submitTxResponse.GetReceipt(client)
	
	// Get the transaction status
	transactionStatus := receipt.Status
	fmt.Println("The message transaction status " + transactionStatus.String())
	
	// Prevent the program from exiting to display the message from the mirror node to the console
	time.Sleep(30000)
    }
```

</details>

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
