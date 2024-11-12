# How to Create a Personal Access Token (API Key) on the Hedera Portal

In the Hedera Portal, the Personal Access Token (API key) is a new feature for seamless application development and management. This feature facilitates the process of account recreation and querying for account ID after a test network (Testnet or Previewnet) resets. Whenever the testnet is reset, developers have to manually log in to the portal, recreate an account, get the account ID, and input the new account ID into their development environment. Utilizing this new feature to generate an API key eliminates the need to manually recreate accounts. There are no prerequisites for this tutorial.

***

**By the end of this tutorial, you will know how to:**

* [Create a Hedera Portal profile](how-to-create-a-personal-access-token-api-key-on-the-hedera-portal.md#step-1-create-a-hedera-portal-profile)
* [Create a new token/API key](how-to-create-a-personal-access-token-api-key-on-the-hedera-portal.md#step-2-create-a-new-personal-access-token)
* [Make API calls ](how-to-create-a-personal-access-token-api-key-on-the-hedera-portal.md#step-3-api-calls-to-list-and-get-accounts)

***

## Step 1: Create a Hedera Portal Profile

To create your Hedera Portal profile, register [here](https://portal.hedera.com/register) and follow the instructions to complete your profile. Once you've completed setting up your profile, select the test network (Testnet or Previewnet) from the network drop-down menu and create an account.&#x20;

{% hint style="info" %}
_**Note**: For the purposes of this tutorial, we will be using the Hedera Testnet to demonstrate token creation, but the steps to create tokens for both Testnet and Previewnet are identical._
{% endhint %}

***

## Step 2: Create a New Personal Access Token

Navigate to the Hedera Portal token creation page by clicking on the profile icon in the top-right corner of the portal dashboard and select **Personal Access Tokens** from the dropdown menu. This page is dedicated to the creation and management of your personal access tokens. &#x20;

<figure><img src="../../.gitbook/assets/portal-token-creation-account-menu.png" alt="" width="340"><figcaption></figcaption></figure>

Once you're on the token management page, you will find the option to create a new token. Start by typing in a description for your token in the Description field. This description should ideally reflect the intended use or scope of the token. Click the <mark style="background-color:purple;">**CREATE TOKEN**</mark> button after entering the description.

<figure><img src="../../.gitbook/assets/portal-create-token.png" alt="" width="563"><figcaption><p><a href="https://portal.hedera.com/tokens">https://portal.hedera.com/tokens</a></p></figcaption></figure>

{% hint style="info" %}
_**🚨 Note**: Once your Personal Access Token (API key) is generated, please copy and securely save it in a location where only you have access. Be aware that once you navigate away from the page, the API key will no longer be visible. If you do not save your key, you will need to create a new one, as there is no way to retrieve it later._&#x20;
{% endhint %}

***

## Step 3: API Calls to List and Get Accounts

Copy your new API token key and save it in a secure place where only you can access it. This key will be used in the API calls below.&#x20;

<figure><img src="../../.gitbook/assets/portal-personal-access-token-copy-save.png" alt="" width="563"><figcaption></figcaption></figure>

**API calls using `curl`**

Either of these APIs will trigger the account to be recreated in the background if the account does not exist (due to a testnet reset).

<details>

<summary>List accounts</summary>

{% code overflow="wrap" %}
```bash
curl https://portal.hedera.com/api/account -H "Authorization: Bearer <YOUR GENERATED API TOKEN>"
```
{% endcode %}

Example API call

{% code overflow="wrap" %}
```bash
curl https:/portal.hedera.com/api/account -H "Authorization: Bearer v4.public.eyJzdWIiOiI1M2RlMmI0MC1iOTNiLTExZWUtODk4NC1iYjdkMDE2NTU2ZGQiLCJpYXQiOiIyMDI0LTAxLTIyVDE1OjMyOjA2Ljk1MloiLCJqdGkiOiI2Njg4Nzc4Mi1iOTNiLTExZWUtYmRmYi05ZmEzMWI3Yjc0ZGIifYer-H5VVjpQloP2U4qwUBBSsb-SQFEYNSrr9-8pqsdouDeAp00AWeDre8eGKLEt32JfaQiJ8UrcJyTlwbLfiwk"
```
{% endcode %}

</details>

<details>

<summary>Get account by public key</summary>

{% code overflow="wrap" %}
```bash
curl https://portal.hedera.com/api/account/<YOUR PUBLIC KEY> -H "Authorization: Bearer <YOUR GENERATED API TOKEN>"
```
{% endcode %}

Example API call

{% code overflow="wrap" %}
```bash
curl https://portal.hedera.com/api/account/302d300706052b8104000a03220003aaec818ba60d7f4e259319804317820f7f4aba3d0048a2f43573ddbbfe9a2254 -H "Authorization: Bearer v4.public.eyJzdWIiOiI1M2RlMmI0MC1iOTNiLTExZWUtODk4NC1iYjdkMDE2NTU2ZGQiLCJpYXQiOiIyMDI0LTAxLTIyVDE1OjMyOjA2Ljk1MloiLCJqdGkiOiI2Njg4Nzc4Mi1iOTNiLTExZWUtYmRmYi05ZmEzMWI3Yjc0ZGIifYer-H5VVjpQloP2U4qwUBBSsb-SQFEYNSrr9-8pqsdouDeAp00AWeDre8eGKLEt32JfaQiJ8UrcJyTlwbLfiwk"
```
{% endcode %}

</details>

**Example API call response**

{% code overflow="wrap" %}
```json
{"accounts":[{"accountNum":"7685875","shard":"0","realm":"0","balanceLimit":"100000000000","network":"testnet","keyType":"ecdsa","lastDisbursementAt":"2024-01-22 15:29:40.004689+00","publicKey":"302d300706052b8104000a03220003aaec818ba60d7f4e259319804317820f7f4aba3d0048a2f43573ddbbfe9a2254","privateKey":"302d300706052b8104000a03220003aaec818ba60d7f4e259319804317820f7f4aba3d0048a2f43573ddbbfe9a2254","scheduledForDisbursement":false,"balance":{"_valueInTinybar":"100000000000"}}]}
```
{% endcode %}

The data returned by the `curl` command is a JSON object detailing information about the accounts on the Hedera network. Here's a breakdown of each field and what it represents:

* **`accountNum`**: Account identifier, e.g., "7685875".
* **`shard`**: Shard ID, part of the account address, e.g., "0".
* **`realm`**: Realm ID within the shard, e.g., "0".
* **`balanceLimit`**: Maximum account balance, e.g., "100000000000".
* **`network`**: Network type - mainnet, testnet, previewnet.&#x20;
* **`keyType`**: Cryptographic key type (ECDSA, ED25519), here "ecdsa".
* **`lastDisbursementAt`**: Last disbursement timestamp, e.g., "2024-01-22 15:29:40.004689+00".
* **`publicKey`**: Account's public key in hexadecimal.
* **`privateKey`**: Account's private key in hexadecimal.
* **`scheduledForDisbursement`**: Indicates if disbursement is scheduled, here `false`.
* **`balance`**: Contains `_valueInTinybar`, the account balance in tinybar units, e.g., "100000000000".

***

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://github.com/theekrystallee">https://github.com/theekrystallee</a></td></tr><tr><td align="center"><p>Editor: Simi, Sr. Software Manager</p><p><a href="https://github.com/SimiHunjan">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/shunjan/">https://www.linkedin.com/in/shunjan/</a></td></tr></tbody></table>
