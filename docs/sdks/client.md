# Build your Hedera client

## 1. Configure your Hedera network

Build your client to interact with any of the Hedera network nodes. Mainnet, testnet, and previewnet are the three Hedera networks you can submit transactions and queries to.

For a predefined network (preview, testnet, and mainnet), the mirror node client is configured to the corresponding network mirror node. The default mainnet mirror node connection is to the [whitelisted mirror node](../../mirrornet/hedera-mirror-node.md#mainnet).\
\
To access the _**public mainnet mirror node**_, use `setMirrorNetwork()` and enter `mainnet-public.mirrornode.hedera.com:433` for the endpoint. The gRPC API requires TLS. The following SDK versions are compatible with TLS:

* **Java:** v2.3.0+
* **JavaScript**: v2.4.0+
* **Go:** v2.4.0+

| **Method**                                        | **Type**                | **Description**                                                                                                                                                                                                                         |
| ------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Client.forPreviewnet()`                          |                         | Constructs a Hedera client pre-configured for Previewnet access                                                                                                                                                                         |
| `Client.forTestnet()`                             |                         | Constructs a Hedera client pre-configured for Testnet access                                                                                                                                                                            |
| `Client.forMainnet()`                             |                         | Constructs a Hedera client pre-configured for Mainnet access                                                                                                                                                                            |
| `Client.forNetwork(<network>)`                    | Map\<String, AccountId> | Construct a client given a set of nodes. It is the responsibility of the caller to ensure that all nodes in the map are part of the same Hedera network. Failure to do so will result in undefined behavior.                            |
| `Client.fromConfig(<json>)`                       | String                  | Configure a client from the given JSON string describing a [ClientConfiguration object](https://github.com/hashgraph/hedera-sdk-js/blob/3240cd5c1ddef1eab1b796d0a4b44f23c884621f/src/client/Client.js#L48-L53)                          |
| `Client.fromConfig(<json>)`                       | Reader                  | Configure a client from the given JSON reader                                                                                                                                                                                           |
| `Client.fromConfigFile(<file>)`                   | File                    | Configure a client based on a JSON file.                                                                                                                                                                                                |
| `Client.fromConfigFile(<fileName>)`               | String                  | Configure a client based on a JSON file at the given path.                                                                                                                                                                              |
| `Client.forName(<name>)`                          | String                  | <p>Provide the name of the network.<br><code>mainnet</code><br><code>testnet</code><br><code>previewnet</code></p>                                                                                                                      |
| `Client.<network>.setMirrorNetwork(<network>)`    | List\<String>           | Define a specific mirror network node(s) ip:port in string format                                                                                                                                                                       |
| `Client.<network>.getMirrorNetwork()`             | List\<String>           | Return the mirror network node(s) ip:port in string format                                                                                                                                                                              |
| `Client.setTransportSecurity()`                   | boolean                 | Set if transport security should be used. If transport security is enabled all connections to nodes will use TLS, and the server's certificate hash will be compared to the hash stored in the node address book for the given network. |
| `Client.setNetworkUpdatePeriod()`                 | Duration                | Client automatically updates the network via a mirror node query at regular intervals. You can set the interval at which the address book is updated.                                                                                   |
| `Client.setNetworkFromAddressBook(<addressBook>)` | AddressBook             | Client can be set from a `NodeAddressBook`.                                                                                                                                                                                             |
| `Client.setLedgerId(<ledgerId>)`                  | LedgerId                | <p>The ID of the network.<br><code>LedgerId.MAINNET</code><br><code>LedgerId.TESTNET</code><br><code>LedgerId.PREVIEWNET</code></p>                                                                                                     |
| `Client.getLedgerId()`                            | LedgerId                | Get the ledger ID                                                                                                                                                                                                                       |
| `Client.setVerifyCertificates()`                  | boolean                 | Set if server certificates should be verified against an existing address book.                                                                                                                                                         |

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
AccountId OPERATOR_ID = AccountId.fromString("0.0.96928");
Ed25519PrivateKey OPERATOR_KEY = PrivateKey.fromString("302e020100300506032b657004220420b9c3ebac81a72aafa5490cc78111643d016d311e60869436fbb91c7330796928");

// Pre-configured client for test network (testnet)
Client client = Client.forTestnet()

//Set the operator with the operator ID and operator key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
// Operator account ID and private key from string value
const OPERATOR_ID = AccountId.fromString("0.0.96928");
const OPERATOR_KEY = PrivateKey.fromString("302e020100300506032b657004220420b9c3ebac81a72aafa5490cc78111643d016d311e60869436fbb91c7330796928");

// Pre-configured client for test network (testnet)
const client = Client.forTestnet()

//Set the operator with the operator ID and operator key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);
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

The .env file is created in the root directory of the SDK. The .env file stores account ID and the associated private key information to reference throughout your code. You will need to import the relevant dotenv module to your project files. The sample .env file may look something like this:

{% code title=".env" %}
```
OPERATOR_ID= 0.0.9410
OPERATOR_KEY= 302e020100300506032b65700422042012a4a4add3d885bd61d7ce5cff88c5ef2d510651add00a7f64cb90de3359bc5e
```
{% endcode %}

{% tabs %}
{% tab title="Java" %}
```java
//Grab the account ID and private key of the operator account from the .env file
AccountId OPERATOR_ID = AccountId.fromString(Objects.requireNonNull(Dotenv.load().get("OPERATOR_ID")));
Ed25519PrivateKey OPERATOR_KEY = Ed25519PrivateKey.fromString(Objects.requireNonNull(Dotenv.load().get("OPERATOR_KEY")));

// Pre-configured client for test network (testnet)
Client client = Client.forTestnet()

//Set the operator with the operator ID and operator key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Grab the account ID and private key of the operator account from the .env file
const operatorAccount = process.env.OPERATOR_ID;
const operatorPrivateKey = process.env.OPERATOR_KEY;

// Pre-configured client for test network (testnet)
const client = Client.forTestnet()

//Set the operator with the operator ID and operator key
client.setOperator(operatorAccount, operatorPrivateKey );
```
{% endtab %}

{% tab title="Go" %}
```go
    err := godotenv.Load(".env")
    if err != nil {
        panic(fmt.Errorf("Unable to load environment variables from demo.env file. Error:\n%v\n", err))
    }

    //Get the operator ID and operator key
    OPERATOR_ID := os.Getenv("OPERATOR_ID")
    OPERATOR_KEY := os.Getenv("OPERATOR_KEY")


    operatorAccountID, err := hedera.AccountIDFromString(OPERATOR_ID)
    if err != nil {
        panic(err)
    }

    operatorKey, err := hedera.PrivateKeyFromString(OPERATOR_KEY)
    if err != nil {
        panic(err)
    }
```
{% endtab %}
{% endtabs %}

## 3. Additional client modifications

{% hint style="warning" %}
The **max transaction fee** and **max query payment** are both set to 100\_000\_000 tinybar (1 hbar). This amount can be modified by using `setDefaultMaxTransactionFee()`and `setDefaultMaxQueryPayment().`
{% endhint %}

| Method                                                                          | Type                    | Description                                                                                                                                                     |
| ------------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Client.<network>.SetDefaultRegenerateTransactionId(<regenerateTransactionId>)` | boolean                 | Whether or not to regenerate the transaction IDs                                                                                                                |
| `Client.<network>.getDefaultRegenerateTransactionId(<regenerateTransactionId>)` | boolean                 | Get the default regenerate transaction ID boolean value                                                                                                         |
| `Client.<network>.setDefaultMaxTransactionFee(<fee>)`                           | Hbar                    | The maximum transaction fee the client is willing to pay                                                                                                        |
| `Client.<network>.getDefaultMaxTransactionFee()`                                | Hbar                    | Get the default max transaction fee that is set                                                                                                                 |
| `Client<network>.setDefaultMaxQueryPayment(<maxQueryPayment>)`                  | Hbar                    | <p>The maximum query payment the client will pay.</p><p>Default: 1 hbar</p>                                                                                     |
| `Client<network>.getDefaultMaxQueryPayment()`                                   | Hbar                    | Get the default max query payment                                                                                                                               |
| `Client.<network>.setNetwork(<nodes>)`                                          | Map\<String, AccountId> | <p>Replace all nodes in this Client with a new set of nodes (e.g. for an Address Book update)<br><br></p>                                                       |
| `Client.<network>.getNetwork()`                                                 | Map\<String, AccountId> | Get the network nodes                                                                                                                                           |
| `Client.<network>.setRequestTimeout(<requestTimeout>)`                          | Duration                | The period of time a transaction or query request will retry from a "busy" network response                                                                     |
| `Client.<network>.getRequestTimeout()`                                          | Duration                | Get the period of time a transaction or query request will retry from a "busy" network response                                                                 |
| `Client.<network>.setMinBackoff(<minBackoff>)`                                  | Duration                | The minimum amount of time to wait between retries. When retrying, the delay will start at this time and increase exponentially until it reaches the maxBackoff |
| `Client.<network>.getMinBackoff()`                                              | Duration                | Get the minimum amount of time to wait between retries                                                                                                          |
| `Client.<network>.setMaxBackoff(<maxBackoff>)`                                  | Duration                | The maximum amount of time to wait between retries. Every retry attempt will increase the wait time exponentially until it reaches this time                    |
| `Client.<network>.getMaxBackoff()`                                              | Duration                | Get the maximum amount of time to wait between retries                                                                                                          |
| `Client.<network>.setAutoValidateChecksums(<value>)`                            | boolean                 | Validate checksums                                                                                                                                              |
| `Client.<network>.setCloseTimeout(<closeTimeout>)`                              | Duration                | Timeout for closing either a single node when setting a new network, or closing the entire network                                                              |
| `Client.<network>.setMaxNodeAttempts(<nodeAttempts>)`                           | int                     | Limit for how many Set the max number of times a node can return a bad gRPC status before we remove it from the list                                            |
| `Client.<network>.getMaxNodeAttempts()`                                         | int                     | Get the max node attempts set                                                                                                                                   |
| `Client.<network>.setMinNodeReadmitTime(<readmitTime>)`                         | Duration                | The min time to wait before attempting to readmit nodes                                                                                                         |
| `Client.<network>.getMinNodeReadmitTime()`                                      | Duration                | Get the minimum node readmit time                                                                                                                               |
| `Client.<network>.setMaxNodeReadmitTime(<readmitTime>)`                         | Duration                | The max time to wait before attempting to readmit nodes                                                                                                         |
| `Client.<network>.getMaxNodeReadmitTime()`                                      | Duration                | Get the max node readmit time                                                                                                                                   |

{% tabs %}
{% tab title="Java" %}
```java
// For test network (testnet)
Client client = Client.forTestnet()

//Set the operator and operator private key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);

//Set the max transaction fee the client is willing to pay to 2 hbars
client.setMaxTransactionFee(new Hbar(2)); 

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
{% code title="JavaScript" %}
```java
// For test network (testnet)
const client = Client.forTestnet()

//Set the operator and operator private key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);

//Set the max transaction fee the client is willing to pay to 2 hbars
client.setMaxTransactionFee(new Hbar(2));

//v2.0.0
```
{% endcode %}
{% endtab %}

{% tab title="Go" %}
```go
// For test network (testnet)
client := hedera.ClientForTestnet()

//Set the operator and operator private key
client.SetOperator(operatorAccountID, operatorKey)

//Set the max transaction fee the client is willing to pay to 2 hbars
client.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//v2.0.0
```
{% endtab %}
{% endtabs %}
