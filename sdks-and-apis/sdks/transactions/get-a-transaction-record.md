# Get a transaction record

You can request a transaction record for up to 3 minutes after a transaction has reached consensus. This query returns a maximum of [180 records](https://github.com/hashgraph/hedera-services/blob/master/hedera-node/src/test/resources/bootstrap/standard.properties#L83) per request. The transaction record provides the following information about a transaction:

#### Transaction Record Contents

<table><thead><tr><th width="281">Property</th><th>Description</th></tr></thead><tbody><tr><td><strong>Transaction ID</strong></td><td>The ID of the transaction.</td></tr><tr><td><strong>Consensus timestamp</strong></td><td>The time the transaction reached consensus and was added to the ledger.</td></tr><tr><td><strong>Contract Call Result</strong></td><td>Record of the value returned by the smart contract function (if it completed and didn't fail) from ContractCallTransaction.</td></tr><tr><td><strong>Contract Create Result</strong></td><td>Record of the value returned by the smart contract constructor (if it completed and didn't fail) from ContractCreateTransaction.</td></tr><tr><td><strong>Receipt</strong></td><td>The receipt of the transaction.</td></tr><tr><td><strong>Transaction Fee</strong></td><td>The transaction fee that was charged.</td></tr><tr><td><strong>Transaction Hash</strong></td><td>The transaction hash.</td></tr><tr><td><strong>Transaction Memo</strong></td><td>The transaction memo if there was one added.</td></tr><tr><td><strong>Transfers</strong></td><td>A list of transfers made in the transaction. The list of transfers includes a payment made to the node, the service fee, and transaction fee.</td></tr><tr><td><strong>Token Transfers</strong></td><td>A list of the token transfers .</td></tr><tr><td><strong>ScheduleRef</strong></td><td>The schedule ID of the schedule transaction the record represents.</td></tr><tr><td><strong>Assessed Custom Fees</strong></td><td>This field applies to tokens that have custom fees and returns the custom fee(s) assessed in a token transfer transaction. This includes the amount, token ID, fee collector account ID (if applicable), and effective payer account ID. The effective payer accounts are accounts that were charged the custom fees.</td></tr><tr><td><strong>Automatic Associations</strong></td><td>The token(s) that were auto associated to the account in this transaction, if any</td></tr><tr><td><strong>Alias</strong></td><td>In the record of an internal <code>AccountCreateTransaction</code> triggered by a user transaction with a (previously unused) alias, the new account's alias.</td></tr><tr><td><strong>Parent Consensus Timestamp</strong></td><td>The parent consensus timestamp is found in the record of a child transaction. The parent consensus timestamp is the consensus timestamp related to the parent transaction to this child transaction.</td></tr><tr><td><strong>Ethereum Hash</strong></td><td>The keccak256 hash of the ethereumData. This field will only be populated for EthereumTransaction.</td></tr><tr><td><strong>Paid Staking Rewards</strong></td><td>List of accounts with the corresponding staking rewards paid as a result of a transaction. See <a href="https://hips.hedera.com/hip/hip-406">HIP-406</a>.<br>Network: <code>previewnet/testnet</code></td></tr><tr><td><strong>PRNG Bytes</strong></td><td>In the record of a PRNG transaction with no output range, a pseudorandom 384-bit string. See <a href="https://hips.hedera.com/hip/hip-351">HIP-351</a>.<br>Network: <code>previewnet</code></td></tr><tr><td><strong>PRNG Number</strong></td><td>In the record of a PRNG transaction with an output range, the output of a PRNG whose input was a 384-bit string. See <a href="https://hips.hedera.com/hip/hip-351">HIP-351</a>.<br>Network: <code>previewnet</code></td></tr><tr><td><strong>Pending Airdrop</strong></td><td>The pending airdrops are a result of the transaction. </td></tr><tr><td><strong>Include Children</strong></td><td>Whether or not to include the record for children transactions triggered by a parent transaction.</td></tr><tr><td><strong>Include Duplicates</strong></td><td>Whether or not to include the receipts for duplicate transactions.</td></tr><tr><td><strong>Reject Airdrop</strong></td><td>Transfer one or more tokens or token balances held by the requesting account to the treasury for each token type.</td></tr></tbody></table>

**Transaction Signing Requirements**

* The client operator account private key is required to sign

| **Constructor**                | **Description**                                 |
| ------------------------------ | ----------------------------------------------- |
| `new TransactionRecordQuery()` | Initializes the `TransactionRecordQuery` Object |

<table data-header-hidden><thead><tr><th width="434"></th><th width="140.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Requirement</strong></td></tr><tr><td><code>setTransactionId(&#x3C;transactionId>)</code></td><td>TransactionId</td><td>Required</td></tr><tr><td><code>setIncludeChildren(&#x3C;value>)</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>setIncludeDuplicates(&#x3C;value>)</code></td><td>boolean</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
new TransactionRecordQuery()
    .setTransactionId(transactionId)
    .execute(client)
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
new TransactionRecordQuery()
    .setTransactionId(transactionId)
    .execute(client)
```
{% endtab %}

{% tab title="Go" %}
```go
hedera.NewTransactionRecordQuery().
    SetTransactionId(transactionId).
    Execute(client)
```
{% endtab %}
{% endtabs %}

### Methods

<table data-header-hidden><thead><tr><th width="396"></th><th width="220.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Requirement</strong></td></tr><tr><td><code>&#x3C;TransactionResponse>.getRecord(&#x3C;client>)</code></td><td>TransactionRecord</td><td>Required</td></tr><tr><td><code>&#x3C;TransactionRecord>.transactionId</code></td><td>TransactionId</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.consensusTimestamp</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.contractFunctionResult</code></td><td>ContractFunctionResult</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.receipt</code></td><td>TransactionReceipt</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.transactionFee</code></td><td>Hbar</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.transactionHash</code></td><td>ByteString</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.transactionMemo</code></td><td>String</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.transfers</code></td><td>List&#x3C;Transfer></td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.tokentransfers</code></td><td>Map&#x3C;TokenId, Map&#x3C;AccountId, List&#x3C;Long>>></td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.scheduleRef</code></td><td>ScheduleId</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.assessedCustomFees</code></td><td>List&#x3C;AssessedCustomFees></td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.automaticTokenAssociations</code></td><td>List&#x3C;TokenAssociation></td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.ethereumHash</code></td><td>ByteString</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.parentConsensusTimestamp</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.paidStakingRewards</code></td><td>List&#x3C;Transfer></td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.prngBytes</code></td><td>ByteString</td><td>Optional</td></tr><tr><td><code>&#x3C;TransactionRecord>.prngNumber</code></td><td>Integer</td><td>Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create a transaction
AccountCreateTransaction transaction = new AccountCreateTransaction()
        .setKey(newKey.getPublicKey())
        .setInitialBalance(new Hbar(1));

//Sign with the client operator account key and submit to a Hedera network
TransactionResponse txResponse = transaction.execute(client);

//Request the record of the transaction
TransactionRecord record = txResponse.getRecord(client);

System.out.println("The transaction record is " +record);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a transaction
const transaction = new AccountCreateTransaction()
        .setKey(newKey.getPublicKey())
        .setInitialBalance(new Hbar(1));

//Sign with the client operator account key and submit to a Hedera network
const txResponse = await transaction.execute(client);

//Request the record of the transaction
const record = await txResponse.getRecord(client);

console.log("The transaction record is " +record);

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```java
//Create a transaction
transaction := hedera.NewAccountCreateTransaction().
		SetKey(privateKey.PublicKey()).
		SetInitialBalance(hedera.NewHbar(1000))

//Sign with the client operator account key and submit to a Hedera network
txResponse, err := transaction.Execute(client)

//Request the record of the transaction
record, err := txResponse.GetRecord(client)

fmt.Printf("The transaction record is %v\n", record)

//v2.0.0
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Sample Output:" %}
```
TransactionRecord{
     receipt=TransactionReceipt{
          status=SUCCESS, 
          exchangeRate=ExchangeRate{
               hbars=30000, cents=116646, 
               expirationTime=2020-09-04T03:00:007
          }, 
         accountId=0.0.97001, 
         fileId=null, 
         contractId=null, 
         topicId=null, 
         topicSequenceNumber=null
    },                  
    transactionHash=e005670a1f49c4fd776b2d432db3e5cb31441 bb5a35bff412ec3b41cb1 3366ce00b5c1b9900aad1467f9709a649ccc20, 
     consensusTimestamp=2020-11-05T08:34:31.107311002Z,
     transactionId=0.0.9401@160456525 8.479476328,
     transactionMemo=, 
     transactionFee=0.25401241 ℏ, 
     contractFunctionResult=null, 
     transfers=[
          Transfer{accountId=0.0.5, amount=0.01501152 ℏ}, (node fee)
          Transfer{accountId=0.0.98, amount=0.23900089 ℏ}, (service fee)
          Transfer{accountId=0.0.9401, amount=-1.25401241 ℏ}, (transaction fee) initial balance of new account
          Transfer{accountId=0.0.97001, amount=1 ℏ} (Initial balance of the new account)
     ]
     tokenTransfers={},
     scheduleRef=null,
     assessedCustomFees=[],
     automaticTokenAssociations=[TokenAssociation{
          tokenId=0.0.27335, accountId=0.0.27333}]
}
```
{% endtab %}
{% endtabs %}
