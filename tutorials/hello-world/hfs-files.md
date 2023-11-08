---
description: >-
  Hello World sequence:
  Store a file on Hedera File Service (HFS),
  and retrieve the same file from the network.
---

# HFS: Files

## What you will accomplish

- ✅ Upload a file onto HFS
- ✅ Retrieve the file from HFS

The repo, [`github.com/hedera-dev/hello-future-world`](https://github.com/hedera-dev/hello-future-world/),
is intended to be used alongside this tutorial.

***

## Prerequisites

Before you begin, you should have completed the "Create and Fund Account" sequence:
[`docs.hedera.com/tutorials/hello-world/create-fund-account`](https://docs.hedera.com/tutorials/hello-world/create-fund-account/).

***

## Steps

### Set up project

To follow along, start with the `main` branch,
which is the *default branch* of the repo.
This gives you the initial state from which you can follow along
with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence.
If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file
and populate it as required.
{% endhint %}

In the terminal, reuse the `.env` file by copying
the one that you have previously created into the directory for this sequence.

```shell
cd 01-hfs-files-sdk/
cp ../00-create-fund-account/.env ./
```

Next, install the dependencies using `npm`.
Then open the script file in a code editor.

```shell
npm install
code script-hfs-files-sdk.js
```

You will also need a file to write onto the network.
Copy the sample text file provided,
and edit it to replace `YOUR_NAME` with your name.

```shell
cp my-file.txt.sample my-file.txt
code my-file.txt
```

***

### Write the script

An almost-complete script has already been prepared for you,
and you will only need to make a few modifications (outlined below)
for it to run successfully.

#### Step 1: File create transaction

The contents of `my-file.txt` have been read from disk,
and stored in a `Buffer`, `localFileContents`.

Set the contents of this in `FileCreateTransaction`,
so that your file is written onto the network.

```js
        .setContents(localFileContents.toString())
```

#### Step 2: File contents query

After the `FileCreateTransaction` has been executed,
the response contains a file ID.

You will be reading this file back from the network,
and check that it has worked,
and its contents are the same what you have on disk.

Set the file ID in `FileContentsQuery`.

```js
        .setFileId(fileId);
```

***

### Run the script

Run the script using the following command:

```shell
node script-hfs-files-sdk.js
```

You should see output similar to the following:

```text
fileId: 0.0.5835692
fileCreateTxId: 0.0.1186@1699277862.561525871
txExplorerUrl: https://hashscan.io/testnet/transaction/0.0.1186@1699277862.561525871
localFileContents: Hello future! - bguiz

networkFileContents: Hello future! - bguiz

```

Open the URL, that was output as `txExplorerUrl` above,
in your browser and check that:

- (1) The transaction exists
- (2) The "type" is shown as "FILE CREATE"

***

## Complete

Congratulations, you have completed this Hello World sequence! 🎉🎉🎉

***

### Next Steps

Now that you have completed this Hello World sequence,
you have interacted with Hedera File Service (HFS).
There are [other Hello World sequences](/tutorials/hello-world/)
for Hedera Smart Contract Service (HSCS),
and Hedera Token Service (HTS),
which you may wish to check out next.

***

## Cheat sheet

<details>

<summary>Skip to final state</summary>

To skip ahead to the final state, use the `completed` branch.
This gives you the final state with which you can compare
your implementation to the completed steps of the tutorial.

```shell
git fetch origin completed:completed
git checkout completed
```

To see the full set of differences between
the initial and final states of the repo,
you can use `diff`.

```shell
cd 01-hfs-files-sdk/
git diff main..completed -- ./
```

Alternatively, you may view the `diff` rendered on Github:
[`hedera-dev/hello-future-world/compare/main..completed`](https://github.com/hedera-dev/hello-future-world/compare/main..completed)
(This will show the `diff` for *all* sequences.)

{% hint style="info" %}
Note that the branch names are delimited by `..`, and not by `...`,
as the latter finds the `diff` with the latest common ancestor commit,
which *is not* what we want in this case.
{% endhint %}

</details>

***

<table data-card-size="large" data-view="cards">
<thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead>
<tbody>
<tr><td align="center"><p>Writer: Brendan, DevRel Engineer</p><p><a href="https://github.com/bguiz">GitHub</a> | <a href="https://blog.bguiz.com">Blog</a></p></td><td><a href="https://blog.bguiz.com">https://blog.bguiz.com</a></td></tr>
<tr><td align="center"><p>Editor: Abi Castro, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://twitter.com/ridley___">Twitter</a></p></td><td><a href="https://twitter.com/ridley___">https://twitter.com/ridley___</a></td></tr>
</tbody>
</table>
