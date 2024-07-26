# Transferir HBAR

## Summary

En esta sección, aprenderás a transferir **HBAR** de tu cuenta a otra en la red de pruebas de Hedera.

***

## Prerrequisitos <a href="#pre-requisites" id="pre-requisites"></a>

- Completado el paso [Introduction](introduction.md)
- Completado el paso [Configuración del entorno](environment-set-up.md.
- Completado el paso [Creado una Cuenta](create-an-account.md)

{% hint style="info" %}
_**Nota:** Siempre puedes comprobar "_[_Code Check ✅_](transfer-hbar. d#code-check) _" sección en la parte inferior de cada página para ver el código completo si tiene problemas. También puedes publicar tu problema en el canal SDK respectivo en nuestra comunidad de Discord_ [_here_](http://hedera. om/discord) _o en el repositorio de GitHub_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## Paso 1. Crear transacción de transferencia

Usa tu nueva cuenta creada en la sección "[Crear una cuenta](create-an-account.md)" y transfiere 1.000 **tinybars** de tu cuenta a la nueva cuenta. La cuenta que envía el **HBAR** necesita firmar la transacción usando sus claves privadas para autorizar la transferencia. Puesto que estás transfiriendo desde la cuenta asociada al cliente, no necesitas firmar explícitamente la transacción ya que la cuenta del operador (cuenta que transfiere el **HBAR**) firma todas las transacciones para autorizar el pago de la tarifa de la transacción.

{% tabs %}
{% tab title="Java" %}

```java
//System.out.println("El nuevo saldo de cuenta es: " +accountBalance. barras);
//-----------------------<enter code below>--------------------------------------

//Transfer HBAR
TransactionResponse sendHbar = new TransferTransaction()
     . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Enviando cuenta
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Recibiendo cuenta
     .execute(cliente);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//console.log("El nuevo saldo de cuenta es: " +accountBalance.hbars.toTinybars() +" tinybar. );
//-------------------------------<enter code below>------------------------------

//Crear la transacción de transferencia
const sendHbar = await new TransferTransaction()
     . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000)) //Enviando cuenta
     .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000)) //Recibiendo cuenta
     .execute(cliente);
```

{% endtab %}

{% tab title="Go" %}

```java
//Imprime el saldo de tinybars
//fmt.Println("El saldo de la cuenta para la nueva cuenta es ", accountBalance.Hbars. sTinybar())
//-----------------------<enter code below>--------------------------------------

//Transferencia hbar de tu cuenta testnet a la nueva cuenta
transacción := hedera.NewTransferTransaction().
        AddHbarTransfer(myAccountId, hedera.HbarFrom(-1000, hedera.HbarUnits.Tinybar)).
        AddHbarTransfer(newAccountId,hedera.HbarFrom(1000, hedera.HbarUnits. inybar))

//Envía la transacción a una red Hedera
txResponse, err := transacción. xecute(client)

if err != nil {
    panic(err)
}
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Nota:** El valor neto de la transferencia debe ser igual a cero (el número total de_ **HBAR** _enviado por el remitente debe ser igual al número total de_ **HBAR** _recibido por el destinatario).
{% endhint %}

***

## Paso 2. Verificar el consenso alcanzado en la transacción de transferencia

Para verificar la transacción de transferencia alcanzada por la red, usted presentará una solicitud para obtener la recepción de la transacción. El estado del recibo le informará si la transacción fue exitosa (alcanzado consensus) o no.

{% tabs %}
{% tab title="Java" %}

```java
System.out.println("La transacción de transferencia fue: " +sendHbar.getReceipt(cliente).status);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Verificar la transacción alcanzado consenso
const transactionReceipt = await sendHbar.getReceipt(client);
consola. og("La transacción de mi cuenta a la nueva cuenta fue: " + transactionReceipt.status.toString());
```

{% endtab %}

{% tab title="Go" %}

```java
//Solicita la recepción de la transacción
transferRecipt, err := txResponse. etReceipt(client)

if err != nil {
    panic(err)
}

//Obtener el estado del consenso de transacción
transactionStatus := transferRecipt. tatus

fmt.Printf("El estado del consenso de transacciones es %v\n", transactionStatus)
```

{% endtab %}
{% endtabs %}

***

## Comprobar código ✅

Tu archivo de código completo debería verse algo así:

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
  Cliente,
  PrivateKey,
  AccountBalanceQuery,
  Transferencia,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
requerido("dotenv"). onfig();

async function environmentSetup() {
  // Acoge el ID de tu cuenta testnet de Hedera y la clave privada de tu . archivo nv
  const myAccountId = process.env. Y_ACCOUNT_ID;
  const myPrivateKey = process.env. PLAYLIST_BTN

  // Si no fuimos capaces de agarrarlo, deberíamos lanzar un nuevo error
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Las variables de entorno myAccountId y myPrivateKey deben estar presentes"
    );
  }

  // Crea tu conexión a la red Hedera
  const client = Client. orTestnet();

  //Establecer su cuenta como el operador del cliente
  cliente. etOperator(myAccountId, myPrivateKey);

  // Establecer la cuota máxima de transacción por defecto y el pago máximo de la consulta
  client.setDefaultMaxTransactionFee(new Hbar(100));
  cliente. etDefaultMaxQueryPayment(new Hbar(50));

  // Crear nuevas claves
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey. ublicKey;

  // Crear una nueva cuenta con 1, 00 tinybar starting balance
  const newAccountTransactionResponse = await new AccountCreateTransaction()
    . etKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    . xecute(client);

  // Obtener el nuevo ID de cuenta
  const getReceipt = await newAccountTransactionResponse. etReceipt(client);
  const newAccountId = getReceipt.accountId;

  consola. og("\nNuevo ID de cuenta: " + newAccountId);

  // Verificar el saldo de la cuenta
  const accountBalance = await new AccountBalanceQuery()
    . etAccountId(newAccountId)
    .execute(cliente);

  consola. og(
    "\nNuevo saldo de cuenta es: " +
      accountBalance.hbars. oTinybars() +
      " tinybars.
  );

  // Crear la transacción de transferencia
  const sendHbar = await new TransferTransaction()
    . ddHbarTransfer(myAccountId, Hbar.fromTinybars(-1000))
    .addHbarTransfer(newAccountId, Hbar.fromTinybars(1000))
    . xecute(client);

  // Verifica el consenso alcanzado por la transacción
  const transactionReceipt = await sendHbar.getReceipt(client);
  consola. og(
    "\nLa transacción de transferencia de mi cuenta a la nueva cuenta fue: " +
      transactionReceipt. tatus.toString()
  );
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Ir</summary>

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

#### Salida de muestreo:

```bash
Nueva cuenta ID: 0.0.4382765

El saldo de nueva cuenta es: 1000 tinybars.

La transacción de transferencia de mi cuenta a la nueva cuenta fue: SUCCESS
```

{% hint style="info" %}
¿Tienes una pregunta? [Preguntarlo en StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
