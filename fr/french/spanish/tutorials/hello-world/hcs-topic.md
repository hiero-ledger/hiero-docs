---
description: "Hello World sequence: Create a new topic Hedera Consensus Service (HCS), and publish a message to this topic."
---

# HCS: Topic

## What you will accomplish

- [ ] Create a new Topic on HCS
- [ ] Publish a new message to this Topic

***

## Prerequisites

Before you begin, you should have **completed** the following Hello World sequence:

- [x] [create-fund-account.md](create-fund-account.md "mention")

***

## Get started: Set up project

To follow along, start with the `main` branch, which is the _default branch_ of the repo. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence. If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file and populate it as required.
{% endhint %}

In the terminal, from the `hello-future-world` directory, enter the subdirectory for this sequence.

```shell
cd 06-hcs-topic/
```

Reuse the `.env` file by copying the one that you have previously created into the directory for this sequence.

```shell
cp ../00-create-fund-account/.env ./
```

<details>

<summary>Check that you have copied the `.env` file correctly</summary>

To do so, use the `pwd` command to check that you are indeed in the right subdirectory within the repo.

```shell
pwd
```

This should output a path that ends with `/hello-future-world/06-hcs-topic`. If not, you will need to start over.

```
/some/path/hello-future-world/06-hcs-topic
```

Next, use the `ls` command to check that the `.env` file has been copied into this subdirectory.

```shell
ls -a
```

The first few line of the output should look display `.env`. If not, you'll need to start over.

```
.
..
.env
```

</details>

Next, install the dependencies using `npm`.

```shell
npm install
```

Then open the `script-hcs-topic.js` file in a code editor, such as VS Code.

***

## Write the script

An almost complete script has already been prepared for you, `script-hcs-topic.js`, and you will only need to make a few modifications (outlined below) to run successfully.

### Step 1: Create a topic

To create a new HCS topic, submit a `TopicCreateTransaction`, from the Hedera SDK. This transaction does not need any input parameters.

{% code title="script-hcs-topic.js" overflow="wrap" %}

```js
    const topicCreateTx = await
        new TopicCreateTransaction()
        .freezeWith(client);
```

{% endcode %}

Note that once this transaction has been completed, it is important to extract the **topicID** from it, as you will need this to submit messages to it. This has already been done, and no modification is necessary.

{% code title="script-hcs-topic.js" overflow="wrap" %}

```js
    const topicId = topicCreateTxReceipt.topicId;
```

{% endcode %}

### Step 2: Publish message to topic

Once the topic has been registered, you're ready to submit messages to it. Messages may be any data up to 1024 bytes.

To do so, submit a `TopicMessageSubmitTransaction` from the Hedera SDK. This transaction needs 2 input parameters, `topicId` and `message`. Use the `topicId` obtained from the receipt of the `TopicCreateTransaction` submitted earlier. For `message`, use a string `Hello HCS -` followed by your name (or nickname). For example, if you used `bguiz`, the string should be `Hello HCS - bguiz`.

{% code title="script-hcs-topic.js" overflow="wrap" %}

```js
    const topicMsgSubmitTx = await
        new TopicMessageSubmitTransaction({
            topicId: topicId,
            message: 'Hello HCS - bguiz',
        })
        .freezeWith(client);
```

{% endcode %}

***

## Run the script

In the terminal, run the script using the following command:

```shell
node script-hcs-topic.js
```

You should see output similar to the following:

```
Topic created. Waiting a few seconds for propagation...
accountId: 0.0.3643569
topicId: 0.0.3791978
topicExplorerUrl: https://hashscan.io/testnet/topic/0.0.3791978
topicCreateTxId: 0.0.3643569@1711530800.714890962
topicMsgSubmitTxId: 0.0.3643569@1711530810.343147411
topicMessageMirrorUrl: https://testnet.mirrornode.hedera.com/api/v1/topics/0.0.3791978/messages/1
```

To verify that both the `TopicCreateTransaction` and `TopicMessageSubmitTransaction` have worked, check Hashscan (the network explorer). To do so, open `topicExplorerUrl` in your browser and check that:

<img src="../../.gitbook/assets/hello-world--hcs--topic.drawing.svg" alt="HCS topic in Hashscan, with annotated items to check." class="gitbook-drawing">

- The topic exists, and its topic ID matches `topicId` output by the script. **(1)**
- There is one entry in the topic, and its message is `Hello HCS -` followed by your name/ nickname. **(2)**

***

## Complete

Congratulations, you have completed the **Hedera Consensus Service** Hello World sequence! 🎉🎉🎉

You have learned how to:

- [x] Create a new Topic on HCS
- [x] Publish a new message to this Topic

***

## Next Steps

Now that you have completed this Hello World sequence, you have interacted with Hedera Consensus Service (HCS). There are [other Hello World sequences](./) for Hedera Smart Contract Service (HSCS), Hedera File Service (HFS), and Hedera Token Service (HTS), which you may wish to check out next.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/), is intended to be used alongside this tutorial.

To skip ahead to the final state, use the `completed` branch. You may use this to compare your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

Alternatively, you may view the `completed` branch on Github: [`github.com/hedera-dev/hello-future-world/tree/completed/06-hcs-topic`](https://github.com/hedera-dev/hello-future-world/tree/completed/06-hcs-topic)

</details>

***

**Writer**: [Brendan](https://blog.bguiz.com/)
