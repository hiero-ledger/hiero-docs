# Hedera Token Service - Part 1: How to Mint NFTs

Hedera Token Service (HTS) enables you to configure, mint, and manage tokens on the Hedera network without the need to set up and deploy a smart contract. Tokens are as fast, fair, and secure as HBAR and cost a fraction of 1¢ USD to transfer.

{% embed url="https://youtu.be/lp3mwdYEZEk?si=DN-IMnO3bz8eG6u3" %}

Let’s look at some of the functionality available to you with HTS. You will see that the ease of use and flexibility this service provides make HTS tokens an excellent alternative to tokens with smart contracts on Ethereum. For those coming from Ethereum, HTS functionality can be mapped to multiple types of ERC token standards, including ERC20, ERC721, and ERC1155 – you can learn more about the mapping in this [blog post](https://hedera.com/blog/mapping-hedera-token-service-standards-to-erc20-erc721-erc1155). Starting in early 2022, you can use HTS with smart contracts for cases needing advanced logic and programmability for your tokens.

In this part of the series, you will learn how to:

* Create a custom fee schedule
* Configure a **non-fungible token (NFT)**
* Mint and burn NFTs
* Associate and Transfer NFTs

We will configure an NFT art collection for autumn images. With HTS, you can also create fungible tokens representing anything from a stablecoin pegged to the USD value to an in-game reward system.&#x20;

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](../../getting-started/introduction.md).
* Set up your environment [here](../../getting-started/environment-set-up.md).

✅ _If you want the entire code used for this tutorial, skip to the_ [_Code Check_](hedera-token-service-part-1-how-to-mint-nfts.md#code-check) _section below._

{% hint style="info" %}
**Note**: While the following examples are in JavaScript, official SDKs supporting [Go](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/define-a-token) and [Java](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/define-a-token) are also available and implemented very similarly alongside community-supported SDKs in [.NET](https://hedera.com/blog/creating-tokens-hedera-net-part-1) and various other frameworks or languages.
{% endhint %}

## Create New Hedera Accounts and Generate Keys for the NFT

Let's create additional Hedera accounts to represent users for this scenario, such as the Treasury, Alice, and Bob. These accounts are created using your Testnet account credentials from the Hedera portal (see the resources for [getting started](https://docs.hedera.com/hedera/getting-started/introduction)). Account creation starts by generating a private key for the new account and then calling a reusable function (_**accountCreatorFcn**_) that uses the new key, an initial balance, and the Hedera client. You can easily reuse this function if you need to create more accounts in the future.

Once accounts are created for Treasury, Alice, and Bob, new private keys are generated to manage specific token functionality. Always provide the corresponding public key when specifying the key value for specific functionality.&#x20;

{% hint style="warning" %}
**Never** expose or share your private key(s) with others, as that may result in lost funds or loss of control over your account.
{% endhint %}

{% code title="nft-part1.js" %}
```javascript
// CREATE NEW HEDERA ACCOUNTS TO REPRESENT OTHER USERS
const initBalance = new Hbar(200);
const treasuryKey = PrivateKey.generateECDSA();
const [treasurySt, treasuryId] = await accountCreateFcn(
  treasuryKey,
  initBalance,
  client
);
console.log(`- Treasury's account: https://hashscan.io/testnet/account/${treasuryId}`);
const aliceKey = PrivateKey.generateECDSA();
const [aliceSt, aliceId] = await accountCreateFcn(aliceKey, initBalance, client);
console.log(`- Alice's account: https://hashscan.io/testnet/account/${aliceId}`);
const bobKey = PrivateKey.generateECDSA();
const [bobSt, bobId] = await accountCreateFcn(bobKey, initBalance, client);
console.log(`- Bob's account: https://hashscan.io/testnet/account/${bobId}`);

// GENERATE KEYS TO MANAGE FUNCTIONAL ASPECTS OF THE TOKEN
const supplyKey = PrivateKey.generate();
const adminKey = PrivateKey.generate();
const pauseKey = PrivateKey.generate();
const freezeKey = PrivateKey.generate();
const wipeKey = PrivateKey.generate();
const kycKey = PrivateKey.generate();
const newKycKey = PrivateKey.generate();
```
{% endcode %}

{% code title="nft-part1.js" %}
```javascript
// ACCOUNT CREATOR FUNCTION ==========================================
async function accountCreateFcn(pvKey, iBal, client) {
  const response = await new AccountCreateTransaction()
    .setInitialBalance(iBal)
    .setKey(pvKey.publicKey)
    .setMaxAutomaticTokenAssociations(10)
    .execute(client);
  const receipt = await response.getReceipt(client);
  return [receipt.status, receipt.accountId];
}
```
{% endcode %}

#### Console output:

```bash
- Treasury's account: https://hashscan.io/testnet/account/0.0.4672116
- Alice's account: https://hashscan.io/testnet/account/0.0.4672117
- Bob's account: https://hashscan.io/testnet/account/0.0.4672118
```

## Create a Custom Fee Schedule

Let’s start by defining the custom fees for the NFT. [Custom fees](../../sdks-and-apis/sdks/token-service/custom-token-fees.md) are distributed to the specified accounts each time the token is transferred. Depending on the token type (fungible or non-fungible), you can specify a custom fee to be fixed, fractional, or a royalty. An NFT can only have fixed or royalty fees, so in this example, we’ll go with a royalty fee. This enables collecting a fraction of the value exchanged for the NFT when ownership is transferred from one person to another.

{% code title="nft-part1.js" %}
```javascript
// DEFINE CUSTOM FEE SCHEDULE
let nftCustomFee = await new CustomRoyaltyFee()
  .setNumerator(1)
  .setDenominator(10)
  .setFeeCollectorAccountId(treasuryId)
  .setFallbackFee(new CustomFixedFee().setHbarAmount(new Hbar(200)));
```
{% endcode %}

## Create a Non-Fungible Token (NFT)

These are the images for our NFT collection.

<figure><img src="../../.gitbook/assets/get-started-with-hedera-token-service-pt1-image-to-nft.webp" alt=""><figcaption></figcaption></figure>

The images and their metadata live in the InterPlanetary File System (IPFS), which provides decentralized storage. In the next section, we will need the metadata to mint NFTs. For the metadata, we use the standard in [this specification](https://hips.hedera.com/hip/hip-412).&#x20;

These are the content identifiers (CIDs) for the NFT metadata, which points to the images, and below is a sample of the metadata:

{% code title="nft-part1.js" %}
```javascript
// IPFS CONTENT IDENTIFIERS FOR WHICH WE WILL CREATE NFTs - SEE uploadJsonToIpfs.js
let CIDs = [
    Buffer.from("ipfs://bafkreibr7cyxmy4iyckmlyzige4ywccyygomwrcn4ldcldacw3nxe3ikgq"),
    Buffer.from("ipfs://bafkreig73xgqp7wy7qvjwz33rp3nkxaxqlsb7v3id24poe2dath7pj5dhe"),
    Buffer.from("ipfs://bafkreigltq4oaoifxll3o2cc3e3q3ofqzu6puennmambpulxexo5sryc6e"),
    Buffer.from("ipfs://bafkreiaoswszev3uoukkepctzpnzw56ey6w3xscokvsvmfrqdzmyhas6fu"),
    Buffer.from("ipfs://bafkreih6cajqynaqwbrmiabk2jxpy56rpf25zvg5lbien73p5ysnpehyjm"),
];
```
{% endcode %}

```javascript
{
  "name": "LEAF1",
  "creator": "Mother Nature & Hashgraph",
  "description": "Autumn",
  "image": "ipfs://Qmb3CMWJzxWZJ34TgJgjASvdTc4x6PEz6LGm2QTWPPpkw5",
  "type": "image/jpg",
  "format": "HIP412@2.0.0",
  "properties": {
    "city": "Boston",
    "season": "Fall",
    "decade": "20's",
    "license": "MIT-0",
    "collection": "Fall Collection",
    "website": "www.hashgraph.com"
  }
}
```

Now, let’s [create the token](../../sdks-and-apis/sdks/token-service/define-a-token.md). Use _**TokenCreateTransaction()**_ to configure and set the token properties. At a minimum, this constructor requires setting a name, symbol, and treasury account ID. All other fields are optional, so default values are used if they’re not specified. For instance, not specifying an _admin key_, makes a token immutable (can’t change or add properties); not specifying a _supply key_, makes a token supply fixed (can’t mint new or burn existing tokens); not specifying a _token type_, makes a token fungible; for more info on the defaults check out the documentation.

After submitting the transaction to the Hedera network, you can obtain the new token ID by requesting the receipt. This token ID represents an NFT class.

{% code title="nft-part1.js" %}
```javascript
// CREATE NFT WITH CUSTOM FEE
let nftCreateTx = await new TokenCreateTransaction()
    .setTokenName("Fall Collection")
    .setTokenSymbol("LEAF")
    .setTokenType(TokenType.NonFungibleUnique)
    .setDecimals(0)
    .setInitialSupply(0)
    .setTreasuryAccountId(treasuryId)
    .setSupplyType(TokenSupplyType.Finite)
    .setMaxSupply(CIDs.length)
    .setCustomFees([nftCustomFee])
    .setAdminKey(adminKey)
    .setSupplyKey(supplyKey)
    .setPauseKey(pauseKey)
    .setFreezeKey(freezeKey)
    .setWipeKey(wipeKey)
    .freezeWith(client)
    .sign(treasuryKey);

let nftCreateTxSign = await nftCreateTx.sign(adminKey)
let nftCreateSubmit = await nftCreateTxSign.execute(client);
let nftCreateRx = await nftCreateSubmit.getReceipt(client);
let tokenId = nftCreateRx.tokenId;
console.log(`\n- Created NFT with Token ID: ${tokenId}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${nftCreateSubmit.transactionId}`
);

// TOKEN QUERY TO CHECK THAT THE CUSTOM FEE SCHEDULE IS ASSOCIATED WITH NFT
var tokenInfo = await new TokenInfoQuery().setTokenId(tokenId).execute(client);
console.log(` `);
console.table(tokenInfo.customFees[0]);
```
{% endcode %}

#### Console output:

```bash
- Created NFT with Token ID: 0.0.4672119
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230932.976832324
```

<figure><img src="../../.gitbook/assets/created-nft-with-token-id-hedera-token-service-blog-pt1.png" alt=""><figcaption></figcaption></figure>

## Mint and Burn NFTs

In the code above for the NFT creation, the decimals and initial supply must be set to zero. Once the token is created, you will have to mint each NFT using the token mint operation. Specifying a _supply key_ during token creation is a requirement to be able to [mint](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/mint-a-token) and [burn](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/burn-a-token) tokens.

In terms of use cases, you may want to mint new NFTs to add items to your NFT class, or you may need to burn NFTs to take a specific item out of circulation. Alternatively, if you’re working with a fungible token (like a stablecoin), you may want to mint new tokens every time there is a new deposit and burn tokens anytime that someone converts their tokens back into fiat.

In this case we’re creating a batch of five NFTs for a collection of five images. We’ll use a “token minter” function and a _for_ loop to speed up the batch NFT minting from our array of content identifiers (CID array):

{% code title="nft-part1.js" %}
```javascript
// MINT NEW BATCH OF NFTs - CAN MINT UP TO 10 NFT SERIALS IN A SINGLE TRANSACTION
let [nftMintRx, mintTxId] = await tokenMinterFcn(CIDs);
console.log(
  `\n- Mint ${CIDs.length} serials for NFT collection ${tokenId}: ${nftMintRx.status}`
);
console.log(`- See: https://hashscan.io/${network}/transaction/${mintTxId}`);
```
{% endcode %}

{% code title="nft-part1.js" %}
```javascript
// TOKEN MINTER FUNCTION ==========================================
 async function tokenMinterFcn(CIDs) {
    let mintTx = new TokenMintTransaction()
	.setTokenId(tokenId)
	.setMetadata(CIDs)
	.freezeWith(client);
        let mintTxSign = await mintTx.sign(supplyKey);
        let mintTxSubmit = await mintTxSign.execute(client);
        let mintRx = await mintTxSubmit.getReceipt(client);
        return [mintRx, mintTxSubmit.transactionId];
    }
```
{% endcode %}

#### Console output:

```bash
- Mint 5 serials for NFT collection 0.0.4672119: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230934.771266715
```

If you change your mind and decide that you don’t need the last NFT, then you can burn it as follows:

{% code title="nft-part1.js" %}
```javascript
// BURN THE LAST NFT IN THE COLLECTION
let tokenBurnTx = await new TokenBurnTransaction()
    .setTokenId(tokenId)
    .setSerials([CIDs.length])
    .freezeWith(client)
    .sign(supplyKey);
    
let tokenBurnSubmit = await tokenBurnTx.execute(client);
let tokenBurnRx = await tokenBurnSubmit.getReceipt(client);
console.log(`\n- Burn NFT with serial ${CIDs.length}: ${tokenBurnRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenBurnSubmit.transactionId}`
 );

var tokenInfo = await new TokenInfoQuery()
    .setTokenId(tokenId)
    .execute(client);
console.log(`\n- Current NFT supply: ${tokenInfo.totalSupply}`);
```
{% endcode %}

#### Console output:

```bash
- Burn NFT with serial 5: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230939.588918306
- Current NFT supply: 4
```

## Auto-Associate and Transfer NFTs

Before an account that is not the treasury for a token can receive or send this specific token ID, it must become “associated” with the token. This helps reduce unwanted spam and other concerns from users who don’t want to be associated with any of the variety of tokens created on the Hedera network.

This association between an account and a token ID can be done in two ways, [manually](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/associate-tokens-to-an-account) or automatically. Note that **automatic associations** can be done for both [existing](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/update-an-account) and [newly created](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/create-an-account) accounts. For the purposes of our example, we’ll do both.

* Alice’s account will be updated to associate with the token automatically
* Bob’s account will be manually associated with the token ID

{% code title="nft-part1.js" %}
```javascript
// AUTO-ASSOCIATION FOR ALICE'S ACCOUNT
let associateTx = await new AccountUpdateTransaction()
    .setAccountId(aliceId)
    .setMaxAutomaticTokenAssociations(10)
    .freezeWith(client)
    .sign(aliceKey);
let associateTxSubmit = await associateTx.execute(client);
let associateRx = await associateTxSubmit.getReceipt(client);
console.log(`\n- Alice NFT Auto-Association: ${associateRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${associateTxSubmit.transactionId}`
);

// MANUAL ASSOCIATION FOR BOB'S ACCOUNT
let associateBobTx = await new TokenAssociateTransaction()
    .setAccountId(bobId)
    .setTokenIds([tokenId])
    .freezeWith(client)
    .sign(bobKey);
let associateBobTxSubmit = await associateBobTx.execute(client);
let associateBobRx = await associateBobTxSubmit.getReceipt(client);
console.log(`\n- Bob NFT Manual Association: ${associateBobRx.status}`);
console.log(
`- See: https://hashscan.io/${network}/transaction/${associateBobTxSubmit.transactionId}`
);
```
{% endcode %}

#### Console output:&#x20;

```bash
- Alice NFT auto-association: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230939.742284556

- Bob NFT manual association: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230942.397064432
```

Finally, let’s do two [transfers](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/transfer-tokens) of the NFT with serial number **2** and see how the royalty fees are collected. The first transfer will be from the Treasury to Alice, and the second NFT transfer will be from Alice to Bob in exchange for 100 HBAR.

## NFT Transfer from Treasury to Alice

Now, let’s do the first NFT transfer and check the account balances before and after the send.

{% code title="nft-part1.js" %}
```javascript
// BALANCE CHECK 1
oB = await bCheckerFcn(treasuryId);
aB = await bCheckerFcn(aliceId);
bB = await bCheckerFcn(bobId);
console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`);
console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

// 1st TRANSFER NFT Treasury->Alice
let tokenTransferTx = await new TransferTransaction()
  .addNftTransfer(tokenId, 2, treasuryId, aliceId)
  .freezeWith(client)
  .sign(treasuryKey);
let tokenTransferSubmit = await tokenTransferTx.execute(client);
let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);
console.log(`\n NFT transfer Treasury->Alice status: ${tokenTransferRx.status}`);
console.log(
`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit.transactionId}`
);

// BALANCE CHECK 2: COPY/PASTE THE CODE ABOVE IN BALANCE CHECK 1
// BALANCE CHECK 2
oB = await bCheckerFcn(treasuryId);
aB = await bCheckerFcn(aliceId);
bB = await bCheckerFcn(bobId);
console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`);
console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);
```
{% endcode %}

{% code title="nft-part1.js" %}
```javascript
// BALANCE CHECKER FUNCTION ==========================================
async function bCheckerFcn(id) {
  balanceCheckTx = await new AccountBalanceQuery()
    .setAccountId(id)
    .execute(client);
  return [
    balanceCheckTx.tokens._map.get(tokenId.toString()),
    balanceCheckTx.hbars,
  ];
}
```
{% endcode %}

#### Console output:

```bash
- Treasury balance: 4 NFTs of ID:0.0.4672119 and 1 ℏ
- Alice balance: undefined NFTs of ID:0.0.4672119 and 1 ℏ
- Bob balance: 0 NFTs of ID:0.0.4672119 and 1 ℏ

- NFT transfer Treasury -> Alice status: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230943.472094366

- Treasury balance: 3 NFTs of ID:0.0.4672119 and 1 ℏ
- Alice balance: 1 NFTs of ID:0.0.4672119 and 1 ℏ
- Bob balance: 0 NFTs of ID:0.0.4672119 and 1 ℏ
```

As you remember from the [Custom Token Fees documentation](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/custom-token-fees), the treasury account and any fee-collecting accounts for a token are exempt from paying custom transaction fees when the token is transferred. Since the treasury account is also the fee collector for the token, that means there are no royalty fees collected in this first transfer.

## NFT Transfer from Alice to Bob

{% code title="nft-part1.js" %}
```javascript
// 2nd NFT TRANSFER NFT Alice->Bob
let nftPrice = new Hbar(10000000, HbarUnit.Tinybar); // 1HBAR = 10,000,000 Tinybar
let tokenTransferTx2 = await new TransferTransaction()
	.addNftTransfer(tokenId, 2, aliceId, bobId)
	.addHbarTransfer(aliceId, nftPrice)
	.addHbarTransfer(bobId, nftPrice.negated())
	.freezeWith(client)
	.sign(aliceKey);
let tokenTransferTx2Sign = await tokenTransferTx2.sign(bobKey);
let tokenTransferSubmit2 = await tokenTransferTx2Sign.execute(client);
let tokenTransferRx2 = await tokenTransferSubmit2.getReceipt(client);
console.log(`\n NFT transfer Alice->Bob status: ${tokenTransferRx2.status}`);
console.log(
`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit2.transactionId}`
);

// BALANCE CHECK 3: COPY/PASTE THE CODE ABOVE IN BALANCE CHECK 1
```
{% endcode %}

#### Console output:

```bash
- NFT transfer Alice -> Bob status: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4649505@1723230945.514991505

- Treasury balance: 3 NFTs of ID:0.0.4672119 and 1.01 ℏ
- Alice balance: 0 NFTs of ID:0.0.4672119 and 1.09 ℏ
- Bob balance: 1 NFTs of ID:0.0.4672119 and 0.9 ℏ
```

Remember from the documentation that royalty fees are paid from the fungible value exchanged, which was 100 HBAR in this case. The royalty fee is specified to be 50%, so that’s why the treasury collects 50 HBAR, and Alice collects the remaining 50 HBAR. Remember that when there’s no exchange of fungible value (like HBAR or a fungible token), the fallback fee is charged (currently 200 HBAR in our custom fee schedule).

***

## Conclusion

**You just learned how to create an NFT on the Hedera network at the native layer without the need to code complex smart contracts!** You can create, mint, burn, associate, and transfer NFTs with just a few lines of code in your favorite programming language. Continue to [Part 2](hedera-token-service-part-2-kyc-update-and-scheduled-transactions.md) to learn how to work with compliance features like [Know your Customer (KYC)](../../support-and-community/glossary.md#know-your-customer-kyc), update tokens, and schedule transactions. Then in[ Part 3](hedera-token-service-part-3-how-to-pause-freeze-wipe-and-delete-nfts.md), you will see how to pause, freeze, wipe, and delete tokens.

<details>

<summary>Code Check ✅</summary>

{% code title="nft-pt1.js" %}
```javascript
console.clear();
require("dotenv").config();

const {
  AccountId,
  PrivateKey,
  Client,
  TokenCreateTransaction,
  TokenInfoQuery,
  TokenType,
  CustomRoyaltyFee,
  CustomFixedFee,
  Hbar,
  HbarUnit,
  TokenSupplyType,
  TokenMintTransaction,
  TokenBurnTransaction,
  TransferTransaction,
  AccountBalanceQuery,
  AccountUpdateTransaction,
  TokenAssociateTransaction,
  TokenNftInfoQuery,
  NftId,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");

// CONFIGURE ACCOUNTS AND CLIENT, AND GENERATE  accounts and client, and generate needed keys
const operatorId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorKey = PrivateKey.fromStringECDSA(process.env.OPERATOR_KEY_HEX);
const network = process.env.NETWORK;

const client = Client.forNetwork(network).setOperator(operatorId, operatorKey);
client.setDefaultMaxTransactionFee(new Hbar(50));
client.setDefaultMaxQueryPayment(new Hbar(1));

async function main() {
  // CREATE NEW HEDERA ACCOUNTS TO REPRESENT OTHER USERS
  const initBalance = new Hbar(1);

  const treasuryKey = PrivateKey.generateECDSA();
  const [treasurySt, treasuryId] = await accountCreateFcn(
    treasuryKey,
    initBalance,
    client
  );
  console.log(
    `- Treasury's account: https://hashscan.io/testnet/account/${treasuryId}`
  );
  const aliceKey = PrivateKey.generateECDSA();
  const [aliceSt, aliceId] = await accountCreateFcn(
    aliceKey,
    initBalance,
    client
  );
  console.log(
    `- Alice's account: https://hashscan.io/testnet/account/${aliceId}`
  );
  const bobKey = PrivateKey.generateECDSA();
  const [bobSt, bobId] = await accountCreateFcn(bobKey, initBalance, client);
  console.log(`- Bob's account: https://hashscan.io/testnet/account/${bobId}`);

  // GENERATE KEYS TO MANAGE FUNCTIONAL ASPECTS OF THE TOKEN
  const supplyKey = PrivateKey.generateECDSA();
  const adminKey = PrivateKey.generateECDSA();
  const pauseKey = PrivateKey.generateECDSA();
  const freezeKey = PrivateKey.generateECDSA();
  const wipeKey = PrivateKey.generateECDSA();
  const kycKey = PrivateKey.generate();
  const newKycKey = PrivateKey.generate();

  // DEFINE CUSTOM FEE SCHEDULE
  let nftCustomFee = new CustomRoyaltyFee()
    .setNumerator(1)
    .setDenominator(10)
    .setFeeCollectorAccountId(treasuryId)
    .setFallbackFee(
      new CustomFixedFee().setHbarAmount(new Hbar(1, HbarUnit.Tinybar))
    ); // 1 HBAR = 100,000,000 Tinybar

  // IPFS CONTENT IDENTIFIERS FOR WHICH WE WILL CREATE NFTs - SEE uploadJsonToIpfs.js
  let CIDs = [
    Buffer.from(
      "ipfs://bafkreibr7cyxmy4iyckmlyzige4ywccyygomwrcn4ldcldacw3nxe3ikgq"
    ),
    Buffer.from(
      "ipfs://bafkreig73xgqp7wy7qvjwz33rp3nkxaxqlsb7v3id24poe2dath7pj5dhe"
    ),
    Buffer.from(
      "ipfs://bafkreigltq4oaoifxll3o2cc3e3q3ofqzu6puennmambpulxexo5sryc6e"
    ),
    Buffer.from(
      "ipfs://bafkreiaoswszev3uoukkepctzpnzw56ey6w3xscokvsvmfrqdzmyhas6fu"
    ),
    Buffer.from(
      "ipfs://bafkreih6cajqynaqwbrmiabk2jxpy56rpf25zvg5lbien73p5ysnpehyjm"
    ),
  ];

  // CREATE NFT WITH CUSTOM FEE
  let nftCreateTx = await new TokenCreateTransaction()
    .setTokenName("Fall Collection")
    .setTokenSymbol("LEAF")
    .setTokenType(TokenType.NonFungibleUnique)
    .setDecimals(0)
    .setInitialSupply(0)
    .setTreasuryAccountId(treasuryId)
    .setSupplyType(TokenSupplyType.Finite)
    .setMaxSupply(CIDs.length)
    .setCustomFees([nftCustomFee])
    .setAdminKey(adminKey.publicKey)
    .setSupplyKey(supplyKey.publicKey)
    .setPauseKey(pauseKey.publicKey)
    .setFreezeKey(freezeKey.publicKey)
    .setWipeKey(wipeKey.publicKey)
    .freezeWith(client)
    .sign(treasuryKey);

  let nftCreateTxSign = await nftCreateTx.sign(adminKey);
  let nftCreateSubmit = await nftCreateTxSign.execute(client);
  let nftCreateRx = await nftCreateSubmit.getReceipt(client);
  let tokenId = nftCreateRx.tokenId;
  console.log(`\n- Created NFT with Token ID: ${tokenId}`);
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${nftCreateSubmit.transactionId}`
  );

  // TOKEN QUERY TO CHECK THAT THE CUSTOM FEE SCHEDULE IS ASSOCIATED WITH NFT
  var tokenInfo = await new TokenInfoQuery()
    .setTokenId(tokenId)
    .execute(client);
  console.log(` `);
  console.table(tokenInfo.customFees[0]);

  // MINT NEW BATCH OF NFTs - CAN MINT UP TO 10 NFT SERIALS IN A SINGLE TRANSACTION
  let [nftMintRx, mintTxId] = await tokenMinterFcn(CIDs);
  console.log(
    `\n- Mint ${CIDs.length} serials for NFT collection ${tokenId}: ${nftMintRx.status}`
  );
  console.log(`- See: https://hashscan.io/${network}/transaction/${mintTxId}`);

  // BURN THE LAST NFT IN THE COLLECTION
  let tokenBurnTx = await new TokenBurnTransaction()
    .setTokenId(tokenId)
    .setSerials([CIDs.length])
    .freezeWith(client)
    .sign(supplyKey);
  let tokenBurnSubmit = await tokenBurnTx.execute(client);
  let tokenBurnRx = await tokenBurnSubmit.getReceipt(client);
  console.log(`\n- Burn NFT with serial ${CIDs.length}: ${tokenBurnRx.status}`);
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${tokenBurnSubmit.transactionId}`
  );

  var tokenInfo = await new TokenInfoQuery()
    .setTokenId(tokenId)
    .execute(client);
  console.log(`- Current NFT supply: ${tokenInfo.totalSupply}`);

  // AUTO-ASSOCIATION FOR ALICE'S ACCOUNT
  let associateTx = await new AccountUpdateTransaction()
    .setAccountId(aliceId)
    .setMaxAutomaticTokenAssociations(10)
    .freezeWith(client)
    .sign(aliceKey);
  let associateTxSubmit = await associateTx.execute(client);
  let associateRx = await associateTxSubmit.getReceipt(client);
  console.log(`\n- Alice NFT auto-association: ${associateRx.status}`);
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${associateTxSubmit.transactionId}`
  );

  // MANUAL ASSOCIATION FOR BOB'S ACCOUNT
  let associateBobTx = await new TokenAssociateTransaction()
    .setAccountId(bobId)
    .setTokenIds([tokenId])
    .freezeWith(client)
    .sign(bobKey);
  let associateBobTxSubmit = await associateBobTx.execute(client);
  let associateBobRx = await associateBobTxSubmit.getReceipt(client);
  console.log(`\n- Bob NFT manual association: ${associateBobRx.status}`);
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${associateBobTxSubmit.transactionId}`
  );

  // BALANCE CHECK 1
  oB = await bCheckerFcn(treasuryId);
  aB = await bCheckerFcn(aliceId);
  bB = await bCheckerFcn(bobId);
  console.log(
    `\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`
  );
  console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
  console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

  // 1st TRANSFER NFT Treasury -> Alice
  let tokenTransferTx = await new TransferTransaction()
    .addNftTransfer(tokenId, 2, treasuryId, aliceId)
    .freezeWith(client)
    .sign(treasuryKey);
  let tokenTransferSubmit = await tokenTransferTx.execute(client);
  let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);
  console.log(
    `\n- NFT transfer Treasury -> Alice status: ${tokenTransferRx.status}`
  );
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit.transactionId}`
  );

  // BALANCE CHECK 2
  oB = await bCheckerFcn(treasuryId);
  aB = await bCheckerFcn(aliceId);
  bB = await bCheckerFcn(bobId);
  console.log(
    `\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`
  );
  console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
  console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

  // 2nd NFT TRANSFER NFT Alice -> Bob
  let nftPrice = new Hbar(10000000, HbarUnit.Tinybar); // 1 HBAR = 10,000,000 Tinybar

  let tokenTransferTx2 = await new TransferTransaction()
    .addNftTransfer(tokenId, 2, aliceId, bobId)
    .addHbarTransfer(aliceId, nftPrice)
    .addHbarTransfer(bobId, nftPrice.negated())
    .freezeWith(client)
    .sign(aliceKey);
  let tokenTransferTx2Sign = await tokenTransferTx2.sign(bobKey);
  let tokenTransferSubmit2 = await tokenTransferTx2Sign.execute(client);
  let tokenTransferRx2 = await tokenTransferSubmit2.getReceipt(client);
  console.log(
    `\n- NFT transfer Alice -> Bob status: ${tokenTransferRx2.status}`
  );
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit2.transactionId}`
  );

  // BALANCE CHECK 3
  oB = await bCheckerFcn(treasuryId);
  aB = await bCheckerFcn(aliceId);
  bB = await bCheckerFcn(bobId);
  console.log(
    `\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`
  );
  console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
  console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

  console.log(
    `\n- THE END ============================================================\n`
  );
  console.log(`- 👇 Go to:`);
  console.log(`- 🔗 www.hedera.com/discord\n`);

  client.close();

  // ACCOUNT CREATOR FUNCTION ==========================================
  async function accountCreateFcn(pvKey, iBal, client) {
    const response = await new AccountCreateTransaction()
      .setInitialBalance(iBal)
      .setKey(pvKey.publicKey)
      .setMaxAutomaticTokenAssociations(10)
      .execute(client);
    const receipt = await response.getReceipt(client);
    return [receipt.status, receipt.accountId];
  }

  // TOKEN MINTER FUNCTION ==========================================
  async function tokenMinterFcn(CIDs) {
    let mintTx = new TokenMintTransaction()
      .setTokenId(tokenId)
      .setMetadata(CIDs)
      .freezeWith(client);
    let mintTxSign = await mintTx.sign(supplyKey);
    let mintTxSubmit = await mintTxSign.execute(client);
    let mintRx = await mintTxSubmit.getReceipt(client);
    return [mintRx, mintTxSubmit.transactionId];
  }

  // BALANCE CHECKER FUNCTION ==========================================
  async function bCheckerFcn(id) {
    balanceCheckTx = await new AccountBalanceQuery()
      .setAccountId(id)
      .execute(client);
    return [
      balanceCheckTx.tokens._map.get(tokenId.toString()),
      balanceCheckTx.hbars,
    ];
  }
}

main();
```
{% endcode %}

</details>

## Additional Resources

**➡** [**Project Repository**](https://github.com/hedera-dev/hedera-example-hts-nft-blog-p1-p2-p3/blob/main/nft-part1.js)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Ed, DevRel Engineer</p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/ed-marquez/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ed-marquez/">https://www.linkedin.com/in/ed-marquez/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://x.com/theekrystallee">Twitter</a></p></td><td><a href="https://x.com/theekrystallee">https://x.com/theekrystallee</a></td></tr></tbody></table>
