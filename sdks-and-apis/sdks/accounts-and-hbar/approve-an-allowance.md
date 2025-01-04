# Approve an allowance

A transaction that allows a token owner to delegate a token spender to spend the specified token amount on behalf of the token owner. A Hedera account owner can provide an allowance for HBAR, non-fungible, and fungible tokens.&#x20;

The **owner** is the Hedera account that owns the tokens and grants the token allowance to the spender. The **spender** is the account that spends tokens, authorized by the owner, from the owner's account. The spender pays for the transaction fees when transferring tokens from the owner's account to another recipient. This means that the transaction fee payer for the `TransferTransaction` is required to set the spender account ID as the transaction fee payer. If the spender account ID is not set as the transaction fee payer, the system will error with `SPENDER_DOES_NOT_HAVE_ALLOWANCE`.

The maximum number of token approvals for the `AccountAllowanceApproveTransaction` cannot exceed 20. Note that each NFT serial number counts as a single approval. An `AccountAllowanceApproveTransaction` granting 20 NFT serial numbers to a spender will use all of the approvals permitted for the transaction.

A single NFT serial number can only be granted to one spender at a time. If an approval assigns a previously approved NFT serial number to a new user, the old user will have their approval removed.

Each owner account is limited to granting 100 allowances. This limit spans HBAR, fungible token allowances, and non-fungible token `approved_for_all` grants. No limit exists on the number of NFT serial number approvals an owner may grant.

The number of allowances set on an account will increase the auto-renewal fee for the account. Conversely, removing allowances will decrease the auto-renewal fee for the account.

To decrease the allowance for a given spender, you must set the amount to the value you would like to authorize the account for. If the spender account was authorized to spend 25 HBAR and the owner wants to modify their allowance to 5 HBAR, the owner would submit the `AccountAllowanceApproveTransaction` for 5 HBAR.

Only when a spender is set on an explicit NFT ID of a token, do we return the spender ID in `TokenNftInfoQuery` for the respective NFT. If `approveTokenNftAllowanceAllSerials` is used to approve all NFTs for a given token class, and no NFT ID is specified; we will not return a spender ID for all the serial numbers of that token.

**Transaction Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

* Must be signed by the owner's account
* Must be signed by the transaction fee payer if different then the owner account
* If the owner and transaction fee payer key are the same only one signature is required

**Reference:** [HIP-336](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-336.md)

### Methods

| **Type**                                                                                                                                                                                       | **Description**                                                                                                                                                                               | **Method**                                                                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| [AccountId](../specialized-types.md#accountid), [AccountId](../specialized-types.md#accountid), [Hbar](../hbars.md)                                                                            | The owner account ID that is authorizing the allowance, the spender account ID to authorize, the amount of hbar the owner account is authorizing the spender account to use.                  | `approveHbarAllowance(<ownerAccountId>,<spenderAccountId>, <amount>)`                |
| <p><a href="../token-service/token-id.md">TokenId</a>,<br><a href="../specialized-types.md#accountid">AccountId</a>,</p><p><a href="../specialized-types.md#accountid">AccountId</a>, long</p> | The token ID of the token being granted an allowance by the spender account, the account ID of the owner account, the account ID of the spender account.                                      | `approveTokenAllowance(<tokenId>,<ownerAccountId>,<spenderAccountId>, <amount>)`     |
| <p><a href="../token-service/nft-id.md">nftId</a>, <a href="../specialized-types.md#accountid">AccountId</a>,<br><a href="../specialized-types.md#accountid">AccountId</a></p>                 | The NFT ID of the NFT being granted an allowance by the owner account, the account ID of the owner account, the account ID of the spender account.                                            | `approveTokenNftAllowance(<nftId>,<ownerAccountId>, <spenderAccountId>)`             |
| <p><a href="../token-service/token-id.md">TokenId</a>,<br><a href="../specialized-types.md#accountid">AccountId</a>,<br><a href="../specialized-types.md#accountid">AccountId</a>,</p>         | Grant a spender account access to all NFTs in a given token class/collection. The token ID of the NFT collection, the account ID of the owner account, the account ID of the spender account. | `approveTokenNftAllowanceAllSerials(<tokenId>,<ownerAccountId>, <spenderAccountId>)` |

{% tabs %}
{% tab title="Java" %}
```java
//Create the transaction
AccountAllowanceApproveTransaction transaction = new AccountAllowanceApproveTransaction()
    .approveHbarAllowance(ownerAccount, spenderAccountId, Hbar.from(1));

//Sign the transaction with the owner account key and the transaction fee payer key (client)  
TransactionResponse txResponse = transaction.freezeWith(client).sign(ownerAccountKey).execute(client);

//Request the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the transaction consensus status
Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus);

//v2.12.0+
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new AccountAllowanceApproveTransaction()
    .approveHbarAllowance(ownerAccount, spenderAccountId, Hbar.from(1));
    
//Sign the transaction with the owner account key
const signTx = await transaction.sign(ownerAccountKey);

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the transaction consensus status
const transactionStatus = receipt.status;

console.log("The transaction consensus status is " +transactionStatus.toString());

//v2.13.0
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the transaction
transaction := hedera.NewAccountAllowanceApproveTransaction().
     ApproveHbarAllowance(ownerAccount, spenderAccountId, Hbar.fromTinybars(1))
        FreezeWith(client)

if err != nil {
    panic(err)
}

//Sign the transaction with the owner account private key   
txResponse, err := transaction.Sign(ownerAccountKey).Execute(client)

//Request the receipt of the transaction
receipt, err := txResponse.GetReceipt(client)
if err != nil {
    panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

println("The transaction consensus status is ", transactionStatus)
//v2.13.1+
```
{% endtab %}
{% endtabs %}
