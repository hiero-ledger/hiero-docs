# 查询账单数据

## Summary

在本节中，我们将引导您查询您的账户余额。 允许您检索关于您新Hedera账户可用资金的最新信息。

## Prerequisites <a href="#pre-requisites" id="pre-requisites"></a>

- 完成 [Introduction](introduction.md) 步骤。
- 完成了[环境设置](环境-设置.md)。
- 完成了[创建一个帐户](创建一个帐户。md)
- 完成[Transfer HBAR ](transfer-hbar.md)步骤。

{% hint style="info" %}
_**注意:** 您可以随时检查"_[_Code检查:check_mark_buton:_](query-data)。 d#code-check) _" 部分在每个页面底部以查看整个代码，如果你遇到了问题。 您还可以将您的问题发布到我们Discord社区的SDK频道_ [_here_](http://hedera. om/discord) _或 GitHub 仓库_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## 查询帐户余额

### **获取请求查询的成本**

您可以在将查询提交给Hedera网络之前请求查询的费用。 今天检查帐户余额是免费的。 您可以通过下面的方法验证这一点。

{% tabs %}
{% tab title="Java" %}

```java
//request the cost of the queryCost
Hbar queryCost = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

System.out.println("此查询的成本: " +queryCost);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//request the cost
const queryCost = require new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

console.log("查询的成本: " +queryCost);
```

{% endtab %}

{% tab title="Go" %}

```java
//Create the query that you want to submit
balanceQuery := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Get the cost of the query
cost, err := balanceQuery.GetCost(client)

if err != nil {
        panic(err)
}

println("The account balance query cost is:", cost.String())
```

{% endtab %}
{% endtabs %}

### **获取账户余额**

您将通过请求获取账户余额查询来验证新账户余额已更新。 经常账户余额应为初始余额(1,000_**tinybars**_)加上转账金额(1,000**tinybars**)，等于2,000**tinybars**。

{% tabs %}
{% tab title="Java" %}

```java
//Check the new account's balance
AccountBalance accountBalanceNew = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("The account balance after the transfer: " +accountBalanceNew.hbars);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//检查新账户余额
const getNewBalance = 等待新账户余额()
     etAccountId(newAccountId)
     xecute(client);

console.log("传输后的帐户余额: " +getNewBalance.hbars.toTinybars() +" tinybar.")
```

{% endtab %}

{% tab title="Go" %}

```go
//检查新账户余额
newAccountBalancequer:= hedera.NewAccountBalanceQuery().
     设置帐户ID(newAccountId)

//Signing with client operators private key and submitter to a Hedera 网络
newAccountBalance, err := newAccountBalance查询. xecute(client)
if err != nil 密切相关,
    panic(err)
}

/ 打印tinybars
fmt. rintln("此帐户的 hbar 帐户余额", newAccountBalance.Hbars.AsTinybar())
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
:star: **Congratulations! 您已成功将 **_**HBAR**_\*\* 转到Hedera Testnet上的另一个帐户！ 如果你从一开始就学习了这个教程，那么到目前为止你已经完成了以下课程：\*\*&#x20

- 设置您的 Hedera 环境来提交交易和查询。
- 创建了一个帐户。
- 已将 **HBAR** 转到另一个账户。

您想要继续学习吗？ 访问我们的 [SDK 和 APIs] (../sdks-andapis/) 部分来将您的学习体验提升到下一关。 您也可以找到额外的 Java SDK 示例 [here](https://github.com/hashgraph/hedera-sdk-java/tree/main/examples/src/main/java)。
{% endhint %}

***

## 代码检查 :check_mark_buton：

您完整的代码文件应该看起来像这样：

<details>

<summary>Java</summary>

{% code fullWidth="true" %}

```java
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.Client;
import io.github.cdimascio.dotenv.Dotenv;
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.AccountBalance;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.ReceiptStatusException;
import com.hedera.hashgraph.sdk.PrecheckStatusException;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;

import java.util.concurrent.TimeoutException;

public class HederaExamples {

        public static void main(String[] args)
                        throws TimeoutException, PrecheckStatusException, ReceiptStatusException {

                // Grab your Hedera testnet account ID and private key
                AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
                PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));

                // Create your connection to the Hedera network
                Client client = Client.forTestnet();

                // Set your account as the client's operator
                client.setOperator(myAccountId, myPrivateKey);

                // Set default max transaction fee & max query payment
                client.setDefaultMaxTransactionFee(new Hbar(100));
                client.setDefaultMaxQueryPayment(new Hbar(50));

                // Generate a new key pair
                PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
                PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();

                // Create new account and assign the public key
                TransactionResponse newAccount = new AccountCreateTransaction()
                                .setKey(newAccountPublicKey)
                                .setInitialBalance(Hbar.fromTinybars(1000))
                                .execute(client);

                // Get the new account ID
                AccountId newAccountId = newAccount.getReceipt(client).accountId;

                System.out.println("\nNew account ID: " + newAccountId);

                // Check the new account's balance
                AccountBalance accountBalance = new AccountBalanceQuery()
                                .setAccountId(newAccountId)
                                .execute(client);

                System.out.println("New account balance is: " + accountBalance.hbars);

                // Transfer HBAR
                TransactionResponse sendHbar = new TransferTransaction()
                                .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
                                .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
                                .execute(client);

                System.out.println("\nThe transfer transaction was: " + sendHbar.getReceipt(client).status);

                // Request the cost of the query
                Hbar queryCost = new AccountBalanceQuery()
                                .setAccountId(newAccountId)
                                .getCost(client);

                System.out.println("\nThe cost of this query: " + queryCost);

                // Check the new account's balance
                AccountBalance accountBalanceNew = new AccountBalanceQuery()
                                .setAccountId(newAccountId)
                                .execute(client);

                System.out.println("The account balance after the transfer: " + accountBalanceNew.hbars + "\n");
        }
}

```

{% endcode %}

</details>

<details>

<summary>JavaScript</summary>

```javascript
const {
  Hbar,
  Client,
  PrivateKey,
  AccountCreateTransaction,
  AccountBalanceQuery,
  TransferTransaction,
} = require("@hashgraph/sdk
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

  // Create your connection to the Hedera network
  const client = Client.forTestnet();

  //Set your account as the client's operator
  client.setOperator(myAccountId, myPrivateKey);

  // Set default max transaction fee & max query payment
  client.setDefaultMaxTransactionFee(new Hbar(100));
  client.setDefaultMaxQueryPayment(new Hbar(50));

  // Create new keys
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey.publicKey;

  // Create a new account with 1,000 tinybar starting balance
  const newAccountTransactionResponse = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);

  // Get the new account ID
  const getReceipt = await newAccountTransactionResponse.getReceipt(client);
  const newAccountId = getReceipt.accountId;

  console.log("\nNew account ID: " + newAccountId);


  // Verify the account balance
  const accountBalance = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .execute(client);

  console.log(
    "New account balance is: " +
      accountBalance.hbars.toTinybars() +
      " tinybars."
  );

  // Create the transfer transaction
  const sendHbar = await new TransferTransaction()
    .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
    .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
    .execute(client);

  // Verify the transaction reached consensus
  const transactionReceipt = await sendHbar.getReceipt(client);
  console.log(
    "The transfer transaction from my account to the new account was: " +
      transactionReceipt.status.toString()
  );

  // Request the cost of the query
  const queryCost = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .getCost(client);

  console.log("\nThe cost of query is: " + queryCost);

  // Check the new account's balance
  const getNewBalance = await new AccountBalanceQuery()
    .setAccountId(newAccountId)
    .execute(client);

  console.log(
    "The account balance after the transfer is: " +
      getNewBalance.hbars.toTinybars() +
      " tinybars."
  );
}
environmentSetup();
```

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

	//Transfer hbar from your testnet account to the new account
	transaction := hedera.NewTransferTransaction().
		AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
		AddHbarTransfer(newAccountId, hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar))

	// Submit the transaction to a Hedera network
	txResponse, err := transaction.Execute(client)

	if err != nil {
		panic(err)
	}

	// Request the receipt of the transaction
	transferReceipt, err := txResponse.GetReceipt(client)

	if err != nil {
		panic(err)
	}

	// Get the transaction consensus status
	transactionStatus := transferReceipt.Status

	fmt.Printf("\nThe transaction consensus status is %v\n\n", transactionStatus)

	//Create the query that you want to submit
	balanceQuery := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Get the cost of the query
	cost, err := balanceQuery.GetCost(client)

	if err != nil {
		panic(err)
	}

	fmt.Println("The account balance query cost is:", cost.String())

	//Check the new account's balance
	newAccountBalancequery := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign with client operator private key and submit the query to a Hedera network
	newAccountBalance, err := newAccountBalancequery.Execute(client)
	if err != nil {
		panic(err)
	}

	//Print the balance of tinybars
	fmt.Println("The HBAR balance for this account is", newAccountBalance.Hbars.AsTinybar())
}

```

</details>

#### 示例输出：

```bash
New account ID: 0.0.13724748
New account balance: 1000 tinybars.

The transfer transaction from my account to the new account was: SUCCESS

The cost of query: 0 tℏ
The account balance after the transfer: 2000 tinybars.
```

{% hint style="info" %}
有一个问题？ [在 StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
