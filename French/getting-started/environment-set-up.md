# Configuration de l'environnement

## Summary

Ce guide de configuration de l'environnement vous fournira les étapes nécessaires pour préparer votre environnement de développement à la construction d'applications sur le réseau Hedera. Vous allez configurer un nouveau répertoire de projet, établir un `. nv` fichier variable d'environnement pour stocker votre identifiant de compte Hedera Testnet et vos clés privées et configurer votre client Hedera Testnet.

***

## Pré-requis

- L'étape [Introduction](introduction.md) a été terminée.

{% hint style="info" %}
_**Note:** Vous pouvez toujours vérifier "_[vérification de _Code ✅_](environment-set-up. d#code-check) _" section en bas de chaque page pour afficher le code entier si vous rencontrez des problèmes. Vous pouvez également poster votre problème sur le salon SDK respectif dans notre communauté Discord_ [_here_](http://hedera. om/discord) _ou sur le dépôt GitHub_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## **Étape 1 : Créer votre répertoire de projet**

Ouvrez votre IDE de choix et suivez les étapes ci-dessous pour créer votre nouveau répertoire de projet.

{% tabs %}
{% tab title="Java Gradle" %}
Créez un nouveau projet Gradle et nommez-le `HederaExamples`. Ajoutez les dépendances suivantes à votre fichier `build.gradle`.

{% code title="build.gradle " %}

```gradle
dépendances {

    implémentation 'com.hedera.hashgraph:sdk:2.32.0'
    implémentation 'io.grpc:grpc-netty-shaded:1.57.2'
    implémentation 'io.github.cdimascio:dotenv-java:2.3.2'
    implémentation 'org.slf4j:slf4j-nop:2.0.9'
    implémentation 'com.google.code.gson:gson:2.8.8'
}
```

{% endcode %}
{% endtab %}

{% tab title="Java Maven" %}
Créez un nouveau projet Maven et nommez-le `HederaExamples`. Ajoute les dépendances suivantes à votre fichier `pom.xml`.

{% code title="pom.xml " %}

```xml
<dependencies>
        <dependency>
            <groupId>com. edera. ashgraph</groupId>
            <artifactId>sdk</artifactId>
            <version>2. 2.0</version>
        </dependency>
        <dependency>
            <groupId>io. rpc</groupId>
            <artifactId>grpc-netty-shaded</artifactId>
            <version>1.57.</version>
        </dependency>
        <dependency>
            <groupId>io. ithub. dimascio</groupId>
            <artifactId>dotenv-java</artifactId>
            <version>2.3.</version>
        </dependency>
        <dependency>
            <groupId>org. lf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>2. .9</version>
        </dependency>
        <dependency>
            <groupId>com. Code fils</groupId>
            <artifactId>gson</artifactId>
            <version>2. .8</version>
        </dependency>
</dependencies>
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
Ouvrez votre terminal et créez un répertoire appelé _`hello-hedera-js-sdk`_. Après avoir créé le répertoire du projet, accédez au répertoire en exécutant la commande suivante :

```bash
mkdir hello-hedera-js-sdk && cd hello-hedera-js-sdk
```

Initialiser un projet _`node.js`_ dans ce nouveau répertoire en exécutant la commande suivante :

```bash
npm init -y
```

C'est à quoi votre console devrait ressembler après avoir exécuté la commande :

```bash
{
  "name": "hello-hedera-js-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index. s",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "mots-clés": [],
  "auteur": "",
  "licence": "ISC"
}
```

{% endtab %}

{% tab title="Go" %}
Ouvrez votre terminal et créez un répertoire de projet appelé quelque chose comme `hedera-go-examples` pour stocker votre code source Go.

```bash
mkdir hedera-go-examples && cd hedera-go-examples
```

{% endtab %}
{% endtabs %}

***

## Étape 2 : Installer les dépendances et les SDK

{% tabs %}
{% tab title="Java" %}
Créez une nouvelle classe Java et nommez-la comme _`HederaExamples`_. Importer les classes suivantes à utiliser dans votre exemple :

```java
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.Client;
import io.github.cdimascio.dotenv.Dotenv;
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.AccountBalance;
import com.hedera.hashgraph.sdk. ccountBalanceQuery;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.ReceiptStatusException;
import com.hedera.hashgraph.sdk.PrecheckStatusException;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;

import java.util.concurrent.TimeoutException;
```

_**Remarque :** Vous pouvez installer la dernière version du Java SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-java)_._
{% endtab %}

{% tab title="JavaScript" %}
Installez le [SDK JavaScript](https://github.com/hashgraph/hedera-sdk-js) avec votre gestionnaire de paquets préféré _`npm`_ ou _`yarn`_ en exécutant la commande suivante :

```bash
// Installez Hedera's JS SDK avec NPM
npm install --save @hashgraph/sdk

// Installez avec Yarn
yarn add @hashgraph/sdk
```

Installez _`dotenv`_ avec votre gestionnaire de paquets favori. Cela permettra à notre environnement de node d'utiliser votre identifiant de compte testnet _\*\*_ et la _**clé privée**_ que nous stockerons dans un fichier _`.env`_ suivant.

```bash
// Installer avec NPM
npm install dotenv

// Installer avec Yarn
yarn add dotenv
```

Créez un fichier _`index.js`_ en exécutant la commande suivante :

```bash
touch index.js
```

La structure de votre projet devrait ressembler à ceci :

![](../.gitbook/assets/project\_directory.png)
{% endtab %}

{% tab title="Go" %}
Créez un fichier `hedera_examples.go` dans le répertoire racine `hedera-go-examples`. Tu vas écrire tout ton code dans ce fichier.

```bash
touch hedera_examples.go
```

Créez le fichier Go "module" en exécutant la commande ci-dessous. Le fichier `go.mod` définit les propriétés et les dépendances du module et fournit un moyen de gérer le versioning pour les projets Go.

```go
go mod init hedera_examples.go
```

Installez le [Go SDK](https://github.com/hashgraph/hedera-sdk-go):

```go-module
aller sur github.com/hashgraph/hedera-sdk-go/v2@latest
```

Et le [paquet DotEnv](https://github.com/joho/godotenv):&#x20

```go-module
go get github.com/joho/godotenv
```

Importez les paquets suivants dans votre fichier `hedera_examples.go` :

```go
import du paquet

(
    "fmt"
    "os"

    "github.com/joho/godotenv"
    "github.com/hashgraph/hedera-sdk-go/v2"
)
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Note :** Le réseau de test **HBAR** est requis pour cette étape suivante. Veuillez suivre les instructions pour créer un compte Hedera sur le_ [_portal_](https://docs.hedera.com/guides/getting-started/introduction) _avant de passer à l'étape suivante._
{% endhint %}

***

## Étape 3 : \*\*Créez votre fichier .env \*\*

Créez le fichier `.env` à la racine de votre projet. Le fichier `.env` stocke vos variables d'environnement, telles que l'ID de votre compte et la clé privée.&#x20

_**📣 Note**: Si vous n'avez pas créé de compte, veuillez le faire_ [_here_](introduction.md) _avant cette étape._

{% tabs %}
{% tab title="Hedera Developer Portal" %}
Si vous avez créé votre compte testnet via le portail développeur, récupérez l'ID du compte Hedera Testnet et la clé privée encodée en DER à partir de votre [profil portail Heder](https://portal. edera.com/) (voir capture d'écran ci-dessous) et attribuez-les aux variables d'environnement `MY_ACCOUNT_ID` et `MY_PRIVATE_KEY` dans votre fichier `.env`: &#x20

<figure><img src="../.gitbook/assets/DER portal (1).png" alt="" width="563"><figcaption><p>du portail développeur Hedera</p></figcaption></figure>

```markdown
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e020100300506032b657004220420ed5a93073.....
```

{% endtab %}

{% tab title="Hedera Faucet" %}
Alternativement, si vous avez utilisé le robinet pour créer un compte testnet, saisissez votre ID de compte de robinet et la clé privée (comment exporter une clé privée depuis MetaMask [here](https://support. etamask.io/hc/en-us/articles/360015289632-How-to-export-an-account-s-private-key)) et assignez-les aux variables d'environnement `MY_ACCOUNT_ID` et `MY_PRIVATE_KEY` dans votre fichier `.env` :

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

```
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=0xfd154395435c81233b2fc906486f35e068...
```

{% endtab %}
{% endtabs %}

Ensuite, vous chargerez votre ID de compte et les variables de clé privée à partir du fichier `.env` créé à l'étape précédente.

{% tabs %}
{% tab title="Java" %}
Dans la méthode _`main`_, ajoutez votre ID de compte testnet et votre clé privée à partir du fichier d'environnement.

{% code title="HederaExamples.java" %}

```java
public class HederaExemples {

    public static void main(String[] args) {

        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId. romString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));  
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

    //Charge le . nv file and throws an error if it cannot load the variables from this file correctly
    err := godotenv. oad(".env")
    if err != nil {
        panique (fmt. rrorf("Impossible de charger les variables d'environnement à partir du fichier .env. Erreur :\n%v\n", err))
    }

    //Saisissez votre ID de compte testnet et votre clé privée à partir du . fichier nv
    monAccountId, err := hedera.AccountIDFromString(os. etenv("MY_ACCOUNT_ID"))
    si erreur! nil {
        panique
    }

    myPrivateKey, err := hedera. rivateKeyFromString(os. etenv("MY_PRIVATE_KEY"))
    if err ! nil {
        panique


    //Imprime l'ID de votre compte testnet et votre clé privée dans la console pour vous assurer qu'il n'y a pas eu d'erreur
    fmt. rintf("The account ID is = %v\n", monAccountId)
    fmt. rintf (La clé privée est = %v\n", myPrivateKey)
}
```

{% endcode %}

Dans votre terminal, entrez la commande suivante pour créer votre fichier `go.mod`. Ce module est utilisé pour le suivi des dépendances et est requis.

```go-module
go mod init hedera_examples.go
```

Exécutez votre code pour voir votre ID de compte testnet et votre clé privée imprimés sur la console.

```go-module
go run hedera_examples.go
```

{% endtab %}
{% endtabs %}

***

## Étape 4 : Créez votre client Hedera Testnet

Créez un réseau de test Hedera [client](../support-and-community/glossary.md#client) et définissez les informations de l'opérateur en utilisant l'ID du compte testnet et la clé privée pour l'autorisation de transaction et de requête. _operator_ est le compte par défaut qui paiera les frais de transaction et de requête en HBAR. Vous devrez signer la transaction ou la requête avec la clé privée de ce compte pour autoriser le paiement. Dans ce cas, l'identifiant de l'opérateur est votre `ID du compte testnet**.**` et la clé privée de l'opérateur est la clé privée correspondante du compte testnet.

{% hint style="warning" %}
Pour éviter de rencontrer l'erreur **`INSUFFICIENT_TX_FEE`** lors de la conduite des transactions, vous pouvez ajuster la limite maximale des frais de transaction via la méthode **`.setDefaultMaxTransactionFee()`**. De même, le paiement maximal des requêtes peut être ajusté en utilisant la méthode **.setDefaultMaxQueryPayment()\`** .
{% endhint %}

<details>

<summary>🚨 Comment résoudre l' <em>INSUFFIENT_TX_FEE</em> erreur</summary>

Pour résoudre cette erreur, vous devez ajuster les frais de transaction maximum à une valeur plus élevée adaptée à vos besoins.

Voici un exemple simple d'ajout à votre code :

```javascript
const maxTransactionFee = new Hbar(XX); // remplace XX avec les frais désirés dans Hbar
```

Dans cet exemple, vous pouvez définir `maxTransactionFee` à n'importe quelle valeur supérieure à 5 HBAR (ou 500,000, 00 tinybars) pour éviter l'erreur "_INSUFFICIENT\_TX\_FEE_" pour les transactions supérieures à 5 HBAR. Veuillez remplacer `XX` par la valeur désirée.

Pour implémenter ce nouveau frais de transaction maximum, vous utilisez la méthode `setDefaultMaxTransactionFee()` comme montré ci-dessous:

```javascript
client.setDefaultMaxTransactionFee(maxTransactionFee);
```

</details>

{% tabs %}
{% tab title="Java" %}

```java
//Créez votre client Hedera Testnet
Client client = Client.forTestnet();

//Définir votre compte comme l'opérateur du client
client. etOperator(monAccountId, monPrivateKey);

//Définir les frais de transaction maximum par défaut (en Hbar)
client. etDefaultMaxTransactionFee(new Hbar(100));

//Définir le paiement maximum pour les requêtes (en Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Créez votre client Hedera Testnet
const client = Client.forTestnet();

//Définir votre compte en tant que client opérateur
du client. etOperator(monAccountId, monPrivateKey);

//Définir les frais de transaction maximum par défaut (en Hbar)
client. etDefaultMaxTransactionFee(new Hbar(100));

//Définir le paiement maximum pour les requêtes (en Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="Go" %}

```go
//Créer votre client testnet
client := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)

// Définir les frais de transaction max par défaut
client. etDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))

// Définir max query payment
client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
```

{% endtab %}
{% endtabs %}

**Votre environnement de projet est maintenant configuré pour soumettre des transactions et des requêtes au réseau de test Hedera avec succès !**

Ensuite, vous apprendrez comment [créer un compte](create-an-account.md).

## Vérification du code :white\_check\_mark:

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
import com.hedera. ashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.TransferTransaction;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com.hedera.hashgraph.sdk.ReceiptStatusException;
import com.hedera.hashgraph.sdk.PrecheckStatusException;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import java.util.concurrent. ImeoutException ;

classes publiques HederaExemples {

        public static void main(String[] args) {
                
        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId. romString(Dotenv.load().get("MY_ACCOUNT_ID"));+
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load(). et("MY_PRIVATE_KEY"));
        //Créez votre client Hedera Testnet
        
<strong>        Client client = Client. orTestnet();
client</strong>        . etOperator(monAccountId, monPrivateKey);
        
        // Définir les frais de transaction max par défaut & max paiement de requête
        client. etDefaultMaxTransactionFee(new Hbar(100)); 
        client. etMaxQueryPayment(new Hbar(50)); 
        
        System. ut.println("Configuration du client terminée.");
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

require("dotenv"). onfig();

fonction async environmentSetup() {
  //Récupérez votre identifiant de compte Hedera testnet et votre clé privée . nv file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env. Y_PRIVATE_KEY;

  // Si nous n'avons pas pu le saisir, nous devrions lancer une nouvelle erreur
  si (!myAccountId || ! yPrivateKey) {
    throw new Error(
      "Les variables d'environnement MY_ACCOUNT_ID et MY_PRIVATE_KEY doivent être présentes"
    );
  }
  
  //Créez votre client Hedera Testnet
  const client = client. orTestnet();

  //Définir votre compte en tant qu'opérateur client
  . etOperator(monAccountId, monPrivateKey);

  //Définir les frais de transaction maximum par défaut (en Hbar)
  client. etDefaultMaxTransactionFee(new Hbar(100));

  //Définir le paiement maximum pour les requêtes (en Hbar)
  client. etDefaultMaxQueryPayment(new Hbar(50));
  
  
 console.log("Configuration du client.");
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Aller à</summary>

{% code title="hedera_examples.go" %}

```go
import du package main

(
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github. om/joho/godotenv"
)

func main() {

	//Charge le . fichier nv et lance une erreur si elle ne peut pas charger les variables de ce fichier correctement
	err := godotenv. oad(".env")
	si err != nil {
		panique(fmt.Errorf("Impossible de charger les variables d'environnement à partir du fichier .env. Erreur:\n%v\n", err))
	}

	//Saisissez votre identifiant de compte testnet et votre clé privée à partir du . fichier nv
	monAccountId, err := hedera.AccountIDFromString(os. etenv("MY_ACCOUNT_ID"))
	if err != nil {
		panique(err)


	myPrivateKey, err := hedera.PrivateKeyFromString(os. etenv("MY_PRIVATE_KEY"))
	si err != nil {
		panique(err)


	//Créez votre client testnet
	client := hedera.ClientForTestnet()
	client. etOperator(myAccountId, myPrivateKey)

	// Définir les frais de transaction max et le paiement max de requête
	client.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera. barUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
	
	fmt.Println("Client setup complete.")
}
```

{% endcode %}

</details>

{% hint style="info" %}
Vous avez une question ? [Demandez-le sur StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

***

**Contributeurs:** [fabianstraubinger99](https://github.com/fabianstraubinger99)
