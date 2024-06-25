---
description: "Hello World sequence: Create a new fungible token using Hedera Token Service (HTS)."
---

# HTS: Fungible Token

## What you will accomplish

- [ ] Create and mint a new fungible token on HTS
- [ ] Query the token balance

***

## Prerequisites

Before you begin, you should have **completed** the following Hello World sequence:

- [create-fund-account.md](create-fund-account.md "mention")

***

## Get started: Set up project

To follow along, start with the `main` branch, which is the _default branch_ of the repo. This gives you the initial state from which you can follow along with the steps as described in the tutorial.

{% hint style="warning" %}
You should already have this from the "Create and Fund Account" sequence. If you have not completed this, you are strongly encouraged to do so.

Alternatively, you may wish to create a `.env` file and populate it as required.
{% endhint %}

In the terminal, from the `hello-future-world` directory, enter the subdirectory for this sequence.

```shell
cd 04-hts-ft-sdk/
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

This should output a path that ends with `/hello-future-world/04-hts-ft-sdk`. If not, you will need to start over.

```
/some/path/hello-future-world/04-hts-ft-sdk
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

Then open the `script-hts-ft.js` file in a code editor, such as VS Code.

***

## Write the script

An almost-complete script has already been prepared for you, and you will only need to make a few modifications (outlined below) for it to run successfully.

### Step 1: Configure HTS token to be created

To create a new HTS token, we will use `TokenCreateTransaction`. This transaction requires many properties to be set on it.

- For fungible tokens (which are analogous to ERC20 tokens), set the token type to `TokenType.FungibleCommon`.
- Set the token name and token symbol based on your name (or nickname).
- Set the decimal property to `2`.
- Set the initial supply to 1 million.

{% code title="script-hts-ft.js" overflow="wrap" %}

```js
        .setTokenType(TokenType.FungibleCommon)
        .setTokenName("bguiz coin")
        .setTokenSymbol("BGZ")
        .setDecimals(2)
        .setInitialSupply(1_000_000)
```

{% endcode %}

<details>

<summary>Key terminology for HTS token create transaction</summary>

- [**Token Type**](../../sdks-and-apis/sdks/token-service/token-types.md): Fungible tokens, declared using `TokenType.FungibleCommon`, may be thought of as analogous to _ERC20_ tokens. Note that HTS also supports another token type, `TokenType.NonFungibleUnique`, which may be thought of as analogous to _ERC721_ tokens.
- **Token Name**: This is the full name of the token. For example, "Singapore Dollar".
- **Token Symbol**: This is the abbreviation of the token's name. For example, "SGD".
- **Decimals**: This is the number of decimal places the currency uses. For example, `2` mimics "cents", where the smallest unit of the token is 0.01 (1/100) of a single token.
- **Initial Supply**: This is the number of units of the token to "mint" when first creating the token. Note that this is specified in the smallest units, so `1_000_000` initial supply when decimals is 2, results in `10_000` full units of the token being minted. It might be easier to think about it as "one million cents equals ten thousand dollars".
- **Treasury Account ID**: This is the account for which the initial supply is credited. For example, using `accountId` would mean that your own account receives all the tokens when they are minted.
- [**Admin Key**](../../sdks-and-apis/sdks/token-service/define-a-token.md#token-properties): This is the account that is authorized to administrate this token. For example, using `accountKey` would mean that your own account would get to perform actions such as minting additional supply.

</details>

### Step 2: Mirror Node API to query the specified token balance

Now, query the token balance of our account. Since the _treasury account_ was configured as your own account, it will have the entire initial supply of the token.

You will want to use the Mirror Node API with the path `/api/v1/accounts/{idOrAliasOrEvmAddress}/tokens` for this task.

- Specify `accountId` within the URL path
- Specify `tokenId` as the `token.id` query parameter
- Specify `1` as the `limit` query parameter (you are only interested in one token)

Using string interpolation, construct `accountBalanceFetchApiUrl` like so:

{% code title="script-hts-ft.js" overflow="wrap" %}

```js
    const accountBalanceFetchApiUrl = `https://testnet.mirrornode.hedera.com/api/v1/accounts/${accountId}/tokens?token.id=${tokenId}&limit=1&order=desc`;
```

{% endcode %}

<details>

<summary>Learn more about Mirror Node APIs</summary>

You can explore the Mirror Node APIs interactively via its Swagger page: [Hedera Testnet Mirror Node REST API](https://testnet.mirrornode.hedera.com/api/v1/docs/#/).

You can perform the same Mirror Node API query as `accountBalanceFetchApiUrl` above. This is what the relevant part of the Swagger page would look like when doing so:

<img src="../../.gitbook/assets/hello-world--hts--mirror-node-swagger.drawing.svg" alt="" data-size="original">

You can learn more about the Mirror Nodes via its documentation: [REST API](https://docs.hedera.com/hedera/sdks-and-apis/rest-api).

</details>

***

## Run the script

In the terminal, run the script using the following command:

```shell
node script-hts-ft.js
```

You should see output similar to the following:

```
accountId: 0.0.1201
tokenId: 0.0.5878530
tokenExplorerUrl: https://hashscan.io/testnet/token/0.0.5878530
accountTokenBalance: 1000000
accountBalanceFetchApiUrl: https://testnet.mirrornode.hedera.com/api/v1/accounts/0.0.1201/tokens?token.id=0.0.5878530&limit=1&order=desc
```

Open `tokenExplorerUrl` in your browser and check that:

<img src="../../.gitbook/assets/hello-world--hts--token.drawing.svg" alt="HTS transaction in Hashscan, with annotated items to check." class="gitbook-drawing">

- The token should exist, and its "token ID" should match `tokenId`. **(1)**
- The "name" and "symbol" should be shown as the same values derived from your name (or nickname) that you chose earlier. **(2)**
- The "treasury account" should match `accountId`. **(3)**
- Both the "total supply" and "initial supply" should be `10,000`. **(4)**

{% hint style="info" %}
**Note**: "total supply" and "initial supply" are not displayed as `1,000,000` because of the two decimal places configured. Instead, these are displayed as `10,000.00`.
{% endhint %}

***

## Complete

Congratulations, you have completed the **Hedera Token Service** Hello World sequence! 🎉🎉🎉

You have learned how to:

- [x] Create and mint a new fungible token on HTS
- [x] Query the token balance

***

## Next Steps

Now that you have completed this Hello World sequence, you have interacted with Hedera Token Service (HTS). There are [other Hello World sequences](../) for Hedera Smart Contract Service (HSCS), and Hedera File Service (HFS), which you may wish to check out next.

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

Alternatively, you may view the `completed` branch on Github: [`github.com/hedera-dev/hello-future-world/tree/completed/04-hts-ft-sdk`](https://github.com/hedera-dev/hello-future-world/tree/completed/04-hts-ft-sdk)

</details>

***

**Writer**: [Brendan](https://blog.bguiz.com/) **Editors**: [Abi](https://github.com/a-ridley), [Michiel](https://www.linkedin.com/in/michielmulders/), [Ryan](https://www.linkedin.com/in/ryaneh/), [Krystal](https://www.linkedin.com/in/theekrystallee/)
