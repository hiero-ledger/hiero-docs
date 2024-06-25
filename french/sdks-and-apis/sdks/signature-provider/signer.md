# Signer

{% hint style="info" %}
This feature is available in the [Hedera JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) only. (version >=2.14.0).
{% endhint %}

The **Signer** class an interface and is responsible for signing requests.

## Interface Signer

### **Methods**

<mark style="color:purple;">**`getLedgerId()`**</mark><mark style="color:purple;">**->**</mark>\*\* `LedgerId`\*\*

Returns the ledger ID.

<mark style="color:purple;">**`getAccountId()`**</mark><mark style="color:purple;">**->**</mark>**`AccountId`**

Returns the account ID associated with this signer.

<mark style="color:purple;">**`getAccountKey()`**</mark><mark style="color:purple;">**->**</mark>**`Key`**

Returns the account key.

<mark style="color:purple;">**`getNetwork()`**</mark><mark style="color:purple;">**->**</mark>**\[`Key:string]: string`**

Returns the account key.

<mark style="color:purple;">**`sign (`**</mark>**`messages; Uint8Array[]`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark> **`Promise<SignerSignature[]>`**

Sign an arbitrary list of messages.

<mark style="color:purple;">**`getAccountBalance()`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise<AccountBalance>`\*\*

Returns the account balance.

<mark style="color:purple;">**`getAccountInfo`**</mark><mark style="color:purple;">**()->**</mark>\*\* `Promise<AccountInfo>`\*\*

Returns the account info.

<mark style="color:purple;">**`getAccountRecords`**</mark><mark style="color:purple;">**()->**</mark>\*\* `Promise<TransactionRecord[]>`\*\*

Fetch the account record for the signer's account ID.

<mark style="color:purple;">**signTransaction \<T extends Transaction>**</mark>**`(transaction:T` )->**\*\* `Promise<T>`\*\*

Signs a transaction, returning the signed transaction

**NOTE**: This method is allowed to mutate the parameter being passed in so the returned transaction is not guaranteed to be a new instance of the transaction.

<mark style="color:purple;">**`checkTransaction<T extends Transaction>`**</mark><mark style="color:purple;">\*\*</mark> ( `transaction`: `T` )-> `Promise<T>`\*\*

Check whether all the required fields are set appropriately. Fields such as the transaction ID's account ID should either be `null` or be equal to the signer's account ID, and the node account IDs on the request should exist within the signer's network.

<mark style="color:purple;">**`populateTransaction<T extends Transaction>`**</mark><mark style="color:purple;">\*\*</mark> ( `transaction`: `T` )-> `Promise<T>`\*\*

Populate the requests with the required fields. The transaction ID should be constructed from the signer's account ID, and the node account IDs should be set using the signer's network.

<mark style="color:purple;">**`call(`**</mark>**`<RequestT, ResponseT, OutputT>(request: Executable<RequestT, ResponseT, OutputT>`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise <Output>`\*\*

Responsible for serializing your request and sending it over the wire to be executed, and then deserializing the response into the appropriate type.

Note: This is a wrapper around the `Provder.call()` method.
