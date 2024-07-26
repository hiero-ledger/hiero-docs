# 创建帐户

## Summary

在本节中，您将学习如何创建一个简单的 Hedera 帐户。 Hedera 账户是您可以与 [Hedera API] (../sdks-and-apis/hedera-api/)交互的切入点。 账户持有用于支付各种交易和查询类型的 API 调用的HBAR 余额。

***

## 必备条件

- 完成 [Introduction](introduction.md) 步骤。
- 完成了[环境设置](环境-设置.md)。

***

## 第 1 步：导入模块

导入以下模块到您的代码文件。

{% tabs %}
{% tab title="Java" %}

```java
导入 com.hedera.hedera.hashgraph.sdk.AccountId;
import com.hedera.hedera.hederaPreCheckStatusException;
importer com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hedera.hasgraph.sdk.Client;
import com.hedera.hedera.hgraph.sdk.TransactionResponse;
import com.himport。 edera.hashgraph.sdk.PublicKey;
importer com.hedera.hashgraph.sdk.AccountCreateTransaction;
importer com.hedera.hashgraph.sdk.Hbar;
importer com.hedera.hasgraphs.sdk.AccountBalance;
importer com.hedera.hedera.hasgraph.sdk.AccountBalation;
importyo.github.cdimascio.dotenv.Dotenv;
import java.util.urt.TimeoutException;
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const Community, Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar } = require("@hashgraph/sdk");
require("dotenv").config();
```

{% endtab %}

{% tab title="Go" %}

```go
导入 (
    "fmt"
    "os"

    "github.com/hashgraph/hedera-sdk-go/v2"
    "github.com/joho/godotenv"
)
```

{% endtab %}
{% endtabs %}

***

## 第 2 步：为新账户生成密钥

生成一个私钥和公钥来关联您将要创建的帐户。

{% tabs %}
{% tab title="Java" %}

```java
//Create your Hedera Testnet client
//Client client = Client.forTestnet();
//client.setOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

// Generate a new key pair
PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//const client = Client.forTestnet();
//client.setOperator(myAccountId, myPrivateKey);
/------------------------------------------------------<enter code below>------------------------------

/create new key
const newPrivateKey = PrivateKey.generateED25519(); 
const newAccountPublicKey = newAccountPrivateKey.publicKey;
```

{% endtab %}

{% tab title="Go" %}

```go
//client := hedera.ClientForTestnet()
//client.SetOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

//Generate new keys for the account you will create
newAccountPrivateKey, err := hedera.PrivateKeyGenerateEd25519()

if err != nil {
  panic(err)
}

newAccountPublicKey := newAccountPrivateKey.PublicKey()
```

{% endtab %}
{% endtabs %}

***

## 第 3 步：创建一个新账户

使用 _`AccountCreateTransaction()` 创建一个新帐户。 使用前一步创建的公钥输入`setKey()`_ 字段。 这将把前一步生成的密钥对与新账户关联起来。 该帐户的公钥对公众来说是可见的，可以在镜像节点探索器中查看。 私钥用于授权与账户有关的交易，例如从该账户传输_HBAR_或令牌到另一账户。 该账户的初始余额为 _1,000tinybars_，由您由Hedera portal创建的测试网账户供资。

您可以通过获取事务 ID并在镜像节点探索器中搜索来查看提交到网络的交易。 事务ID由支付交易费用的帐户ID和有效的交易开始时间，例如_`0.0'。 234@16093448302`_<mark style="color:blue;">。</mark> 交易的有效开始时间是交易在网络上有效的时间。 SDK 自动为幕后每个交易生成一个事务ID。

{% tabs %}
{% tab title="Java" %}

```java
//Create new account and assign the public key
TransactionResponse newAccount = new AccountCreateTransaction()
     .setKey(newAccountPublicKey)
     .setInitialBalance(Hbar.fromTinybars(1000))
     .execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create a new account with 1,000 tinybar starting balance
const newAccount = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);
```

{% endtab %}

{% tab title="Go" %}

```go
//Create new account and assigning public key
newAccount, err := hedera.NewAccountCreateTransaction().
    SetKey(newAccountPublicKey).
    SetalBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
    Execute(client)
```

{% endtab %}
{% endtabs %}

## 第 4 步：获取新帐户 ID

新账户的 _account ID_ 将在收到创建该账户的交易时退回。 收据提供了有关交易的信息，如交易是否成功，以及是否创建了任何新的实体ID。 实体包括账户、智能合同、令牌、文件、主题和预定交易。 _account ID_ 是 x.y.z 格式，z是帐户号码。 上面的值 (x 和 y) 预设为零，分别表示碎片和范围数。 您新的 _account ID_ 应该产生类似_`0.0.1234`_的结果。

{% tabs %}
{% tab title="Java" %}

```java
// 获取新账户 ID
AccountId = newAccount.getreceipt(client).accountId;

//Log 帐户 ID
System.out.println("新帐户 ID 是：" +newAccountId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// 获取新账户 ID
const getReceipt = 等待newAccount.getreceipt(client);
const newAccountID = getReceipt. ccountId;

//Log 账户 ID
console.log("新帐户 ID 是"+newAccountId")；
```

{% endtab %}

{% tab title="Go" %}

```go
//request the receiving of the transaction
receivt, err := newAccount. etreceipt(客户端)
nil ●
    panic(err)
}

/get the new account ID from the receiving
newAccountId := *receive. ccountID

//Log 帐户 ID
fmt.Printf("新帐户 ID 是 %v\n", newAccountId)
```

{% endtab %}
{% endtabs %}

***

## 第 5 步：验证新账户余额

接下来，您将向Hedera测试网络提交查询，使用新账户ID_返回新账户的余额。 新账户的经常账户余额应该是1,000_tinybars_。 获取帐户余额今天是免费的。

{% tabs %}
{% tab title="Java" %}

```java
//检查新帐户余额
AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("新帐户余额: "+accountBalance.hbars);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
///校验帐户余额
const account BalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

console.log("The new account balance is "+accountBalance.hbars.toTinybars() +" tinybar.");
```

{% endtab %}

{% tab title="Go" %}

```go
//Create 账户余额查询
查询 := hedera.NewAccountBalanceQuery().
     设置帐户ID(newAccountId)

//Signing with client operators private key and submitting to a Hedera 网络
accountBalance, err := 查询。 xecute(client)
if err != nil 密切相关,
    panic(err)
}

/ 打印tinybars
fmt. rintln("新帐户的帐户余额是 ", accountBalance.Hbars.AsTinybar())
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
:star: **Congratulations! 您已成功完成以下任务:**

- 创建了一个新的 Hedera 帐户，初始余额为1 000个小巴。
- 通过请求收到交易来获取新的帐户 ID。
- 通过向网络提交查询来验证新账户的起始余额。

您现在准备好将一些HBAR 转移到新帐户 :money\_mouth: ！
{% endhint %}

***

## 代码检查:whit\_check\_mark：

<details>

<summary>Java</summary>

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;
import io.github.cdimascio.dotenv.Dotenv;
​
import java.util.concurrent.TimeoutException;
​
public class HederaExamples {
​
    public static void main(String[] args) throws TimeoutException, HederaPreCheckStatusException, HederaReceiptStatusException {​
        
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        
        //Create your Hedera Testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);
        
        // Set default max transaction fee & max query payment
        client.setDefaultMaxTransactionFee(new Hbar(100)); 
        client.setDefaultMaxQueryPayment(new Hbar(50)); 
        
        // Generate a new key pair
        PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
        PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();
​
        //Create new account and assign the public key
        TransactionResponse newAccount = new AccountCreateTransaction()
                .setKey(newAccountPublicKey)
                .setInitialBalance( Hbar.fromTinybars(1000))
                .execute(client);
​
        // Get the new account ID
        AccountId newAccountId = newAccount.getReceipt(client).accountId;
​
        System.out.println("\nNew account ID: " +newAccountId);
        
        //Check the new account's balance
        AccountBalance accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);
​
        System.out.println("New account balance: " +accountBalance.hbars);
​
    }
}
```

</details>

<details>

<summary>JavaScript</summary>

{% code title="index.js" %}

```javascript
const {
  Hbar,
  Client,
  PrivateKey,
  AccountBalanceQuery,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
require("dotenv").config();

async function environmentSetup() {
  // Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Environment variables myAccountId and myPrivateKey must be present"
    );
  }

  // Create your connection to the Hedera Network
  const client = Client.forTestnet();
  client.setOperator(myAccountId, myPrivateKey);

  //Set the default maximum transaction fee (in Hbar)
  client.setDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client.setDefaultMaxQueryPayment(new Hbar(50));

  // Create new keys
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey.publicKey;

  // Create a new account with 1,000 tinybar starting balance
  const newAccount = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);

  // Get the new account ID
  const getReceipt = await newAccount.getReceipt(client);
  const newAccountId = getReceipt.accountId;
  
  console.log("\nNew account ID: " + newAccountId);

  // Verify the account balance
  const accountBalance = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .execute(client);

  console.log(
    "The new account balance is: " +
      accountBalance.hbars.toTinybars() +
      " tinybar."
  );

  return newAccountId;
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Go</summary>

```go
package main

import (
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {

	//Loads the .env file and throws an error if it cannot load the variables from that file correctly
	err := godotenv.Load(".env")
	if err != nil {
		panic(fmt.Errorf("Unable to load environment variables from .env file. Error:\n%v\n", err))
	}

	//Grab your testnet account ID and private key from the .env file
	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os.Getenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	//Print your testnet account ID and private key to the console to make sure there was no error
	fmt.Printf("\nThe account ID is = %v\n", myAccountId)
	fmt.Printf("The private key is = %v", myPrivateKey)

	//Create your testnet client
	client := hedera.ClientForTestnet()
	client.SetOperator(myAccountId, myPrivateKey)

	// Set default max transaction fee & max query payment
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))

	//Generate new keys for the account you will create
	newAccountPrivateKey, err := hedera.PrivateKeyGenerateEd25519()
	if err != nil {
		panic(err)
	}

	newAccountPublicKey := newAccountPrivateKey.PublicKey()

	//Create new account and assign the public key
	newAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(newAccountPublicKey).
		SetInitialBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
		Execute(client)

	//Request the receipt of the transaction
	receipt, err := newAccount.GetReceipt(client)
	if err != nil {
		panic(err)
	}

	//Get the new account ID from the receipt
	newAccountId := *receipt.AccountID

	//Print the new account ID to the console
	fmt.Println("\n")
	fmt.Printf("New account ID: %v\n", newAccountId)

	//Create the account balance query
	query := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign with client operator private key and submit the query to a Hedera network
	accountBalance, err := query.Execute(client)
	if err != nil {
		panic(err)
	}

	//Print the balance of tinybars
	fmt.Println("New account balance for the new account is", accountBalance.Hbars.AsTinybar())
}

```

</details>

#### 示例输出：

```bash
新账户ID： 0.0.13724748
新账户余额： 1000 tinybar。
```

{% hint style="info" %}
有一个问题？ [在 StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
