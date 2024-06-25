# Set-up Your Local Network

While you are developing your application, you can use the Hedera supported networks (previewnet and testnet) to test your application against. In addition to using those networks, you have the option to set-up your own local consensus node and mirror node for testing purposes.\
\
With your local network set-up you can:

- Create and submit transactions and queries to a consensus node
- Interact with the mirror node via REST APIs

### 1. Set-up your local network

Set-up your local network by following the instructions found in the [readme](https://github.com/hashgraph/hedera-local-node#docker) of the `hedera-local-node` project. This will create a Hedera network composed of one consensus node and one mirror node. The consensus node will process incoming transactions and queries. The mirror node stores the history of transactions. Both nodes are created at startup.

### 2. Configure your network

Once you have your local network up and running, you will need to configure your Hedera client to point to your local network in your project of choice. Your project should have your language specific Hedera SDK as a dependency and imported into your project. You may reference the [environment set-up](../../../getting-started/environment-set-up.md) instructions if you don't know how.

Your local network IP address and port will be <mark style="color:purple;">`127.0.0.1:50211`</mark> and your local mirror node IP and port will be <mark style="color:purple;">`127.0.0.1:5600`</mark>. The consensus node account ID is <mark style="color:purple;">`0.0.3`</mark>. This is the node account ID that will receive your transaction and query requests. It is recommended to store these variables in an environment or config file. These values will be hard-coded in the example for demonstration purposes.

Configure your local network by using <mark style="color:purple;">`Client.forNetwork()`</mark>. This allows you to set a custom consensus network by providing the IP address and port. <mark style="color:purple;">`Client.setMirrorNetwork()`</mark> allows you to set a custom mirror node network by providing the IP address and port.

{% tabs %}
{% tab title="Java" %}

```java
//Create your local client
Client client = Client.forNetwork(Collections.singletonMap("127.0.0.1:50211", AccountId.fromString("0.0.3"))).setMirrorNetwork(List.of("127.0.0.1:5600"));
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create your local client
const node = {"127.0.0.1:50211": new AccountId(3)};
const client = Client.forNetwork(node).setMirrorNetwork("127.0.0.1:5600");
```

{% endtab %}

{% tab title="Go" %}

```go
//Create your local client
node := make(map[string]hedera.AccountID, 1)
node["127.0.0.1:50211"] = hedera.AccountID{Account: 3}

mirrorNode := []string{"127.0.0.1:5600"}

client := hedera.ClientForNetwork(node)
client.SetMirrorNetwork(mirrorNode)
```

{% endtab %}
{% endtabs %}

### 3. Set your local node transaction fee paying account

You will need and account ID and key to pay for the [fees](../../../networks/mainnet/fees/) associated with each transaction and query that is submitted to your local network. You will use the account ID and key provided by the local node on startup to set-up your operator account ID and key. The operator is the default account that pays for transaction and query fees.

| **Account ID**  | `0.0.2`                                                                                            |
| --------------- | -------------------------------------------------------------------------------------------------- |
| **Private Key** | `302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137` |

{% hint style="danger" %}
**Note**: It is not good practice to post your private keys in any public place. These keys are provided only for development and testing purposes only. They do not exist on any production networks.
{% endhint %}

{% tabs %}
{% tab title="Java" %}

```java
client.setOperator(AccountId.fromString("0.0.2"), PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"));
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
client.setOperator(AccountId.fromString("0.0.2"),PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"));
```

{% endtab %}

{% tab title="Go" %}

```go
accountId, err := hedera.AccountIDFromString("0.0.2")
privateKey, err := hedera.PrivateKeyFromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137")
client.SetOperator(accountId, privateKey)
```

{% endtab %}
{% endtabs %}

### 4. Submit your transaction

Submit a transaction that will create a new account in your local network. The console should print out the new account ID. In this example, we are using the same key as the transaction fee paying account as the key for the new account. You can also create a [new key](../../sdks/keys/generate-a-new-key-pair.md) if you wish.

{% tabs %}
{% tab title="Java" %}

```java
//Submit a transaction to your local node
TransactionResponse newAccount = new AccountCreateTransaction()
        .setKey(PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"))
        .setInitialBalance(new Hbar(1))
        .execute(client);
                
//Get the receipt
TransactionReceipt receipt = newAccount.getReceipt(client);
        
//Get the account ID
AccountId newAccountId = receipt.accountId;
System.out.println(newAccountId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Submit a transaction to your local node
const newAccount = await new AccountCreateTransaction()
        .setKey(PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"))
        .setInitialBalance(new Hbar(1))
        .execute(client);

//Get receipt
const receipt = await newAccount.getReceipt(client);

//Get the account ID
const newAccountId = receipt.accountId;
console.log(newAccountId);
```

{% endtab %}

{% tab title="Go" %}

```go
//Submit a transaction to your local node
newAccount, err := hedera.NewAccountCreateTransaction().
	SetKey(privateKey).
	SetInitialBalance(hedera.HbarFromTinybar(1000)).
	Execute(client)

if err != nil {
	println(err.Error(), ": error getting balance")
	return
}

//Get receipt
receipt, err := newAccount.GetReceipt(client)

//Get the account ID
newAccountId := receipt.AccountID
fmt.Print(newAccountId)
```

{% endtab %}
{% endtabs %}

### 5. View your transaction

You can view the executed transaction by querying your local mirror node.

The local mirror node endpoint URL is `http://localhost:5551/`.

You can view the transactions that were submitted to your local node by submitting this request:

```http
http://localhost:5551/api/v1/transactions
```

The list of supported mirror node REST APIs can be found [here](../../rest-api.md). You have now set-up your local environment. Check out the following links for more examples.

{% content-ref url="../../../tutorials/" %}
[tutorials](../../../tutorials/)
{% endcontent-ref %}

{% content-ref url="../../sdks/" %}
[sdks](../../sdks/)
{% endcontent-ref %}

### Code Check :white\_check\_mark:

{% tabs %}
{% tab title="Java" %}

```java
import com.hedera.hashgraph.sdk.*;
import java.io.IOException;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.TimeoutException;

public class LocalNode {
    public static void main(String[] args) throws TimeoutException, PrecheckStatusException, ReceiptStatusException, InterruptedException, IOException {

        //Create your local client
        Client client = Client.forNetwork(Collections.singletonMap("127.0.0.1:50211", AccountId.fromString("0.0.3"))).setMirrorNetwork(List.of("127.0.0.1:5600"));

        //Set the transaction fee paying account
        client.setOperator(AccountId.fromString("0.0.2"), PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"));
        
        //Submit a transaction to your local node
        TransactionResponse newAccount = new AccountCreateTransaction()
                .setKey(PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"))
                .setInitialBalance(new Hbar(1))
                .execute(client);
       
        //Get the receipt     
        TransactionReceipt receipt = newAccount.getReceipt(client);
        
        //Get the account ID
        AccountId newAccountId = receipt.accountId;
        System.out.println(newAccountId);
    }
}
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const {
    Client,
    PrivateKey,
    Hbar,
    AccountId,
    AccountCreateTransaction,
} = require("@hashgraph/sdk");

async function main() {

        //Create your local client
        const node = {"127.0.0.1:50211": new AccountId(3)}
        const client = Client.forNetwork(node).setMirrorNetwork("127.0.0.1:5600");

        //Set the transaction fee paying account
        client.setOperator(AccountId.fromString("0.0.2"),PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"));

        //Submit a transaction to your local node
        const newAccount = await new AccountCreateTransaction()
                .setKey(PrivateKey.fromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137"))
                .setInitialBalance(new Hbar(1))
                .execute(client);

        //Get receipt
        const receipt = await newAccount.getReceipt(client);

        //Get the account ID
        const newAccountId = receipt.accountId;
        console.log(newAccountId);
    }
void main();
```

{% endtab %}

{% tab title="Go" %}

```go
package main

import (
	"fmt"

	"github.com/hashgraph/hedera-sdk-go/v2"
)

func main() {
	//Create your local node client
	node := make(map[string]hedera.AccountID, 1)
	node["127.0.0.1:50211"] = hedera.AccountID{Account: 3}

	mirrorNode := []string{"127.0.0.1:5600"}

	client := hedera.ClientForNetwork(node)
	client.SetMirrorNetwork(mirrorNode)

	//Set the transaction fee paying account
	accountId, err := hedera.AccountIDFromString("0.0.2")
	privateKey, err := hedera.PrivateKeyFromString("302e020100300506032b65700422042091132178e72057a1d7528025956fe39b0b847f200ab59b2fdd367017f3087137")
	client.SetOperator(accountId, privateKey)

	//Submit a transaction to your local node
	newAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(privateKey).
		SetInitialBalance(hedera.HbarFromTinybar(1000)).
		Execute(client)

	if err != nil {
		println(err.Error(), ": error getting balance")
		return
	}

	//Get receipt
	receipt, err := newAccount.GetReceipt(client)

	//Get the account ID
	newAccountId := receipt.AccountID
	fmt.Print(newAccountId)
}
```

{% endtab %}
{% endtabs %}
