# Build your Hedera client

## 1. Configure your Hedera network

Build your client to interact with any of the Hedera network nodes. Mainnet, testnet, and previewnet are the three Hedera networks you can submit transactions and queries to.

{% tabs %}
{% tab title="V1" %}

| **Method**                     | **Type**                  | **Description**                                                 |
| ------------------------------ | ------------------------- | --------------------------------------------------------------- |
| `Client.forPreviewnet()`       |                           | Constructs a Hedera client pre-configured for Previewnet access |
| `Client.forTestnet()`          |                           | Constructs a Hedera client pre-configured for Testnet access    |
| `Client.forMainnet()`          |                           | Constructs a Hedera client pre-configured for Mainnet access    |
| `Client.fromFile(<file>)`      | File                      | Configures a client from a file                                 |
| `Client.fromFile(<fileName>)`  | String                    | Constructs a network from a file                                |
| `Client.fromJson(<json>)`      | String                    | Configure a client from the given JSON string                   |
| `Client.fromJson(<json>)`      | Reader                    | Configure a client from the given JSON reader                   |
| `Client.replaceNodes(<nodes>)` | Map\\<AccountId, String> | Replaces nodes in the network                                   |

{% code title="Java" %}

```java
// From a pre-configured network
Client client = Client.forTestnet();

//Replace nodes
Map<AccountId, String> nodes = new HashMap<>();
nodes.put(AccountId.fromString("0.0.10"), "34.94.106.61:50211" ,);

client.replaceNodes(nodes);

//v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
// From a pre-configured network
const client = Client.forTestnet();

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}

## 2. Define the operator account ID and private key

The operator is the account that will, by default, pay the transaction fee for transactions and queries built with this client. The operator account ID is used to generate the default transaction ID for all transactions executed with this client. The operator private key is used to sign all transactions executed by this client.

| Method                                                                         | Type                                                                                                                                                              |
| ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Client.<network>.setOperator(<accountId, privateKey>)`                        | AccountId, PrivateKey                                                                                                                                             |
| `Client.<network>.setOperatorWith(<accountId, privateKey, transactionSigner>)` | AccountId, PrivateKey, Function\\<byte\[ ], byte \[ ]> |

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

{% tabs %}
{% tab title="V1" %}

| Method                                  | Type      | Description                                                                                               |
| --------------------------------------- | --------- | --------------------------------------------------------------------------------------------------------- |
| `setMaxTransactionFee(<fee>)`           | Hbar/long | The maximum transaction fee the client is willing to pay. Default: 1 hbar |
| `setMaxQueryPayment(<maxQueryPayment>)` | Hbar/long | <p>The maximum query payment the client will pay.</p><p>Default: 1 hbar</p>                               |

{% code title="Java" %}

```java
 Client client = Client.forTestnet();

 client.setOperator(OPERATOR_ID, OPERATOR_KEY);

 client.setMaxTransactionFee(new Hbar(2));

 //v1.3.2
```

{% endcode %}

{% code title="JavaScript" %}

```javascript
// For test network (testnet)
const client = Client.forTestnet()

//Set the operator and operator private key
client.setOperator(OPERATOR_ID, OPERATOR_KEY);

//Set the max transaction fee the client is willing to pay to 2 hbars
client.setMaxTransactionFee(new Hbar(2)); 

//v1.4.4
```

{% endcode %}
{% endtab %}
{% endtabs %}
