# Build Your Hedera Client

## 1. Configure your Hedera Network

Build your client to interact with any of the Hedera network nodes. Mainnet, testnet, and previewnet are the three Hedera networks you can submit transactions and queries to.

For a predefined network (preview, testnet, and mainnet), the mirror node client is configured to the corresponding network mirror node. The default mainnet mirror node connection is to the [whitelisted mirror node](../../core-concepts/mirror-nodes/hedera-mirror-node.md#mainnet).\
\
To access the _**public mainnet mirror node**_, use `setMirrorNetwork()` and enter `mainnet-public.mirrornode.hedera.com:433` for the endpoint. The gRPC API requires TLS. The following SDK versions are compatible with TLS:

* **Java:** v2.3.0+
* **JavaScript**: v2.4.0+
* **Go:** v2.4.0+

<table data-header-hidden><thead><tr><th width="332"></th><th width="211.33333333333331"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Type</strong></td><td><strong>Description</strong></td></tr><tr><td><code>Client.forPreviewnet()</code></td><td></td><td>Constructs a Hedera client pre-configured for Previewnet access</td></tr><tr><td><code>Client.forTestnet()</code></td><td></td><td>Constructs a Hedera client pre-configured for Testnet access</td></tr><tr><td><code>Client.forMainnet()</code></td><td></td><td>Constructs a Hedera client pre-configured for Mainnet access</td></tr><tr><td><code>Client.forNetwork(&#x3C;network>)</code></td><td>Map&#x3C;String, AccountId></td><td>Construct a client given a set of nodes. It is the responsibility of the caller to ensure that all nodes in the map are part of the same Hedera network. Failure to do so will result in undefined behavior.</td></tr><tr><td><code>Client.fromConfig(&#x3C;json>)</code></td><td>String</td><td>Configure a client from the given JSON string describing a <a href="https://github.com/hashgraph/hedera-sdk-js/blob/3240cd5c1ddef1eab1b796d0a4b44f23c884621f/src/client/Client.js#L48-L53">ClientConfiguration object</a></td></tr><tr><td><code>Client.fromConfig(&#x3C;json>)</code></td><td>Reader</td><td>Configure a client from the given JSON reader</td></tr><tr><td><code>Client.fromConfigFile(&#x3C;file>)</code></td><td>File</td><td>Configure a client based on a JSON file.</td></tr><tr><td><code>Client.fromConfigFile(&#x3C;fileName>)</code></td><td>String</td><td>Configure a client based on a JSON file at the given path.</td></tr><tr><td><code>Client.forName(&#x3C;name>)</code></td><td>String</td><td>Provide the name of the network.<br><code>mainnet</code><br><code>testnet</code><br><code>previewnet</code></td></tr><tr><td><code>Client.&#x3C;network>.setMirrorNetwork(&#x3C;network>)</code></td><td>List&#x3C;String></td><td>Define a specific mirror network node(s) ip:port in string format</td></tr><tr><td><code>Client.&#x3C;network>.getMirrorNetwork()</code></td><td>List&#x3C;String></td><td>Return the mirror network node(s) ip:port in string format</td></tr><tr><td><code>Client.setTransportSecurity()</code></td><td>boolean</td><td>Set if transport security should be used. If transport security is enabled all connections to nodes will use TLS, and the server's certificate hash will be compared to the hash stored in the node address book for the given network.</td></tr><tr><td><code>Client.setNetworkUpdatePeriod()</code></td><td>Duration</td><td>Client automatically updates the network via a mirror node query at regular intervals. You can set the interval at which the address book is updated.</td></tr><tr><td><code>Client.setNetworkFromAddressBook(&#x3C;addressBook>)</code></td><td>AddressBook</td><td>Client can be set from a <code>NodeAddressBook</code>.</td></tr><tr><td><code>Client.setLedgerId(&#x3C;ledgerId>)</code></td><td>LedgerId</td><td>The ID of the network.<br><code>LedgerId.MAINNET</code><br><code>LedgerId.TESTNET</code><br><code>LedgerId.PREVIEWNET</code></td></tr><tr><td><code>Client.getLedgerId()</code></td><td>LedgerId</td><td>Get the ledger ID</td></tr><tr><td><code>Client.setVerifyCertificates()</code></td><td>boolean</td><td>Set if server certificates should be verified against an existing address book.</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
// From a pre-configured network
Client client = Client.forTestnet();

//For a specified network
Map<String, AccountId> nodes = new HashMap<>();
nodes.put("34.94.106.61:50211" ,AccountId.fromString("0.0.10"));

Client.forNetwork(nodes);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// From a pre-configured network
const client = Client.forTestnet();

//For a specified network
const nodes = {"34.94.106.61:50211": new AccountId(10)}
const client = Client.forNetwork(nodes);

//v2.0.7
```
{% endtab %}

{% tab title="Go" %}
```go
// From a pre-configured network
client := hedera.ClientForTestnet()

//For a specified network
node := map[string]AccountID{
    "34.94.106.61:50211": {Account: 10}
}

client := Client.forNetwork(nodes)

//v2.0.0
```
{% endtab %}
{% endtabs %}

## 2. Define the operator account ID and private key

The operator is the account that will, by default, pay the transaction fee for transactions and queries built with this client. The operator account ID is used to generate the default transaction ID for all transactions executed with this client. The operator private key is used to sign all transactions executed by this client.

| Method                                                                         | Type                                                  |
| ------------------------------------------------------------------------------ | ----------------------------------------------------- |
| `Client.<network>.setOperator(<accountId, privateKey>)`                        | AccountId, PrivateKey                                 |
| `Client.<network>.setOperatorWith(<accountId, privateKey, transactionSigner>)` | AccountId, PrivateKey, Function\<byte\[ ], byte \[ ]> |

### From an account ID and private key

{% tabs %}
{% tab title="Java" %}
```java
// Operator account ID and private key from string value
AccountId MY_ACCOUNT_ID = AccountId.fromString("0.0.96928");
Ed25519PrivateKey MY_PRIVATE_KEY = PrivateKey.fromString("302e020100300506032b657004220420b9c3ebac81a72aafa5490cc78111643d016d311e60869436fbb91c7330796928");

// Pre-configured client for test network (testnet)
Client client = Client.forTestnet()

//Set the operator with the account ID and private key
client.setOperator(MY_ACCOUNT_ID, MY_PRIVATE_KEY);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Your account ID and private key from string value
const MY_ACCOUNT_ID = AccountId.fromString("0.0.96928");
const MY_PRIVATE_KEY = PrivateKey.fromString("302e020100300506032b657004220420b9c3ebac81a72aafa5490cc78111643d016d311e60869436fbb91c7330796928");

// Pre-configured client for test network (testnet)
const client = Client.forTestnet()

//Set the operator with the account ID and private key
client.setOperator(MY_ACCOUNT_ID, MY_PRIVATE_KEY);
```
{% endtab %}

{% tab title="Go" %}
```go
// Operator account ID and private key from string value
operatorAccountID, err := hedera.AccountIDFromString("0.0.96928")
if err != nil {
    panic(err)
}

operatorKey, err := hedera.PrivateKeyFromString("302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de33596928")
if err != nil {
    panic(err)
}

// Pre-configured client for test network (testnet)
client := hedera.ClientForTestnet()

//Set the operator with the operator ID and operator key
client.SetOperator(operatorAccountID, operatorKey)
```
{% endtab %}
{% endtabs %}

### From a .env file

The .env file is created in the root directory of the SDK. The `.env` file stores account ID and the associated private key information to reference throughout your code. You will need to import the relevant dotenv module to your project files. The sample .env file may look something like this:

**.env**

```
MY_ACCOUNT_ID=0.0.941
MY_PRIVATE_KEY=302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5e
```

{% tabs %}
{% tab title="Java" %}
```java
//Grab the account ID and private key of the operator account from the .env file
AccountId MY_ACCOUNT_ID = AccountId.fromString(Objects.requireNonNull(Dotenv.load().get("OPERATOR_ID")));
Ed25519PrivateKey MY_PRIVATE_KEY = Ed25519PrivateKey.fromString(Objects.requireNonNull(Dotenv.load().get("OPERATOR_KEY")));

// Pre-configured client for test network (testnet)
Client client = Client.forTestnet()

//Set the operator with the account ID and private key
client.setOperator(MY_ACCOUNT_ID, MY_PRIVATE_KEY);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Grab the account ID and private key of the operator account from the .env file
const myAccountId = process.env.MY_ACCOUNT_ID;
const myPrivateKey = process.env.MY_PRIVATE_KEY;

// Pre-configured client for test network (testnet)
const client = Client.forTestnet()

//Set the operator with the account ID and private key
client.setOperator(myAccountId, myPrivateKey);
```
{% endtab %}

{% tab title="Go" %}
```go
    err := godotenv.Load(".env")
    if err != nil {
        panic(fmt.Errorf("Unable to load environment variables from demo.env file. Error:\n%v\n", err))
    }

    //Get the operator account ID and private key
    MY_ACCOUNT_ID := os.Getenv("MY_ACCOUNT_ID")
    MY_PRIVATE_KEY := os.Getenv("MY_PRIVATE_KEY")


    myAccountID, err := hedera.AccountIDFromString(MY_ACCOUNT_ID)
    if err != nil {
        panic(err)
    }

    myPrivateKey, err := hedera.PrivateKeyFromString(MY_PRIVATE_KEY)
    if err != nil {
        panic(err)
    }
```
{% endtab %}
{% endtabs %}

## 3. Additional client modifications

{% hint style="warning" %}
The **max transaction fee** and **max query payment** are both set to 100\_000\_000 tinybar (1 HBAR). This amount can be modified by using **`setDefaultMaxTransactionFee()`**&#x61;nd **`setDefaultMaxQueryPayment()`**`.`
{% endhint %}

<table><thead><tr><th width="368.33333333333326">Method</th><th width="131">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>Client.&#x3C;network>.SetDefaultRegenerateTransactionId(&#x3C;regenerateTransactionId>)</code></td><td>boolean</td><td>Whether or not to regenerate the transaction IDs</td></tr><tr><td><code>Client.&#x3C;network>.getDefaultRegenerateTransactionId(&#x3C;regenerateTransactionId>)</code></td><td>boolean</td><td>Get the default regenerate transaction ID boolean value</td></tr><tr><td><code>Client.&#x3C;network>.setDefaultMaxTransactionFee(&#x3C;fee>)</code></td><td>Hbar</td><td>The maximum transaction fee the client is willing to pay</td></tr><tr><td><code>Client.&#x3C;network>.getDefaultMaxTransactionFee()</code></td><td>Hbar</td><td>Get the default max transaction fee that is set</td></tr><tr><td><code>Client&#x3C;network>.setDefaultMaxQueryPayment(&#x3C;maxQueryPayment>)</code></td><td>Hbar</td><td><p>The maximum query payment the client will pay.</p><p>Default: 1 hbar</p></td></tr><tr><td><code>Client&#x3C;network>.getDefaultMaxQueryPayment()</code></td><td>Hbar</td><td>Get the default max query payment</td></tr><tr><td><code>Client.&#x3C;network>.setNetwork(&#x3C;nodes>)</code></td><td>Map&#x3C;String, AccountId></td><td>Replace all nodes in this Client with a new set of nodes (e.g. for an Address Book update)<br><br></td></tr><tr><td><code>Client.&#x3C;network>.getNetwork()</code></td><td>Map&#x3C;String, AccountId></td><td>Get the network nodes</td></tr><tr><td><code>Client.&#x3C;network>.setRequestTimeout(&#x3C;requestTimeout>)</code></td><td>Duration</td><td>The period of time a transaction or query request will retry from a "busy" network response</td></tr><tr><td><code>Client.&#x3C;network>.getRequestTimeout()</code></td><td>Duration</td><td>Get the period of time a transaction or query request will retry from a "busy" network response</td></tr><tr><td><code>Client.&#x3C;network>.setMinBackoff(&#x3C;minBackoff>)</code></td><td>Duration</td><td>The minimum amount of time to wait between retries. When retrying, the delay will start at this time and increase exponentially until it reaches the maxBackoff</td></tr><tr><td><code>Client.&#x3C;network>.getMinBackoff()</code></td><td>Duration</td><td>Get the minimum amount of time to wait between retries</td></tr><tr><td><code>Client.&#x3C;network>.setMaxBackoff(&#x3C;maxBackoff>)</code></td><td>Duration</td><td>The maximum amount of time to wait between retries. Every retry attempt will increase the wait time exponentially until it reaches this time.</td></tr><tr><td><code>Client.&#x3C;network>.getMaxBackoff()</code></td><td>Duration</td><td>Get the maximum amount of time to wait between retries</td></tr><tr><td><code>Client.&#x3C;network>.setAutoValidateChecksums(&#x3C;value>)</code></td><td>boolean</td><td>Validate checksums</td></tr><tr><td><code>Client.&#x3C;network>.setCloseTimeout(&#x3C;closeTimeout>)</code></td><td>Duration</td><td>Timeout for closing either a single node when setting a new network, or closing the entire network</td></tr><tr><td><code>Client.&#x3C;network>.setMaxNodeAttempts(&#x3C;nodeAttempts>)</code></td><td>int</td><td>Set the max number of times a node can return a bad gRPC status before we remove it from the list</td></tr><tr><td><code>Client.&#x3C;network>.getMaxNodeAttempts()</code></td><td>int</td><td>Get the max node attempts set</td></tr><tr><td><code>Client.&#x3C;network>.setMinNodeReadmitTime(&#x3C;readmitTime>)</code></td><td>Duration</td><td>The min time to wait before attempting to readmit nodes</td></tr><tr><td><code>Client.&#x3C;network>.getMinNodeReadmitTime()</code></td><td>Duration</td><td>Get the minimum node readmit time</td></tr><tr><td><code>Client.&#x3C;network>.setMaxNodeReadmitTime(&#x3C;readmitTime>)</code></td><td>Duration</td><td>The max time to wait before attempting to readmit nodes</td></tr><tr><td><code>Client.&#x3C;network>.getMaxNodeReadmitTime()</code></td><td>Duration</td><td>Get the max node readmit time</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
// For test network (testnet)
Client client = Client.forTestnet()

//Set your account as the client's operator
client.setOperator(myAccountId, myPrivateKey);

//Set the default maximum transaction fee (in Hbar)
client.setDefaultMaxTransactionFee(new Hbar(10));

//Set the maximum payment for queries (in Hbar)
client.setDefaultMaxQueryPayment(new Hbar(5));

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
{% code title="JavaScript" %}
```java
// For test network (testnet)
const client = Client.forTestnet()

//Set your account as the client's operator
client.setOperator(myAccountId, myPrivateKey);

//Set the default maximum transaction fee (in Hbar)
client.setDefaultMaxTransactionFee(new Hbar(10));

//Set the maximum payment for queries (in Hbar)
client.setDefaultMaxQueryPayment(new Hbar(5));

//v2.0.0
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```go
// For test network (testnet)
client := hedera.ClientForTestnet()

//Set your account as the client's operator
client.SetOperator(myAccountId, myPrivateKey)

// Set default max transaction fee
client.SetDefaultMaxTransactionFee(hedera.HbarFrom(10, hedera.HbarUnits.Hbar))

// Set max query payment
client.setDefaultMaxQueryPayment(hedera.HbarFrom(5, hedera.HbarUnits.Hbar))

//v2.0.0
```
{% endtab %}
{% endtabs %}
