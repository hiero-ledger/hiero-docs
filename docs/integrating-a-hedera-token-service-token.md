# Integrating a Hedera Token Service Token

## Prerequisites

**Hedera Mainnet Account**

Hedera accounts are stored on the public ledger and hold hbars which are used to pay for the transaction and query fees. Hedera accounts are referenced by an account ID in following format x.y.z (0.0.10). You can create a Hedera mainnet account by visiting the [Hedera portal](https://portal.hedera.com/?network=testnet) website and creating a profile. Every account has an Ed25519 private key that is used to sign and authorize transactions.

Users will require the creation of a Hedera accounts to interact receive tokens created by the Hedera Token Service. When a user purchases a token that was created using the Hedera Token Service, the Hedera account will need to be associated with the token first before the tokens can be transferred into the users Hedera account.

**Hbars**

You can get hbars that are used to pay for creating accounts and transferring tokens by visiting an exchange.

**Hedera Mirror Node**

A Hedera mirror node stores the history of transactions that occur on the network. You can access the Hedera mirror node operated by Hedera or another third-party mirror node operator. You can also run your own mirror node.

You can find instructions on how to operate your own Hedera mirror node below.

{% content-ref url="../mirrornet/run-your-own-beta-mirror-node/" %}
[run-your-own-beta-mirror-node](../mirrornet/run-your-own-beta-mirror-node/)
{% endcontent-ref %}

**Third-party mirror nodes:**

* [Dragon Glass](https://app.dragonglass.me/hedera/home) &#x20;
* [Kabuto](https://kabuto.sh/)&#x20;
* [Ledger Works](https://lworks.io) &#x20;

## **SDKs and Docs**

The following SDKs support HTS:

* [Java](https://github.com/hashgraph/hedera-sdk-java)&#x20;
* [JavaScript](https://github.com/hashgraph/hedera-sdk-js)&#x20;
* [Go](https://github.com/hashgraph/hedera-sdk-go)&#x20;
* [.NET](https://github.com/bugbytesinc/Hashgraph)

Documentation can be found below.

{% content-ref url="sdks/" %}
[sdks](sdks/)
{% endcontent-ref %}

## **Hedera Token Service**

The Hedera Token Service is a service that allows users to create new tokens on the Hedera network. A Hedera token can have the following properties:

**Token ID**

The token ID in x.z.y format is the only unique property of a token created by the Hedera network. This ID would be used to reference a specific token.

**Name**

Publicly visible name of the token specified as a string of UTF-8 characters. Maximum of 100 characters. The token names are not unique.

**Symbol**

Publicly visible token symbol. It is UTF-8 string, with a maximum of 100 characters, identifying the token.

**Decimal**

The number of decimal places a token is divisible by.

**Treasury Account**

The account ID which will act as a treasury for the token. This account will receive the specified initial supply

**Admin Key**

The key which can perform update/delete operations on the token.

**Freeze Key**

The key which can sign to freeze or unfreeze an account for token transactions.

**KYC Key**

The key which can grant or revoke KYC of an account for the token's transactions.

**Supply Key**

The key which can change the total supply of a token.

**Wipe Key**

The key which can wipe the token balance of an account.

**Transferring Tokens**

Before Hedera accounts can accept a token transfer, a token associate transaction needs to be submitted in order to associate the token to a Hedera account (TokenAssociateTransaction). If you do not associate the token to a Hedera account, a token transfer transaction will not be successful. You will receive a “TOKEN\_NOT\_ASSOCIATED\_TO\_ACCOUNT” error from the networkYou have to issue this transaction for every unique token that you would like to transfer to a Hedera account.

To transfer tokens from one account to another, you will use the CryptoTransfer HAPI API ([TransferTransaction](sdks/cryptocurrency/transfer-cryptocurrency.md) via SDK). This API transfers both hbars and tokens created by users on the network.

**Token Transfer List**

The TransferTransaction returns a token transfer list. The token transfer list contains the ID of the token that was transferred and the amount of the token that was transferred to an account. You can view the token transfer list by requesting the record of the transaction. A [record](https://docs.hedera.com/guides/docs/sdks/transactions/get-a-transaction-record) of a transaction contains information about the transaction that was executed by the Hedera network.

**Calculating Total Supply**

The total supply of a token is to be calculated by the following method:

Initial Supply + TokenMint - TokenBurn - TokenWipe = current supply of the token

The circulating supply of a token can be calculated by the following method:

Total supply – amount of token in the treasury account of the token = circulating supply of the token

**Frozen Hedera Accounts**

A user’s account can be frozen from transferring token. Token transfers to a Hedera account that has been frozen will not execute and the network will return an “ACCOUNT\_FROZEN\_FOR\_TOKEN.” You can view which Hedera accounts are frozen for a given token by querying a mirror node.

**Wipe Tokens**

The token owner has the ability to wipe tokens from a Hedera account. Wiping tokens from an account burns the token and decreases the supply of the token.

**Know Your Customer (KYC)**

Hedera accounts can be optionally flagged for meeting KYC requirements or not. Tokens that require KYC will need to meet the requirements prior to transferring tokens into that Hedera account.

## Hedera Mirror Node

The Hedera Mirror Node has the following REST APIs you can use to query token information from:

* The accounts API returns the tokens that are associated with an account&#x20;
* The transactions API returns the token transfers in a given transaction&#x20;
* The token APIs you can:&#x20;
  * Get all of the tokens that were created in a Hedera network&#x20;
  * Get all the accounts the specified token is associated with&#x20;
  * Get a specified token’s current state on the network with the following information&#x20;

You can find the token REST API information below.

{% content-ref url="mirror-node-api/rest-api.md" %}
[rest-api.md](mirror-node-api/rest-api.md)
{% endcontent-ref %}
