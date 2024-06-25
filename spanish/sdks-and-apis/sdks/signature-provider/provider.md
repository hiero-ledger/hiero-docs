# Provider

{% hint style="info" %}
This feature is available in the [Hedera JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) only. (version >=2.14.0).
{% endhint %}

`Provider` provides access to a Hedera network to which requests should be submitted to. The Hedera network can be specified to `previewnet`, `testnet`, or `mainnet`.

The most important method on the provider interface is the `call` method which allows a user to submit _any_ request and get the correct response for that request. For instance,Java

```javascript
// Balance of node account ID 0.0.3 
const balance = provider.call(new AccountBalanceQuery().setAccountId(AccountId.fromString("0.0.3")));Interface Provider
```

### **Methods**

<mark style="color:purple;">**`getLedgerId()`**</mark><mark style="color:purple;">**->**</mark>\*\* `LedgerId`\*\*

Returns the ledger ID of the current network.

<mark style="color:purple;">**`getNetwork()`**</mark><mark style="color:purple;">**->**</mark>\*\* `Map` < `[key: string]: (string | AccountId)`>\*\*

Returns the entire network map for the current network.

<mark style="color:purple;">**`getAccountBalance(`**</mark>**`accountId: AccountId | string`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise<AccountBalance>`\*\*

Get the balance for the specified account ID.

<mark style="color:purple;">**`getAccountInfo(`**</mark>**`accountId: AccountId | string`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise<AccountInfo>`\*\*

Get the info for the specified account ID.

<mark style="color:purple;">**`getAccountRecords(`**</mark>**`accountId: AccountId | string`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise<TransactionRecord[]>`\*\*

Get the account record for the specified account ID.

<mark style="color:purple;">**`getTransactionReceipt(`**</mark>**`transactionId:TransactionId`**<mark style="color:purple;">**`)`**</mark>**: `TransactionReceipt`**

Get a receipt for the specified transaction ID.

<mark style="color:purple;">**`waitForReceipt(`**</mark>**`response: TransactionResponse`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise<TransactionReceipt>`\*\*

Execute multiple `TransactionReceiptQuery`'s until we get an erroring status code, or a success state code.

<mark style="color:purple;">**`call(`**</mark>**`<RequestT, ResponseT, OutputT>(request: Executable<RequestT, ResponseT, OutputT>`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise <Output>`\*\*

Responsible for serializing your request and sending it over the wire to be executed, and then deserializing the response into the appropriate type.
