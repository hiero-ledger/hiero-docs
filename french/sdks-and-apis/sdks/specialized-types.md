# Specialized Types

## [AccountId](https://github.com/hashgraph/hedera-sdk-java/blob/master/src/main/java/com/hedera/hashgraph/sdk/account/AccountId.java)

An `AccountId` is composed of a \<shardNum>.\<realmNum>.\<accountNum> (eg. 0.0.10).

* Shard number (`shardNum`**)** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
* Realm number (`realmNum`) represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
* Account represents either an account number or an account alias
  * Account number (`accountNum`) represents the account number (`accountId`)
  * Account alias (alias) represented by the public key bytes
    * The public key bytes are the result of serializing a protobuf Key message for any primitive key type
    * Currently, only primitive key bytes are supported as an alias
    * Threshold keys, key list, contract ID, and delegatable\_contract\_id are not supported
    * The alias can only be used in place of an account ID in transfer transactions in its current version

Together these values make up your `AccountId`. When an `AccountId` is specified, be sure all three values are included.

### Constructor

<table data-header-hidden><thead><tr><th width="293.3333333333333"></th><th width="151" align="center"></th><th></th></tr></thead><tbody><tr><td><strong>Constructor</strong></td><td align="center"><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>new AccountId(&#x3C;shardNum>,&#x3C;realmNum>,&#x3C;accountNum>)</code></td><td align="center">long, long, long</td><td>Constructs an <code>AccountId</code> with 0 for <code>shardNum</code> and <code>realmNum</code> (e.g., <code>0.0.&#x3C;accountNum></code>)</td></tr></tbody></table>

### Methods

<table data-header-hidden><thead><tr><th></th><th width="118.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Methods</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>AccountId.fromString(&#x3C;account>)</code></td><td>String</td><td>Constructs an <code>AccountId</code> from a string formatted as &#x3C;shardNum>.&#x3C;realmNum>.&#x3C;accountNum></td></tr><tr><td><code>AccountId.fromEvmAddress(&#x3C;address>)</code></td><td>String</td><td>Constructs an <code>AccountId</code> from a solidity address in string format</td></tr><tr><td><code>AccountId.fromBytes(bytes)</code></td><td>byte[]</td><td>Constructs an <code>AccountId</code> from bytes</td></tr><tr><td><code>AccountId.toSolidityAddress()</code></td><td>String</td><td>Constructs a solidity address from <code>AccountID</code></td></tr><tr><td><code>AccountId.toString()</code></td><td>String</td><td>Constructs an <code>AccountID</code> from string</td></tr><tr><td><code>AccountId.aliasKey</code></td><td>PublicKey</td><td>The alias key of the <code>AccountID</code></td></tr><tr><td><code>AccountId.aliasEvmAddress</code></td><td>EVM address</td><td>The EVM address of the <code>AccountID</code></td></tr><tr><td><code>AccountId.toBytes()</code></td><td>byte[]</td><td>Constructs an <code>AccountID</code> from bytes</td></tr></tbody></table>

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

* **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
* **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
* **fileNum** represents the file number

Together these values make up your accountId. When an `FileId` is requested, be sure all three values are included.

### Constructor

<table data-header-hidden><thead><tr><th></th><th width="160.33333333333331" align="center"></th><th></th></tr></thead><tbody><tr><td><strong>Constructor</strong></td><td align="center"><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>new FileId(&#x3C;shardNum>,&#x3C;realmNum>,&#x3C;fileNum>)</code></td><td align="center">long, long, long</td><td>Constructs a <code>FileId</code> with 0 for <code>shardNum</code> and <code>realmNum</code> (e.g., <code>0.0.&#x3C;fileNum></code>)</td></tr></tbody></table>

### Methods

<table data-header-hidden><thead><tr><th></th><th width="92.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Methods</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>FileId.fromString()</code></td><td>String</td><td><p>Constructs an <code>FileId</code> from a string formatted as</p><p>&#x3C;shardNum>.&#x3C;realmNum>.&#x3C;fileNum></p></td></tr><tr><td><code>FileId.fromSolidityAddress()</code></td><td>String</td><td>Constructs an <code>FileId</code> from a solidity address in string format</td></tr><tr><td><code>FileId.ADDRESS_BOOK</code></td><td>FileId</td><td>The public node address book for the current network</td></tr><tr><td><code>FileId.EXCHANGE_RATES</code></td><td>FileId</td><td>The current exchange rate of HBAR to USD</td></tr><tr><td><code>FileId.FEE_SCHEDULE</code></td><td>FileId</td><td>The current fee schedule for the network</td></tr></tbody></table>

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

* **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
* **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
* **contractNum** represents the contract number

Together these values make up your `ContractId`. When an `ContractId` is requested, be sure all three values are included. ContractId's are automatically assigned when you create a new smart contract.

### Constructor

| **Constructor**                                       | **Type**         | **Description**                                                                            |
| ----------------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| `new ContractId(<shardNum>,<realmNum>,<contractNum>)` | long, long, long | Constructs a `ContractId` with 0 for `shardNum` and `realmNum` (e.g., `0.0.<contractNum>`) |

### Methods

<table data-header-hidden><thead><tr><th></th><th width="164.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Methods</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>ContractId.fromString(&#x3C;account>)</code></td><td>String</td><td><p>Constructs a <code>ContractId</code> from a string formatted as</p><p>&#x3C;shardNum>.&#x3C;realmNum>.&#x3C;contractNum></p></td></tr><tr><td><code>ContractId.fromSolidityAddress(&#x3C;address>)[deprecated use ConractId.fromEvmAddress()]</code></td><td>String</td><td>Constructs a <code>ContractId</code> from a solidity address in string format [deprecated use <code>ContractId.fromEvmAddres()]</code></td></tr><tr><td><code>ContractId.toSolidityAddress(&#x3C;contractId>)</code></td><td>String</td><td>Contruct a Solidity address from a Hedera contract ID</td></tr><tr><td><code>ContractId.fromEvmAddress(&#x3C;shard>, &#x3C;realm>, &#x3C;evmAddress>)</code></td><td>long, long, String</td><td>Constructs a <code>ContractId</code> from evm address</td></tr></tbody></table>

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

* **shardNum** represents the shard number (`shardId`). It will default to 0 today, as Hedera only performs in one shard.
* **realmNum** represents the realm number (`realmId`). It will default to 0 today, as realms are not yet supported.
* **topicNum** represents the topic number (`topicId`)

### Constructor

<table data-header-hidden><thead><tr><th></th><th width="168.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Constructor</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>new ConsensusTopicId(&#x3C;shardNum>,&#x3C;realmNum>,&#x3C;topicNum>)</code></td><td>long, long, long</td><td>Constructs a <code>TopicId</code> with <code>0</code> for <code>shardNum</code> and <code>realmNum</code> (e.g., <code>0.0.&#x3C;topicNum></code>)</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th></th><th width="80.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Methods</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>fromString(&#x3C;topic>)</code></td><td>String</td><td>Constructs a topic ID from a String</td></tr><tr><td><code>ConsensusTopicId.toString()</code></td><td></td><td>Constructs a topic ID to String format</td></tr></tbody></table>

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
