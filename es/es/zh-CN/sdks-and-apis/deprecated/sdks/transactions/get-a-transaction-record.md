# Get a transaction record

You can request a transaction record for up to 3 minutes after a transaction has reached consensus. This query returns a maximum of [180 records](https://github.com/hashgraph/hedera-services/blob/master/hedera-node/src/test/resources/bootstrap/standard.properties#L83) per request. The transaction record provides the following information about a transaction:

#### Transaction Record Contents

| **Fields**                     | **Description**                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Transaction ID**             | The ID of the transaction.                                                                                                                                                                                                                                                                                                                                                                  |
| **Consensus timestamp**        | The time the transaction reached consensus and was added to the ledger.                                                                                                                                                                                                                                                                                                                     |
| **Contract Call Result**       | Record of the value returned by the smart contract function (if it completed and didn't fail) from ContractCallTransaction.                                                                                                                                                                                                                                              |
| **Contract Create Result**     | Record of the value returned by the smart contract constructor (if it completed and didn't fail) from ContractCreateTransaction.                                                                                                                                                                                                                                         |
| **Receipt**                    | The receipt of the transaction.                                                                                                                                                                                                                                                                                                                                                             |
| **Transaction Fee**            | The transaction fee that was charged.                                                                                                                                                                                                                                                                                                                                                       |
| **Transaction Hash**           | The transaction hash.                                                                                                                                                                                                                                                                                                                                                                       |
| **Transaction Memo**           | The transaction memo if there was one added.                                                                                                                                                                                                                                                                                                                                                |
| **Transfers**                  | A list of transfers made in the transaction. The list of transfers includes a payment made to the node, the service fee, and transaction fee.                                                                                                                                                                                                                               |
| **Token Transfers**            | A list of the token transfers .                                                                                                                                                                                                                                                                                                                                                             |
| **ScheduleRef**                | The schedule ID of the schedule transaction the record represents.                                                                                                                                                                                                                                                                                                                          |
| **Assessed Custom Fees**       | This field applies to tokens that have custom fees and returns the custom fee(s) assessed in a token transfer transaction. This includes the amount, token ID, fee collector account ID (if applicable), and effective payer account ID. The effective payer accounts are accounts that were charged the custom fees. |
| **Automatic Associations**     | The token(s) that were auto associated to the account in this transaction, if any                                                                                                                                                                                                                                                                                                        |
| **Alias**                      | In the record of an internal `AccountCreateTransaction` triggered by a user transaction with a (previously unused) alias, the new account's alias.                                                                                                                                                                                                                       |
| **Parent Consensus Timestamp** | The parent consensus timestamp is found in the record of a child transaction. The parent consensus timestamp is the consensus timestamp related to the parent transaction to this child transaction.                                                                                                                                                                        |
| **Ethereum Hash**              | The keccak256 hash of the ethereumData. This field will only be populated for EthereumTransaction.                                                                                                                                                                                                                                                                          |
| **Paid Staking Rewards**       | <p>List of accounts with the corresponding staking rewards paid as a result of a transaction. See <a href="https://hips.hedera.com/hip/hip-406">HIP-406</a>.<br>Network: <code>previewnet/testnet</code></p>                                                                                                                                                                                                |
| **PRNG Bytes**                 | <p>In the record of a PRNG transaction with no output range, a pseudorandom 384-bit string. See <a href="https://hips.hedera.com/hip/hip-351">HIP-351</a>.<br>Network: <code>previewnet</code></p>                                                                                                                                                                                                          |
| **PRNG Number**                | <p>In the record of a PRNG transaction with an output range, the output of a PRNG whose input was a 384-bit string. See <a href="https://hips.hedera.com/hip/hip-351">HIP-351</a>.<br>Network: <code>previewnet</code></p>                                                                                                                                                                                  |

**Transaction Signing Requirements**

- The client operator account private key is required to sign

| **Constructor**                | **Description**                                 |
| ------------------------------ | ----------------------------------------------- |
| `new TransactionRecordQuery()` | Initializes the `TransactionRecordQuery` Object |

- Transaction ID: The ID of the transaction to return the record for
- Include Children: Whether or not to include the record for children transactions triggered by a parent transaction
- Include Duplicates: Whether or not to include the receipts for duplicate transactions

| **Method**                          | **Type**      | **Requirement** |
| ----------------------------------- | ------------- | --------------- |
| `setTransactionId(<transactionId>)` | TransactionId | Required        |
| `setIncludeChildren(<value>)`       | boolean       | Optional        |
| `setIncludeDuplicates(<value>)`     | boolean       | Optional        |

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

{% tabs %}
{% tab title="V2" %}

| **Method**                                       | **Type**                                                 | **Requirement** |
| ------------------------------------------------ | -------------------------------------------------------- | --------------- |
| `<TransactionResponse>.getRecord(<client>)`      | TransactionRecord                                        | Required        |
| `<TransactionRecord>.transactionId`              | TransactionId                                            | Optional        |
| `<TransactionRecord>.consensusTimestamp`         | Instant                                                  | Optional        |
| `<TransactionRecord>.contractFunctionResult`     | ContractFunctionResult                                   | Optional        |
| `<TransactionRecord>.receipt`                    | TransactionReceipt                                       | Optional        |
| `<TransactionRecord>.transactionFee`             | Hbar                                                     | Optional        |
| `<TransactionRecord>.transactionHash`            | ByteString                                               | Optional        |
| `<TransactionRecord>.transactionMemo`            | String                                                   | Optional        |
| `<TransactionRecord>.transfers`                  | List\<Transfer>                                         | Optional        |
| `<TransactionRecord>.tokentransfers`             | Map\\\\<TokenId, Map\\\\<AccountId, List\<Long>>> | Optional        |
| `<TransactionRecord>.scheduleRef`                | ScheduleId                                               | Optional        |
| `<TransactionRecord>.assessedCustomFees`         | List\<AssessedCustomFees>                               | Optional        |
| `<TransactionRecord>.automaticTokenAssociations` | List\<TokenAssociation>                                 | Optional        |
| `<TransactionRecord>.ethereumHash`               | ByteString                                               | Optional        |
| `<TransactionRecord>.parentConsensusTimestamp`   | Instant                                                  | Optional        |
| `<TransactionRecord>.paidStakingRewards`         | List\<Transfer>                                         | Optional        |
| `<TransactionRecord>.prngBytes`                  | ByteString                                               | Optional        |
| `<TransactionRecord>.prngNumber`                 | Integer                                                  | Optional        |

{% code title="Java" %}

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

{% endcode %}

{% code title="JavaScript" %}

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

{% endcode %}

{% code title="Go" %}

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

{% endcode %}

**Sample Output:**

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

{% tab title="V1" %}

| **Method**                                   | **Type**                                                       | **Requirement** |
| -------------------------------------------- | -------------------------------------------------------------- | --------------- |
| `<TransactionId>.getRecord(<client>)`        | TransactionRecord                                              | Required        |
| `<TransactionRecord>.transactionId`          | TransactionId                                                  | Optional        |
| `<TransactionRecord>.consensusTimestamp`     | Instant                                                        | Optional        |
| `<TransactionRecord>.contractFunctionResult` | ContractFunctionResult                                         | Optional        |
| `<TransactionRecord>.receipt`                | TransactionReceipt                                             | Optional        |
| `<TransactionRecord>.transactionFee`         | long                                                           | Optional        |
| `<TransactionRecord>.transactionHash`        | byte \[ ] | Optional        |
| `<TransactionRecord>.transactionMemo`        | String                                                         | Optional        |
| `<TransactionRecord>.transfers`              | List\<Transfer>                                               | Optional        |
| `<TransactionRecord>.tokentransfers`         | Map\\\\<TokenId, Map\\\\<AccountId, List\<Long>>>       | Optional        |
| `<TransactionRecord>.scheduleRef`            | ScheduleId                                                     | Optional        |
| `<TransactionRecord>.assessedCustomFees`     | List\<AssessedCustomFees>                                     | Optional        |

{% code title="Java" %}

```java
//Create a transaction
AccountCreateTransaction transaction = new AccountCreateTransaction()
        .setKey(newKey.getPublicKey())
        .setInitialBalance(new Hbar(1));

//Sign with the client operator account key and submit to a Hedera network
TransactionId txId = transaction.execute(client);

//Request the record of the transaction
TransactionRecord record = txId.getRecord(client);

System.out.println("The transaction record is " +record);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
//Create a transaction
const transaction = new AccountCreateTransaction()
        .setKey(newKey.getPublicKey())
        .setInitialBalance(new Hbar(1));

//Sign with the client operator account key and submit to a Hedera network
const txId = await transaction.execute(client);

//Request the record of the transaction
const record = await txId.getRecord(client);

console.log("The transaction record is " +record);

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

##
