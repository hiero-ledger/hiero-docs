# Hedera Token Service - Part 3: How to Pause, Freeze, Wipe, and Delete NFTs

In [Part 1](hedera-token-service-part-1-how-to-mint-nfts.md) of the series, you saw how to mint and transfer an NFT using the Hedera Token Service (HTS). In [Part 2](hedera-token-service-part-2-kyc-update-and-scheduled-transactions.md), you saw how to enable and disable token [Know Your Customer (KYC)](../../support-and-community/glossary.md#know-your-customer-kyc), update token properties (if a token is mutable), and schedule transactions. In Part 3, you will learn how to use HTS capabilities that help you manage your tokens. Specifically, you will learn how to:

* Pause a token (stops all operations for a token ID)
* Freeze an account (stops all token operations only for a specific account)
* Wipe a token (wipe a partial or entire token balance for a specific account)
* Delete a token (the token will remain on the ledger)

{% embed url="https://youtu.be/8FWcsbm0udI" %}

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](../more-tutorials/create-and-fund-your-hedera-testnet-account.md).
* Set up your environment [here](../../getting-started/environment-setup.md).

✅ _If you want the entire code used for this tutorial, skip to the_ [_Code Check_](hedera-token-service-part-3-how-to-pause-freeze-wipe-and-delete-nfts.md#code-check) _section below._

## Pause a Token

The [pause transaction](../../sdks-and-apis/sdks/token-service/pause-a-token.md) prevents a token from being involved in any kind of operation across all accounts. Specifying a _**\<pauseKey>**_ during the creation of a token is a requirement to be able to pause token operations. The code below shows you that this key must sign the pause transaction. Note that you can’t pause a token if it doesn’t have a pause key. Also keep in mind that if this key was not set during token creation, then a token update to add this key is not possible.

Pausing a token may be useful in cases where a third party requests that you, as the administrator of a token, stop all operations for that token while something like an audit is conducted. The pause transaction provides you with a way to comply with requests of that nature.

In our example below, we pause the token, test that by trying a token transfer and checking the token _**pauseStatus**_, and then we [unpause the token](https://docs.hedera.com/hedera/sdks-and-apis/sdks/tokens/unpause-a-token) to enable operations again.

{% code title="nft-part3.js" %}
```javascript
// PAUSE ALL TOKEN OEPRATIONS
let tokenPauseTx = await new TokenPauseTransaction()
    .setTokenId(tokenId)
    .freezeWith(client)
    .sign(pauseKey);
let tokenPauseSubmitTx = await tokenPauseTx.execute(client);
let tokenPauseRx = await tokenPauseSubmitTx.getReceipt(client);
console.log(`- Token pause: ${tokenPauseRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenPauseSubmitTx.transactionId}`
);

// TEST THE TOKEN PAUSE BY TRYING AN NFT TRANSFER (TREASURY -> ALICE)
let tokenTransferTx3 = await new TransferTransaction()
    .addNftTransfer(tokenId, 3, treasuryId, aliceId)
    .freezeWith(client)
    .sign(treasuryKey);
let tokenTransferSubmit3 = await tokenTransferTx3.execute(client);
try {
   let tokenTransferRx3 = await tokenTransferSubmit3.getReceipt(client);
   console.log(
   `\n-NFT transfer Treasury -> Alice status: ${tokenTransferRx3.status}`
 );
} catch {
    // TOKEN QUERY TO CHECK PAUSE
   var tokenInfo = await tQueryFcn();
   console.log(
   `\n- NFT transfer unsuccessful: Token ${tokenId} is paused (${tokenInfo.pauseStatus})`
 );
   console.log(
   `- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit3.transactionId}`
 );
}

// UNPAUSE ALL TOKEN OPERATIONS
let tokenUnpauseTx = await new TokenUnpauseTransaction()
    .setTokenId(tokenId)
    .freezeWith(client)
    .sign(pauseKey);
let tokenUnpauseSubmitTx = await tokenUnpauseTx.execute(client);
let tokenUnpauseRx = await tokenUnpauseSubmitTx.getReceipt(client);
console.log(`- Token unpause: ${tokenUnpauseRx.status}\n`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenUnpauseSubmitTx.transactionId}`
);
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
- Token pause: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772395.193039130

- NFT transfer unsuccessful: Token 0.0.4686491 is paused (true)
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772400.030831515

- Token unpause: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772398.843664980
```

## Freeze a Token

[Freezing a token](../../sdks-and-apis/sdks/token-service/freeze-an-account.md) stops "freezes" transfers of that token for a specific account ID. Note that this transaction must be signed by the _**\<freezeKey>**_ of the token. Once a freeze executes, the specified account is marked as “Frozen” and will not be able to receive or send tokens unless unfrozen.

In our example below, we first freeze Alice’s account for the token ID we’re working with, test the freeze by trying a token transfer, and then [unfreeze](../../sdks-and-apis/sdks/token-service/unfreeze-an-account.md) Alice’s account so she can transact the token again.

{% code title="nft-part3.js" %}
```javascript
// FREEZE ALICE'S ACCOUNT FOR THIS TOKEN
let tokenFreezeTx = await new TokenFreezeTransaction()
    .setTokenId(tokenId)
    .setAccountId(aliceId)
    .freezeWith(client)
    .sign(freezeKey);
let tokenFreezeSubmitTx = await tokenFreezeTx.execute(client);
let tokenFreezeRx = await tokenFreezeSubmitTx.getReceipt(client);
console.log(
  `\n- Freeze Alice's account for token ${tokenId}: ${tokenFreezeRx.status}`
);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenFreezeSubmitTx.transactionId}`
);

// TEST THE TOKEN FREEZE FOR THE ACCOUNT BY TRYING A TRANSFER (ALICE -> BOB)
let tokenTransferTx4 = await new TransferTransaction()
    .addNftTransfer(tokenId, 2, aliceId, bobId)
    .addHbarTransfer(aliceId, nftPrice)
    .addHbarTransfer(bobId, nftPrice.negated())
    .freezeWith(client)
    .sign(aliceKey);
let tokenTransferTx4Sign = await tokenTransferTx4.sign(bobKey);
let tokenTransferSubmit4 = await tokenTransferTx4Sign.execute(client);
try {
  let tokenTransferRx4 = await tokenTransferSubmit4.getReceipt(client);
  console.log(
    `\n- NFT transfer Alice -> Bob status: ${tokenTransferRx4.status}`
 );
} catch {
  console.log(
    `\n- NFT transfer Alice -> Bob unsuccessful: Alice's account is frozen for this token`
 );
  console.log(
    `- See: https://hashscan.io/${network}/transaction/${
     tokenTransferSubmit4.transactionId
  }`
 );
}

// UNFREEZE ALICE'S ACCOUNT FOR THIS TOKEN
let tokenUnfreezeTx = await new TokenUnfreezeTransaction()
    .setTokenId(tokenId)
    .setAccountId(aliceId)
    .freezeWith(client)
    .sign(freezeKey);
let tokenUnfreezeSubmitTx = await tokenUnfreezeTx.execute(client);
let tokenUnfreezeRx = await tokenUnfreezeSubmitTx.getReceipt(client);
console.log(
  `\n- Unfreeze Alice's account for token ${tokenId}: ${tokenUnfreezeRx.status}`
);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${
    tokenUnfreezeSubmitTx.transactionId
 }`
);
```
{% endcode %}

**Console output:**

```bash
- Freeze Alice's account for token 0.0.46864: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772402.841848773

- NFT transfer Alice -> Bob unsuccessful: Alice's account is frozen for this token
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772405.380352596

- Unfreeze Alice's account for token 0.0.4686491: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.46446@1723772403.655969673
```

## Wipe a Token

[This operation](../../sdks-and-apis/sdks/token-service/wipe-a-token.md) wipes the provided amount of fungible or non-fungible tokens from the specified account. You see from the code below that this transaction must be signed by the token's _**\<wipeKey>**_.

{% hint style="info" %}
**Note**: Wiping an account's tokens burns the tokens and decreases the total supply. This transaction does not delete tokens from the treasury account. You must use the Token Burn operation to delete tokens from the treasury.
{% endhint %}

In this case, we wipe the NFT that Alice currently holds. We then check Alice’s balance and the NFT supply to see how these change with the wipe operation (these two values before the wipe are provided for comparison – see [Part 2](hedera-token-service-part-2-kyc-update-and-scheduled-transactions.md) for the details).

{% code title="nft-part3.js" %}
```javascript
// WIPE THE TOKEN FROM ALICE'S ACCOUNT
let tokenWipeTx = await new TokenWipeTransaction()
    .setAccountId(aliceId)
    .setTokenId(tokenId)
    .setSerials([2])
    .freezeWith(client)
    .sign(wipeKey);
let tokenWipeSubmitTx = await tokenWipeTx.execute(client);
let tokenWipeRx = await tokenWipeSubmitTx.getReceipt(client);
console.log(`\n- Wipe token ${tokenId} from Alice's account: ${tokenWipeRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${tokenWipeSubmitTx.transactionId}`
);

// CHECK ALICE'S BALANCE
aB = await bCheckerFcn(aliceId);
console.log(`\n- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);

// TOKEN QUERY TO CHECK TOTAL TOKEN SUPPLY
var tokenInfo = await tQueryFcn();
console.log(`- Current NFT supply: ${tokenInfo.totalSupply}`);
```
{% endcode %}

**Console output:**

<figure><img src="../../.gitbook/assets/wipe-token-hedera-token-service-blog-pt3.webp" alt=""><figcaption></figcaption></figure>

## Delete a Token

After you [delete a token](../../sdks-and-apis/sdks/token-service/delete-a-token.md), it’s no longer possible to perform any operations for that token, and transactions resolve to the error `TOKEN_WAS_DELETED`. Note that the token remains in the ledger, and you can still retrieve some information about it.

The delete operation must be signed by the token _**\<adminKey>**_. Remember from [Part 1](hedera-token-service-part-1-how-to-mint-nfts.md) that if this key is not set during token creation, then the token is immutable and deletion is not possible.

In our example, we delete the token and perform a query to double-check that the deletion was successful. Note that for NFTs, you can’t delete a specific serial ID. Instead, you delete the entire class of the NFT specified by the token ID.

{% code title="nft-part3.js" %}
```javascript
// DELETE THE TOKEN
let tokenDeleteTx = await new TokenDeleteTransaction()
    .setTokenId(tokenId)
    .freezeWith(client);
let tokenDeleteSign = await tokenDeleteTx.sign(adminKey);
let tokenDeleteSubmit = await tokenDeleteSign.execute(client);
let tokenDeleteRx = await tokenDeleteSubmit.getReceipt(client);
console.log(`\n- Delete token ${tokenId}: ${tokenDeleteRx.status}`);
console.log(
  `- See: https://hashscan.io/${network}/transaction/${
   tokenDeleteSubmitTx.transactionId
  }`
);

// TOKEN QUERY TO CHECK DELETION
var tokenInfo = await tQueryFcn();
console.log(`- Token ${tokenId} is deleted: ${tokenInfo.isDeleted}`);
```
{% endcode %}

**Console output:**

```bash
- Delete token 0.0.4686491: SUCCESS
- See: https://hashscan.io/testnet/transaction/0.0.4644601@1723772409.964607591

- Token 0.0.4686491 is deleted: true
```

## Conclusion

In this article, you saw key capabilities to help you manage your HTS tokens, including how to: pause, freeze, wipe, and delete tokens. If you haven’t already, check out [Part 1](hedera-token-service-part-1-how-to-mint-nfts.md) and [Part 2](hedera-token-service-part-2-kyc-update-and-scheduled-transactions.md) of this tutorial series to see examples of how to do even more with HTS – you will see how to mint NFTs, transfer NFTs, perform token KYC, schedule transactions, and more.

Continue learning more in our [learning center](https://hedera.com/learning/what-is-hedera-hashgraph)!

<details>

<summary><strong>Code Check</strong> ✅</summary>

{% code title="nft-part3.js" %}
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
	TokenUpdateTransaction,
	TokenGrantKycTransaction,
	TokenRevokeKycTransaction,
	ScheduleCreateTransaction,
	ScheduleSignTransaction,
	ScheduleInfoQuery,
	TokenPauseTransaction,
	TokenUnpauseTransaction,
	TokenWipeTransaction,
	TokenFreezeTransaction,
	TokenUnfreezeTransaction,
	TokenDeleteTransaction,
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

	// PART 3 ============================================================
	console.log(`\nPART 3 STARTS ============================================================`);

	// PAUSE ALL TOKEN OEPRATIONS
	let tokenPauseTx = await new TokenPauseTransaction().setTokenId(tokenId).freezeWith(client).sign(pauseKey);
	let tokenPauseSubmitTx = await tokenPauseTx.execute(client);
	let tokenPauseRx = await tokenPauseSubmitTx.getReceipt(client);
	console.log(`\n- Token pause: ${tokenPauseRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenPauseSubmitTx.transactionId}`);

	// TEST THE TOKEN PAUSE BY TRYING AN NFT TRANSFER (TREASURY -> ALICE)
	let tokenTransferTx3 = await new TransferTransaction().addNftTransfer(tokenId, 3, treasuryId, aliceId).freezeWith(client).sign(treasuryKey);
	let tokenTransferSubmit3 = await tokenTransferTx3.execute(client);
	try {
		let tokenTransferRx3 = await tokenTransferSubmit3.getReceipt(client);
		console.log(`\n- NFT transfer Treasury -> Alice status: ${tokenTransferRx3.status}`);
	} catch {
		// TOKEN QUERY TO CHECK PAUSE
		var tokenInfo = await tQueryFcn();
		console.log(`\n- NFT transfer unsuccessful: Token ${tokenId} is paused (${tokenInfo.pauseStatus})`);
		console.log(`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit3.transactionId}`);
	}

	// UNPAUSE ALL TOKEN OEPRATIONS
	let tokenUnpauseTx = await new TokenUnpauseTransaction().setTokenId(tokenId).freezeWith(client).sign(pauseKey);
	let tokenUnpauseSubmitTx = await tokenUnpauseTx.execute(client);
	let tokenUnpauseRx = await tokenUnpauseSubmitTx.getReceipt(client);
	console.log(`\n- Token unpause: ${tokenUnpauseRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenUnpauseSubmitTx.transactionId}`);

	// FREEZE ALICE'S ACCOUNT FOR THIS TOKEN
	let tokenFreezeTx = await new TokenFreezeTransaction().setTokenId(tokenId).setAccountId(aliceId).freezeWith(client).sign(freezeKey);
	let tokenFreezeSubmitTx = await tokenFreezeTx.execute(client);
	let tokenFreezeRx = await tokenFreezeSubmitTx.getReceipt(client);
	console.log(`\n- Freeze Alice's account for token ${tokenId}: ${tokenFreezeRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenFreezeSubmitTx.transactionId}`);

	// TEST THE TOKEN FREEZE FOR THE ACCOUNT BY TRYING A TRANSFER (ALICE -> BOB)
	let tokenTransferTx4 = await new TransferTransaction()
		.addNftTransfer(tokenId, 2, aliceId, bobId)
		.addHbarTransfer(aliceId, nftPrice)
		.addHbarTransfer(bobId, nftPrice.negated())
		.freezeWith(client)
		.sign(aliceKey);
	let tokenTransferTx4Sign = await tokenTransferTx4.sign(bobKey);
	let tokenTransferSubmit4 = await tokenTransferTx4Sign.execute(client);
	try {
		let tokenTransferRx4 = await tokenTransferSubmit4.getReceipt(client);
		console.log(`\n- NFT transfer Alice -> Bob status: ${tokenTransferRx4.status}`);
	} catch {
		console.log(`\n- NFT transfer Alice -> Bob unsuccessful: Alice's account is frozen for this token`);
		console.log(`- See: https://hashscan.io/${network}/transaction/${tokenTransferSubmit4.transactionId}`);
	}
	// UNFREEZE ALICE'S ACCOUNT FOR THIS TOKEN
	let tokenUnfreezeTx = await new TokenUnfreezeTransaction().setTokenId(tokenId).setAccountId(aliceId).freezeWith(client).sign(freezeKey);
	let tokenUnfreezeSubmitTx = await tokenUnfreezeTx.execute(client);
	let tokenUnfreezeRx = await tokenUnfreezeSubmitTx.getReceipt(client);
	console.log(`\n- Unfreeze Alice's account for token ${tokenId}: ${tokenUnfreezeRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenUnfreezeSubmitTx.transactionId}`);

	// WIPE THE TOKEN FROM ALICE'S ACCOUNT
	let tokenWipeTx = await new TokenWipeTransaction().setAccountId(aliceId).setTokenId(tokenId).setSerials([2]).freezeWith(client).sign(wipeKey);
	let tokenWipeSubmitTx = await tokenWipeTx.execute(client);
	let tokenWipeRx = await tokenWipeSubmitTx.getReceipt(client);
	console.log(`\n- Wipe token ${tokenId} from Alice's account: ${tokenWipeRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenWipeSubmitTx.transactionId}`);

	// CHECK ALICE'S BALANCE
	aB = await bCheckerFcn(aliceId);
	console.log(`\n- Alice balance: ${aB[0]} NFTs of ID:${tokenId} and ${aB[1]}`);

	// TOKEN QUERY TO CHECK TOTAL TOKEN SUPPLY
	var tokenInfo = await tQueryFcn();
	console.log(`- Current NFT supply: ${tokenInfo.totalSupply}`);

	// DELETE THE TOKEN
	let tokenDeleteTx = new TokenDeleteTransaction().setTokenId(tokenId).freezeWith(client);
	let tokenDeleteSign = await tokenDeleteTx.sign(adminKey);
	let tokenDeleteSubmitTx = await tokenDeleteSign.execute(client);
	let tokenDeleteRx = await tokenDeleteSubmitTx.getReceipt(client);
	console.log(`\n- Delete token ${tokenId}: ${tokenDeleteRx.status}`);
	console.log(`- See: https://hashscan.io/${network}/transaction/${tokenDeleteSubmitTx.transactionId}`);

	// TOKEN QUERY TO CHECK DELETION
	var tokenInfo = await tQueryFcn();
	console.log(`\n- Token ${tokenId} is deleted: ${tokenInfo.isDeleted}`);

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

**➡** [**Project Repository**](https://github.com/hedera-dev/hedera-example-hts-nft-blog-p1-p2-p3/blob/main/nft-part3.js)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Ed, DevRel Engineer</p><p><a href="https://github.com/ed-marquez">GitHub</a> | <a href="https://www.linkedin.com/in/ed-marquez/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/ed-marquez/">https://www.linkedin.com/in/ed-marquez/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://x.com/theekrystallee">Twitter</a></p></td><td><a href="https://x.com/theekrystallee">https://x.com/theekrystallee</a></td></tr></tbody></table>
