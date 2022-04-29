# Create a smart contract

A transaction that creates a new smart contract instance. After the contract is created you can get the new contract ID by requesting the receipt of the transaction. To create the solidity smart contract, you can use [remix](https://remix.ethereum.org/#optimize=false\&runs=200\&evmVersion=null\&version=soljson-v0.8.7+commit.e28d00a7.js) or another [Solidity](https://docs.soliditylang.org/en/v0.8.9/) compiler. After you have the hex-encoded bytecode of the smart contract you need to store that on a file using the [Hedera File Service](../file-storage/create-a-file.md). Then you will create the smart contract instance that will run the bytecode stored in the Hedera file, referenced by file ID. Alternatively, you can use the <mark style="color:purple;">`ContractCreateFlow()`</mark> API to create the file storing the bytecode and contract in a single step.

The constructor will be executed using the given amount of gas, and any unspent gas will be refunded to the paying account. Constructor inputs are passed in the `constructorParameters`.&#x20;

If this constructor stores information, it is charged gas to store it. There is a fee in hbars to maintain that storage until the expiration time, and that fee is added as part of the transaction fee.

{% hint style="danger" %}
Smart contract entity auto renewal and expiry will be enabled in a future release. Please check out [HIP-16](https://hips.hedera.com/hip/hip-16) for more information.&#x20;
{% endhint %}

{% hint style="info" %}
**Solidity Support**\
****The latest version of Solidity is supported on all networks (v0.8.9).
{% endhint %}

{% hint style="info" %}
**Smart Contract State Size and Gas Limits**

The Hedera Services 0.22 release increases the contract state size to 10 MB and the system gas throttle to 15 million gas per second. Contract call and contract create are throttled at 4 million gas per second.
{% endhint %}

{% content-ref url="../../../core-concepts/smart-contracts/gas-and-fees.md" %}
[gas-and-fees.md](../../../core-concepts/smart-contracts/gas-and-fees.md)
{% endcontent-ref %}

{% content-ref url="../../../core-concepts/smart-contracts/solidity-variables-and-opcodes.md" %}
[solidity-variables-and-opcodes.md](../../../core-concepts/smart-contracts/solidity-variables-and-opcodes.md)
{% endcontent-ref %}

**Transaction Signing Requirements**

* The client operator account is required to sign the transaction
* The admin key, if specified&#x20;

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Smart Contract Properties**

| **Field**                  | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin Key**              | Sets the state of the instance and its fields can be modified arbitrarily if this key signs a transaction to modify it. If this is null, then such modifications are not possible, and there is no administrator that can override the normal operation of this smart contract instance. Note that if it is created with no admin keys, then there is no administrator to authorize changing the admin keys, so there can never be any admin keys for that instance. |
| **Gas**                    | The gas to run the constructor.                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Initial Balance**        | The initial number of hbars to put into the cryptocurrency account associated with and owned by the smart contract.                                                                                                                                                                                                                                                                                                                                                  |
| **Byte Code File**         | The file containing the hex encoded smart contract byte code.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Proxy Account**          | The ID of the account to which this account is proxy staked. If proxyAccountID is null, or is an invalid account, or is an account that isn't a node, then this account is automatically proxy staked to a node chosen by the network, but without earning payments. If the proxyAccountID account refuses to accept proxy staking, or if it is not currently running a node, then it will behave as if proxyAccountID was null.                                     |
| **Auto Renew Period**      | The period that the instance will charge its account every this many seconds to renew.                                                                                                                                                                                                                                                                                                                                                                               |
| **Constructor Parameters** | The constructor parameters to pass.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **Memo**                   | The memo to be associated with this contract. (max 100 bytes)                                                                                                                                                                                                                                                                                                                                                                                                        |

## `ContractCreateFlow()`

The <mark style="color:purple;">`ContractCreateFlow()`</mark>streamlines the creation of a contract by taking the bytecode of the contract and creating the file on Hedera to store the bytecode for you.&#x20;

First, a <mark style="color:purple;">`FileCreateTransaction()`</mark> will be executed to create a file on Hedera to store the specified contract bytecode. Second, the <mark style="color:purple;">`ContractCreateTransaction()`</mark> will be executed to create the contract instance on Hedera.

The response will return the contract create transaction information like the new contract ID. You will not get the ID of the file that was created that stored your contract bytecode. If you would like to know the file ID of your contract bytecode, you can [create a file](../file-storage/create-a-file.md) and use the <mark style="color:purple;">`ContractCreateTransaction()`</mark> API directly.

| Constructor                | Description                                 |
| -------------------------- | ------------------------------------------- |
| `new ContractCreateFlow()` | Initializes the ContractCreateFlow() object |

### Methods

{% tabs %}
{% tab title="V2" %}
{% hint style="info" %}
**Note:** Please refer to [ContractCreateTransaction()](create-a-smart-contract.md#contractcreatetransaction) for a complete list of applicable methods.&#x20;
{% endhint %}

| Method                    | Type       |
| ------------------------- | ---------- |
| `setBytecode(<bytecode>)` | byte       |
| `setBytecode(<bytecode>)` | String     |
| `setBytecode(<bytecode>)` | ByteString |

{% code title="Java" %}
```java
//Create the transaction
ContractCreateFlow contractCreate = new ContractCreateFlow()
     .setBytecode(bytecode)
     .setGas(100_000);

//Sign the transaction with the client operator key and submit to a Hedera network
TransactionResponse txResponse = contractCreate.execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the new contract ID
ContractId newContractId = receipt.contractId;
        
System.out.println("The new contract ID is " +newContractId);
//SDK Version: v2.10.0-beta.1
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const contractCreate = new ContractCreateFlow()
    .setGas(100000)
    .setBytecode(bytecode);

//Sign the transaction with the client operator key and submit to a Hedera network
const txResponse = contractCreate.execute(client);

//Get the receipt of the transaction
const receipt = (await txResponse).getReceipt(client);

//Get the new contract ID
const newContractId = (await receipt).contractId;
        
console.log("The new contract ID is " +newContractId);
//SDK Version: v2.11.0-beta.1
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
contractCreate := hedera.NewContractCreateFlow().
		SetGas(100000).
		SetBytecode(byteCode)

//Sign the transaction with the client operator key and submit to a Hedera network
txResponse, err := contractCreate.Execute(client)
if err != nil {
		panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)
if err != nil {
		panic(err)
}

//Get the topic ID
newContractId := *receipt.ContractID

fmt.Printf("The new topic ID is %v\n", newContractId)
//SDK Version: 2.11.0-beta.1
```
{% endcode %}
{% endtab %}
{% endtabs %}

## `ContractCreateTransaction()`

Creates a smart contract instance using the file ID of the contract bytecode.

| Constructor                       | Description                                        |
| --------------------------------- | -------------------------------------------------- |
| `new ContractCreateTransaction()` | Initializes the ContractCreateTransaction() object |

### Methods

{% tabs %}
{% tab title="V2" %}
| Method                                              | Type                                           | Requirement |
| --------------------------------------------------- | ---------------------------------------------- | ----------- |
| `setGas(<gas>)`                                     | long                                           | Required    |
| `setBytecodeFileId(<fileId>)`                       | [FileId](../specialized-types.md#fileid)       | Required    |
| `setInitialBalance(<initialBalance>)`               | Hbar                                           | Optional    |
| `setAdminKey(<keys>)`                               | Key                                            | Optional    |
| `setProxyAccountId(<accountId>`)                    | [AccountId](../specialized-types.md#accountid) | Optional    |
| `setConstructorParameters(<constructorParameters>)` | byte \[ ]                                      | Optional    |
| `setConstructorParameters(<constructorParameters>)` | ContractFunctionParameters                     | Optional    |
| `setContractMemo(<memo>)`                           | String                                         | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`             | Duration                                       | Optional    |

{% code title="Java" %}
```java
//Create the transaction
ContractCreateTransaction transaction = new ContractCreateTransaction()
    .setGas(100_000)
    .setBytecodeFileId(bytecodeFileId);
    
//Modify the default max transaction fee (default: 1 hbar)
ContractCreateTransaction modifyTransactionFee = transaction.setMaxTransactionFee(new Hbar(16));

//Sign the transaction with the client operator key and submit to a Hedera network
TransactionResponse txResponse = modifyTransactionFee.execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txResponse.getReceipt(client);

//Get the new contract ID
ContractId newContractId = receipt.contractId;
        
System.out.println("The new contract ID is " +newContractId);
//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new ContractCreateTransaction()
    .setGas(100_000)
    .setBytecodeFileId(bytecodeFileId);

//Modify the default max transaction fee (default: 1 hbar)
const modifyTransactionFee = transaction.setMaxTransactionFee(new Hbar(16));

//Sign the transaction with the client operator key and submit to a Hedera network
const txResponse = await modifyTransactionFee.execute(client);

//Get the receipt of the transaction
const receipt = await txResponse.getReceipt(client);

//Get the new contract ID
const newContractId = receipt.contractId;
        
console.log("The new contract ID is " +newContractId);

```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewContractCreateTransaction().
		SetGas(100000).
		SetBytecodeFileID(byteCodeFileID)

//Sign the transaction with the client operator key and submit to a Hedera network
txResponse, err := transaction.Execute(client)
if err != nil {
		panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)
if err != nil {
		panic(err)
}

//Get the topic ID
newContractId := *receipt.ContractID

fmt.Printf("The new topic ID is %v\n", newContractId)
//v2.0.0
```
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method                                              | Type                                     | Requirement |
| --------------------------------------------------- | ---------------------------------------- | ----------- |
| `setGas(<gas>)`                                     | long                                     | Required    |
| `setBytecodeFileId(<fileId>)`                       | [FileId](../specialized-types.md#fileid) | Required    |
| `setInitialBalance(<initialBalance>)`               | long/Hbar                                | Optional    |
| `setAdminKey(<publicKeys>)`                         | Ed25519PublicKey                         | Optional    |
| `setProxyAccountId(<accountId>`)                    | AccountId                                | Optional    |
| `setConstructorParameters(<constructorParameters>)` | byte \[ ]                                | Optional    |
| `setConstructorParameters(<constructorParameters>)` | ContractFunctionParameters               | Optional    |
| `setContractMemo(<memo>)`                           | String                                   | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`             | Duration                                 | Optional    |

{% code title="Java" %}
```java
//Create the transaction
ContractCreateTransaction transaction = new ContractCreateTransaction()
     .setBytecodeFileId(fileId)
     .setGas(100_000_000)
     .setConstructorParams(new ContractFunctionParams().addString("hello from hedera!"));

//Sign with the client operator account private key, submit to a Hedera network        
TransactionId txId = transaction.execute(client);

//Get the receipt of the transaction
TransactionReceipt receipt = txId.getReceipt(client);

//Get the new contract ID
ContractId newContractId = receipt.getContractId();
        
System.out.println("The new contract ID is " + newContractId);

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = new ContractCreateTransaction()
     .setBytecodeFileId(fileId)
     .setGas(100_000_000)
     .setConstructorParams(new ContractFunctionParams().addString("hello from hedera!"));

//Sign with the client operator account private key, submit to a Hedera network        
const txId = await transaction.execute(client);

//Get the receipt of the transaction
const receipt = await txId.getReceipt(client);

//Get the new contract ID
const newContractId = receipt.getContractId();
        
System.out.println("The new contract ID is " + newContractId);

//v1.4.4

```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}
| Method                                              | Type       | Requirement |
| --------------------------------------------------- | ---------- | ----------- |
| `getAdminKey(<keys>)`                               | Key        | Optional    |
| `getGas(<gas>)`                                     | long       | Optional    |
| `getInitialBalance(<initialBalance>)`               | Hbar       | Optional    |
| `getBytecodeFileId(<fileId>)`                       | FileId     | Optional    |
| `getProxyAccountId(<accountId>`)                    | AccountId  | Optional    |
| `getConstructorParameters(<constructorParameters>)` | ByteString | Optional    |
| `getContractMemo(<memo>)`                           | String     | Optional    |
| `getAutoRenewPeriod(<autoRenewPeriod>)`             | Duration   | Optional    |

{% code title="Java" %}
```java
//Create the transaction
ContractCreateTransaction transaction = new ContractCreateTransaction()
    .setGas(500)
    .setBytecodeFileId(bytecodeFileId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new ContractCreateTransaction()
    .setGas(500)
    .setBytecodeFileId(bytecodeFileId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewContractCreateTransaction().
		SetGas(500).
		SetBytecodeFileID(byteCodeFileID).
		SetAdminKey(adminKey)

//Get admin key
transaction.GetAdminKey()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}



##
