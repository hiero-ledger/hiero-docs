# Hedera Token Service - Part 2: KYC, Update, and Scheduled Transactions

In [Part 1](hedera-token-service-part-1-how-to-mint-nfts.md) of the series, you saw how to mint and transfer an NFT using the Hedera Token Service (HTS). Now, in Part 2, you will see how to take advantage of the flexibility you get when you create and configure your tokens with HTS. More specifically, you will learn how to:

* Enable and disable a KYC flag for a token (**KYC stands for “know your customer”**)
* Update token properties (only possible if it’s a mutable token. Hint: AdminKey)
* Schedule transactions (like a token transfer)

{% embed url="https://youtu.be/sxs53OLnF48" %}

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](../more-tutorials/create-and-fund-your-hedera-testnet-account.md).
* Set up your environment [here](../../getting-started/environment-setup.md).

✅ _If you want the entire code used for this tutorial, skip to the_ [_Code Check_](hedera-token-service-part-2-kyc-update-and-scheduled-transactions.md#code-check) _section below._

## Understanding KYC and Hedera Tokens

### KYC Key

When you [create a token](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/define-a-token) with HTS, you can optionally use the _**.setKycKey(\<key>)**_ method to enable this _**\<key>**_ to grant (or revoke) the KYC status of other accounts so they can transact with your token. You would consider using the KYC flag when you need your token to be used only within parties that have been “authorized” to use it. For instance, known registered users or those who have passed identity verification. Think of this as identity and compliance features like anti-money laundering (AML) requirements or any type of off-ledger authentication mechanism, like if a user has signed up for your application.

The _**.setKycKey(\<key>)**_ method is not required when you create your token, so if you don’t use the method that means anyone who is [associated with your token](../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md) can transact without having to be “authorized”. No KYC key also means that KYC grant or revoke operations are not possible for the token in the future.

### Enable Token KYC on an Account

We will continue with the NFT example from [Part 1](hedera-token-service-part-1-how-to-mint-nfts.md). However, we must create a new token using _**.setKycKey(\<key>)**_. Before users can transfer the newly created token, we must grant KYC to those users, namely Alice and Bob.

{% code title="nft-part2.js" %}
```javascript
// ENABLE TOKEN KYC FOR ALICE AND BOB
let [aliceKycRx, aliceKycTxId] = await kycEnableFcn(aliceId);
let [bobKyc, bobKycTxId] = await kycEnableFcn(bobId);

console.log(`\n- Enabling token KYC for Alice's account: ${aliceKycRx.status}`);
console.log(`- See: https://hashscan.io/${network}/transaction/${aliceKycTxId}`);
console.log(`\n- Enabling token KYC for Bob's account: ${bobKyc.status}`);
console.log(`- See: https://hashscan.io/${network}/transaction/${bobKycTxId}`);
```
{% endcode %}

```javascript
// KYC ENABLE FUNCTION ==========================================
async function kycEnableFcn(id) {
  let kycEnableTx = await new TokenGrantKycTransaction()
    .setAccountId(id)
    .setTokenId(tokenId)
    .freezeWith(client)
    .sign(kycKey);
  let kycSubmitTx = await kycEnableTx.execute(client);
  let kycRx = await kycSubmitTx.getReceipt(client);
  return [kycRx, kycSubmitTx.transactionId];
}
```

**Console output:**

```bash
- Enabling token KYC for Alice's account: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.2520793@1723765780.358402849

- Enabling token KYC for Bob's account: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.2520793@1723765782.680313758
```

### Disable Token KYC on an Account

After the KYC flag has been set to true for a user, the administrator, identity provider, or compliance manager can revoke or disable the KYC flag. After KYC is disabled for a user, he or she can no longer receive or send that token. Here’s a sample code for disabling token KYC on Alice’s account:

{% code title="nft-part2.js" %}
```javascript
// DISABLE TOKEN KYC FOR ALICE
let kycDisableTx = await new TokenRevokeKycTransaction()
    .setAccountId(aliceId)
    .setTokenId(tokenId)
    .freezeWith(client)
    .sign(kycKey);
let kycDisableSubmitTx = await kycDisableTx.execute(client);
let kycDisableRx = await kycDisableSubmitTx.getReceipt(client);

console.log(`\n- Disabling token KYC for Alice's account: ${kycDisableRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${kycDisableSubmitTx.transactionId}`
);
```
{% endcode %}

**Console output:**

```bash
- Disabling token KYC for Alice's account: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723796731.003707280
```

{% hint style="info" %}
**Note**: The following sections require Alice to have token KYC enabled.
{% endhint %}

## Updating tokens

If you create a token using the _**.setAdminKey(\<key>)**_ method, then you can “update” that token, meaning change its metadata and characteristics. For instance, you can change the token name, symbol, or the keys that are associated with its [controlled mutability](https://hedera.com/blog/code-is-law-but-what-if-the-law-needs-to-change). You could create a token that initially has a 1-to-1 key for minting and burning and, over time, change this to a threshold or multi-signature key. You can rotate the keys associated with compliance and administration or even remove them entirely, offering a more decentralized approach over time.

On the other hand, if you create a token without using _**.setAdminKey(\<key>)**_, that token is immutable, and its properties cannot be modified.

In our example, we start by checking the initial KYC key for the token, then we update the KYC key from _**\<kycKey>**_ to _**\<newKycKey>**_, and then we query the token again to make sure the key change took place.

{% code title="nft-part2.js" %}
```javascript
// QUERY TO CHECK INITIAL KYC KEY
var tokenInfo = await tQueryFcn();
console.log(`\n- KYC key for the NFT is: \n${tokenInfo.kycKey.toString()}`);

// UPDATE TOKEN PROPERTIES: NEW KYC KEY
let tokenUpdateTx = await new TokenUpdateTransaction()
    .setTokenId(tokenId)
    .setKycKey(newKycKey.publicKey)
    .freezeWith(client)
    .sign(adminKey);
let tokenUpdateSubmitTx = await tokenUpdateTx.execute(client);
let tokenUpdateRx = await tokenUpdateSubmitTx.getReceipt(client);
console.log(`\n- Token update transaction (new KYC key): ${tokenUpdateRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenUpdateSubmitTx.transactionId}`
);

// QUERY TO CHECK CHANGE IN KYC KEY
var tokenInfo = await tQueryFcn();
console.log(`\n- KYC key for the NFT is: \n${tokenInfo.kycKey.toString()}`);
```
{% endcode %}

```javascript
// TOKEN QUERY FUNCTION ==========================================
async function tQueryFcn() {
  var tokenInfo = await new TokenInfoQuery().setTokenId(tokenId).execute(client);
  return tokenInfo;
}
```

**Console output:**

```bash
- KYC key for the NFT is: 
302a300506032b65700321009178c6063f1ba8a61bf7e9674edf2aee06d547e0c18d0bb

- Token update transaction (new KYC key): SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.2520793@1723765784.825417112

- KYC key for the NFT is: 
302a300506032b65700321001bfd2a1a63e5b7e5e159ca0911db29fb6013074b1652a253
```

## Schedule Transactions

Scheduled transactions enable you to collect the required signatures for a transaction in preparation for its execution. This can be useful if you don’t have all the required signatures for the network to immediately process the transaction. Currently, you can schedule: _**TransferTransaction()**_ (for hbar and HTS tokens), _**TokenMintTransaction()**_, _**TokenBurnTransaction()**_, and _**TopicMessageSubmitTransaction()**_. More transactions are supported with new releases.

Now, we will schedule a token transfer from Bob to Alice using [scheduled transactions](../../core-concepts/scheduled-transaction.md). This token transfer requires signatures from both parties.

Given that Alice’s and Bob’s signatures are not immediately available (for the purposes of this example), we first create the NFT transfer without signatures. Then, we create the scheduled transaction using the constructor ScheduleCreateTransaction() and specify the NFT transfer as the transaction to schedule using the _**.setScheduledTransaction()**_ method.

{% code title="nft-part2.js" %}
```javascript
// CREATE THE NFT TRANSFER FROM BOB->ALICE TO BE SCHEDULED
// REQUIRES ALICE'S AND BOB'S SIGNATURES
let txToSchedule = new TransferTransaction()
    .addNftTransfer(tokenId, 2, bobId, aliceId)
    .addHbarTransfer(aliceId, nftPrice.negated())
    .addHbarTransfer(bobId, nftPrice);

// SCHEDULE THE NFT TRANSFER TRANSACTION CREATED IN THE LAST STEP
let scheduleTx = await new ScheduleCreateTransaction()
    .setScheduledTransaction(txToSchedule)
    .execute(client);
let scheduleRx = await scheduleTx.getReceipt(client);
let scheduleId = scheduleRx.scheduleId;
let scheduledTxId = scheduleRx.scheduledTransactionId;
console.log(`- The schedule ID is: ${scheduleId}`);
console.log(`- The scheduled transaction ID is: ${scheduledTxId} \n`);
```
{% endcode %}

**Console output:**

```bash
- The schedule ID is: 0.0.3071457
- The scheduled transaction ID is: 0.0.2520793@1723765788.746525125?scheduled
```

The token transfer is now scheduled, and it will be executed as soon as all required signatures are submitted. Note that the scheduled transaction IDs (_**scheduledTxId**_ in this case) have a “scheduled” flag that you can use to confirm the status of the transaction.

As of the time of this writing, a scheduled transaction has 30 minutes to collect all the required signatures before it can be executed or it expires (deleted from the network). If you set an _**\<AdminKey>**_ for the scheduled transaction, then you can delete it before its execution or expiration.

Now, we [submit the required signatures](../../sdks-and-apis/sdks/schedule-transaction/sign-a-schedule-transaction.md) and [get schedule information](../../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) to check the status of our transfer.

{% code title="nft-part2.js" %}
```javascript
// SUBMIT ALICE'S SIGNATURE FOR THE TRANSFER TRANSACTION
let aliceSignTx = await new ScheduleSignTransaction()
    .setScheduleId(scheduleId)
    .freezeWith(client)
    .sign(aliceKey);
let aliceSignSubmit = await aliceSignTx.execute(client);
let aliceSignRx = await aliceSignSubmit.getReceipt(client);
console.log(`- Status of Alice's signature submission: ${aliceSignRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${aliceSignSubmit.transactionId}`
);

// QUERY TO CONFIRM IF THE SCHEDULE WAS TRIGGERED (SIGNATURES HAVE BEEN ADDED)
scheduleQuery = await new ScheduleInfoQuery()
    .setScheduleId(scheduleId)
    .execute(client);
console.log(
  `- Schedule triggered (all required signatures received): ${scheduleQuery.executed !== null}`
);

// SUBMIT BOB'S SIGNATURE FOR THE TRANSFER TRANSACTION
let bobSignTx = await new ScheduleSignTransaction()
    .setScheduleId(scheduleId)
    .freezeWith(client)
    .sign(bobKey);
let bobSignSubmit = await bobSignTx.execute(client);
let bobSignRx = await bobSignSubmit.getReceipt(client);
console.log(`- Status of Bob's signature submission: ${bobSignRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${bobSignSubmit.transactionId}`
);

// QUERY TO CONFIRM IF THE SCHEDULE WAS TRIGGERED (SIGNATURES HAVE BEEN ADDED)
scheduleQuery = await new ScheduleInfoQuery()
    .setScheduleId(scheduleId)
    .execute(client);
console.log(
  `\n- Schedule triggered (all required signatures received): ${
  scheduleQuery.executed !== null
  }`
);
```
{% endcode %}

**Console output:**

```bash
- Status of Alice's signature submission: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.2520793@@1723765792.396881487

- Schedule triggered (all required signatures received): false

- Status of Bob's signature submission: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.2520793@@1723765789.984805782

- Schedule triggered (all required signatures received): true
```

The scheduled transaction was executed. It is still a good idea to verify that the transfer happened as we expected, so we check all the balances once more to confirm.

{% code title="nft-part2.js" %}
```javascript
// VERIFY THAT THE SCHEDULED TRANSACTION (TOKEN TRANSFER) EXECUTED
oB = await bCheckerFcn(treasuryId);
aB = await bCheckerFcn(aliceId);
bB = await bCheckerFcn(bobId);
console.log(`- Treasury balance: ${oB[0]} NFTs of ID: ${tokenId} and ${oB[1]}`);
console.log(`- Alice balance: ${aB[0]} NFTs of ID: ${tokenId} and ${aB[1]}`);
console.log(`- Bob balance: ${bB[0]} NFTs of ID: ${tokenId} and ${bB[1]}`);
```
{% endcode %}

```javascript
// BALANCE CHECKER FUNCTION ==========================================
async function bCheckerFcn(id) {
   balanceCheckTx = await new AccountBalanceQuery().setAccountId(id).execute(client);
   return [balanceCheckTx.tokens._map.get(tokenId.toString()), balanceCheckTx.hbars];
 }
```

**Console output:**

<figure><img src="../../.gitbook/assets/treasury-balance-hedera-token-service-blog-pt2.webp" alt=""><figcaption></figcaption></figure>

## Conclusion

In this article, you saw examples of the flexibility you get when you create and configure your tokens with HTS - in a transparent and cryptographically provable way. Take advantage of this flexibility to tackle entirely new opportunities in the tokenization industry!

<details>

<summary><strong>Code Check ✅</strong></summary>

{% code title="nft-part2.js" %}
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
	TokenAssociateTransaction,
	TokenUpdateTransaction,
	TokenGrantKycTransaction,
	TokenRevokeKycTransaction,
	ScheduleCreateTransaction,
	ScheduleSignTransaction,
	ScheduleInfoQuery,
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
	const [treasurySt, treasuryId] = await accountCreateFcn(treasuryKey, initBalance, client);
	console.log(`- Treasury's account: https://hashscan.io/testnet/account/${treasuryId}`);
	const aliceKey = PrivateKey.generateECDSA();
	const [aliceSt, aliceId] = await accountCreateFcn(aliceKey, initBalance, client);
	console.log(`- Alice's account: https://hashscan.io/testnet/account/${aliceId}`);
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
		.setFallbackFee(new CustomFixedFee().setHbarAmount(new Hbar(1, HbarUnit.Tinybar))); // 1 HBAR = 100,000,000 Tinybar

	// IPFS CONTENT IDENTIFIERS FOR WHICH WE WILL CREATE NFTs - SEE uploadJsonToIpfs.js
	let CIDs = [
		Buffer.from("ipfs://bafkreibr7cyxmy4iyckmlyzige4ywccyygomwrcn4ldcldacw3nxe3ikgq"),
		Buffer.from("ipfs://bafkreig73xgqp7wy7qvjwz33rp3nkxaxqlsb7v3id24poe2dath7pj5dhe"),
		Buffer.from("ipfs://bafkreigltq4oaoifxll3o2cc3e3q3ofqzu6puennmambpulxexo5sryc6e"),
		Buffer.from("ipfs://bafkreiaoswszev3uoukkepctzpnzw56ey6w3xscokvsvmfrqdzmyhas6fu"),
		Buffer.from("ipfs://bafkreih6cajqynaqwbrmiabk2jxpy56rpf25zvg5lbien73p5ysnpehyjm"),
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
		.setKycKey(kycKey.publicKey)
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
	console.log(`- See: https://hashscan.io/${network}/transaction/${nftCreateSubmit.transactionId}`);

	// TOKEN QUERY TO CHECK THAT THE CUSTOM FEE SCHEDULE IS ASSOCIATED WITH NFT
	var tokenInfo = await tQueryFcn();
	console.log(` `);
	console.table(tokenInfo.customFees[0]);

	// MINT NEW BATCH OF NFTs - CAN MINT UP TO 10 NFT SERIALS IN A SINGLE TRANSACTION
	let [nftMintRx, mintTxId] = await tokenMinterFcn(CIDs);
	console.log(`\n- Mint ${CIDs.length} serials for NFT collection ${tokenId}: ${nftMintRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${mintTxId}`);

	// BURN THE LAST NFT IN THE COLLECTION
	let tokenBurnTx = await new TokenBurnTransaction().setTokenId(tokenId).setSerials([CIDs.length]).freezeWith(client).sign(supplyKey);
	let tokenBurnSubmit = await tokenBurnTx.execute(client);
	let tokenBurnRx = await tokenBurnSubmit.getReceipt(client);
	console.log(`\n- Burn NFT with serial ${CIDs.length}: ${tokenBurnRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenBurnSubmit.transactionId}`);

	var tokenInfo = await tQueryFcn();
	console.log(`- Current NFT supply: ${tokenInfo.totalSupply}`);

	// MANUAL ASSOCIATION FOR ALICE'S ACCOUNT
	let associateAliceTx = await new TokenAssociateTransaction().setAccountId(aliceId).setTokenIds([tokenId]).freezeWith(client).sign(aliceKey);
	let associateAliceTxSubmit = await associateAliceTx.execute(client);
	let associateAliceRx = await associateAliceTxSubmit.getReceipt(client);
	console.log(`\n- Alice NFT manual association: ${associateAliceRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${associateAliceTxSubmit.transactionId}`);

	// MANUAL ASSOCIATION FOR BOB'S ACCOUNT
	let associateBobTx = await new TokenAssociateTransaction().setAccountId(bobId).setTokenIds([tokenId]).freezeWith(client).sign(bobKey);
	let associateBobTxSubmit = await associateBobTx.execute(client);
	let associateBobRx = await associateBobTxSubmit.getReceipt(client);
	console.log(`\n- Bob NFT manual association: ${associateBobRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${associateBobTxSubmit.transactionId}`);

	// PART 2.1 STARTS ============================================================
	console.log(`\nPART 2.1 STARTS ============================================================`);
	// ENABLE TOKEN KYC FOR ALICE AND BOB
	let [aliceKycRx, aliceKycTxId] = await kycEnableFcn(aliceId);
	let [bobKyc, bobKycTxId] = await kycEnableFcn(bobId);
	console.log(`\n- Enabling token KYC for Alice's account: ${aliceKycRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${aliceKycTxId}`);
	console.log(`\n- Enabling token KYC for Bob's account: ${bobKyc.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${bobKycTxId}`);
	67898;

	// DISABLE TOKEN KYC FOR ALICE
	let kycDisableTx = await new TokenRevokeKycTransaction().setAccountId(aliceId).setTokenId(tokenId).freezeWith(client).sign(kycKey);
	// let kycDisableSubmitTx = await kycDisableTx.execute(client);
	// let kycDisableRx = await kycDisableSubmitTx.getReceipt(client);
	// console.log(`\n- Disabling token KYC for Alice's account: ${kycDisableRx.status}`);
	// console.log(`- See: https://hashscan.io/${network}/transaction/${kycDisableSubmitTx.transactionId}`);

	// QUERY TO CHECK INTIAL KYC KEY
	var tokenInfo = await tQueryFcn();
	console.log(`\n- KYC key for the NFT is: \n${tokenInfo.kycKey.toString()}`);

	// UPDATE TOKEN PROPERTIES: NEW KYC KEY
	let tokenUpdateTx = await new TokenUpdateTransaction().setTokenId(tokenId).setKycKey(newKycKey.publicKey).freezeWith(client).sign(adminKey);
	let tokenUpdateSubmitTx = await tokenUpdateTx.execute(client);
	let tokenUpdateRx = await tokenUpdateSubmitTx.getReceipt(client);
	console.log(`\n- Token update transaction (new KYC key): ${tokenUpdateRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenUpdateSubmitTx.transactionId}`);

	// QUERY TO CHECK CHANGE IN KYC KEY
	var tokenInfo = await tQueryFcn();
	console.log(`\n- KYC key for the NFT is: \n${tokenInfo.kycKey.toString()}`);

	// PART 2.1 ENDS ============================================================
	console.log(`\nPART 2.1 ENDS ============================================================`);

	// BALANCE CHECK 1
	oB = await bCheckerFcn(treasuryId);
	aB = await bCheckerFcn(aliceId);
	bB = await bCheckerFcn(bobId);
	console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID: ${tokenId} and ${oB[1]}`);
	console.log(`- Alice balance: ${aB[0]} NFTs of ID: ${tokenId} and ${aB[1]}`);
	console.log(`- Bob balance: ${bB[0]} NFTs of ID: ${tokenId} and ${bB[1]}`);

	// 1st TRANSFER NFT Treasury -> Alice
	let tokenTransferTx = await new TransferTransaction().addNftTransfer(tokenId, 2, treasuryId, aliceId).freezeWith(client).sign(treasuryKey);
	let tokenTransferSubmit = await tokenTransferTx.execute(client);
	let tokenTransferRx = await tokenTransferSubmit.getReceipt(client);
	console.log(`\n- NFT transfer Treasury -> Alice status: ${tokenTransferRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit.transactionId}`);

	// BALANCE CHECK 2
	oB = await bCheckerFcn(treasuryId);
	aB = await bCheckerFcn(aliceId);
	bB = await bCheckerFcn(bobId);
	console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`);
	console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
	console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

	// 2nd NFT TRANSFER NFT Alice - >Bob
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
	console.log(`\n- NFT transfer Alice -> Bob status: ${tokenTransferRx2.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit2.transactionId}`);

	// BALANCE CHECK 3
	oB = await bCheckerFcn(treasuryId);
	aB = await bCheckerFcn(aliceId);
	bB = await bCheckerFcn(bobId);
	console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID:${tokenId} and ${oB[1]}`);
	console.log(`- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);
	console.log(`- Bob balance: ${bB[0]} NFTs of ID:${tokenId} and ${bB[1]}`);

	// PART 2.2 STARTS ============================================================
	console.log(`\nPART 2.2 STARTS ============================================================`);

	// CREATE THE NFT TRANSFER FROM BOB -> ALICE TO BE SCHEDULED
	// REQUIRES ALICE'S AND BOB'S SIGNATURES
	let txToSchedule = new TransferTransaction()
		.addNftTransfer(tokenId, 2, bobId, aliceId)
		.addHbarTransfer(aliceId, nftPrice.negated())
		.addHbarTransfer(bobId, nftPrice);

	// SCHEDULE THE NFT TRANSFER TRANSACTION CREATED IN THE LAST STEP
	let scheduleTx = await new ScheduleCreateTransaction().setScheduledTransaction(txToSchedule).execute(client);
	let scheduleRx = await scheduleTx.getReceipt(client);
	let scheduleId = scheduleRx.scheduleId;
	let scheduledTxId = scheduleRx.scheduledTransactionId;
	console.log(`\n- The schedule ID is: ${scheduleId}`);
	console.log(`- The scheduled transaction ID is: ${scheduledTxId}`);

	// SUBMIT ALICE'S SIGNATURE FOR THE TRANSFER TRANSACTION
	let aliceSignTx = await new ScheduleSignTransaction().setScheduleId(scheduleId).freezeWith(client).sign(aliceKey);
	let aliceSignSubmit = await aliceSignTx.execute(client);
	let aliceSignRx = await aliceSignSubmit.getReceipt(client);
	console.log(`\n- Status of Alice's signature submission: ${aliceSignRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${aliceSignSubmit.transactionId}`);

	// QUERY TO CONFIRM IF THE SCHEDULE WAS TRIGGERED (SIGNATURES HAVE BEEN ADDED)
	scheduleQuery = await new ScheduleInfoQuery().setScheduleId(scheduleId).execute(client);
	console.log(`\n- Schedule triggered (all required signatures received): ${scheduleQuery.executed !== null}`);

	// SUBMIT BOB'S SIGNATURE FOR THE TRANSFER TRANSACTION
	let bobSignTx = await new ScheduleSignTransaction().setScheduleId(scheduleId).freezeWith(client).sign(bobKey);
	let bobSignSubmit = await bobSignTx.execute(client);
	let bobSignRx = await bobSignSubmit.getReceipt(client);
	console.log(`\n- Status of Bob's signature submission: ${bobSignRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${bobSignSubmit.transactionId}`);

	// QUERY TO CONFIRM IF THE SCHEDULE WAS TRIGGERED (SIGNATURES HAVE BEEN ADDED)
	scheduleQuery = await new ScheduleInfoQuery().setScheduleId(scheduleId).execute(client);
	console.log(`\n- Schedule triggered (all required signatures received): ${scheduleQuery.executed !== null}`);

	// VERIFY THAT THE SCHEDULED TRANSACTION (TOKEN TRANSFER) EXECUTED
	oB = await bCheckerFcn(treasuryId);
	aB = await bCheckerFcn(aliceId);
	bB = await bCheckerFcn(bobId);
	console.log(`\n- Treasury balance: ${oB[0]} NFTs of ID: ${tokenId} and ${oB[1]}`);
	console.log(`- Alice balance: ${aB[0]} NFTs of ID: ${tokenId} and ${aB[1]}`);
	console.log(`- Bob balance: ${bB[0]} NFTs of ID: ${tokenId} and ${bB[1]}`);

	console.log(`\n- THE END ============================================================`);
	console.log(`\n- 👇 Go to:`);
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
		let mintTx = new TokenMintTransaction().setTokenId(tokenId).setMetadata(CIDs).freezeWith(client);
		let mintTxSign = await mintTx.sign(supplyKey);
		let mintTxSubmit = await mintTxSign.execute(client);
		let mintRx = await mintTxSubmit.getReceipt(client);
		return [mintRx, mintTxSubmit.transactionId];
	}

	// BALANCE CHECKER FUNCTION ==========================================
	async function bCheckerFcn(id) {
		balanceCheckTx = await new AccountBalanceQuery().setAccountId(id).execute(client);
		return [balanceCheckTx.tokens._map.get(tokenId.toString()), balanceCheckTx.hbars];
	}

	// KYC ENABLE FUNCTION ==========================================
	async function kycEnableFcn(id) {
		let kycEnableTx = await new TokenGrantKycTransaction().setAccountId(id).setTokenId(tokenId).freezeWith(client).sign(kycKey);
		let kycSubmitTx = await kycEnableTx.execute(client);
		let kycRx = await kycSubmitTx.getReceipt(client);
		return [kycRx, kycSubmitTx.transactionId];
	}

	// TOKEN QUERY FUNCTION ==========================================
	async function tQueryFcn() {
		var tokenInfo = await new TokenInfoQuery().setTokenId(tokenId).execute(client);
		return tokenInfo;
	}
}
main();

```
{% endcode %}

</details>

## Additional Resources

**➡** [**Project Repository**](https://github.com/hedera-dev/hedera-example-hts-nft-blog-p1-p2-p3/blob/main/nft-part2.js)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Ed, DevRel Engineer</p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/ed-marquez/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ed-marquez/">https://www.linkedin.com/in/ed-marquez/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://x.com/theekrystallee">Twitter</a></p></td><td><a href="https://x.com/theekrystallee">https://x.com/theekrystallee</a></td></tr></tbody></table>
