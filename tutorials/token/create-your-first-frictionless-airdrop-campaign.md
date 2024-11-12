# Create Your First Frictionless Airdrop Campaign

## Summary

In this tutorial, we’ll guide you through Hedera’s **Frictionless Airdrop** flow, which streamlines token distribution while giving recipients greater control over the tokens they receive. Throughout the tutorial, you’ll work with four new transaction types designed to simplify the airdrop process and safeguard users' token holdings. Each transaction type plays a unique role in enabling frictionless, user-friendly airdrops.&#x20;

Before diving into the step-by-step guide, here’s a quick overview of the transaction types you’ll encounter:

* [**TokenAirdropTransaction**](../../sdks-and-apis/sdks/token-service/airdrop-a-token.md): This transaction allows you to distribute tokens to multiple recipients in a single transaction, even if the receiving accounts haven’t pre-associated with the token. It also supports pending transfers when accounts don’t have the necessary token associations.
* [**TokenClaimAirdropTransaction**](../../sdks-and-apis/sdks/token-service/claim-a-token.md): When a **recipient** doesn’t have available token associations, this transaction allows them to claim their tokens from a pending airdrop, ensuring full control over which tokens they want to accept.
* [**TokenRejectTransaction**](../../sdks-and-apis/sdks/token-service/reject-an-airdrop.md): If a user **receives** unwanted tokens, they can use this transaction to transfer them back to the token’s treasury without incurring any custom fees or royalties.
* [**TokenCancelAirdropTransaction**](../../sdks-and-apis/sdks/token-service/cancel-a-token.md): This transaction allows the **sender** to cancel a pending airdrop if the recipient hasn’t claimed it yet. It gives the sender flexibility to manage unclaimed token transfers.

Now that we have a basic understanding of the transaction types, let’s move on to the step-by-step implementation of a Frictionless Airdrop on Hedera. The full code solution is provided at the end of the tutorial if you want to see it.&#x20;

***

## Prerequisites

We recommend you complete the following introduction to get a basic understanding of Hedera transactions. This example does not build upon the previous examples.

* Get a [Hedera testnet account](https://docs.hedera.com/hedera/getting-started/introduction).
* Set up your environment [here](https://docs.hedera.com/hedera/getting-started/environment-set-up).
* `.env` file with this additional variable added:
  * <pre><code><strong>HEDERA_NETWORK=testnet
    </strong></code></pre>

***

In this tutorial, we would like to airdrop a fungible token, "GameGold," and a non-fungible token to our gaming community. We would like to send an equal amount of tokens to the top three players in our community without them knowing about the upcoming airdrop. Let's learn!

### Step 1: Setup and Account Creation

First, we need to create the three accounts that will receive our airdrop and a treasury account. Each account will have a different configuration for automatic token association slots:

* **Alice:** Unlimited token association slots by setting the value to `-1` which means she can receive as many tokens as she wants, and they will be automatically associated with her account.
* **Bob:** Only has 1 free token association slot.
* **Charlie:** Has no free token association slots.
* **Treasury**: This account will send the airdrop as it controls the treasury of our token collections.

The setup code imports the required functions from the Hedera SDK and the `dotenv` package to load the required environment variables. Further, it creates the accounts for Alice, Bob, and Charlie.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import {
    Client, PrivateKey, AccountId, AccountCreateTransaction, Hbar, NftId, 
    TokenCreateTransaction, TokenType, TokenMintTransaction, AccountBalanceQuery,
    TokenAirdropTransaction,
    TokenClaimAirdropTransaction,
    TokenCancelAirdropTransaction,
    TokenRejectTransaction,
} from "@hashgraph/sdk";

import dotenv from "dotenv";
dotenv.config();

async function main() {
    if (
        process.env.MY_ACCOUNT_ID == null ||
        process.env.MY_PRIVATE_KEY == null ||
        process.env.HEDERA_NETWORK == null
    ) {
        throw new Error(
            "Environment variables MY_ACCOUNT_ID, HEDERA_NETWORK, and MY_PRIVATE_KEY are required.",
        );
    }

    const client = Client.forName(process.env.HEDERA_NETWORK).setOperator(
        AccountId.fromString(process.env.MY_ACCOUNT_ID),
        PrivateKey.fromStringDer(process.env.MY_PRIVATE_KEY),
    );

    /**
     * Step 1: Create 4 accounts
     */
    const alicePrivateKey = PrivateKey.generateED25519();
    const { accountId: aliceId } = await (
        await new AccountCreateTransaction()
            .setKey(alicePrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(-1) // unlimited associations
            .execute(client)
    ).getReceipt(client);

    const bobPrivateKey = PrivateKey.generateED25519();
    const { accountId: bobId } = await (
        await new AccountCreateTransaction()
            .setKey(bobPrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(1) // 1 association
            .execute(client)
    ).getReceipt(client);

 
    const charliePrivateKey = PrivateKey.generateED25519();
    const { accountId: charlieId } = await (
        await new AccountCreateTransaction()
            .setKey(charliePrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(0) // no association slots
            .execute(client)
    ).getReceipt(client);

    // treasury account for tokens
    const treasuryKey = PrivateKey.generateED25519();
    const { accountId: treasuryAccount } = await (
        await new AccountCreateTransaction()
            .setKey(treasuryKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(-1)
            .execute(client)
    ).getReceipt(client);
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
	"fmt"
	"log"
	"os"
	"time"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
	}

	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))

	// Step 1: Create 4 accounts
	alicePrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Alice: %v", err)
	}
	aliceId := createAccount(client, alicePrivateKey, 10, -1) // Unlimited associations

	bobPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Bob: %v", err)
	}
	bobId := createAccount(client, bobPrivateKey, 10, 1) // 1 association

	charliePrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Charlie: %v", err)
	}
	charlieId := createAccount(client, charliePrivateKey, 10, 0) // No associations

	treasuryPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Treasury: %v", err)
	}
	treasuryAccount := createAccount(client, treasuryPrivateKey, 10, -1) // Unlimited associations

	fmt.Printf("Alice Account ID: %v\n", aliceId)
	fmt.Printf("Bob Account ID: %v\n", bobId)
	fmt.Printf("Charlie Account ID: %v\n", charlieId)
	fmt.Printf("Treasury Account ID: %v\n", treasuryAccount)
	// ...
}

// Helper function to generate a new Hedera account
func createAccount(client *hedera.Client, newAccountPrivateKey hedera.PrivateKey, initialBalance float64, maxAssociations int32) hedera.AccountID {
	newAccountPublicKey := newAccountPrivateKey.PublicKey()

	// Create the new account with the generated public key
	newAccountTx, err := hedera.NewAccountCreateTransaction().
		SetKey(newAccountPublicKey).
		SetInitialBalance(hedera.HbarFrom(initialBalance, hedera.HbarUnits.Hbar)).
		SetMaxAutomaticTokenAssociations(maxAssociations).
		Execute(client)

	if err != nil {
		log.Fatalf("Error creating new account: %v", err)
	}

	receipt, err := newAccountTx.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for account creation: %v", err)
	}

	newAccountId := *receipt.AccountID

	return newAccountId
}
```
{% endtab %}
{% endtabs %}

### Step 2: Create a New Token

Let's create a new token called "GameGold" that we will airdrop to our gaming community. We’ll mint `300` tokens in total and distribute them equally among our top three players, Alice, Bob, and Charlie, with each player receiving `100` tokens.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">    /**
     * Step 2: Create FT and NFT mint
     */
    const INITIAL_SUPPLY = 300;
<strong>    const tokenCreateTx = await new TokenCreateTransaction()
</strong>        .setTokenName("GameGold")
        .setTokenSymbol("GG")
        .setDecimals(3)
        .setInitialSupply(INITIAL_SUPPLY)
        .setTreasuryAccountId(treasuryAccount)
        .setAdminKey(client.operatorPublicKey)
        .setFreezeKey(client.operatorPublicKey)
        .setSupplyKey(client.operatorPublicKey)
        .setMetadataKey(client.operatorPublicKey)
        .setPauseKey(client.operatorPublicKey)
        .freezeWith(client)
        .sign(treasuryKey);

    const { tokenId } = await (
        await tokenCreateTx.execute(client)
    ).getReceipt(client);
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
	// Step 2: Create FT and mint
	tokenCreateTx, err := hedera.NewTokenCreateTransaction().
		SetTokenName("GameGold").
		SetTokenSymbol("GG").
		SetDecimals(3).
		SetInitialSupply(300).
		SetTreasuryAccountID(treasuryAccount).
		SetAdminKey(client.GetOperatorPublicKey()).
		SetFreezeKey(client.GetOperatorPublicKey()).
		SetSupplyKey(client.GetOperatorPublicKey()).
		SetMetadataKey(client.GetOperatorPublicKey()).
		FreezeWith(client)

	if err != nil {
		log.Fatalf("Error creating fungible token transaction: %v", err)
	}

	tokenCreateTxSigned := tokenCreateTx.Sign(treasuryPrivateKey)
	tokenCreateTxReceipt, err := tokenCreateTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing fungible token transaction: %v", err)
	}

	receipt, err := tokenCreateTxReceipt.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for fungible token: %v", err)
	}

	tokenId := *receipt.TokenID
	fmt.Printf("Created fungible token with ID: %v\n", tokenId.String())
```
{% endtab %}
{% endtabs %}

### Step 3: Airdrop Tokens with Frictionless Airdrop

Ok, we are ready to airdrop the `GameGold`  token to the top 3 players Alice, Bob, and Charlie. Each of them receives 100 tokens. The code should print one pending airdrop for Charlie because Alice and Bob had free token association slots. Only Charlie didn't have any free slots, so he had to claim the token himself.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">    /**
     * Step 3: Airdrop fungible tokens to 3 accounts
     */
    const AIRDROP_SUPPLY_PER_ACCOUNT = INITIAL_SUPPLY / 3;
    const airdropRecord = await (
        await (
<strong>            await new TokenAirdropTransaction()
</strong>                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    aliceId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    bobId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    charlieId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getRecord(client);

    // Get the transaction record and see the pending airdrops
    const { newPendingAirdrops } = airdropRecord;
    console.log("Pending airdrop", newPendingAirdrops[0]);

    // Query to verify Alice and Bob have received the airdrops and Charlie has not
    let aliceBalance = await new AccountBalanceQuery()
        .setAccountId(aliceId)
        .execute(client);

    let bobBalance = await new AccountBalanceQuery()
        .setAccountId(bobId)
        .execute(client);

    let charlieBalance = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Alice balance after airdrop: ",
        aliceBalance.tokens.get(tokenId).toInt(),
    );
    console.log(
        "Bob balance after airdrop: ",
        bobBalance.tokens.get(tokenId).toInt(),
    );
    console.log(
        "Charlie balance after airdrop: ",
        charlieBalance.tokens.get(tokenId),
    );
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
	// Step 3: Airdrop 100 tokens each to 3 accounts
	airdropTx, err := hedera.NewTokenAirdropTransaction().
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, aliceId, 100).
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, bobId, 100).
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, charlieId, 100).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token airdrop transaction: %v", err)
	}

	airdropTxSigned := airdropTx.Sign(treasuryPrivateKey)
	airdropTxReceipt, err := airdropTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token airdrop transaction: %v", err)
	}
	airdropTxRecord, err := airdropTxReceipt.GetRecord(client)
	if err != nil {
		log.Fatalf("Error getting record for token airdrop transaction: %v", err)
	}
	fmt.Println("Pending airdrop: ", airdropTxRecord.PendingAirdropRecords[0].String())
	fmt.Println("Airdropped fungible tokens")

	aliceBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(aliceId).Execute(client)
	fmt.Println("Alice's balance: ", aliceBalance.Tokens.Get(tokenId))
	bobBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(bobId).Execute(client)
	fmt.Println("Bob's balance: ", bobBalance.Tokens.Get(tokenId))
	charlieBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))
```
{% endtab %}
{% endtabs %}

The output we expect here is that Alice and Bob have received 100 units of our token, and the balance for Charlie shows `null`.&#x20;

### Step 4: Charlie Claims the Airdrop

Alright, Charlie still needs to claim his tokens. Let's do this first using the `TokenClaimAirdropTransaction` and verify its balance to ensure he has received the 100 units of our `GameGold` token.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">    /**
     * Step 4: Claim the airdrop for Charlie's account
     */
    console.log("Pending airdrop ID: ", newPendingAirdrops[0].airdropId);
<strong>    const tokenClaimTx = await new TokenClaimAirdropTransaction()
</strong>        .addPendingAirdropId(newPendingAirdrops[0].airdropId)
        .freezeWith(client)
        .sign(charliePrivateKey);
    const tokenClaimTxResponse = await tokenClaimTx.execute(client);
    const tokenClaimTxReceipt = await tokenClaimTxResponse.getReceipt(client);

    console.log(
        `Status of token claim airdrop transaction: ${tokenClaimTxReceipt.status.toString()}`,
    );

    const charlieBalanceAfterClaim = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Charlie balance after airdrop claim",
        charlieBalanceAfterClaim.tokens.get(tokenId).toInt(),
    );
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
	// Step 4: Claim the airdrop for Charlie's account
	tokenClaimTx, err := hedera.NewTokenClaimAirdropTransaction().AddPendingAirdropId(airdropTxRecord.PendingAirdropRecords[0].GetPendingAirdropId()).FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token claim airdrop transaction: %v", err)
	}
	tokenClaimTxSigned := tokenClaimTx.Sign(charliePrivateKey)

	_, err = tokenClaimTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token claim airdrop transaction: %v", err)
	}
	fmt.Println("Claimed airdrop for Charlie's account")
	time.Sleep(4 * time.Second)
	charlieBalance, _ = hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))
```
{% endtab %}
{% endtabs %}

### Step 5: Airdrop NFTs to Alice, Bob, and Charlie

We would like to airdrop Game NFTs to Alice, Bob, and Charlie in this step. Bob has already used his free association slot, and Charlie also doesn't have any free association slots. This part of the tutorial will teach you how to use the `TokenCancelAirdropTransaction` and `TokenRejectTransaction`.

First, we need to create the NFT collection and mint new NFts. Then, we can airdrop the NFTs. Two pending airdrops should be created for Bob and Charlie as they don't have free auto association slots available.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript"><strong>    /**
</strong>     * Step 5: Airdrop the NFTs to Alice, Bob, and Charlie
     */
   const CID = [
        "QmNPCiNA3Dsu3K5FxDPMG5Q3fZRwVTg14EXA92uqEeSRXn",
        "QmZ4dgAgt8owvnULxnKxNe8YqpavtVCXmc1Lt2XajFpJs9",
        "QmPzY5GxevjyfMUF5vEAjtyRoigzWp47MiKAtLBduLMC1T",
        "Qmd3kGgSrAwwSrhesYcY7K54f3qD7MDo38r7Po2dChtQx5",
        "QmWgkKz3ozgqtnvbCLeh7EaR1H8u5Sshx3ZJzxkcrT3jbw",
    ];
    
    // Create Game NFT
    const { tokenId: nftId } = await (
        await (
<strong>            await new TokenCreateTransaction()
</strong>                .setTokenName("Game NFT")
                .setTokenSymbol("GNFT")
                .setTokenType(TokenType.NonFungibleUnique)
                .setTreasuryAccountId(treasuryAccount)
                .setAdminKey(client.operatorPublicKey)
                .setFreezeKey(client.operatorPublicKey)
                .setSupplyKey(client.operatorPublicKey)
                .setMetadataKey(client.operatorPublicKey)
                .setPauseKey(client.operatorPublicKey)
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getReceipt(client);

    let serialsNfts = [];
    for (let i = 0; i &#x3C; CID.length; i++) {
        const { serials } = await (
<strong>            await new TokenMintTransaction()
</strong>                .setTokenId(nftId)
                .addMetadata(Buffer.from("-"))
                .execute(client)
        ).getReceipt(client);

        serialsNfts.push(serials[0]);
    }
    
    // Airdrop NFTs
    const { newPendingAirdrops: newPendingAirdropsNfts } = await (
        await (
<strong>            await new TokenAirdropTransaction()
</strong>                .addNftTransfer(
                    nftId,
                    serialsNfts[0],
                    treasuryAccount,
                    aliceId,
                )
                .addNftTransfer(
                    nftId,
                    serialsNfts[1],
                    treasuryAccount,
                    bobId,
                )
                .addNftTransfer(
                    nftId,
                    serialsNfts[2],
                    treasuryAccount,
                    charlieId,
                )
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getRecord(client);

    // Get the tx record and verify two pending airdrops (for Bob and Charlie)
    console.log("Pending airdrops length", newPendingAirdropsNfts.length);
    console.log("Pending airdrop for Bob:", newPendingAirdropsNfts[0]);
    console.log("Pending airdrop for Charlie:", newPendingAirdropsNfts[1]);
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
	// Step 5: Airdrop the NFTs to Alice, Bob, and Charlie
	CID := [][]byte{
		[]byte("ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"),
		[]byte("ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"),
		[]byte("ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"),
		[]byte("ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"),
		[]byte("ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"),
	}
	nftCreate, err := hedera.NewTokenCreateTransaction().
		SetTokenName("Game NFT").
		SetTokenSymbol("GNFT").
		SetTokenType(hedera.TokenTypeNonFungibleUnique).
		SetTreasuryAccountID(treasuryAccount).
		SetSupplyKey(client.GetOperatorPublicKey()).
		SetAdminKey(client.GetOperatorPublicKey()).
		FreezeWith(client)

	nftCreateTxSign := nftCreate.Sign(treasuryPrivateKey)
	nftCreateSubmit, err := nftCreateTxSign.Execute(client)
	if err != nil {
		log.Fatalf("Error creating NFT: %v", err)
	}

	nftCreateReceipt, err := nftCreateSubmit.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for NFT creation: %v", err)
	}

	fmt.Println("Created NFT with ID: 0.0.", nftCreateReceipt.TokenID.Token)
	nftId := *nftCreateReceipt.TokenID
	var serialsNfts []int64
	mintTx, _ := hedera.NewTokenMintTransaction().
		SetTokenID(nftId).
		SetMetadatas(CID).
		FreezeWith(client)
	mintTxSign := mintTx.Sign(treasuryPrivateKey)
	mintTxSubmit, err := mintTxSign.Execute(client)
	if err != nil {
		log.Fatalf("Error minting NFTs: %v", err)
	}
	mintTxReceipt, err := mintTxSubmit.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for NFT minting: %v", err)
	}
	serialsNfts = mintTxReceipt.SerialNumbers
	fmt.Printf("Minted NFT serial numbers: %v\n", serialsNfts)

	// Airdrop NFTs
	nftAirdropTx, err := hedera.NewTokenAirdropTransaction().
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 1}, treasuryAccount, aliceId).
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 2}, treasuryAccount, bobId).
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 3}, treasuryAccount, charlieId).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating NFT airdrop transaction: %v", err)
	}

	nftAirdropTxSigned := nftAirdropTx.Sign(treasuryPrivateKey)
	nftAirdropTxSubmit, err := nftAirdropTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing NFT airdrop transaction: %v", err)
	}
	nftAirdropTxRecord, err := nftAirdropTxSubmit.GetRecord(client)
	if err != nil {
		log.Fatalf("Error getting record for NFT airdrop transaction: %v", err)
	}
	fmt.Println("Pending airdrops length: ", len(nftAirdropTxRecord.PendingAirdropRecords))
	fmt.Println("Pending airdrop for Bob: ", nftAirdropTxRecord.PendingAirdropRecords[0].String())
	fmt.Println("Pending airdrop for Charlie: ", nftAirdropTxRecord.PendingAirdropRecords[1].String())
```
{% endtab %}
{% endtabs %}

### Step 6: Cancel Airdrop for Charlie

When airdropping the NFTs, we realized we made a mistake: Charlie is not actually our third-best player, but Greg is. To fix this, we need to cancel the pending airdrop to Charlie so that the NFT returns to our treasury account. Once the NFT is back, we can airdrop it to Greg, who is the rightful recipient. Let's learn how to use the `TokenCancelAirdropTransaction` to cancel an airdrop for a specific account.

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">    /**
     * Step 6: Cancel the airdrop for Charlie
     * No signature is needed as the operator account is the admin of the token
     */
<strong>    await new TokenCancelAirdropTransaction()
</strong>        .addPendingAirdropId(newPendingAirdropsNfts[1].airdropId) // Charlie's airdrop
        .execute(client);
</code></pre>
{% endtab %}

{% tab title="Go" %}
<pre class="language-go"><code class="lang-go"><strong>	// Step 6: Cancel Airdrop for Charlie
</strong>	hedera.NewTokenCancelAirdropTransaction().
		AddPendingAirdropId(nftAirdropTxRecord.PendingAirdropRecords[1].GetPendingAirdropId()).
		Execute(client)
</code></pre>
{% endtab %}
{% endtabs %}

### Step 7: Token Reject Already Received Airdrop

And lastly, as Charlie is not the rightful owner of the received fungible token, we've asked him if he wants to return the 100 units of our `GameGold` token. Charlie has agreed and can return the token without paying any fees or royalties using the `TokenRejectTransaction`.&#x20;

{% hint style="success" %}
The `reject` functionality can be used to return an already claimed or a successful airdrop into your account. It can't be used to reject a pending airdrop.&#x20;
{% endhint %}

Here's the code for step 7. The rejected `GameGold` tokens will be returned to the treasury account.&#x20;

{% tabs %}
{% tab title="JavaScript" %}
<pre class="language-javascript"><code class="lang-javascript">    /**
     * Step 7: Reject the fungible tokens for Charlie
     */
    await (
        await (
<strong>            await new TokenRejectTransaction()
</strong>                .setOwnerId(charlieId)
                .addTokenId(tokenId)
                .freezeWith(client)
                .sign(charliePrivateKey)
        ).execute(client)
    ).getReceipt(client);

    charlieBalance = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Charlie's balance after reject: ",
        charlieBalance.tokens.get(tokenId).toInt(),
    );
    
    client.close();
}

void main();
</code></pre>
{% endtab %}

{% tab title="Go" %}
```go
	// Step 7: Reject the fungible tokens for Charlie
	tokenRejectTx, err := hedera.NewTokenRejectTransaction().
		AddTokenID(tokenId).
		SetOwnerID(charlieId).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token reject transaction: %v", err)
	}
	tokenRejectTxSigned := tokenRejectTx.Sign(charliePrivateKey)
	_, err = tokenRejectTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token reject transaction: %v", err)
	}
	fmt.Println("Rejected fungible tokens for Charlie")

	time.Sleep(4 * time.Second)
	charlieBalance, _ = hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))
	client.Close()
}
```
{% endtab %}
{% endtabs %}

And that's it. The next section examines the fees for each of the parties involved a bit more thoroughly.

### Concluding Notes on Transaction Costs

When working with Frictionless Airdrops on Hedera, it’s important to consider the [transaction costs ](../../networks/mainnet/fees/#token-service)associated with each step. Here are some key points to keep in mind:

1. **TokenAirdropTransaction Costs**: The sender pays all fees for distributing tokens, including fees for token association and the first auto-renewal period for newly created associations. This makes it easier and less costly for recipients, as they don’t need to pre-associate tokens or cover those fees themselves.
2. **TokenClaimAirdropTransaction Costs**: When a recipient claims tokens from a pending airdrop, they only need to cover the transaction cost for claiming. The sender still covers token association costs, ensuring the user experience remains frictionless.
3. **TokenRejectTransaction Costs**: Rejecting unwanted tokens is free from custom fees or royalties. This ensures users can easily clean up their accounts without worrying about incurring additional costs for getting rid of spam or unwanted tokens.
4. **TokenCancelAirdropTransaction Costs**: Canceling a pending airdrop comes with a nominal fee for the sender, incentivizing mindful use of the airdrop system. It provides flexibility for managing unclaimed tokens but also discourages excessive or careless airdrops.

By understanding these costs and who is responsible for them, you can build efficient, user-friendly token distribution systems while maintaining cost efficiency for both developers and end-users.

## Code Check [✅](https://emojigraph.org/check-mark-button/)

<details>

<summary><strong><code>.env</code> file example</strong></summary>

```
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e020100300506032b657004220420ed5a93073.....
HEDERA_NETWORK=testnet
```

</details>

<details>

<summary><strong>JavaScript</strong></summary>

```javascript
import {
    Client, PrivateKey, AccountId, AccountCreateTransaction, Hbar, NftId, 
    TokenCreateTransaction, TokenType, TokenMintTransaction, AccountBalanceQuery,
    TokenAirdropTransaction,
    TokenClaimAirdropTransaction,
    TokenCancelAirdropTransaction,
    TokenRejectTransaction,
} from "@hashgraph/sdk";

import dotenv from "dotenv";
dotenv.config();

async function main() {
    if (
        process.env.MY_ACCOUNT_ID == null ||
        process.env.MY_PRIVATE_KEY == null ||
        process.env.HEDERA_NETWORK == null
    ) {
        throw new Error(
            "Environment variables MY_ACCOUNT_ID, HEDERA_NETWORK, and MY_PRIVATE_KEY are required.",
        );
    }

    const client = Client.forName(process.env.HEDERA_NETWORK).setOperator(
        AccountId.fromString(process.env.MY_ACCOUNT_ID),
        PrivateKey.fromStringDer(process.env.MY_PRIVATE_KEY),
    );

    /**
     * Step 1: Create 4 accounts
     */
    const alicePrivateKey = PrivateKey.generateED25519();
    const { accountId: aliceId } = await (
        await new AccountCreateTransaction()
            .setKey(alicePrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(-1) // unlimited associations
            .execute(client)
    ).getReceipt(client);

    const bobPrivateKey = PrivateKey.generateED25519();
    const { accountId: bobId } = await (
        await new AccountCreateTransaction()
            .setKey(bobPrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(1) // 1 association
            .execute(client)
    ).getReceipt(client);

 
    const charliePrivateKey = PrivateKey.generateED25519();
    const { accountId: charlieId } = await (
        await new AccountCreateTransaction()
            .setKey(charliePrivateKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(0) // no association slots
            .execute(client)
    ).getReceipt(client);

    // treasury account for tokens
    const treasuryKey = PrivateKey.generateED25519();
    const { accountId: treasuryAccount } = await (
        await new AccountCreateTransaction()
            .setKey(treasuryKey)
            .setInitialBalance(new Hbar(10))
            .setMaxAutomaticTokenAssociations(-1)
            .execute(client)
    ).getReceipt(client);
    
    /**
     * Step 2: Create FT and NFT mint
     */
    const INITIAL_SUPPLY = 300;
    const tokenCreateTx = await new TokenCreateTransaction()
        .setTokenName("GameGold")
        .setTokenSymbol("GG")
        .setDecimals(3)
        .setInitialSupply(INITIAL_SUPPLY)
        .setTreasuryAccountId(treasuryAccount)
        .setAdminKey(client.operatorPublicKey)
        .setFreezeKey(client.operatorPublicKey)
        .setSupplyKey(client.operatorPublicKey)
        .setMetadataKey(client.operatorPublicKey)
        .setPauseKey(client.operatorPublicKey)
        .freezeWith(client)
        .sign(treasuryKey);

    const { tokenId } = await (
        await tokenCreateTx.execute(client)
    ).getReceipt(client);
    
    /**
     * Step 3: Airdrop fungible tokens to 3 accounts
     */
    const AIRDROP_SUPPLY_PER_ACCOUNT = INITIAL_SUPPLY / 3;
    const airdropRecord = await (
        await (
            await new TokenAirdropTransaction()
                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    aliceId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    bobId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    treasuryAccount,
                    -AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .addTokenTransfer(
                    tokenId,
                    charlieId,
                    AIRDROP_SUPPLY_PER_ACCOUNT,
                )
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getRecord(client);

    // Get the transaction record and see the pending airdrops
    const { newPendingAirdrops } = airdropRecord;
    console.log("Pending airdrop", newPendingAirdrops[0]);

    // Query to verify Alice and Bob have received the airdrops and Charlie has not
    let aliceBalance = await new AccountBalanceQuery()
        .setAccountId(aliceId)
        .execute(client);

    let bobBalance = await new AccountBalanceQuery()
        .setAccountId(bobId)
        .execute(client);

    let charlieBalance = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Alice balance after airdrop: ",
        aliceBalance.tokens.get(tokenId).toInt(),
    );
    console.log(
        "Bob balance after airdrop: ",
        bobBalance.tokens.get(tokenId).toInt(),
    );
    console.log(
        "Charlie balance after airdrop: ",
        charlieBalance.tokens.get(tokenId),
    );
    
    /**
     * Step 4: Claim the airdrop for Charlie's account
     */
    console.log("Pending airdrop ID: ", newPendingAirdrops[0].airdropId);
    const tokenClaimTx = await new TokenClaimAirdropTransaction()
        .addPendingAirdropId(newPendingAirdrops[0].airdropId)
        .freezeWith(client)
        .sign(charliePrivateKey);
    const tokenClaimTxResponse = await tokenClaimTx.execute(client);
    const tokenClaimTxReceipt = await tokenClaimTxResponse.getReceipt(client);

    console.log(
        `Status of token claim airdrop transaction: ${tokenClaimTxReceipt.status.toString()}`,
    );

    const charlieBalanceAfterClaim = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Charlie balance after airdrop claim",
        charlieBalanceAfterClaim.tokens.get(tokenId).toInt(),
    );

    /**
     * Step 5: Airdrop the NFTs to Alice, Bob, and Charlie
     */
    const CID = [
        "QmNPCiNA3Dsu3K5FxDPMG5Q3fZRwVTg14EXA92uqEeSRXn",
        "QmZ4dgAgt8owvnULxnKxNe8YqpavtVCXmc1Lt2XajFpJs9",
        "QmPzY5GxevjyfMUF5vEAjtyRoigzWp47MiKAtLBduLMC1T",
        "Qmd3kGgSrAwwSrhesYcY7K54f3qD7MDo38r7Po2dChtQx5",
        "QmWgkKz3ozgqtnvbCLeh7EaR1H8u5Sshx3ZJzxkcrT3jbw",
    ];
    
    // Create Game NFT
    const { tokenId: nftId } = await (
        await (
            await new TokenCreateTransaction()
                .setTokenName("Game NFT")
                .setTokenSymbol("GNFT")
                .setTokenType(TokenType.NonFungibleUnique)
                .setTreasuryAccountId(treasuryAccount)
                .setAdminKey(client.operatorPublicKey)
                .setFreezeKey(client.operatorPublicKey)
                .setSupplyKey(client.operatorPublicKey)
                .setMetadataKey(client.operatorPublicKey)
                .setPauseKey(client.operatorPublicKey)
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getReceipt(client);

    let serialsNfts = [];
    for (let i = 0; i < CID.length; i++) {
        const { serials } = await (
            await new TokenMintTransaction()
                .setTokenId(nftId)
                .addMetadata(Buffer.from("-"))
                .execute(client)
        ).getReceipt(client);

        serialsNfts.push(serials[0]);
    }
    
    // Airdrop NFTs
    const { newPendingAirdrops: newPendingAirdropsNfts } = await (
        await (
            await new TokenAirdropTransaction()
                .addNftTransfer(
                    nftId,
                    serialsNfts[0],
                    treasuryAccount,
                    aliceId,
                )
                .addNftTransfer(
                    nftId,
                    serialsNfts[1],
                    treasuryAccount,
                    bobId,
                )
                .addNftTransfer(
                    nftId,
                    serialsNfts[2],
                    treasuryAccount,
                    charlieId,
                )
                .freezeWith(client)
                .sign(treasuryKey)
        ).execute(client)
    ).getRecord(client);

    // Get the tx record and verify two pending airdrops (for Bob and Charlie)
    console.log("Pending airdrops length", newPendingAirdropsNfts.length);
    console.log("Pending airdrop for Bob:", newPendingAirdropsNfts[0]);
    console.log("Pending airdrop for Charlie:", newPendingAirdropsNfts[1]);
    
    /**
     * Step 6: Cancel the airdrop for Charlie
     * No signature is needed as the operator account is the admin of the token
     */
    await new TokenCancelAirdropTransaction()
        .addPendingAirdropId(newPendingAirdropsNfts[1].airdropId) // Charlie's airdrop
        .execute(client);
        
    /**
     * Step 7: Reject the fungible tokens for Charlie
     */
    await (
        await (
            await new TokenRejectTransaction()
                .setOwnerId(charlieId)
                .addTokenId(tokenId)
                .freezeWith(client)
                .sign(charliePrivateKey)
        ).execute(client)
    ).getReceipt(client);

    charlieBalance = await new AccountBalanceQuery()
        .setAccountId(charlieId)
        .execute(client);

    console.log(
        "Charlie balance after reject: ",
        charlieBalance.tokens.get(tokenId).toInt(),
    );

    client.close();
}

void main();
```

</details>

<details>

<summary><strong>Go</strong></summary>

```go
package main

import (
	"fmt"
	"log"
	"os"
	"time"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
	}

	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))

	// Step 1: Create 4 accounts
	alicePrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Alice: %v", err)
	}
	aliceId := createAccount(client, alicePrivateKey, 10, -1) // Unlimited associations

	bobPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Bob: %v", err)
	}
	bobId := createAccount(client, bobPrivateKey, 10, 1) // 1 association

	charliePrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Charlie: %v", err)
	}
	charlieId := createAccount(client, charliePrivateKey, 10, 0) // No associations

	treasuryPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		log.Fatalf("Error generating private key for Treasury: %v", err)
	}
	treasuryAccount := createAccount(client, treasuryPrivateKey, 10, -1) // Unlimited associations

	fmt.Printf("Alice Account ID: %v\n", aliceId)
	fmt.Printf("Bob Account ID: %v\n", bobId)
	fmt.Printf("Charlie Account ID: %v\n", charlieId)
	fmt.Printf("Treasury Account ID: %v\n", treasuryAccount)

	// Step 2: Create FT and mint
	tokenCreateTx, err := hedera.NewTokenCreateTransaction().
		SetTokenName("GameGold").
		SetTokenSymbol("GG").
		SetDecimals(3).
		SetInitialSupply(300).
		SetTreasuryAccountID(treasuryAccount).
		SetAdminKey(client.GetOperatorPublicKey()).
		SetFreezeKey(client.GetOperatorPublicKey()).
		SetSupplyKey(client.GetOperatorPublicKey()).
		SetMetadataKey(client.GetOperatorPublicKey()).
		FreezeWith(client)

	if err != nil {
		log.Fatalf("Error creating fungible token transaction: %v", err)
	}

	tokenCreateTxSigned := tokenCreateTx.Sign(treasuryPrivateKey)
	tokenCreateTxReceipt, err := tokenCreateTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing fungible token transaction: %v", err)
	}

	receipt, err := tokenCreateTxReceipt.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for fungible token: %v", err)
	}

	tokenId := *receipt.TokenID
	fmt.Printf("Created fungible token with ID: %v\n", tokenId.String())

	// Step 3: Airdrop 100 tokens each to 3 accounts
	airdropTx, err := hedera.NewTokenAirdropTransaction().
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, aliceId, 100).
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, bobId, 100).
		AddTokenTransfer(tokenId, treasuryAccount, -100).
		AddTokenTransfer(tokenId, charlieId, 100).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token airdrop transaction: %v", err)
	}

	airdropTxSigned := airdropTx.Sign(treasuryPrivateKey)
	airdropTxReceipt, err := airdropTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token airdrop transaction: %v", err)
	}
	airdropTxRecord, err := airdropTxReceipt.GetRecord(client)
	if err != nil {
		log.Fatalf("Error getting record for token airdrop transaction: %v", err)
	}
	fmt.Println("Pending airdrop: ", airdropTxRecord.PendingAirdropRecords[0].String())
	fmt.Println("Airdropped fungible tokens")

	aliceBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(aliceId).Execute(client)
	fmt.Println("Alice's balance: ", aliceBalance.Tokens.Get(tokenId))
	bobBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(bobId).Execute(client)
	fmt.Println("Bob's balance: ", bobBalance.Tokens.Get(tokenId))
	charlieBalance, _ := hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))

	// Step 4: Claim the airdrop for Charlie's account
	tokenClaimTx, err := hedera.NewTokenClaimAirdropTransaction().AddPendingAirdropId(airdropTxRecord.PendingAirdropRecords[0].GetPendingAirdropId()).FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token claim airdrop transaction: %v", err)
	}
	tokenClaimTxSigned := tokenClaimTx.Sign(charliePrivateKey)

	_, err = tokenClaimTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token claim airdrop transaction: %v", err)
	}
	fmt.Println("Claimed airdrop for Charlie's account")
	time.Sleep(4 * time.Second)
	charlieBalance, _ = hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))

	// Step 5: Airdrop the NFTs to Alice, Bob, and Charlie
	CID := [][]byte{
		[]byte("ipfs://bafyreiao6ajgsfji6qsgbqwdtjdu5gmul7tv2v3pd6kjgcw5o65b2ogst4/metadata.json"),
		[]byte("ipfs://bafyreic463uarchq4mlufp7pvfkfut7zeqsqmn3b2x3jjxwcjqx6b5pk7q/metadata.json"),
		[]byte("ipfs://bafyreihhja55q6h2rijscl3gra7a3ntiroyglz45z5wlyxdzs6kjh2dinu/metadata.json"),
		[]byte("ipfs://bafyreidb23oehkttjbff3gdi4vz7mjijcxjyxadwg32pngod4huozcwphu/metadata.json"),
		[]byte("ipfs://bafyreie7ftl6erd5etz5gscfwfiwjmht3b52cevdrf7hjwxx5ddns7zneu/metadata.json"),
	}
	nftCreate, err := hedera.NewTokenCreateTransaction().
		SetTokenName("Game NFT").
		SetTokenSymbol("GNFT").
		SetTokenType(hedera.TokenTypeNonFungibleUnique).
		SetTreasuryAccountID(treasuryAccount).
		SetSupplyKey(client.GetOperatorPublicKey()).
		SetAdminKey(client.GetOperatorPublicKey()).
		FreezeWith(client)

	nftCreateTxSign := nftCreate.Sign(treasuryPrivateKey)
	nftCreateSubmit, err := nftCreateTxSign.Execute(client)
	if err != nil {
		log.Fatalf("Error creating NFT: %v", err)
	}

	nftCreateReceipt, err := nftCreateSubmit.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for NFT creation: %v", err)
	}

	fmt.Println("Created NFT with ID: 0.0.", nftCreateReceipt.TokenID.Token)
	nftId := *nftCreateReceipt.TokenID
	var serialsNfts []int64
	mintTx, _ := hedera.NewTokenMintTransaction().
		SetTokenID(nftId).
		SetMetadatas(CID).
		FreezeWith(client)
	mintTxSign := mintTx.Sign(treasuryPrivateKey)
	mintTxSubmit, err := mintTxSign.Execute(client)
	if err != nil {
		log.Fatalf("Error minting NFTs: %v", err)
	}
	mintTxReceipt, err := mintTxSubmit.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for NFT minting: %v", err)
	}
	serialsNfts = mintTxReceipt.SerialNumbers
	fmt.Printf("Minted NFT serial numbers: %v\n", serialsNfts)

	// Airdrop NFTs
	nftAirdropTx, err := hedera.NewTokenAirdropTransaction().
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 1}, treasuryAccount, aliceId).
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 2}, treasuryAccount, bobId).
		AddNftTransfer(hedera.NftID{TokenID: nftId, SerialNumber: 3}, treasuryAccount, charlieId).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating NFT airdrop transaction: %v", err)
	}

	nftAirdropTxSigned := nftAirdropTx.Sign(treasuryPrivateKey)
	nftAirdropTxSubmit, err := nftAirdropTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing NFT airdrop transaction: %v", err)
	}
	nftAirdropTxRecord, err := nftAirdropTxSubmit.GetRecord(client)
	if err != nil {
		log.Fatalf("Error getting record for NFT airdrop transaction: %v", err)
	}
	fmt.Println("Pending airdrops length: ", len(nftAirdropTxRecord.PendingAirdropRecords))
	fmt.Println("Pending airdrop for Bob: ", nftAirdropTxRecord.PendingAirdropRecords[0].String())
	fmt.Println("Pending airdrop for Charlie: ", nftAirdropTxRecord.PendingAirdropRecords[1].String())

	// Step 6: Cancel Airdrop for Charlie
	hedera.NewTokenCancelAirdropTransaction().
		AddPendingAirdropId(nftAirdropTxRecord.PendingAirdropRecords[1].GetPendingAirdropId()).
		Execute(client)

	// Step 7: Reject the fungible tokens for Charlie
	tokenRejectTx, err := hedera.NewTokenRejectTransaction().
		AddTokenID(tokenId).
		SetOwnerID(charlieId).
		FreezeWith(client)
	if err != nil {
		log.Fatalf("Error creating token reject transaction: %v", err)
	}
	tokenRejectTxSigned := tokenRejectTx.Sign(charliePrivateKey)
	_, err = tokenRejectTxSigned.Execute(client)
	if err != nil {
		log.Fatalf("Error executing token reject transaction: %v", err)
	}
	fmt.Println("Rejected fungible tokens for Charlie")

	time.Sleep(4 * time.Second)
	charlieBalance, _ = hedera.NewAccountBalanceQuery().SetAccountID(charlieId).Execute(client)
	fmt.Println("Charlie's balance: ", charlieBalance.Tokens.Get(tokenId))
	client.Close()
}

// Helper function to generate a new Hedera account
func createAccount(client *hedera.Client, newAccountPrivateKey hedera.PrivateKey, initialBalance float64, maxAssociations int32) hedera.AccountID {
	newAccountPublicKey := newAccountPrivateKey.PublicKey()
	newAccountTx, err := hedera.NewAccountCreateTransaction().
		SetKey(newAccountPublicKey).
		SetInitialBalance(hedera.HbarFrom(initialBalance, hedera.HbarUnits.Hbar)).
		SetMaxAutomaticTokenAssociations(maxAssociations).
		Execute(client)

	if err != nil {
		log.Fatalf("Error creating new account: %v", err)
	}

	receipt, err := newAccountTx.GetReceipt(client)
	if err != nil {
		log.Fatalf("Error getting receipt for account creation: %v", err)
	}

	newAccountId := *receipt.AccountID

	return newAccountId
}
```

</details>

***

## Additional Resources

* [**Airdrop List Verifier (NFT Studio)**](https://docs.hedera.com/hedera/open-source-solutions/nft-studio/airdrop-list-verifier)
* [**Token Holders List Builder (NFT Studio)**](https://docs.hedera.com/hedera/open-source-solutions/nft-studio/nft-token-holders-list-builder)
