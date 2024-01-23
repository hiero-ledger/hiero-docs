---
description: >-
  Hello World sequence: Store a file on Hedera File Service (HFS), and retrieve
  the same file from the network.
---

# HFS: Files

## What you will accomplish

* [ ] Upload a file onto HFS
* [ ] Retrieve the file from HFS

***

## Prerequisites

Before you begin, you should have completed the "Create and Fund Account" sequence:&#x20;

{% content-ref url="create-fund-account.md" %}
[create-fund-account.md](create-fund-account.md)
{% endcontent-ref %}

***

## Get started

### Set up project

To follow along, start with the `main` branch, which is the _default branch_ of the repo. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence. If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file and populate it as required.
{% endhint %}

In the terminal, from the `hello-future-world` directory, enter the subdirectory for this sequence.

```shell
cd 01-hfs-files-sdk/
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

This should output a path that ends with `/hello-future-world/01-hfs-files-sdk`. If not, you will need to start over.

```text
/some/path/hello-future-world/01-hfs-files-sdk
```

Next, use the `ls` command to check that the `.env` file has been copied into this subdirectory.

```shell
ls -a
```

The first few line of the output should look display `.env`. If not, you'll need to start over.

```text
.
..
.env
```

</details>

Next, install the dependencies using `npm`.

```shell
npm install
```

Then open the `script-hfs-files-sdk.js` file in a code editor, such as VS Code.

You will also need a file to write onto the network. Copy the sample text file provided.

```shell
cp my-file.txt.sample my-file.txt
```

Edit `my-file.txt` to replace `YOUR_NAME` with your name (or nickname) in a code editor.

***

### Write the script

An almost-complete script has already been prepared for you, `script-hfs-files-sdk.js`, and you will only need to make a few modifications (outlined below) for it to run successfully.

#### Step 1: File create transaction

The contents of `my-file.txt` have been read from disk, and stored in a `Buffer` named `localFileContents`.

Set the contents of `localFileContents` in `FileCreateTransaction`, to write your file onto Hedera Testnet.

```js
        .setContents(localFileContents.toString())
```

#### Step 2: File contents query

After the `FileCreateTransaction` has been executed, the response will contain a file ID. Read this file from the network, by querying it.

Set the file ID in `FileContentsQuery`.

```js
        .setFileId(fileId);
```

***

### Run the script

In the terminal, run the script using the following command:

```shell
node script-hfs-files-sdk.js
```

You should see output similar to the following:

```
fileId: 0.0.5835692
fileCreateTxId: 0.0.1186@1699277862.561525871
txExplorerUrl: https://hashscan.io/testnet/transaction/0.0.1186@1699277862.561525871
localFileContents: Hello future! - bguiz

networkFileContents: Hello future! - bguiz

```

To verify that both the `FileCreateTransaction` and `FileContentsQuery` have worked, check that `localFileContents` and `networkFileContents` are the same. This indicates that what has been stored on Hedera Testnet is the same as what you have on disk.

Open `txExplorerUrl` in your browser and check that:

* (1) The transaction exists
* (2) The "type" is shown as "FILE CREATE"

<img src="../../.gitbook/assets/hello-world--hfs--transaction.drawing.svg" alt="HFS transaction in Hashscan, with annotated items to check." class="gitbook-drawing">

***

## Complete

Congratulations, you have completed the **Hedera File Service** Hello World sequence! 🎉🎉🎉

You have learned how to:

* [x] Upload a file onto HFS
* [x] Retrieve the file from HFS

***

### Next Steps

Now that you have completed this Hello World sequence, you have interacted with Hedera File Service (HFS). There are [other Hello World sequences](./) for Hedera Smart Contract Service (HSCS), and Hedera Token Service (HTS), which you may wish to check out next.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/), is intended to be used alongside this tutorial.

To skip ahead to the final state, use the `completed` branch. This gives you the final state with which you can compare your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

To see the full set of differences between the initial and final states of the repo, you can use `diff`.

```shell
cd 01-hfs-files-sdk/
git diff main..completed -- ./
```

Alternatively, you may view the `diff` rendered on Github: [`hedera-dev/hello-future-world/compare/main..completed`](https://github.com/hedera-dev/hello-future-world/compare/main..completed) (This will show the `diff` for _all_ sequences.)

Note that the branch names are delimited by `..`, and not by `...`, as the latter finds the `diff` with the latest common ancestor commit, which _is not_ what we want in this case.

</details>

***

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody>
<tr><td align="center"><p>Writer: Brendan, DevRel Engineer</p><p><a href="https://github.com/bguiz">GitHub</a> | <a href="https://blog.bguiz.com">Blog</a></p></td><td><a href="https://blog.bguiz.com">https://blog.bguiz.com</a></td></tr>
<tr><td align="center"><p>Editor: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr>
<tr><td align="center"><p>Editor: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr>
<tr><td align="center"><p>Editor: Ryan Arndt, DevRel Education</p><p><a href="https://github.com/swirlds-ryan">GitHub</a> | <a href="https://www.linkedin.com/in/ryaneh/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ryaneh/">https://www.linkedin.com/in/ryaneh/</a></td></tr>
</tbody></table>

***
