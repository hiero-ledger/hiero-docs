# Specialized Types

## [AccountId](https://github.com/hashgraph/hedera-sdk-java/blob/master/src/main/java/com/hedera/hashgraph/sdk/account/AccountId.java)

An `AccountId` is composed of a \<shardNum>.\<realmNum>.\<accountNum> (eg. 0.0.10).

- Shard number (`shardNum`**)** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
- Realm number (`realmNum`) represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
- Account represents either an account number or an account alias
  - Account number (`accountNum`) represents the account number (`accountId`)
  - Account alias (alias) represented by the public key bytes
    - The public key bytes are the result of serializing a protobuf Key message for any primitive key type
    - Currently, only primitive key bytes are supported as an alias
    - Threshold keys, key list, contract ID, and delegatable\_contract\_id are not supported
    - The alias can only be used in place of an account ID in transfer transactions in its current version

Together these values make up your `AccountId`. When an `AccountId` is specified, be sure all three values are included.

### Constructor

| **Constructor**                                     |     **Type**     | **Description**                                                                                                                              |
| --------------------------------------------------- | :--------------: | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `new AccountId(<shardNum>,<realmNum>,<accountNum>)` | long, long, long | Constructs an `AccountId` with 0 for `shardNum` and `realmNum` (e.g., `0.0.<accountNum>`) |

### Methods

| **Methods**                                | **Type**                                                     | **Description**                                                                                                               |
| ------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `AccountId.fromString(<account>)`          | String                                                       | Constructs an `AccountId` from a string formatted as \<shardNum>.\<realmNum>.\<accountNum> |
| `AccountId.fromSolidityAddress(<address>)` | String                                                       | Constructs an `AccountId` from a solidity address in string format                                                            |
| `AccountId.fromBytes(bytes)`               | byte\[] | Constructs an `AccountId` from bytes                                                                                          |
| `AccountId.toSolidityAddress()`            | String                                                       | Constructs a solidity address from `AccountID`                                                                                |
| `AccountId.toString()`                     | String                                                       | Constructs an `AccountID` from string                                                                                         |
| `AccountId.aliasKey`                       | PublicKey                                                    | The alias key of the `AccountID`                                                                                              |
| `AccountId.aliasEvmAddress`                | EVM address                                                  | The EVM address of the `AccountID`                                                                                            |
| `AccountId.toBytes()`                      | byte\[] | Constructs an `AccountID` from bytes                                                                                          |

### Example

{% tabs %}
{% tab title="Java" %}

```java
AccountId accountId = new AccountId(0 ,0 ,10);
System.out.println(accountId);

// Constructs an accountId from String
AccountId accountId = AccountId.fromString("0.0.10");
System.out.println(accountId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const accountId = new AccountId(100);
console.log(`${accountId}`);

// Construct accountId from String
const accountId = AccountId.fromString(`100`);
console.log(`${accountId}`);
```

{% endtab %}

{% tab title="Go" %}

```go
hedera.AccountIDFromString("0.0.3")
```

{% endtab %}
{% endtabs %}

## [FileId](https://github.com/hashgraph/hedera-sdk-java/blob/master/src/main/java/com/hedera/hashgraph/sdk/file/FileId.java)

A `FileId` is composed of a \<shardNum>.\<realmNum>.\<fileNum> (eg. 0.0.15).

- **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
- **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
- **fileNum** represents the file number

Together these values make up your accountId. When an `FileId` is requested, be sure all three values are included.

### Constructor

| **Constructor**                               |     **Type**     | **Description**                                                                                                                       |
| --------------------------------------------- | :--------------: | ------------------------------------------------------------------------------------------------------------------------------------- |
| `new FileId(<shardNum>,<realmNum>,<fileNum>)` | long, long, long | Constructs a `FileId` with 0 for `shardNum` and `realmNum` (e.g., `0.0.<fileNum>`) |

### Methods

| **Methods**                    | **Type** | **Description**                                                                                                          |
| ------------------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| `FileId.fromString()`          | String   | <p>Constructs an <code>FileId</code> from a string formatted as</p><p>&#x3C;shardNum>.&#x3C;realmNum>.&#x3C;fileNum></p> |
| `FileId.fromSolidityAddress()` | String   | Constructs an `FileId` from a solidity address in string format                                                          |
| `FileId.ADDRESS_BOOK`          | FileId   | The public node address book for the current network                                                                     |
| `FileId.EXCHANGE_RATES`        | FileId   | The current exchange rate of HBAR to USD                                                                                 |
| `FileId.FEE_SCHEDULE`          | FileId   | The current fee schedule for the network                                                                                 |

### Example

{% tabs %}
{% tab title="Java" %}

```java
FileId fileId = new FileId(0,0,15);
System.out.println(fileId);

//Constructs a FileId from string
FileId fileId = FileId.fromString("0.0.15");
System.out.println(fileId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const newFileId = new FileId(100);
console.log(`${newFileId}`);

//Construct a fileId from a String
const newFileIdFromString = FileId.fromString(`100`); 
console.log(`${newFileIdFromString}`);
```

{% endtab %}

{% tab title="Go" %}

```go
hedera.FileIDFromString("0.0.3")
```

{% endtab %}
{% endtabs %}

## [ContractId](https://github.com/hashgraph/hedera-sdk-java/blob/master/src/main/java/com/hedera/hashgraph/sdk/contract/ContractId.java)

A `ContractId` is composed of a \<shardNum>.\<realmNum>.\<contractNum> (eg. 0.0.20).

- **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
- **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
- **contractNum** represents the contract number

Together these values make up your `ContractId`. When an `ContractId` is requested, be sure all three values are included. ContractId's are automatically assigned when you create a new smart contract.

### Constructor

| **Constructor**                                       | **Type**         | **Description**                                                                                                                               |
| ----------------------------------------------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `new ContractId(<shardNum>,<realmNum>,<contractNum>)` | long, long, long | Constructs a `ContractId` with 0 for `shardNum` and `realmNum` (e.g., `0.0.<contractNum>`) |

### Methods

| **Methods**                                                                            | **Type**           | **Description**                                                                                                                              |
| -------------------------------------------------------------------------------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `ContractId.fromString(<account>)`                                                     | String             | <p>Constructs a <code>ContractId</code> from a string formatted as</p><p>&#x3C;shardNum>.&#x3C;realmNum>.&#x3C;contractNum></p>              |
| `ContractId.fromSolidityAddress(<address>)[deprecated use ConractId.fromEvmAddress()]` | String             | Constructs a `ContractId` from a solidity address in string format \[deprecated use `ContractId.fromEvmAddres()]` |
| `ContractId.toSolidityAddress(<contractId>)`                                           | String             | Contruct a Solidity address from a Hedera contract ID                                                                                        |
| `ContractId.fromEvmAddress(<shard>, <realm>, <evmAddress>)`                            | long, long, String | Constructs a `ContractId` from evm address                                                                                                   |

### Example

{% tabs %}
{% tab title="Java" %}

```java
ContractId contractId = new ContractId(0,0,20);
System.out.println(contractId);

// Constructs a ContractId from string
ContractId contractId = ContractId.fromString("0.0.20");
System.out.println(contractId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const newContractId = new ContractId(100);
console.log(`${newContractId}`);

// Construct a contractId from a String
const newContractId = ContractId.fromString(`100`); 
console.log(`${newContractId}`);
```

{% endtab %}

{% tab title="Go" %}

```
hedera.ContractIDFromString("0.0.3")
```

{% endtab %}
{% endtabs %}

## [TopicId](https://github.com/hashgraph/hedera-sdk-java/blob/master/src/main/java/com/hedera/hashgraph/sdk/consensus/ConsensusTopicId.java)

A `topicId` is composed of a \<shardNum>.\<realmNum>.\<topicNum> (eg. 0.0.100).

- **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
- **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
- **topicNum** represents the topic number (`topicId`)

### Constructor

| **Constructor**                                          | **Type**         | **Description**                                                                                                                           |
| -------------------------------------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `new ConsensusTopicId(<shardNum>,<realmNum>,<topicNum>)` | long, long, long | Constructs a `TopicId` with `0` for `shardNum` and `realmNum` (e.g., `0.0.<topicNum>`) |

| **Methods**                   | **Type** | **Description**                        |
| ----------------------------- | -------- | -------------------------------------- |
| `fromString(<topic>)`         | String   | Constructs a topic ID from a String    |
| `ConsensusTopicId.toString()` |          | Constructs a topic ID to String format |

### Example

{% tabs %}
{% tab title="Java" %}

```java
ConsensusTopicId topicId = new ConsensusTopicId(0,0,100);
System.out.println(topicId)
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const topicId = new ConsensusTopicId(0,0,100);
console.log(topicId)
```

{% endtab %}

{% tab title="Go" %}

```go
hedera.TopicIDFromString("0.0.3")
```

{% endtab %}
{% endtabs %}
