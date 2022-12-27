# Submit Your First Message

![](<../../.gitbook/assets/Screen Shot 2021-12-01 at 2.26.12 PM.png>)

## Summary

With the Hedera Consensus Service, you can develop applications like stock markets, audit logs, stable coins, or new network services that require high throughput and decentralized trust. This is made possible by having direct access to the native speed, security, and fair ordering guarantees of the hashgraph consensus algorithm, with the full trust of the Hedera ledger.

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

{% content-ref url="../../getting-started/environment-set-up.md" %}
[environment-set-up.md](../../getting-started/environment-set-up.md)
{% endcontent-ref %}

## 1. Create your first topic

To create your first topic, you will use the _<mark style="color:purple;">**`TopicCreateTransaction()`**</mark>_, set its properties, and submit it to the Hedera network. A public topic that anyone can submit messages to does not require any other properties to be set.

If you would like to create a private topic you can optionally set a topic key ([_`setSubmitKey()`_](https://docs.hedera.com/guides/docs/sdks/consensus/create-a-topic#methods)). This means that messages submitted to this topic will require the topic key to sign the message submit transaction. If the topic key does not sign the message submit transaction, the message will not be submitted to the topic.

After submitting the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt.

{% tabs %}
{% tab title="Java" %}
```java
//Create a new topic
TransactionResponse txResponse = new TopicCreateTransaction()
   .execute(client);

//Get the receipt
TransactionReceipt receipt = txResponse.getReceipt(client);
        
//Get the topic ID
TopicId topicId = receipt.topicId;

//Log the topic ID
System.out.println("Your topic ID is: " +topicId);

// Wait 5 seconds between consensus topic creation and subscription creation
Thread.sleep(5000);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a new topic
let txResponse = await new TopicCreateTransaction().execute(client);

//Get the receipt of the transaction
let receipt = await txResponse.getReceipt(client);

//Grab the new topic ID from the receipt
let topicId = receipt.topicId;

//Log the topic ID
console.log(`Your topic ID is: ${topicId}`);

// Wait 5 seconds between consensus topic creation and subscription 
await new Promise((resolve) => setTimeout(resolve, 5000));
```
{% endtab %}

{% tab title="Go" %}
```go
//Create a new topic
transactionResponse, err := hedera.NewTopicCreateTransaction().
	Execute(client)

if err != nil {
	println(err.Error(), ": error creating topic")
	return
}

//Get the topic create transaction receipt
transactionReceipt, err := transactionResponse.GetReceipt(client)

if err != nil {
	println(err.Error(), ": error getting topic create receipt")
	return
}

//Get the topic ID from the transaction receipt
topicID := *transactionReceipt.TopicID

//Log the topic ID to the console
fmt.Printf("topicID: %v\n", topicID)
```
{% endtab %}
{% endtabs %}

## 2. Subscribe to a topic

After you create the topic, you will want to subscribe to the topic via a Hedera mirror node. Subscribing to a topic via a Hedera mirror node allows you to receive the stream of messages that are being submitted to it.

The Hedera testnet client already establishes a connection to a Hedera mirror node. You can set a custom mirror node by calling _<mark style="color:purple;">**`client.SetMirrorNetwork()`**</mark>_. Please note that you can currently subscribe to Hedera Consensus Service (HCS) topics via [gRPC API](https://docs.hedera.com/guides/docs/mirror-node-api/hedera-consensus-service-api-1) only, so remember to set the mirror node's host and port accordingly.

To subscribe to a topic, you will use _<mark style="color:purple;">**`TopicMessageQuery()`**</mark>_. You will provide it the topic ID to subscribe to, the Hedera mirror node client information, and the topic message contents to return.

{% tabs %}
{% tab title="Java" %}
```java
//Subscribe to the topic
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
//Create the query to subscribe to a topic
new TopicMessageQuery()
    .setTopicId(topicId)
    .subscribe(client, null, (message) => {
        let messageAsString = Buffer.from(message.contents, "utf8").toString();
        console.log(`${message.consensusTimestamp.toDate()} Received: ${messageAsString}`);
    });
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the query to subscribe to a topic
_, err = hedera.NewTopicMessageQuery().
	SetTopicID(topicID).
	Subscribe(client, func(message hedera.TopicMessage) {
		fmt.Println(message.ConsensusTimestamp.String(), "received topic message ", string(message.Contents), "\r")
   })
```
{% endtab %}
{% endtabs %}

## 3. Submit a message

Now you are ready to submit your first message to the topic. To do this, you will use _<mark style="color:purple;">**`TopicMessageSubmitTransaction().`**</mark>_ For this transaction, you will provide the topic ID and the message to submit to it.

{% tabs %}
{% tab title="Java" %}
```java
//Submit a message to a topic
TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
      .setTopicId(topicId)
      .setMessage("hello, HCS!")
      .execute(client);

//Get the receipt of the transaction
 TransactionReceipt receipt2 = submitMessage.getReceipt(client);

//Prevent the main thread from existing so the topic message can be returned and printed to the console
Thread.sleep(30000);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Send one message
let sendResponse = await new TopicMessageSubmitTransaction({
	topicId: topicId,
	message: "Hello, HCS!",
}).execute(client);

//Get the receipt of the transaction
const getReceipt = await sendResponse.getReceipt(client);

//Get the status of the transaction
const transactionStatus = getReceipt.status
console.log("The message transaction status " + transactionStatus)
```
{% endtab %}

{% tab title="Go" %}
```go
//Send "Hello, HCS!" to the topic
submitMessage, err := hedera.NewTopicMessageSubmitTransaction().
	SetMessage([]byte("Hello, HCS!")).
	SetTopicID(topicID).
	Execute(client)

if err != nil {
	println(err.Error(), ": error submitting to topic")
	return
}

//Get the receipt of the transaction
receipt, err := submitMessage.GetReceipt(client)

//Get the transaction status
transactionStatus := receipt.Status
fmt.Println("The message transaction status " + transactionStatus.String())

//Prevent the program from exiting to display the message from the mirror to the console
time.Sleep(30000)
```
{% endtab %}
{% endtabs %}

## Code Check ✅

{% tabs %}
{% tab title="Java" %}
```java
import com.hedera.hashgraph.sdk.*;
import io.github.cdimascio.dotenv.Dotenv;

import java.nio.charset.StandardCharsets;
import java.util.concurrent.TimeoutException;

public class CreateTopicTutorial {
    public static void main(String[] args) throws TimeoutException, PrecheckStatusException, ReceiptStatusException, InterruptedException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));

        //Build your Hedera client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);

        //Create a new topic
        TransactionResponse txResponse = new TopicCreateTransaction()
                .execute(client);

        //Get the receipt
        TransactionReceipt receipt = txResponse.getReceipt(client);

        //Get the topic ID
        TopicId topicId = receipt.topicId;

        //Log the topic ID
        System.out.println("Your topic ID is: " +topicId);

        // Wait 5 seconds between consensus topic creation and subscription creation
        Thread.sleep(5000);

        //Subscribe to the topic
        new TopicMessageQuery()
                .setTopicId(topicId)
                .subscribe(client, resp -> {
                    String messageAsString = new String(resp.contents, StandardCharsets.UTF_8);
                    System.out.println(resp.consensusTimestamp + " received topic message: " + messageAsString);
                });

        //Submit a message to a topic
        TransactionResponse submitMessage = new TopicMessageSubmitTransaction()
                .setTopicId(topicId)
                .setMessage("hello, HCS!")
                .execute(client);

        //Get the receipt of the transaction
        TransactionReceipt receipt2 = submitMessage.getReceipt(client);

        //Wait before the main thread exits to return the topic message to the console
        Thread.sleep(30000);

    }
}
```
{% endtab %}

{% tab title="JavaScript" %}
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
const operatorId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorKey = PrivateKey.fromString(process.env.OPERATOR_KEY);

// Build Hedera testnet and mirror node client
const client = Client.forTestnet();

// Set the operator account ID and operator private key
client.setOperator(operatorId, operatorKey);

async function main() {
    //Create a new topic
    let txResponse = await new TopicCreateTransaction().execute(client);
    
    //Grab the newly generated topic ID
    let receipt = await txResponse.getReceipt(client);
    let topicId = receipt.topicId;
    console.log(`Your topic ID is: ${topicId}`);
    
    // Wait 5 seconds between consensus topic creation and subscription creation
    await new Promise((resolve) => setTimeout(resolve, 5000));
    
    //Create the query
    new TopicMessageQuery().setTopicId(topicId).subscribe(client, null, (message) => {
        let messageAsString = Buffer.from(message.contents, "utf8").toString();
        console.log(`${message.consensusTimestamp.toDate()} Received: ${messageAsString}`);
    });
    
    // Send one message
    let sendResponse = await new TopicMessageSubmitTransaction({
        topicId: topicId,
        message: "Hello, HCS!",
    }).execute(client);
    const getReceipt = await sendResponse.getReceipt(client);
    
    //Get the status of the transaction
    const transactionStatus = getReceipt.status
    console.log("The message transaction status: " + transactionStatus)
}
main();
```
{% endtab %}

{% tab title="Go" %}
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

	//Loads the .env file and throws an error if it cannot load the variables from that file corectly
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load enviroment variables from .env file. Error:\n%v\n", err))
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

	//Create a new topic
	transactionResponse, err := hedera.NewTopicCreateTransaction().
		Execute(client)

	if err != nil {
		println(err.Error(), ": error creating topic")
		return
	}

	//Get the topic create transaction receipt
	transactionReceipt, err := transactionResponse.GetReceipt(client)

	if err != nil {
		println(err.Error(), ": error getting topic create receipt")
		return
	}

	//Get the topic ID from the transaction receipt
	topicID := *transactionReceipt.TopicID

	//Log the topic ID to the console
	fmt.Printf("topicID: %v\n", topicID)

	//Create the query to subscribe to a topic
	_, err = hedera.NewTopicMessageQuery().
		SetTopicID(topicID).
		Subscribe(client, func(message hedera.TopicMessage) {
			fmt.Println(message.ConsensusTimestamp.String(), "received topic message ", string(message.Contents), "\r")
		})
        
        //Submit a message to the topic
	submitMessage, err := hedera.NewTopicMessageSubmitTransaction().
		SetMessage([]byte("Hello, HCS!")).
		SetTopicID(topicID).
		Execute(client)

	if err != nil {
		println(err.Error(), ": error submitting to topic")
		return
	}
        
        //Get the transaction receipt
	receipt, err := submitMessage.GetReceipt(client)
        
        //Log the transaction status
	transactionStatus := receipt.Status
	fmt.Println("The transaction message status " + transactionStatus.String())
    
        //Prevent the program from exiting to display the message from the mirror to the console
	time.Sleep(30 * time.Second)
    }
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Have a question? [Ask it on StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
