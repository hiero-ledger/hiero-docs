# Deploy By Leveraging Ethereum Developer Tools On Hedera

Learning how to properly use new developer tools requires time and effort. Many seasoned engineers already have their ol’ reliable set of frameworks and libraries they frequently use. With the release of the Hedera [JSON-RPC relay](https://github.com/hashgraph/hedera-json-rpc-relay), Ethereum developer tools combined with ECDSA-based Hedera accounts are available for developers only. You can continue to utilize familiar Ethereum tooling to build on Hedera. This blog speaks to the support of 4 Ethereum tools and the enablement of Metamask.

## Supported Ethereum Developer Tools

The most common EVM-based tools and workflows across Web3 ecosystems are built on the JSON-RPC specification. You can continue to utilize the following familiar Ethereum tooling, Web3JS, Truffle, Ethers, and Hardhat, to build on Hedera, thanks to the JSON-RPC Relay. As an Ethereum developer, your workflow does not have to change.

<table data-header-hidden><thead><tr><th width="219"></th><th width="124" align="center"></th><th width="142" align="center"></th><th width="117" align="center"></th><th align="center"></th></tr></thead><tbody><tr><td><br></td><td align="center"><strong>web3js</strong></td><td align="center"><strong>Truffle</strong></td><td align="center"><strong>ethers</strong></td><td align="center"><strong>Hardhat</strong></td></tr><tr><td>Transfer HBARS</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td></tr><tr><td>Contract Deployment</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td></tr><tr><td>Can use the contract instance after deploy without re-initialization</td><td align="center">✅</td><td align="center">✅</td><td align="center">⚠️</td><td align="center">⚠️</td></tr><tr><td>Contract View Function Call</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td></tr><tr><td>Contract Function Call</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td><td align="center">✅</td></tr></tbody></table>

You can transfer HBAR, deploy contracts, and perform contract calls bringing even greater usability to the developer community.

Check out the Web3js, Truffle, and Hardhat examples on the [repo](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools). It is important to note that when working with Ethersjs and Hardhat, there is an extra step to retrieve the valid Hedera contract address. Learn more about it [here.](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools)

## Getting Started

Before you get started, it is important to create a new ECDSA-based account with an alias. Currently, the JSON-RPC Relay only supports Hedera accounts with an alias set (i.e., public address) based on its ECDSA public key. You can easily do this by following the steps below:

```javascript
// generate an ECDSA key-pair
const newPrivateKey = PrivateKey.generateECDSA();
console.log(`The raw private key (use this for JSON RPC wallet import): ${newPrivateKey.toStringRaw()}`);

const newPublicKey = newPrivateKey.publicKey;

// account publickey alias
const aliasAccountId = newPublicKey.toAccountId(0, 0);
console.log(`The alias account id: ${aliasAccountId}`);
```

```javascript
const operatorAccountId = AccountId.fromString(process.env.OPERATOR_ID);
const operatorPrivateKey = PrivateKey.fromString(process.env.OPERATOR_PVKEY);

// Hbar transfers will auto-create a Hedera Account
// for long-form account Ids that do not have accounts yet
const tokenTransferTxn = async (senderAccountId, receiverAccountId, hbarAmount) => {
    const transferToAliasTx = new TransferTransaction()
        .addHbarTransfer(senderAccountId, new Hbar(-hbarAmount))
        .addHbarTransfer(receiverAccountId, new Hbar(hbarAmount))
        .freezeWith(client);

    const signedTx = await transferToAliasTx.sign(operatorPrivateKey);
    const txResponse = await signedTx.execute(client);
    await txResponse.getReceipt(client);
}
```

Create a function to help log your account info.

```javascript
const logAccountInfo = async (accountId) => {
    const info = await new AccountInfoQuery()
        .setAccountId(accountId)
        .execute(client);
 
    console.log(`The normal account ID: ${info.accountId}`);
    console.log(`Account Balance: ${info.balance}`);
}
```

```javascript
const main = async () => {
    await tokenTransferTxn(operatorAccountId, aliasAccountId, 5);
    await logAccountInfo(aliasAccountId);
}
 
main();
```

**Console output**

```javascript
The raw private key (use this for JSON RPC wallet import): 81cd442a945d2c9f04ed5bf355a59db9e9f7553b9d4c319938eb9176085cb4c8
The alias account id: 0.0.302d300706052b8104000a03220002d47f1da5a3e086c568776d5be31165c65a135bb48951b4ccbf4284b025225ff4
The normal account ID: 0.0.47995491
Account Balance: 99.31142415 ℏ
```

The account is officially registered with Hedera when HBAR is initially deposited to the account alias. The transaction fee to create the account is deducted from the initial hbar transfer. The remaining balance, minus the transaction fee to create the account, is the initial balance of the new account. If interested in learning more about auto account creation, read the following [documentation](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/create-an-account#create-an-account-via-an-account-alias) and [HIP-32](https://hips.hedera.com/hip/hip-32).

> _**IMPORTANT NOTE: Private keys for Testnet are displayed here for educational purposes only. Never share your private key(s) with others, as that may result in lost funds or loss of control over your account.**_

## Import Hedera Account into Metamask

#### Step 1: Go to [HashIO](https://swirldslabs.com/hashio/), the SwirldsLabs hosted version of the JSON-RPC Relay, and copy the Testnet URL.

HashIO provides the URLs for each Hedera environment that will let you interact with the respective environment nodes on Hedera the same way you would an Ethereum node.

<figure><img src="https://images.hedera.com/hashio-sc_2022-08-26-185203_wkuj.png?w=2670&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680224076&#x26;s=ca1432cbea2ad58f66b28093b3cddf0e" alt=""><figcaption></figcaption></figure>

#### Step 2: Open Metamask and add Hedera as a custom network.

| **Network Name**                                               |
| -------------------------------------------------------------- |
| Hedera                                                         |
| **New RPC URL**                                                |
| [https://testnet.hashio.io/api](https://testnet.hashio.io/api) |
| **Chain ID**                                                   |
| 296                                                            |
| **Currency Symbol**                                            |
| HBAR                                                           |

| **Config** | **Default** | **Description**                                                                                                                                                          |
| ---------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| CHAIN\_ID  | 0x12a       | The network chain id. Local and previewnet envs should use 0x12a (298). Previewnet, Testnet and Mainnet should use 0x129 (297), 0x128 (296) and 0x127 (295) respectively |

#### Step 3: Import your Hedera account into Metamask

Import your newly created ECDSA-based Hedera account into Metamask using your private key from above.

<div>

<figure><img src="../../.gitbook/assets/import accounts mm1.png" alt=""><figcaption><p>Use your private key to import your Hedera account into Metamask</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/import accounts mm2.png" alt=""><figcaption><p>Your Hedera Account in Metamask</p></figcaption></figure>

</div>

## Summary

Congratulations! 🎉 You’ve successfully connected your Hedera account to MetaMask! You may now send and receive HBAR on the Hedera Testnet via MetaMask!

The release of the JSON-RPC Relay to developers brings greater usability to the developer community by supporting common Ethereum developer tooling while building on Hedera.

Happy Building! Feel free to reach out if you have any questions:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Abi, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://www.linkedin.com/in/a-ridley/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/a-ridley/">https://www.linkedin.com/in/a-ridley/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
