# Local Provider

{% hint style="info" %}
This feature is available in the [Hedera JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) only. (version >=2.14.0).
{% endhint %}

LocalProvider is a quality of life implementation that creates a provider using the `HEDERA_NETWORK` environment variable.

The `LocalProvider()` requires the following variable to be defined in the `.env` file. The `.env` file is located in the root directory of the project.

- `HEDERA_NETWORK`
  - The network the wallet submits transactions to

{% code title=".env" %}

```
//Example .env file
HEDERA_NETWORK= previewnet/testnet/mainnet (select one network)
```

{% endcode %}

## class LocalProvider implements Wallet

### Constructor

#### new <mark style="color:purple;">LocalProvider</mark><mark style="color:purple;">`()`</mark>

Instantiates the LocalProvider object. The local provider is built using `HEDERA_NETWORK` network specified in the `.env` file.

### Methods

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getAccountBalance()`**</mark><mark style="color:purple;">\*\*</mark> -> Promise \<AccountBalance>\*\*

Returns the account balance of the account in the local wallet.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getAccountInfo()`**</mark><mark style="color:purple;">\*\*</mark> -> Promise \<AccountInfo>\*\*

Returns the account information of the account in the local wallet.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getAccountRecords()`**</mark><mark style="color:purple;">\*\*</mark> -> Promise \<AccountInfo>\*\*

Returns the last transaction records for this account using `TransactionRecordQuery`.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getLedgerId()`**</mark><mark style="color:purple;">**->**</mark>\*\* LedgerId\*\*

Returns the ledger ID (`previewnet`, `testnet`, or `mainnet`).

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getMirrorNetwork()`**</mark><mark style="color:purple;">**->**</mark>\*\* string\*\*

The mirror network the wallet is connected to.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getNetwork()`**</mark><mark style="color:purple;">**->**</mark>\*\* \[key: string]: string | <mark style="color:purple;">**\*\*\*\***</mark> AccountId\*\*

Returns the network map information.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`getTransactionReceipt()`**</mark><mark style="color:purple;">**->**</mark> Promise\<TransactionReceipt>

Returns the transaction receipt.

#### **`<LocalProvider>.`**<mark style="color:purple;">**`waitForReceipt()`**</mark><mark style="color:purple;">**->**</mark> Promise\<TransactionReceipt>

Wait for the receipt for a transaction response.

**`<LocalProvider>.`**<mark style="color:purple;">**`call(`**</mark>**`<RequestT, ResponseT, OutputT>(request: Executable<RequestT, ResponseT, OutputT>`**<mark style="color:purple;">**`)`**</mark><mark style="color:purple;">**->**</mark>\*\* `Promise <Output>`\*\*

Sign and send a request using the wallet.

#### Local Wallet Example:

```javascript
require("dotenv").config();
import { Wallet, LocalProvider } from "@hashgraph/sdk";

async function main() {

    const wallet = new Wallet(
        process.env.OPERATOR_ID,
        process.env.OPERATOR_KEY,
        new LocalProvider()
    );

    const info = await wallet.getAccountInfo();

    console.log(`info.key                          = ${info.key.toString()}`);
}

void main();
```

#### Sign with Local Wallet Example:

```javascript
require("dotenv").config();
const {Wallet, LocalProvider, TransferTransaction} = require("@hashgraph/sdk");

async function main() {

    const wallet = new Wallet(
        process.env.OPERATOR_ID,
        process.env.OPERATOR_KEY,
        new LocalProvider()
    );

    //Transfer 1 hbar from my wallet account to account 0.0.3
    const transaction = await new TransferTransaction()
        .addHbarTransfer(wallet.getAccountId(), -1)
        .addHbarTransfer("0.0.3", 1)
        .freezeWithSigner(wallet);
    
    //Sign the transaction
    const signTransaction = await transaction.signWithSigner(wallet);

    //Submit the transaction
    const response = await signTransaction.executeWithSigner(wallet);

    //Get the receipt
    const receipt = await wallet.getProvider().waitForReceipt(response);
    
    console.log(`status: ${receipt.status.toString()}`);
}

main();
```
