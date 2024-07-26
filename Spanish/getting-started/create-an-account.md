# Crear una cuenta

## Summary

En esta sección, aprenderás a hacer una simple cuenta de Hedera. Las cuentas de hedera son el punto de entrada por el cual puedes interactuar con las [APIs de Hedera](../sdks-and-apis/hedera-api/). Las cuentas tienen un saldo de HBAR utilizado para pagar llamadas API para los diversos tipos de transacciones y consultas.

***

## Prerrequisitos

- Completado el paso [Introduction](introduction.md)
- Completado el paso [Configuración del entorno](environment-set-up.md.

***

## Paso 1: Importar módulos

Importa los siguientes módulos a tu archivo de código.

{% tabs %}
{% tab title="Java" %}

```java
import com.hedera.hashgraph.sdk.AccountId;
import com.hedera.hashgraph.sdk.HederaPreCheckStatusException;
import com.hedera.hashgraph.sdk.HederaReceiptStatusException;
import com.hedera.hashgraph.sdk.PrivateKey;
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.TransactionResponse;
import com. edera.hashgraph.sdk.PublicKey;
import com.hedera.hashgraph.sdk.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.AccountBalance;
import io.github.cdimascio.dotenv.Dotenv;
import java.util.concurrent.TimeoutException;
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
const { Client, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar } = require("@hashgraph/sdk");
require("dotenv").config();
```

{% endtab %}

{% tab title="Go" %}

```go
import (
    "fmt"
    "os"

    "github.com/hashgraph/hedera-sdk-go/v2"
    "github.com/joho/godotenv"
)
```

{% endtab %}
{% endtabs %}

***

## Paso 2: Generar claves para la nueva cuenta

Generar una clave privada y pública para asociar con la cuenta que va a crear.

{% tabs %}
{% tab title="Java" %}

```java
//Create your Hedera Testnet client
//Client client = Client.forTestnet();
//cliente. etOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

// Generar un nuevo par de claves
newAccountPrivateKey = PrivateKey.generateED25519();
PublicKey newAccountPublicKey = newAccountPrivateKey.getPublicKey();
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//const client = Client.forTestnet();
//client.setOperator(myAccountId, myPrivateKey);
//-----------------------<enter code below>--------------------------------------

//Create new keys
const newAccountPrivateKey = PrivateKey.generateED25519(); 
const newAccountPublicKey = newAccountPrivateKey.publicKey;
```

{% endtab %}

{% tab title="Go" %}

```go
//client := hedera.ClientForTestnet()
//client.SetOperator(myAccountId, myPrivateKey)
//-----------------------<enter code below>--------------------------------------

//Genera nuevas claves para la cuenta crearás
newAccountPrivateKey, err := hedera. rivateKeyGenerateEd25519()

if err != nil {
  panic(err)
}

newAccountPublicKey := newAccountPrivateKey.PublicKey()
```

{% endtab %}
{% endtabs %}

***

## Paso 3: Crear una nueva cuenta

Crea una nueva cuenta usando _`AccountCreateTransaction()`_. Utilice la clave pública creada en el paso anterior para ingresar en el campo _`setKey()`_. Esto asociará el par de claves generado en el paso anterior con la nueva cuenta. La clave pública de la cuenta es visible para el público y se puede ver en un explorador de nodo espejo. La clave privada se utiliza para autorizar transacciones relacionadas con la cuenta como transferir _HBAR_ o tokens de esa cuenta a otra cuenta. La cuenta tendrá un saldo inicial de _1,000 tinybars_ financiado con su cuenta testnet creada por el portal Hedera.

Puede ver las transacciones enviadas con éxito a la red obteniendo el ID de transacción y buscando por ellas en un explorador de nodos espejos. El ID de la transacción está compuesto por el ID de la cuenta que pagó por la transacción y la hora de inicio válida de la transacción, por ejemplo, _`0.0. 234@1609348302`_<mark style="color:blue;">.</mark> La hora de inicio válida de la transacción es la hora en que la transacción comienza a ser válida en la red. El SDK genera automáticamente un ID de transacción para cada transacción detrás de las escenas.

{% tabs %}
{% tab title="Java" %}

```java
//Crear nueva cuenta y asignar la clave pública
TransactionResponse newAccount = new AccountCreateTransaction()
     .setKey(newAccountPublicKey)
     .setInitialBalance(Hbar.fromTinybars(1000))
     .execute(client);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Crear una nueva cuenta con 1000 tinybar empezando saldo
const newAccount = await new AccountCreateTransaction()
    .setKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    .execute(client);
```

{% endtab %}

{% tab title="Go" %}

```go
//Crear nueva cuenta y asignar la clave pública
newAccount, err := hedera.NewAccountCreateTransaction().
    SetKey(newAccountPublicKey).
    SetInitialBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
    Execute(client)
```

{% endtab %}
{% endtabs %}

## Paso 4: Obtener el nuevo ID de cuenta

El ID de _cuenta_ para la nueva cuenta es devuelto en el recibo de la transacción que creó la cuenta. El recibo proporciona información sobre la transacción como si fue exitosa o no y cualquier nueva entidad IDs que fueron creados. Las entidades incluyen cuentas, contratos inteligentes, tokens, archivos, temas y transacciones programadas. El ID de _cuenta_ está en formato x.y.z donde z es el número de cuenta. Los valores anteriores (x y y) por defecto a cero hoy y representan el fragmento y número de reino respectivamente. Su nuevo ID de _cuenta_ debería resultar en algo como _`0.0.1234`_.

{% tabs %}
{% tab title="Java" %}

```java
// Obtener el nuevo ID de cuenta
AccountId newAccountId = newAccount.getReceipt(client).accountId;

//Registrar el ID de la cuenta
System.out.println("New account ID is: " +newAccountId);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// Obtener el nuevo ID de cuenta
const getReceipt = await newAccount.getReceipt(client);
const newAccountId = getReceipt. ccountId;

//Log el ID de la cuenta
console.log("El nuevo ID de la cuenta es: " +newAccountId);
```

{% endtab %}

{% tab title="Go" %}

```go
//Solicita el recibo de la transacción
, err := newAccount. etReceipt(cliente)
si es error! nil {
    panic(err)
}

//Obtener el nuevo ID de cuenta del recibo
newAccountId := *receipt. ccountID

//Registrar el ID de la cuenta
fmt.Printf("El nuevo ID de la cuenta es %v\n", newAccountId)
```

{% endtab %}
{% endtabs %}

***

## Paso 5: Verifique el nuevo saldo de la cuenta

A continuación, enviará una consulta a la red de pruebas de Hedera para devolver el saldo de la nueva cuenta usando el nuevo _account ID_. El saldo de la cuenta corriente para la nueva cuenta debe ser de 1,000 _tinybars_. Obtener el saldo de una cuenta es gratis hoy.

{% tabs %}
{% tab title="Java" %}

```java
//Compruebe el saldo de la nueva cuenta
AccountBalance de cuenta = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("Nuevo saldo de cuenta: " +accountBalance.hbars);
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Verificar el saldo de la cuenta
const accountBalance = await new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

console.log("The new account balance is: " +accountBalance.hbars.toTinybars() +" tinybar.");
```

{% endtab %}

{% tab title="Go" %}

```go
//Crear consulta de saldo de cuenta
consulta := hedera.NewAccountBalanceQuery().
     SetAccountID(newAccountId)

//Sign with client operator private key and submit the query to a Hedera network
accountBalance, err := consulta. xecute(client)
if err != nil {
    panic(err)
}

//Imprime el balance de tinybars
fmt. rintln("El saldo de cuenta para la nueva cuenta es ", cuenta Balance.Hbars.AsTinybar())
```

{% endtab %}
{% endtabs %}

{% hint style="success" %}
:star: **¡Felicidades! Has completado con éxito lo siguiente:**

- Creó una nueva cuenta de Hedera con un saldo inicial de 1.000 tinybars.
- Obtuvo el nuevo ID de cuenta solicitando la recepción de la transacción.
- Verificó el saldo inicial de la nueva cuenta enviando una consulta a la red.

Ahora estás listo para transferir algo de HBAR a la nueva cuenta :money\_mouth:!
{% endhint %}

***

## Comprobar código :white\_check\_mark:

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
  Cliente,
  PrivateKey,
  AccountBalanceQuery,
  AccountCreateTransaction,
} = require("@hashgraph/sdk");
requerido("dotenv"). onfig();

async function environmentSetup() {
  // Acoge el ID de tu cuenta testnet de Hedera y la clave privada de tu . nv file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env. PLAYLIST_BTN

  // Si no fuimos capaces de agarrarlo, deberíamos lanzar un nuevo error
  if (myAccountId == null || myPrivateKey == null) {
    throw new Error(
      "Las variables de entorno myAccountId y myPrivateKey deben estar presentes"
    );
  }

  // Crea tu conexión a la Red Hedera
  const client = Client. orTestnet();
  client.setOperator(myAccountId, myPrivateKey);

  //Set the default maximum transaction fee (in Hbar)
  client. etDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client. etDefaultMaxQueryPayment(new Hbar(50));

  // Crear nuevas claves
  const newAccountPrivateKey = PrivateKey.generateED25519();
  const newAccountPublicKey = newAccountPrivateKey. ublicKey;

  // Crear una nueva cuenta con 1, 00 tinybar starting balance
  const newAccount = await new AccountCreateTransaction()
    . etKey(newAccountPublicKey)
    .setInitialBalance(Hbar.fromTinybars(1000))
    . xecute(client);

  // Obtener el nuevo ID de cuenta
  const getReceipt = await newAccount. etReceipt(client);
  const newAccountId = getReceipt.accountId;
  
  consola. og("\nNuevo ID de cuenta: " + newAccountId);

  // Verificar el saldo de la cuenta
  const accountBalance = await new AccountBalanceQuery()
    . etAccountId(newAccountId)
    .execute(cliente);

  consola. og(
    "El nuevo saldo de cuenta es: " +
      contabilidad. bars.toTinybars() +
      " tinybar."
  );

  return newAccountId;
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Ir</summary>

```go
paquete principal

import (
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github.com/joho/godotenv"
)

func main() {

	//Carga el . archivo nv y arroja un error si no puede cargar correctamente las variables de ese archivo
	err := godotenv. oad(".env")
	if err != nil {
		panic(fmt. rrorf("No se pueden cargar las variables de entorno desde el archivo .env. Error:\n%v\n", err))
	}

	//Acoge el ID de tu cuenta de testnet y la clave privada del . nv file
	myAccountId, err := hedera.AccountIDFromString(os.Getenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera. rivateKeyFromString(os. etenv("MY_PRIVATE_KEY"))
	si err ! nil {
		panic(err)
	}

	//Imprime el ID de tu cuenta testnet y la clave privada en la consola para asegurarse de que no hubo ningún error
	fmt. rintf("\nEl ID de la cuenta es = %v\n", myAccountId)
	fmt. rintf("La clave privada es = %v", myPrivateKey)

	//Crear su cliente de red de pruebas
	cliente := hedera.ClientForTestnet()
	cliente. etOperator(myAccountId, myPrivateKey)

	// Establecer la tarifa máxima de transacción por defecto y el pago máximo de la consulta
	cliente. SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))
	client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera. barUnits.Hbar))

	//Generar nuevas claves para la cuenta creará
	newAccountPrivateKey, err := hedera. rivateKeyGenerateEd25519()
	if err != nil {
		panic(err)
	}

	newAccountPublicKey := newAccountPrivateKey. ublicKey()

	//Crear nueva cuenta y asignar la clave pública
	newAccount, err := hedera.NewAccountCreateTransaction().
		SetKey(newAccountPublicKey).
		SetInitialBalance(hedera.HbarFrom(1000, hedera.HbarUnits.Tinybar)).
		Execute(client)

	//Request la recepción de la transacción
	recibo, err := newAccount. etReceipt(client)
	if err != nil {
		panic(err)
	}

	//Obtener el nuevo ID de cuenta desde el recibo
	newAccountId := *receipt. ccountID

	//Imprime el nuevo ID de cuenta en la consola
	fmt.Println("\n")
	fmt. rintf("Nueva cuenta ID: %v\n", newAccountId)

	//Crear la consulta de saldo de cuenta
	consulta := hedera.NewAccountBalanceQuery().
		SetAccountID(newAccountId)

	//Sign con la clave privada del operador del cliente y envíe la consulta a una red Hedera
	accountBalance, err := query. xecute(client)
	si err != nil {
		panic(err)
	}

	//Imprime el balance de tinybars
	fmt. rintln("Nuevo saldo de cuenta para la nueva cuenta es", accountBalance.Hbars.AsTinybar())
}

```

</details>

#### Salida de muestreo:

```bash
Nueva cuenta ID: 0.0.13724748
Nuevo saldo de cuenta: 1000 tinybars.
```

{% hint style="info" %}
¿Tienes una pregunta? [Preguntarlo en StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}
