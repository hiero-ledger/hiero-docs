# How to Auto-Create Hedera Accounts with HBAR and Token Transfers

[HIP-32](https://hips.hedera.com/hip/hip-32) introduced the ability to auto-create accounts when sending HBAR to an [alias](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/create-an-account#create-an-account-via-an-account-alias) that does not exist on the network. When HBAR is sent to an alias that does not exist on the network, the account creation fee is deducted from the HBAR sent and the account is auto-created. The new account's initial balance is (sent HBAR - account creation fee). This new method of account creation allowed wallet providers to create free "accounts" to users. However, if a user sends fungible tokens or NFT's to an alias, it would result in an _INVALID\_ACCOUNT\_ID_ error because the alias does not exist on the network. The auto-account creation flow could not deduct the account creation fee from an HTS token; the account creation fee must be paid in HBAR.

[HIP-542](https://hips.hedera.com/hip/hip-542) provides a solution to allow sending HTS tokens to an alias that does not exist on the network. This is achieved by charging the account creation fee to the transfer transaction payer. In addition, there will be one auto-association slot included in the transaction for the new account to associate with the HTS token. You won't have to first create the account, complete a token association, and then finally do a token transfer.

Furthermore, this change also applies to sending HBAR to an alias. Instead of deducting the creation fee from the sent HBAR, it will be deducted from the payer of the transfer transaction. The new account balance will receive the sent HBAR amount in full. Learn more [here](https://hedera.com/blog/auto-create-a-hedera-account-with-hbar-and-token-transfers).

The figure below highlights the transaction flow before HIP-542 and after.

<figure><img src="https://images.hedera.com/hip-542-updated.png?w=1298&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680225836&#x26;s=6739c20c796b4bde9b716fdb9042b1a3" alt=""><figcaption></figcaption></figure>

In this tutorial, a treasury account will be created to transfer HTS tokens to Bob's ECDSA public key alias, involving a transfer of 10 FT and 1 NFT. The tutorial will also cover creating fungible tokens and an NFT collection (1000 FT / 5 NFT), creating Bob's ECDSA public key alias, and returning Bob's new account ID while confirming their ownership of the transferred tokens and NFT.

***

## **Prerequisites**

* Get a [Hedera Testnet](https://portal.hedera.com/register) account [here](create-and-fund-your-hedera-testnet-account.md).&#x20;
* Set up your environment and create a client [here](../../getting-started/environment-setup.md).&#x20;

<details>

<summary>Get the example code on GitHub:</summary>

* [auto-create account by sending FT](https://github.com/a-ridley/hedera-auto-account-creation-with-ft)
* [auto-create account by sending NFT](https://github.com/a-ridley/hedera-auto-create-account-with-nft)

</details>

***

## Table of Contents

1. [Create Treasury Account](how-to-auto-create-hedera-accounts-with-hbar-and-token-transfers.md#create-treasury-account)
2. [Create FTs and NFT Collection](how-to-auto-create-hedera-accounts-with-hbar-and-token-transfers.md#create-fts-and-an-nft-collection)
3. [Create Bob's ECDSA Public Key Alias](how-to-auto-create-hedera-accounts-with-hbar-and-token-transfers.md#create-bobs-ecdsa-public-key-alias)
4. [Return New Account ID](how-to-auto-create-hedera-accounts-with-hbar-and-token-transfers.md#return-new-account-id)

***

## Create Treasury Account

We create the treasury account, which will be the holder of the fungible and non-fungible tokens. The treasury account will be created with an initial balance of 5 HBAR.

```javascript
const [treasuryAccId, treasuryAccPvKey] = await createAccount(client, 5);
```

#### Set Up Helper Functions

We will create the functions necessary to create a new account, create fungible tokens, and create a new NFT collection. Use the tabs to see the helper functions.

{% tabs %}
{% tab title="Create Account With Initial Balance" %}
```javascript
const createAccount = async (client:  Client, initialBalance: number) => {
 const accountPrivateKey = PrivateKey.generateECDSA();
 const accountPublicKey = accountPrivateKey.publicKey;
 
 const response = await new AccountCreateTransaction()
   .setInitialBalance(new Hbar(initialBalance))
   .setKey(accountPublicKey)
   //Do NOT set an alias if you need to update/rotate keys in the future
   .setAlias(accountPublicKey.toEvmAddress())
   .execute(client);
 
 const receipt = await response.getReceipt(client);
 
 return [receipt.accountId, accountPrivateKey];
};
```
{% endtab %}

{% tab title="Create Simple Fungible Token" %}
```javascript
export const createFungibleToken = async (
 client: Client,
 treasureyAccId: string | AccountId,
 supplyKey: PrivateKey,
 treasuryAccPvKey: PrivateKey,
 initialSupply: number,
 tokenName: string,
 tokenSymbol: string,
): Promise<TokenId> => {
 /*
   * Create a transaction with token type fungible
   * Returns Fungible Token Id
 */
 const createTokenTxn = await new TokenCreateTransaction()
   .setTokenName(tokenName)
   .setTokenSymbol(tokenSymbol)
   .setTokenType(TokenType.FungibleCommon)
   .setInitialSupply(initialSupply)
   .setTreasuryAccountId(treasureyAccId)
   .setSupplyKey(supplyKey)
   .setMaxTransactionFee(new Hbar(30))
   .freezeWith(client); //freeze tx from from any further mods.
 
 const createTokenTxnSigned = await createTokenTxn.sign(treasuryAccPvKey);
 // submit txn to heder network
 const txnResponse = await createTokenTxnSigned.execute(client);
 // request receipt of txn
 const txnRx = await txnResponse.getReceipt(client);
 const txnStatus = txnRx.status.toString();
 const tokenId = txnRx.tokenId;
 if (tokenId === null) {
   throw new Error("Somehow tokenId is null");
 }
 
 console.log(
   `Token Type Creation was a ${txnStatus} and was created with token id: ${tokenId}`
 );
 
 return tokenId;
};
```
{% endtab %}

{% tab title="Create Simple Non-Fungible Token" %}
```javascript
export const createNonFungibleToken = async (
 client: Client,
 treasureyAccId: string | AccountId,
 supplyKey: PrivateKey,
 treasuryAccPvKey: PrivateKey,
 initialSupply: number,
 tokenName: string,
 tokenSymbol: string,
): Promise<[TokenId | null, string]> => {
 /*
   * Create a transaction with token type fungible
   * Returns Fungible Token Id and Token Id in solidity format
 */
 const createTokenTxn = await new TokenCreateTransaction()
   .setTokenName(tokenName)
   .setTokenSymbol(tokenSymbol)
   .setTokenType(TokenType.NonFungibleUnique)
   .setDecimals(0)
   .setInitialSupply(initialSupply)
   .setTreasuryAccountId(treasureyAccId)
   .setSupplyKey(supplyKey)
   .setAdminKey(treasuryAccPvKey)
   .setMaxTransactionFee(new Hbar(30))
   .freezeWith(client); //freeze tx from from any further mods.
 
 const createTokenTxnSigned = await createTokenTxn.sign(treasuryAccPvKey);
 // submit txn to hedera network
 const txnResponse = await createTokenTxnSigned.execute(client);
 // request receipt of txn
 const txnRx = await txnResponse.getReceipt(client);
 const txnStatus = txnRx.status.toString();
 const tokenId = txnRx.tokenId;
 if (tokenId === null) { throw new Error("Somehow tokenId is null."); }
 
 const tokenIdInSolidityFormat = tokenId.toSolidityAddress();
 
 console.log(
   `Token Type Creation was a ${txnStatus} and was created with token id: ${tokenId}`
 );
 
 return [tokenId, tokenIdInSolidityFormat];
};

```
{% endtab %}

{% tab title="Mint Token And Create NFT Collection" %}
```javascript
 const mintTokenTxn = new TokenMintTransaction()
   .setTokenId(tokenId)
   .setMetadata(metadatas)
   .freezeWith(client);
 
 const mintTokenTxnSigned = await mintTokenTxn.sign(supplyKey);
 
 // submit txn to hedera network
 const txnResponse = await mintTokenTxnSigned.execute(client);
 
 const mintTokenRx = await txnResponse.getReceipt(client);
 const mintTokenStatus = mintTokenRx.status.toString();
 
 console.log(`Token mint was a ${mintTokenStatus}`);
};

export const createNewNftCollection = async (
 client: Client,
 tokenName: string,
 tokenSymbol: string,
 metadataIPFSUrls: Buffer[], // already uploaded ipfs metadata json files
 treasuryAccountId: string | AccountId,
 treasuryAccountPrivateKey: PrivateKey,
): Promise<{
 tokenId: TokenId,
 supplyKey: PrivateKey,
}> => {
 // generate supply key
 const supplyKey = PrivateKey.generateECDSA();
 
 const [tokenId,] = await createNonFungibleToken(client, treasuryAccountId, supplyKey, treasuryAccountPrivateKey, 0, tokenName, tokenSymbol);
 if (tokenId === null || tokenId === undefined) {
   throw new Error("Somehow tokenId is null");
 }
 
 const metadatas: Uint8Array[] = metadataIPFSUrls.map(url => Buffer.from(url));
 
 // mint token
 await mintToken(client, tokenId, metadatas, supplyKey);
 return {
   tokenId: tokenId,
   supplyKey: supplyKey,
 };
}
```
{% endtab %}
{% endtabs %}

***

## Create FTs and an NFT Collection

Leverage the _**createFungibleToken**_ helper function defined above to create 10000 "Hip-542 example" fungible tokens. Use the code tab switch on the upper left of the code block to see how we use _**createNewNftCollection**_ to create our new NFT collection consisting of 5 NFTs.

{% tabs %}
{% tab title="CreateFungibleToken" %}
```javascript
const tokenId = await createFungibleToken(client, treasuryAccId, supplyKey, treasuryAccPvKey, 10000, 'HIP-542 Token', 'H542');
```
{% endtab %}

{% tab title="CreateNewNftCollection" %}
```javascript
 // IPFS content identifiers for the NFT metadata
 const metadataIPFSUrls: Buffer[] = [
   Buffer.from("ipfs://bafkreiap62fsqxmo4hy45bmwiqolqqtkhtehghqauixvv5mcq7uofdpvt4"),
   Buffer.from("ipfs://bafkreibvluvlf36lilrqoaum54ga3nlumms34m4kab2x67f5piofmo5fsa"),
   Buffer.from("ipfs://bafkreidrqy67amvygjnvgr2mgdgqg2alaowoy34ljubot6qwf6bcf4yma4"),
   Buffer.from("ipfs://bafkreicoorrcx3d4foreggz72aedxhosuk3cjgumglstokuhw2cmz22n7u"),
   Buffer.from("ipfs://bafkreidv7k5vfn6gnj5mhahnrvhxep4okw75dwbt6o4r3rhe3ktraddf5a"),
 ];
 
 /**
  * Step 2
  * Create nft collection
 */
 const nftCreateTxnResponse = await createNewNftCollection(client, 'HIP-542 Example Collection', 'HIP-542', metadataIPFSUrls, treasuryAccId, treasuryAccPvKey);
```
{% endtab %}
{% endtabs %}

***

## Create Bob's ECDSA Public Key Alias

An alias is an initial public key that will convert into a Hedera account through auto-account creation. An alias consists of \<shard>.\<realm>.\<bytes>.

To learn more about accounts created via an account alias go [here](../../sdks-and-apis/sdks/accounts-and-hbar/create-an-account.md).

```javascript
 const privateKey = PrivateKey.generateECDSA();
 const publicKey = privateKey.publicKey;
 
 // Assuming that the target shard and realm are known.
 // For now they are virtually always 0 and 0.
 const aliasAccountId = publicKey.toAccountId(0, 0);
 
 console.log(`- New account ID: ${aliasAccountId.toString()}`);
 if (aliasAccountId.aliasKey === null) { throw new Error('alias key is empty') }
 console.log(`- Just the aliasKey: ${aliasAccountId.aliasKey.toString()}\n`);
```

<figure><img src="https://images.hedera.com/new-account-just-alisk-key-for-both.png?w=949&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680227584&#x26;s=325b99dd5eb404587868f06839157f62" alt=""><figcaption></figcaption></figure>

#### Set up helper functions for transferring HTS tokens

Once we have our treasury account with FT and a new NFT collection created, our next step is to transfer them to Bob using their alias. We'll create the _**sendToken**_ helper function to send fungible tokens and create _**transferNft**_ to send a single NFT.&#x20;

A quick reminder to use the tab on the left of the code block to switch between the two helper functions.

{% tabs %}
{% tab title="SendToken" %}
```javascript
export const sendToken = async (client: Client, tokenId: TokenId, owner: AccountId, aliasAccountId: AccountId, sendBalance: number, treasuryAccPvKey: PrivateKey) => {
 const tokenTransferTx = new TransferTransaction()
   .addTokenTransfer(tokenId, owner, -sendBalance)
   .addTokenTransfer(tokenId, aliasAccountId, sendBalance)
   .freezeWith(client);
 
    // Sign the transaction with the operator key
    let tokenTransferTxSign = await tokenTransferTx.sign(treasuryAccPvKey);
 
    // Submit the transaction to the Hedera network
    let tokenTransferSubmit = await tokenTransferTxSign.execute(client);
    
    // Get transaction receipt information
    await tokenTransferSubmit.getReceipt(client);
}
```
{% endtab %}

{% tab title="TransferNft" %}
```javascript
export const transferNft = async (client: Client, nftTokenId: TokenId, nftId: number, treasuryAccId: AccountId, treasuryAccPvKey: PrivateKey, aliasAccountId: AccountId) => {
 const nftTransferTx = new TransferTransaction()
   .addNftTransfer(nftTokenId, nftId, treasuryAccId, aliasAccountId)
   .freezeWith(client);
   
 // Sign the transaction with the treasury account private key
 const nftTransferTxSign = await nftTransferTx.sign(treasuryAccPvKey);
 
 // Submit the transaction to the Hedera network
 const nftTransferSubmit = await nftTransferTxSign.execute(client);
 
 // Get transaction receipt information here
 await nftTransferSubmit.getReceipt(client);
}
```
{% endtab %}
{% endtabs %}

#### Transfer FT and an NFT to Bob using their alias&#x20;

Transfer 5 fungible tokens to Bob using their alias and the helper function _**sendToken**_.

Transfer the NFT with serial number 1 to Bob using the helper function _**transfertNFT**_.

{% tabs %}
{% tab title="Send Bob 10 FT" %}
```javascript
await sendToken(client, tokenId, treasuryAccId, aliasAccountId, 5, treasuryAccPvKey);
```
{% endtab %}

{% tab title="Send Bob An NFT" %}
```javascript
const nftTokenId = nftCreateTxnResponse.tokenId;
 const exampleNftId = 1;
 await transferNft(client, nftTokenId, exampleNftId, treasuryAccId, treasuryAccPvKey, aliasAccountId);
```
{% endtab %}
{% endtabs %}

***

## Return New Account ID

Create a helper function to return the corresponding account Id to the given an alias.

```javascript
export const getAccountIdByAlias = async (client: Client, aliasAccountId: AccountId ) => {
 const accountInfo =  await new AccountInfoQuery()
   .setAccountId(aliasAccountId)
   .execute(client);
  
return accountInfo.accountId;
}
```

Next we call getAccountIdByAlias and pass in our client and Bob's alias as the arguments.

```javascript
const accountId = await getAccountIdByAlias(client, aliasAccountId);
console.log(`The normal account ID of the given alias: ${accountId}`);
```

<figure><img src="https://images.hedera.com/normal-account-id-both-hip-542.png?w=436&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680227702&#x26;s=ed33d949bedd10e7de6a645ff9805f57" alt=""><figcaption></figcaption></figure>

#### Show Bob's new account owns the 5 FT tokens

Complete an AccountBalanceQuery to show that Bob's new account owns the 5 fungible tokens the treasury account sent.

```javascript
 const accountBalances = await new AccountBalanceQuery()
   .setAccountId(aliasAccountId)
   .execute(client);
 
 if (!accountBalances.tokens || !accountBalances.tokens._map) {
   throw new Error('account balance shows no tokens.')
 }
 
 const tokenBalanceAccountId = accountBalances.tokens._map
   .get(tokenId.toString());
 
 if (!tokenBalanceAccountId) {
   throw new Error(`account balance does not have tokens for token id: ${tokenId}.`);
 }
 
 tokenBalanceAccountId.toInt() === 5
   ? console.log(
     `Account is created successfully using HTS 'TransferTransaction'`
   )
   : console.log(
     "Creating account with HTS using public key alias failed"
   );
 
 client.close();
```

<figure><img src="https://images.hedera.com/ft-account-created-successfully-hip-542.png?w=509&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680223897&#x26;s=a1aa2ca87a7f9d3fe3dcf8abb8d59aa7" alt=""><figcaption></figcaption></figure>

#### Show Bob's new account owns the NFT

First, create a helper function that creates a TokenNftInfoQuery transaction and returns the account id of the NFT owner for a specific NFT serial number.

```javascript
export const getNftOwnerByNftId = async (client: Client, nftTokenId: TokenId, exampleNftId: number) => {
 const nftInfo = await new TokenNftInfoQuery()
   .setNftId(new NftId(nftTokenId, exampleNftId))
   .execute(client);
 
 if (nftInfo === null) { throw new Error('nftInfo is null.') }
 const nftOwnerAccountId = nftInfo[0].accountId.toString();
 console.log(`- Current owner account id: ${nftOwnerAccountId} for NFT with serial number: ${exampleNftId}`);
  return nftOwnerAccountId;
}
```

Then call _**getNftOwnerByNft**_ and do a simple check to ensure the account id returned matches the account id created when we sent the NFT to Bob's alias.

```javascript
 const nftOwnerAccountId = await getNftOwnerByNftId(client, nftTokenId, exampleNftId);
 
 nftOwnerAccountId === accountId
   ? console.log(
     `The NFT owner accountId matches the accountId created with the HTS\n`
   )
   : console.log(`The two account IDs does not match\n`);
  
 client.close();
```

<figure><img src="https://images.hedera.com/nft-current-owner-account-matches-hip-542.png?w=557&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680227655&#x26;s=c47a55f519581fb79f07228b327f6649" alt=""><figcaption></figcaption></figure>

#### Console Output ✅

<figure><img src="https://images.hedera.com/account-create-with-hts-tokens-output.png?w=1104&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680222036&#x26;s=531aa614802acac1166d1849cb0158fe" alt=""><figcaption></figcaption></figure>

And that's a wrap! 🎬 You've completed sending HTS tokens to an alias and triggering an auto-account creation! As well as learned that the account creation fee is paid by the payer of the transfer transaction.&#x20;

Join and collaborate with Hedera Developers on the [Hedera Discord Server](https://hedera.com/discord)! \
\
Happy Building! 🛠️

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Abi, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://www.linkedin.com/in/a-ridley/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/a-ridley/">https://www.linkedin.com/in/a-ridley/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
