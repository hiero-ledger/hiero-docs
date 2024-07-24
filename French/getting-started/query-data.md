# Requête de données Ledger

## Summary

Dans cette section, nous vous guiderons en interrogeant le solde de votre compte, vous permettant de récupérer les informations les plus récentes sur les fonds disponibles dans votre nouveau compte Hedera.

## Prérequis <a href="#pre-requisites" id="pre-requisites"></a>

- L'étape [Introduction](introduction.md) a été terminée.
- L'étape [Configuration de l'environnement](environment-set-up.md) a été terminée.
- L'étape [Créer un compte](create-an-account.md) a été terminée.
- L'étape du [transfert HBAR ](transfer-hbar.md)est terminée.

{% hint style="info" %}
_**Note:** Vous pouvez toujours vérifier "_[vérification de _Code ✅_](query-data. d#code-check) _" section en bas de chaque page pour afficher le code entier si vous rencontrez des problèmes. Vous pouvez également poster votre problème sur le salon SDK respectif dans notre communauté Discord_ [_here_](http://hedera. om/discord) _ou sur le dépôt GitHub_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## Demander le solde du compte

### **Récupère le coût de la requête**

Vous pouvez demander le coût d'une requête avant de soumettre la requête au réseau Hedera. La vérification du solde d'un compte est gratuite aujourd'hui. Vous pouvez vérifier cela par la méthode ci-dessous.

{% tabs %}
{% tab title="Java" %}

```java
//Demande le coût de la requête
Hbar queryCost = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

System.out.println("Le coût de cette requête est: " +queryCost);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Demande le coût de la requête
const queryCost = waiting new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .getCost(client);

console.log("Le coût de la requête est: " +queryCost);
```

{% endtab %}

{% tab title="Go" %}

```java
//Créez la requête que vous voulez soumettre
balanceQuery := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Get the cost of the query
cost, err := balanceQuery. etCost(client)

if err ! nil {
        panique
}

println("The account balance query cost is:", cost. tring())
```

{% endtab %}
{% endtabs %}

### **Obtenir le solde du compte**

Vous allez vérifier que le solde du compte a été mis à jour pour le nouveau compte en demandant une requête pour obtenir le solde du compte. Le solde du compte courant devrait être la somme de la balance initiale (1,000 _**tinybars**_) plus le montant de transfert (1,000 **tinybars**) et égal à 2000 **tinybars**.

{% tabs %}
{% tab title="Java" %}

```java
//Vérifiez le solde du nouveau compte
AccountBalance accountBalanceNew = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("Le solde du compte après le transfert: " +accountBalanceNew.hbars);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Vérifiez le solde du nouveau compte
const getNewBalance = wait new AccountBalanceQuery()
     . etAccountId(newAccountId)
     . xecute(client);

console.log("Le solde du compte après le transfert est: " +getNewBalance.hbars.toTinybars() +" tinybar.")
```

{% endtab %}

{% tab title="Go" %}

```go
//Vérifiez le solde du nouveau compte
newAccountBalancequery := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Signez avec la clé privée de l'opérateur client et soumettez la requête à un nouveau solde réseau Hedera
newAccountBalance, err := newAccountBalancequery. xecute(client)
if err != nil {
    panic(err)
}

//Imprime le solde de tinybars
fmt. rintln("The hbar account balance for this account is", newAccountBalance.Hbars.AsTinybar())
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
:star: **Félicitations ! Vous avez transféré avec succès **_**HBAR**_\*\* vers un autre compte sur le Testnet Hedera ! Si vous avez suivi le tutoriel depuis le début, vous avez terminé ce qui suit jusqu'à présent :\*\*&#x20

- Configurez votre environnement Hedera pour soumettre des transactions et des requêtes.
- A créé un compte.
- **HBAR** transféré à un autre compte.

Voulez-vous continuer à apprendre ? Visitez notre section [SDKs & API](../sdks-and-apis/) pour faire passer votre expérience d'apprentissage au niveau supérieur. Vous pouvez également trouver des exemples supplémentaires de Java SDK [here](https://github.com/hashgraph/hedera-sdk-java/tree/main/examples/src/main/java).
{% endhint %}

***

## Vérification du code ✅

Votre fichier de code complet devrait ressembler à ceci :

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

<summary>Aller à</summary>

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

#### Exemple de sortie :

```bash
New account ID: 0.0.13724748
New account balance: 1000 tinybars.

The transfer transaction from my account to the new account was: SUCCESS

The cost of query: 0 tℏ
The account balance after the transfer: 2000 tinybars.
```

{% hint style="info" %}
Vous avez une question ? [Demandez-le sur StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
