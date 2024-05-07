# Atomic swaps

An atomic swap is when you swap tokens between two accounts without using a third-party intermediary, such as a centralized exchange or custody provider, to facilitate the transfer. Native tokens issued using the Hedera Token Service (HTS) can be swapped with another or with hbars in a single transaction using the `TransferTransaction` API call. For each atomic swap within a single transaction, you’ll need to designate an account to be debited (-) any number of tokens and the corresponding account which will receive those tokens.

**Signing Requirements**

The private keys for the accounts which are being debited tokens are required to sign the transaction.

{% hint style="info" %}
Hedera accounts must be associated to the specified token before you can transfer a token to their account. Please see how to associate a token to an account [here](../../../sdks/token-service/associate-tokens-to-an-account.md).
{% endhint %}

{% tabs %}
{% tab title="v1" %}
{% code title="Java" %}
```java
//Atomic swap between a Hedera Token Service token and hbar
Transaction atomicSwap = new TransferTransaction()
        .addHbarTransfer(accountId1, new Hbar(-10))
        .addHbarTransfer(accountId2, new Hbar(10))
        .addTokenTransfer(tokenId, accountId2, -1)
        .addTokenTransfer(tokenId, accountId1, 1)
        .build(client);

//Sign the transaction with accountId1 and accountId2 private keys, submit the transaction to a Hedera network
TransactionId txId = atomicSwap.sign(accountKey1).sign(accountKey2).execute(client);


//------------------------------------OR---------------------------------------

//Atomic swap between two hedera Token Service created tokens
Transaction atomicSwap = new TransferTransaction()
        .addTokenTransfer(tokenId1, accountId1, -1)
        .addTokenTransfer(tokenId1, accountId2, 1)
        .addTokenTransfer(tokenId2, accountId2, -1)
        .addTokenTransfer(tokenId2, accountId1, 1)
        .build(client);

//Sign the transaction with accountId1 and accountId2 private keys, submit the transaction to a Hedera network
TransactionId txId = atomicSwap.sign(accountKey1).sign(accountKey2).execute(client);
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Atomic swap between a Hedera Token Service token and hbar
const atomicSwap = await new TransferTransaction()
        .addHbarTransfer(accountId1, new Hbar(-10))
        .addHbarTransfer(accountId2, new Hbar(10))
        .addTokenTransfer(tokenId, accountId2, -1)
        .addTokenTransfer(tokenId, accountId1, 1)
        .build(client);

//Sign the transaction with accountId1 and accountId2 private keys, submit the transaction to a Hedera network
const txId = await (await (await atomicSwap.sign(accountKey1)).sign(accountKey2)).execute(client);


//------------------------------------OR---------------------------------------

//Atomic swap between two hedera Token Service created tokens
const atomicSwap = await new TransferTransaction()
        .addTokenTransfer(tokenId1, accountId1, -1)
        .addTokenTransfer(tokenId1, accountId2, 1)
        .addTokenTransfer(tokenId2, accountId2, -1)
        .addTokenTransfer(tokenId2, accountId1, 1)
        .build(client);

//Sign the transaction with accountId1 and accountId2 private keys, submit the transaction to a Hedera network
const txId = await (await (await atomicSwap.sign(accountKey1)).sign(accountPKey2)).execute(client);
```
{% endcode %}
{% endtab %}
{% endtabs %}
