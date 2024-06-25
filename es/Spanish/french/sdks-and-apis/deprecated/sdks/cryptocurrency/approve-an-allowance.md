# Approve an allowance

A transaction that allows a token owner to delegate a token spender to spend the specified token amount on behalf of the token owner. An owner can provide a token allowance for hbars, non-fungible and fungible tokens. The owner is the account that owns the tokens and grants the allowance to the spender. The spender is the account that spends tokens authorized by the owner from the owners account. The spender pays for the transaction fees when transferring tokens from the owners account to another recipient.

The total number of approvals in this transaction cannot exceed 20. Note that each NFT serial number counts as a single approval, hence a transaction granting 20 serial numbers to a spender will use all of the approvals permitted for the transaction.

A single NFT serial number can only be granted to one spender at a time. If an approval assigns a previously approved NFT serial number to a new user, the old user will have their approval removed.

Each account is limited to 100 allowances. This limit spans hbar and fungible token allowances and non-fungible token `approved_for_all` grants. There is no limit on the number of NFT serial number approvals an owner may grant.

The number of allowances set on an account will increase the auto renewal fee for the account. Conversely, removing allowances will decrease the auto renewal fee for the account.

To decrease the allowance for a given spender, you will need to set the amount to the value you would like to authorize the spender account for. If the spender account was authorized to spend 25 hbars and the owner now wants to modify their allowance to 5 hbars, the owner would submit the AccountAllowanceApproveTransaction for 5 hbars.

Only when a spender is set on an explicit NFT ID of a token, we return the spender ID in the`TokenNftInfoQuery` for the respective NFT. If `approveTokenNftAllowanceAllSerials` is used to approve all NFTs for a given token class and no NFT ID is specified, we will not return a spender ID for all the serial numbers of that token.

**Transaction Fees**

- Please see the transaction and query [fees](../../../../networks/mainnet/fees/#transaction-and-query-fees) table for base transaction fee
- Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Transaction Signing Requirements**

- Must be signed by the owner's account
- Must be signed by the transaction fee payer if different then the owner account
- If the owner and transaction fee payer key are the same only one signature is required

**Reference:** [HIP-336](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-336.md)

| **Constructor**                            | **Description**                                           |
| ------------------------------------------ | --------------------------------------------------------- |
| `new AccountAllowanceApproveTransaction()` | Initializes the AccountAllowanceApproveTransaction object |

### Methods

| **Method**                                                                           | **Type**                                                                                                                                                                                                                                                 | **Description**                                                                                                                                                                                                               |
| ------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `approveHbarAllowance(<ownerAccountId>,<spenderAccountId>, <amount>)`                | [AccountId](../../../sdks/specialized-types.md#accountid), [AccountId](../../../sdks/specialized-types.md#accountid), [Hbar](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/sdks/cryptocurrency/broken-reference/README.md) | The owner account ID that is authorizing the allowance, the spender account ID to authorize, the amount of hbar the owner account is authorizing the spender account to use.                                  |
| `approveTokenAllowance(<tokenId>,<ownerAccountId>,<spenderAccountId>, <amount>)`     | <p><a href="../../../sdks/token-service/token-id.md">TokenId</a>,<br><a href="../../../sdks/specialized-types.md#accountid">AccountId</a>,</p><p><a href="../../../sdks/specialized-types.md#accountid">AccountId</a>, long</p>                          | The token ID of the token being granted an allowance by the spender account, the account ID of the owner account, the account ID of the spender account.                                                      |
| `approveTokenNftAllowance(<nftId>,<ownerAccountId>, <spenderAccountId>)`             | <p><a href="../../../sdks/token-service/nft-id.md">nftId</a>, <a href="../../../sdks/specialized-types.md#accountid">AccountId</a>,<br><a href="../../../sdks/specialized-types.md#accountid">AccountId</a></p>                                          | The NFT ID of the NFT being granted an allowance by the owner account, the account ID of the owner account, the account ID of the spender account.                                                            |
| `approveTokenNftAllowanceAllSerials(<tokenId>,<ownerAccountId>, <spenderAccountId>)` | <p><a href="../../../sdks/token-service/token-id.md">TokenId</a>,<br><a href="../../../sdks/specialized-types.md#accountid">AccountId</a>,<br><a href="../../../sdks/specialized-types.md#accountid">AccountId</a>,</p>                                  | Grant a spender account access to all NFTs in a given token class/collection. The token ID of the NFT collection, the account ID of the owner account, the account ID of the spender account. |

{% tabs %}
{% tab title="Java" %}

```java
//Create the transaction
AccountAllowanceApproveTransaction transaction = new AccountAllowanceApproveTransaction()
    .approveHbarAllowance(ownerAccount, spenderAccountId, Hbar.from(100));

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
    .approveHbarAllowance(ownerAccount, spenderAccountId, Hbar.from(100));
    
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
     ApproveHbarAllowance(ownerAccount, spenderAccountId, Hbar.fromTinybars(100))
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
