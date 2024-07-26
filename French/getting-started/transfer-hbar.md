# Transférer le HBAR

## Summary

Dans cette section, vous apprendrez comment transférer **HBAR** de votre compte vers un autre réseau de test Hedera.

***

## Prérequis <a href="#pre-requisites" id="pre-requisites"></a>

- L'étape [Introduction](introduction.md) a été terminée.
- L'étape [Configuration de l'environnement](environment-set-up.md) a été terminée.
- L'étape [Créer un compte](create-an-account.md) a été terminée.

{% hint style="info" %}
_**Note:** Vous pouvez toujours vérifier "_[Contrôle de _Code ✅_](transfer-hbar. d#code-check) _" section en bas de chaque page pour afficher le code entier si vous rencontrez des problèmes. Vous pouvez également poster votre problème sur le salon SDK respectif dans notre communauté Discord_ [_here_](http://hedera. om/discord) _ou sur le dépôt GitHub_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## Étape 1. Créer une transaction de transfert

Utilisez votre nouveau compte créé dans la section "[Créer un compte](create-an-account.md)" et transférez 1000 **tinybars** de votre compte vers le nouveau compte. Le compte envoyant le **HBAR** doit signer la transaction en utilisant ses clés privées pour autoriser le transfert. Puisque vous transférez depuis le compte associé au client, vous n'avez pas besoin de signer explicitement la transaction car le compte de l'opérateur (compte transférant le **HBAR**) signe toutes les transactions pour autoriser le paiement des frais de transaction.

{% tabs %}
{% tab title="Java" %}

```java
//System.out.println("Le nouveau solde du compte est: "+accountBalance". barres);
//-----------------------<enter code below>--------------------------------------

//Transférer HBAR
TransactionResponse sendHbar = new TransferTransaction()
     . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Envoi du compte
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Réception du compte
     .execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//console.log("The new account balance is: " +accountBalance.hbars.toTinybars() +" tinybar. );
//-----------------------<enter code below>--------------------------------------

//Créer la transaction de transfert
const sendHbar = attendre la nouvelle TransferTransaction()
     . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Envoi du compte
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Réception du compte
     .execute(client);
```

{% endtab %}

{% tab title="Go" %}

```java
//Imprimer le solde de tinybars
//fmt.Println("Le solde du compte pour le nouveau compte est ", accountBalance.Hbars. sTinybar())
//-----------------------<enter code below>--------------------------------------

//Barre de transfert depuis votre compte testnet vers le nouveau compte
transaction := hedera.NewTransferTransaction().
        AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
        AddHbarTransfer(newAccountId,hedera.HbarFrom(1000, hedera.HbarUnits. inybar))

//Soumettre la transaction à un réseau Hedera
txResponse, errr := transaction. xecute(client)

if err != nil {
    panique(err)
}
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Remarque :** La valeur nette du transfert doit être égale à zéro (le nombre total de_ **HBAR** _envoyé par l'expéditeur doit correspondre au nombre total de_ **HBAR** _reçu par le destinataire).
{% endhint %}

***

## Étape 2. Vérifiez que la transaction de transfert a atteint le consensus

Pour vérifier que la transaction de transfert a atteint un consensus par le réseau, vous soumettrez une demande pour obtenir la réception de la transaction. Le statut du reçu vous informera si la transaction a été réussie (consensus atteint) ou non.

{% tabs %}
{% tab title="Java" %}

```java
System.out.println("La transaction de transfert était: " +sendHbar.getReceipt(client).status);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Vérifiez que la transaction a atteint le consensus
const transactionReceipt = attendre sendHbar.getReceipt(client);
console. og("La transaction de transfert de mon compte vers le nouveau compte était: " + transactionReceipt.status.toString());
```

{% endtab %}

{% tab title="Go" %}

```java
//Demande la réception de la transaction
transferReceipt, errr := txResponse. etReceipt(client)

if err != nil {
    panique(err)
}

//Get the transaction consensus status
transactionStatus := transferReceipt. tatus

fmt.Printf("The transaction consensus status is %v\n", transactionStatus)
```

{% endtab %}
{% endtabs %}

***

## Vérification du code ✅

Votre fichier de code complet devrait ressembler à ceci :

<details>

<summary>Java</summary>

{% code title="HederaExamples.java" %}

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

    public static void main(String[] args) throws TimeoutException, HederaPreCheckStatusException, HederaReceiptStatusException {

        //Grab your Hedera testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));
        
        // Create your connection to the Hedera network
        const client = Client.forTestnet();

        //Set your account as the client's operator
        client.setOperator(myAccountId, myPrivateKey);
  
        // Set default max transaction fee & max query payment
        client.setDefaultMaxTransactionFee(new Hbar(100));
        client.setDefaultMaxQueryPayment(new Hbar(50));

        // Generate a new key pair
        PrivateKey newAccountPrivateKey = PrivateKey.generateED25519();
        PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();

        //Create new account and assign the public key
        TransactionResponse newAccount = new AccountCreateTransaction()
                .setKey(newAccountPublicKey)
                .setInitialBalance( Hbar.fromTinybars(1000))
                .execute(client);

        // Get the new account ID
        AccountId newAccountId = newAccount.getReceipt(client).accountId;
        
        System.out.println("\nNew account ID: " +newAccountId);
        
        //Check the new account's balance
        AccountBalance accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);

        //Transfer HBAR
        TransactionResponse sendHbar = new TransferTransaction()
                .addHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
                .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
                .execute(client);

        System.out.println("The transfer transaction was: " +sendHbar.getReceipt(client).status);

    }
}
```

{% endcode %}

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
  TransferTransaction,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
obligatoire ("dotenv"). onfig();

fonction async environmentSetup() {
  // Récupérez votre identifiant de compte Hedera testnet et votre clé privée . fichier nv
  const monAccountId = process.env. Y_ACCOUNT_ID;
  const myPrivateKey = process.env. Y_PRIVATE_KEY ;

  // Si nous n'avons pas pu le saisir, nous devrions lancer une nouvelle erreur
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Environment variables myAccountId and myPrivateKey must be present"
    );
  }

  // Créez votre connexion au réseau Hedera
  const client = Client. orTestnet();

  //Définissez votre compte en tant qu'opérateur client
  client. etOperator(myAccountId, myPrivateKey);

  // Définit les frais de transaction max et le paiement max de requête
  client.setDefaultMaxTransactionFee(new Hbar(100));
  client. etDefaultMaxQueryPayment(new Hbar(50));

  // Créer de nouvelles clés
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey. ublicKey;

  // Créer un nouveau compte avec 1, format@@0 00 tinybar starting balance
  const newAccountTransactionResponse = waiting new AccountCreateTransaction()
    . etKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    . xecute(client);

  // Obtenir le nouveau compte ID
  const getReceipt = attendre newAccountTransactionResponse. etReceipt(client);
  const newAccountId = getReceipt.accountId;

  console. og("\nNouvel ID de compte : " + newAccountId);

  // Vérifiez le solde du compte
  const accountBalance = wait new AccountBalanceQuery()
    . etAccountId(newAccountId)
    .execute(client);

  console. og(
    "\nLe solde du compte est: " +
      accountBalance.hbars. oTinybars() +
      minuscules.
  );

  // Créer la transaction de transfert
  const sendHbar = wait new TransferTransaction()
    . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
    .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
    . xecute(client);

  // Vérifier que la transaction a atteint le consensus
  const transactionReceipt = wait sendHbar.getReceipt(client);
  console. og(
    "\nLa transaction de transfert de mon compte vers le nouveau compte était : " +
      transactionReceipt. tatus.toString()
  );
}
environmentSetup();
```

{% endcode %}

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
	fmt.Printf("The new account ID is %v\n", newAccountId)

	//Create the account balance query
	query := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign with client operator private key and submit the query to a Hedera network
	accountBalance, err := query.Execute(client)
	if err != nil {
		panic(err)
	}

	//Print the balance of tinybars
	fmt.Println("The account balance for the new account is", accountBalance.Hbars.AsTinybar())

	//Transfer hbar from your testnet account to the new account
	transaction := hedera.NewTransferTransaction().
		AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
		AddHbarTransfer(newAccountId, hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar))

	//Submit the transaction to a Hedera network
	txResponse, err := transaction.Execute(client)

	if err != nil {
		panic(err)
	}

	//Request the receipt of the transaction
	transferReceipt, err := txResponse.GetReceipt(client)

	if err != nil {
		panic(err)
	}

	//Get the transaction consensus status
	transactionStatus := transferReceipt.Status

	fmt.Printf("The transaction consensus status is %v\n", transactionStatus)
}
```

</details>

#### Exemple de sortie :

```bash
Nouvel ID de compte : 0.0.4382765

Le solde du compte est : 1000 tinybars.

La transaction de transfert de mon compte vers le nouveau compte a été : SUCCÈS
```

{% hint style="info" %}
Vous avez une question ? [Demandez-le sur StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
