---
description: >-
  How to verify a smart contract using the HashScan Smart Contract Verifier
  tool.
---

# How to Verify a Smart Contract on HashScan (Beta)

Verifying smart contracts helps ensure the deployed bytecode matches the expected source files. HashScan Smart Contract Verifier is a tool that simplifies this process. This guide will show you the basic steps of smart contract verification using the HashScan Smart Contract Verifier tool.

{% hint style="info" %}
**Note**: This is an initial beta release, and both the HashScan user interface and API functionalities are scheduled for enhancements in upcoming updates.
{% endhint %}

***

## Table of Contents

1. [Prerequisites](how-to-verify-a-smart-contract-on-hashscan-beta.md#prerequisites)
2. [Step 1: Find the Contract](how-to-verify-a-smart-contract-on-hashscan-beta.md#step-1-find-the-contract-on-hashscan)
3. [Step 2: Import Your Files](how-to-verify-a-smart-contract-on-hashscan-beta.md#step-2-import-your-files)
4. [Step 3: Verify Contract](how-to-verify-a-smart-contract-on-hashscan-beta.md#step-3-verify-contract)
5. [Step 4: Verification Match](how-to-verify-a-smart-contract-on-hashscan-beta.md#step-4-verification-match)
6. [Step 5: View Verified Contract](how-to-verify-a-smart-contract-on-hashscan-beta.md#step-5-view-verified-contract)

***

## Prerequisites&#x20;

* Solidity source code file of the deployed smart contract.
* Solidity JSON (metadata) file of the deployed smart contract.
* EVM address where the smart contract is deployed on the Hedera network.

***

## Step 1: Find the Contract on HashScan

Open a web browser and navigate to [HashScan](https://hashscan.io/). Make sure you are on the correct Hedera network (Mainnet, Testnet, or Previewnet), and search for the deployed contract address in the search bar at the top of the page. Scroll to the _**Contract Details**_ section and click on _**Verify Contract**_. This will take you to the HashScan Smart Contract Verifier page in a new browser tab.&#x20;

<figure><img src="../../.gitbook/assets/contract hashscan details.png" alt=""><figcaption></figcaption></figure>

***

## Step 2: Import Your Files

Import the Solidity JSON file once you're on the HashScan Smart Contract Verifier page. The `.json` [metadata file](../../core-concepts/smart-contracts/verifying-smart-contracts-beta.md#the-metadata-file) can be found under `artifacts/build-info` in your Hardhat project repository. The file usually has a name correlating with your smart contract and a hash value to differentiate it from other compilations. For example, `HelloWorld.json`.

<figure><img src="../../.gitbook/assets/verifier.png" alt=""><figcaption><p>HashScan Smart Contract Verifier Interface</p></figcaption></figure>

***

## Step 3: Verify Contract

After successfully importing the JSON metadata file, you'll find an input field labeled "_**Address**_." This is where you will paste the address of the deployed contract address corresponding to the contract metadata you imported. Select the network your contract is deployed on from the "_**Chain**_" dropdown menu.

Once the contract address and network are correctly filled in, click the _**Verify**_ button to initiate the verification process. Sourcify will then compare the deployed contract bytecode to the source files you imported in the previous step.

<figure><img src="../../.gitbook/assets/verify.png" alt=""><figcaption></figcaption></figure>

***

## Step 4: Verification Match

The verification process will return either a [_<mark style="color:green;">Full (Perfect) Match</mark>_](https://docs.sourcify.dev/docs/full-vs-partial-match/#full-perfect-matches) or [_<mark style="color:green;">Partial Match</mark>_](https://docs.sourcify.dev/docs/full-vs-partial-match/#partial-matches) status. Let's review each verification status and what they mean:

* **Full (Perfect) Match**: Indicates the bytecode is a full match, including all the metadata. The contract source code and metadata settings are identical to the deployed version.
* **Partial Match**: Indicates the bytecode _mostly_ (partially) matches with the deployed contract, except for the metadata hash like comments or variable names. It is usually sufficient for most verification purposes.

<figure><img src="../../.gitbook/assets/helloworld perfect match.png" alt="" width="563"><figcaption></figcaption></figure>

To learn more about each verification match status, head over to the official Sourcify documentation [here](https://docs.sourcify.dev/docs/full-vs-partial-match/).&#x20;

***

## Step 5: View Verified Contract

To view the verified contract, scroll to the _**Contract Details**_ section and click _**View Contract**_.

<figure><img src="../../.gitbook/assets/view contract.png" alt=""><figcaption></figcaption></figure>

Once you click _**View Contract**_, you'll be directed to the Contract Lookup page, which summarizes the contract verification details.

<figure><img src="../../.gitbook/assets/contract lookup (1).png" alt=""><figcaption></figcaption></figure>

***

## Additional Resources

{% embed url="https://verify.hashscan.io/" %}
[verify.hashscan.io/](https://verify.hashscan.io/)
{% endembed %}

{% embed url="https://hashscan.io/" %}

{% embed url="https://docs.sourcify.dev/" %}

{% embed url="https://docs.soliditylang.org/" %}
