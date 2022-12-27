# How to Exempt Hedera Accounts from Custom Token Fees

The implementation of [HIP-573](https://hips.hedera.com/hip/hip-573) in mainnet release [v0.31](https://docs.hedera.com/guides/docs/release-notes/services#v0.31) (November 10th, 2022) enables token creators whose tokenomics require custom fees and different collection accounts to exempt fee collectors from paying custom fees when exchanging token units.

This example guides you through the following steps:

1. Creating a Helper Function and defining three new accounts (Account 1, 2, 3)
2. Initializing Fractional Fees for each new account and creating a Fungible Token\

3. Transferring 10.000 units from the Treasury Account to Account 2 and then from Account 2 to Account 1

## Try It Yourself

* Get a [Hedera testnet account](https://portal.hedera.com/)
* Use this [Codesandbox](https://codesandbox.io/s/hip-573-fungible-token-j2fwjy) to try the following example
  * Fork the sandbox
  * Remember to provide credentials in the **.env** file
  * Open a new terminal to execute **index.js**
* Get the complete code of this example on [GitHub](https://github.com/ed-marquez/hedera-general-examples/blob/main/src/020\_hip\_573/index.js)

## 1. Set Up Helper Functions

To simplify creating accounts, you need to create a helper function. In this example, the function is called **accountCreator,** and the parameters you need to specify are an initial balance and a private key. Note that the token association is automatic to further simplify the tutorial, as you can see below.\


![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
const accountCreator =  async (initialBalance, privateKey) => {
   const createAccount = new AccountCreateTransaction()
       .setInitialBalance(initialBalance)
       .setKey(privateKey.publicKey)
       .setMaxAutomaticTokenAssociations(10)
       .freezeWith(client);
   const createAccountTx = await createAccount.execute(client);
   const createAccountRx = await createAccountTx.getReceipt(client);
   return createAccountRx.accountId;
}

```

## 2. Define Hedera Accounts

Now, you can use the helper function to create three different accounts that will be the collectors of the token fees.

![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
// STEP 1: Create three accounts using the helper function
const accountKey1 = PrivateKey.generateED25519();
const accountId1 = await accountCreator(50, accountKey1);
 
const accountKey2 = PrivateKey.generateED25519();
const accountId2 = await accountCreator(50, accountKey2);
 
const accountKey3 = PrivateKey.generateED25519();
const accountId3 = await accountCreator(50, accountKey3);
 
console.log(`STEP 1 - Three accounts created: \n ${accountId1} \n ${accountId2} \n ${accountId3}\n`);

```

**Console Output:**\


![Screen Shot 2022 10 26 at 9 32 12 AM](https://images.hedera.com/Screen-Shot-2022-10-26-at-9.32.12-AM\_2022-10-28-200511\_thhu.png?w=470\&auto=compress%2Cformat\&fit=crop\&dm=1670272345\&s=76f748a3f3000a5bb4eff56ceed14982)

## 3. Create Fractional Fees and Fungible Token

Let’s define three fractional fees for the fungible token we are about to create. During a token transfer, the first account will receive 1% of the amount, the second 2%, and the third 3%.

Note that the critical factor of this tutorial is using the **setAllCollectorsAreExempt()** extension method on fractional fee creation, as you can see below.

![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
const fee1 = new CustomFractionalFee()
       .setFeeCollectorAccountId(accountId1)
       .setNumerator(1)
       .setDenominator(100)
       .setAllCollectorsAreExempt(true);
 
const fee2 = new CustomFractionalFee()
       .setFeeCollectorAccountId(accountId2)
       .setNumerator(2)
       .setDenominator(100)
       .setAllCollectorsAreExempt(true);
 
const fee3 = new CustomFractionalFee()
       .setFeeCollectorAccountId(accountId3)
       .setNumerator(3)
       .setDenominator(100)
       .setAllCollectorsAreExempt(true);

```

Now, you can create the fungible token and specify the fees you just defined. Remember to sign the [TokenCreateTransaction()](https://docs.hedera.com/guides/docs/sdks/tokens/define-a-token) using all the accounts (fee collectors included).\


![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
const createToken = new TokenCreateTransaction()
       .setTokenName("HIP-573 Token")
       .setTokenSymbol("H573")
       .setTokenType(TokenType.FungibleCommon)
       .setTreasuryAccountId(operatorId)
       .setAutoRenewAccountId(operatorId)
       .setAdminKey(operatorKey)
       .setFreezeKey(operatorKey)
       .setWipeKey(operatorKey)
       .setInitialSupply(100000000) // Total supply = 100000000 / 10 ^ 2
       .setDecimals(2)
       .setCustomFees([fee1, fee2, fee3])
       .setMaxTransactionFee(new Hbar(40))
       .freezeWith(client);
 
const createTokenSigned1 = await createToken.sign(accountKey1);
const createTokenSigned2 = await createTokenSigned1.sign(accountKey2);
const createTokenSigned3 = await createTokenSigned2.sign(accountKey3);
 
const createTokenTx = await createTokenSigned3.execute(client);
const createTokenRx = await createTokenTx.getReceipt(client);
 
const tokenId = createTokenRx.tokenId;
 
console.log(`STEP 2 - Token with custom fees created: ${tokenId}\n`);

```

**Console Output:**

![Screen Shot 2022 10 26 at 9 34 48 AM](https://images.hedera.com/Screen-Shot-2022-10-26-at-9.34.48-AM\_2022-10-28-200739\_bblg.png?w=726\&auto=compress%2Cformat\&fit=crop\&dm=1670272349\&s=efde6dd48b9c4b09fb52ed784052ed35)

## 4. Transfer Tokens between Fee Collectors

Before transferring a token from one collection account to another, you first need to transfer some tokens from the treasury account (in this example, the operator) to one of the collection accounts.\


![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
// STEP 3: Send token from treasury to one account and from one account to another
const transferFromTreasuryTx = await new TransferTransaction()
       .addTokenTransfer(tokenId, operatorId, -10000)
       .addTokenTransfer(tokenId, accountId2, 10000)
       .freezeWith(client)
       .execute(client);
 
const transferFromTreasuryRx = await transferFromTreasuryTx.getReceipt(client);
const transferFromTreasuryStatus = transferFromTreasuryRx.status.toString();
 
console.log(`STEP 3 \nToken transfer from treasury to account 2: ${transferFromTreasuryStatus}`);

```

Now that Account 2 has 10.000 fungible tokens, you can try to transfer the tokens from Account 2 to Account 1.\


![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript

const transferFromAccount2 = await new TransferTransaction()
       .addTokenTransfer(tokenId, accountId2, -10000)
       .addTokenTransfer(tokenId, accountId1, 10000)
       .freezeWith(client)
       .sign(accountKey2);
  
const transferFromAccount2Tx = await transferFromAccount2.execute(client);
const transferFromAccount2Rx = await transferFromAccount2Tx.getReceipt(client);
 
console.log(`Transfer from account 2 to account 1: ${transferFromAccount2Rx.status.toString()}\n`);
 
```

**Console Output:**\


![Screen Shot 2022 10 26 at 9 34 55 AM](https://images.hedera.com/Screen-Shot-2022-10-26-at-9.34.55-AM\_2022-10-28-200946\_xpik.png?w=728\&auto=compress%2Cformat\&fit=crop\&dm=1670272352\&s=1f284d0b8280e5bec7845024d910199e)

Done! Now you need to check each account balance to verify if they are actually exempt from token fees.\


![Code Snippet Background](https://images.hedera.com/CodeSnippetBackground.jpg?w=3360\&auto=compress%2Cformat\&fit=crop\&dm=1670269159\&s=fc2790f0ebef705338b7fae7db54cf9d)

```javascript
// Check collectors account balance (methods will be deprecated soon, use axios and mirror node api)
const checkBalance1 = await new AccountBalanceQuery()
    .setAccountId(accountId1)
    .execute(client);
const balance1 = checkBalance1.tokens._map.get(tokenId.toString());
 
const checkBalance2 = await new AccountBalanceQuery()
       .setAccountId(accountId2)
       .execute(client);
 
const balance2 = checkBalance2.tokens._map.get(tokenId.toString());
 
const checkBalance3 = await new AccountBalanceQuery()
   .setAccountId(accountId3)
   .execute(client);
 
const balance3 = checkBalance3.tokens._map.get(tokenId.toString());
 
console.log(`Accounts Balance: \nAccount 1: ${balance1} \nAccount 2: ${balance2} \nAccount 3: ${balance3} \n`);

```

**Console Output:**\


![Screen Shot 2022 10 25 at 12 51 32 PM](https://images.hedera.com/Screen-Shot-2022-10-25-at-12.51.32-PM\_2022-10-28-201122\_yrvy.png?w=256\&auto=compress%2Cformat\&fit=crop\&dm=1670272340\&s=756190f873bca8d76353037330e83adb)

**v0.30**

![Screen Shot 2022 10 25 at 12 50 49 PM](https://images.hedera.com/Screen-Shot-2022-10-25-at-12.50.49-PM.png?w=256\&auto=compress%2Cformat\&fit=crop\&dm=1670272331\&s=297bc0a99ad4f10713b6d903fb887ab9)

**v0.31**

As you can see in v0.30, the third collector receives 3% of the 10.000 transferred from the second to the first collector. While in v0.31, fee collectors are exempt from fees.

For further questions, don't hesitate to get in touch with us on the [Hedera Discord Server](https://hedera.com/discord).

