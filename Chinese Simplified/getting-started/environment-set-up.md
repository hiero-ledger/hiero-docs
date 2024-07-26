# 环境设置

## Summary

此环境设置指南将为您提供必要的步骤，使您的开发环境准备好在 Hedera 网络上构建应用程序。 您将设置一个新的项目目录，建立一个 `。 nv`环境变量文件来存储你的 Hedera Testnet 帐户 ID 和私钥，并配置你的 Hedera Testnet 客户端。

***

## 必备条件

- 完成 [Introduction](introduction.md) 步骤。

{% hint style="info" %}
_**注意:** 您可以随时检查"_[_Code检查:check_mark_buton:_](environment-set-up)。 d#code-check) _" 部分在每个页面底部以查看整个代码，如果你遇到了问题。 您还可以将您的问题发布到我们Discord社区的SDK频道_ [_here_](http://hedera. om/discord) _或 GitHub 仓库_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## **步骤1：创建您的项目目录**

打开您选择的 IDE 并按下面的步骤创建您的新项目目录。

{% tabs %}
{% tab title="Java Gradle" %}
创建一个新的 Gradle 项目并命名为“HederaExples”。 添加以下依赖关系到您的 `build.gradle` 文件。

{% code title="build.gradle " %}

```gradle
Dependencies v.

    implementation 'com.hedera.hashgraph:sdk:2.32.0'
    implementation 'io.grpc:grpc-nety-shaded:1.57.2'
    implementation 'io.github.cdimascio:dotenv-java:2.3.2'
    implementation 'org.slf4j-nop:2.0.9'
    implementation 'com.google.code.gson:gson:2.8.8'
}
```

{% endcode %}
{% endtab %}

{% tab title="Java Maven" %}
创建一个新的 Maven 项目并命名为“HederaExples”。 添加以下依赖关系到您的 "pom.xml" 文件。

{% code title="pom.xml " %}

```xml
<dependencies>
        <dependency>
            <groupId>com.hedera.hashgraph</groupId>
            <artifactId>sdk</artifactId>
            <version>2.32.0</version>
        </dependency>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-netty-shaded</artifactId>
            <version>1.57.2</version>
        </dependency>
        <dependency>
            <groupId>io.github.cdimascio</groupId>
            <artifactId>dotenv-java</artifactId>
            <version>2.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>2.0.9</version>
        </dependency>
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.8</version>
        </dependency>
</dependencies>
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
打开您的终端并创建一个名为_`hello-hedera-js-sdk`_的目录。 当你通过运行以下命令创建项目目录导航到目录时：

```bash
mkdir hello-hedera-js-sdk && cd hello-hedera-js-sdk
```

通过运行以下命令来初始化此新目录中的 _`node.js`_ 项目：

```bash
npm init -y
```

这正是你的控制台在运行命令后应该看起来像样：

```bash
{
  "name": "hello-hedera-js-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

{% endtab %}

{% tab title="Go" %}
打开您的终端并创建一个叫做`hedera-go-examples`的项目目录来存储您的 Go 源代码。

```bash
mkdir hedera-go-examples && cd hedera-go-examples
```

{% endtab %}
{% endtabs %}

***

## 步骤 2: 安装依赖关系和 SDK

{% tabs %}
{% tab title="Java" %}
创建一个新的 Java 类并命名它类似于 _`HederaExampples`_ 。 导入以下类用于您的示例：

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
```

_**Note:** You may install the latest version of the Java SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-java)_._
{% endtab %}

{% tab title="JavaScript" %}
通过运行以下命令安装[JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js)

```bash
// 安装Hedera's JSSDK with NPM
npm install --save @hashgraph/sdk

// 安装用 Yarn
yarn 添加 @hashgraph/sdk
```

用您最喜欢的软件包管理器安装 _`dotenv`_ 。 这将允许我们的节点环境使用您的 testnet _**account ID**_ 和 _**private key**_ ，我们下一步将存储在 _`.env`_ 文件中。

```bash
// 用 NPM
npm install dotenv

/Install with Yarn
yarn added dotenv
```

运行以下命令创建一个 _`index.js`_ 文件：

```bash
touch index.js
```

您的项目结构应该看起来像这样：

![](../.gitbook/assets/project\_directory.png)
{% endtab %}

{% tab title="Go" %}
在 `hedera-go-examples ` 根目录中创建一个 `hedera_examples.go` 文件。 您将在此文件中写入您所有的代码。

```bash
touch hedera_examples.go
```

通过运行下面的命令创建转到"模块"文件。 "go.mod"文件定义了模块的属性和依赖性，并提供了管理Go 项目版本控制的方法。

```go
go mod init hedera_examples.go
```

安装 [Go SDK](https://github.com/hashgraph/hedera-sdk-go)：

```go-module
github.com/hashgraph/hedera-sdk-go/v2@latest
```

而且，[DotEnv 包](https://github.com/joho/godotenv):&#x20

```go-module
go get github.com/joho/godotenv
```

导入以下软件包到您的`hedera_examples.go`文件：

```go
包

导入 (
    "fmt"
    "os"

    "github.com/joho/godotenv"
    "github.com/hashgraph/hedera-sdk-go/v2"
)
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**注意：** 测试网 **HBAR** 是下一步所必需的。 Please follow the instructions to create a Hedera account on the_ [_portal_](https://docs.hedera.com/guides/getting-started/introduction) _before you move on to the next step._
{% endhint %}

***

## 第 3 步：**创建您的 .env 文件**

在你项目的根目录中创建 `.env` 文件。 `.env`文件存储您的环境变量，如您的帐户 ID 和私钥。&#x20

_**📣 Note**: 如果你没有创建一个帐户, 请在这个步骤之前完成_ [_here_](introduction.md) _step._

{% tabs %}
{% tab title="Hedera Developer Portal" %}
如果您通过开发者门户创建了您的 testnet 帐户， 从 [Hedera Portal Profile](https://portal)中获取 Hedera Testnet 帐户 ID 和 DER编码的私钥。 edera.com/(参见下面屏幕截图)，并将它们分配给你的`.env`文件中的`MY_ACCOUNT_ID`和`MY_PRIVATE_KEY`环境变量：&#x20

<figure><img src="../.gitbook/assets/DER portal (1).png" alt="" width="563"><figcaption><p>Hedera Developer Portal</p></figcaption></figure>

```markdown
MY_ACOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e0201003005032b657004220420ed5a93073.....
```

{% endtab %}

{% tab title="Hedera Faucet" %}
或者，如果您使用动物群来创建测试网账户。 抓取您的 faucet 帐户 ID 和私钥 (如何从MetaMask [here](https://support) 导出私钥。 etamask.io/hc/en-us/articles/360015289632-How-export-an-accounts-s-private key)并将它们分配到你的`.env`文件中的`MY_ACCOUNT_ID`和`MY_PRIVATE_KEY`环境变量：

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

```
MY_ACOUNT_ID=0.0.1234
MY_PRIVATE_KEY=0xfd15435c81233b2fc906486f35e068...
```

{% endtab %}
{% endtabs %}

接下来，您将从上一步创建的 `.env` 文件中加载您的帐户 ID 和私钥变量。

{% tabs %}
{% tab title="Java" %}
在 _`main`_ 方法内，从环境文件中添加您的 testnet 帐户 ID 和私钥。

{% code title="HederaExamples.java" %}

```java
public class HederaExamples v.

    public 静态无效main(String] args) v.

        ///Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId. romString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_Key"));  
    }
}
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="index.js" %}

```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar, TransferTransaction } = require("@hashgraph/sdk");
require('dotenv').config();

async function environmentSetup() {

    //Grab your Hedera testnet account ID and private key from your .env file
    const myAccountId = process.env.MY_ACCOUNT_ID;
    const myPrivateKey = process.env.MY_PRIVATE_KEY;

    // If we weren't able to grab it, we should throw a new error
    if (!myAccountId || !myPrivateKey) {
        throw new Error("Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present");
    }
}
environmentSetup();
```

{% endcode %}
{% endtab %}

{% tab title="Go" %}
{% code title="hedera_examples.go" %}

```go
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
    fmt.Printf("The account ID is = %v\n", myAccountId)
    fmt.Printf("The private key is = %v\n", myPrivateKey)
}
```

{% endcode %}

在您的终端中，输入以下命令来创建您的 `go.mod` 文件。 此模块用于跟踪依赖关系，是必需的。

```go-module
go mod init hedera_examples.go
```

运行您的代码，看到您的 testnet 帐户 ID 和私钥打印到控制台。

```go-module
go run hedera_examples.go
```

{% endtab %}
{% endtabs %}

***

## 第 4 步：创建您的 Hedera 测试网客户端

创建一个 Hedera Testnet [client](../supportand-community/glossary.md#客户端)，并使用测试网账户ID和私钥处理交易和查询费授权设置操作员信息。 _operator_是支付HBAR交易和查询费用的默认帐户。 您需要用该账户的私钥签名交易或查询以授权付款。 在这种情况下，运算符ID是您的测试网 `account ID****` ，而运营商私人密钥是相应的 testnet 帐户私钥。

{% hint style="warning" %}
为了避免在进行交易时遇到\*\*`INSUFFICIENT_TX_FEE`\*\* 错误，您可以通过 **.setDefaultMaxTransactionFee()`** 方法调整最大交易费限制。 同样，最大查询付款可以使用 **.setDefaultMaxQueryPayment()`** 方法进行调整。
{% endhint %}

<details>

<summary>🚨 如何解析 <em>INSUFIENT_TX_FEE</em> 错误</summary>

要解决这个错误，您必须调整最大交易费用到适合您需要的更高值。

这是一个添加到您代码的简单示例：

```javascript
const maxTransactionFee = new Hbar(XX)；// 以 Hbar 中的预期费用替换XX
```

在此示例中，您可以将 `maxTransactionFee` 设置为大于5 HBAR (或500 000) 如果交易大于 5 HBAR，请避免“_INSUFFICIENT\_TX\_FEE_” 错误。 请将`XX`替换为理想值。

为了实现这个新的最大交易费，您使用 `setDefaultMaxTransactionFee()` 方法如下所示：

```javascript
client.setDefaultMaxTransactionFee(maxTransactionFee)；
```

</details>

{% tabs %}
{% tab title="Java" %}

```java
//Create your Hedera Testnet 客户端
客户端 = Client.forTestnet();

///设置您的账户为客户端的运营商
客户端。 etOperator(myAccountId, myPrivateKey);

//设置默认最大交易费(Hbar)
客户端。 etDefaultMaxTransactionFee(new Hbar(100));

//设置查询的最大付款额(Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create your Hedera Testnet 客户端
const client = Client.forTestnet();

///设置您的账户为客户端的运营商
客户端。 etOperator(myAccountId, myPrivateKey);

//设置默认最大交易费(Hbar)
客户端。 etDefaultMaxTransactionFee(new Hbar(100));

//设置查询的最大付款额(Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="Go" %}

```go
//Create your testnet client
client := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)

// 设置默认最大交易费用
client. etDefaultMaxTransactionFee(hedera.HbarFrom(100,hedera.HbarUnits.Hbar))

// 设置最大查询付款
client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50,hedera.HbarUnits.Hbar))
```

{% endtab %}
{% endtabs %}

**您的项目环境现在已经设置，可以成功地向Hedera测试网络提交交易和查询！**

接下来，你将学习如何[创建一个帐户](创建一个帐户。md)。

## 代码检查:whit\_check\_mark：

***

<details>

<summary>Java</summary>

<pre class="language-java" data-title="HederaExamples.java"><code class="lang-java">import com.hedera.hashgraph.sdk.Hbar;
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

        public static void main(String[] args) {
                
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));+
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        //Create your Hedera Testnet client
        
<strong>        Client client = Client.forTestnet();
</strong>        client.setOperator(myAccountId, myPrivateKey);
        
        // Set default max transaction fee & max query payment
        client.setDefaultMaxTransactionFee(new Hbar(100)); 
        client.setMaxQueryPayment(new Hbar(50)); 
        
        System.out.println("Client setup complete.");
    }
}
</code></pre>

</details>

<details>

<summary>JavaScript</summary>

{% code title="index.js" %}

```javascript
const {
  Hbar,
  Client,
} = require("@hashgraph/sdk");

require("dotenv").config();

async function environmentSetup() {
  //Grab your Hedera testnet account ID and private key from your .env file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env.MY_PRIVATE_KEY;

  // If we weren't able to grab it, we should throw a new error
  if (!myAccountId || !myPrivateKey) {
    throw new Error(
      "Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present"
    );
  }
  
  //Create your Hedera Testnet client
  const client = Client.forTestnet();

  //Set your account as the client's operator
  client.setOperator(myAccountId, myPrivateKey);

  //Set the default maximum transaction fee (in Hbar)
  client.setDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client.setDefaultMaxQueryPayment(new Hbar(50));
  
  console.log("Client setup complete.");
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Go</summary>

{% code title="hedera_examples.go" %}

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
	
	fmt.Println(“Client setup complete.”)
}
```

{% endcode %}

</details>

{% hint style="info" %}
有一个问题？ [在 StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

***

**贡献者:** [fabianstraubinger99](https://github.com/fabianstraubinger99)
