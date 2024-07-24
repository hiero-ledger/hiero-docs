# Configuración de entorno

## Summary

Esta guía de configuración de entorno le proporcionará los pasos necesarios para preparar su entorno de desarrollo para construir aplicaciones en la Red Hedera. Usted establecerá un nuevo directorio de proyecto, establezca un `. archivo de variable de entorno nv` para almacenar su ID de cuenta de Hedera Testnet y claves privadas y configurar su cliente de Hedera Testnet.

***

## Prerrequisitos

- Completado el paso [Introduction](introduction.md)

{% hint style="info" %}
_**Nota:** Siempre puedes comprobar "_[_Code Check ✅_](environment-set-up. d#code-check) _" sección en la parte inferior de cada página para ver el código completo si tiene problemas. También puedes publicar tu problema en el canal SDK respectivo en nuestra comunidad de Discord_ [_here_](http://hedera. om/discord) _o en el repositorio de GitHub_ [_here_](https://github.com/hashgraph/hedera-docs)_._
{% endhint %}

***

## **Paso 1: Crea el directorio de tu proyecto**

Abra su IDE de elección y siga los siguientes pasos para crear su nuevo directorio de proyecto.

{% tabs %}
{% tab title="Java Gradle" %}
Crear un nuevo proyecto de Gradle y nombrarlo `HederaExamples`. Añade las siguientes dependencias a tu archivo `build.gradle`.

{% code title="build.gradle " %}

```gradle
dependencias {

    implementation 'com.hedera.hashgraph:sdk:2.32.0'
    implementation 'io.grpc:grpc-netty-shaded:1.57.2'
    implementation 'io.github.cdimascio:dotenv-java:2.3.2'
    implementation 'org.slf4j:slf4j-nop:2.0.9'
    implementation 'com.google.code.gson:gson:2.8.8'
}
```

{% endcode %}
{% endtab %}

{% tab title="Java Maven" %}
Crear un nuevo proyecto Maven y llamarlo `HederaExamples`. Añade las siguientes dependencias a tu archivo `pom.xml`.

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
            <groupId>com. oogle.code. son</groupId>
            <artifactId>gson</artifactId>
            <version>2. .8</version>
        </dependency>
</dependencies>
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
Abre tu terminal y crea un directorio llamado _`hello-hedera-js-sdk`_. Después de crear el directorio del proyecto vaya al directorio ejecutando el siguiente comando:

```bash
mkdir hola-hedera-js-sdk && cd hello-hedera-js-sdk
```

Inicializa un proyecto _`node.js`_ en este nuevo directorio ejecutando el siguiente comando:

```bash
npm init -y
```

Esto es como debería ser tu consola después de ejecutar el comando:

```bash
{
  "name": "hello-hedera-js-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index. s",
  "scripts": {
    "test": "echo \"Error: no se especificó la prueba\" && salida 1"
  },
  "palabras clave": [],
  "autor": "",
  "license": "ISC"
}
```

{% endtab %}

{% tab title="Go" %}
Abre tu terminal y crea un directorio de proyecto llamado algo como `hedera-go-examples` para almacenar tu código fuente Go.

```bash
mkdir hedera-go-examples && cd hedera-go-examples
```

{% endtab %}
{% endtabs %}

***

## Paso 2: Instalar dependencias y SDKs

{% tabs %}
{% tab title="Java" %}
Crea una nueva clase Java y nombra algo como _`HederaExamples`_. Importa las siguientes clases para usar en tu ejemplo:

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

_**Nota:** Puedes instalar la última versión de Java SDK_ [_here_](https://github.com/hashgraph/hedera-sdk-java)_._
{% endtab %}

{% tab title="JavaScript" %}
Instala [JavaScript SDK](https://github.com/hashgraph/hedera-sdk-js) con tu gestor de paquetes favorito _`npm`_ o _`yarn`_ ejecutando el siguiente comando:

```bash
// Instalar el SDK JS de Hedera con NPM
npm install --save @hashgraph/sdk

// Instalar con Yarn
yarn add @hashgraph/sdk
```

Instala _`dotenv`_ con tu gestor de paquetes favorito. Esto permitirá a nuestro entorno de nodos utilizar su testnet _**ID de cuenta**_ y la _**clave privada**_ que almacenaremos en un archivo _`.env`_ siguiente.

```bash
// Instalar con NPM
npm install dotenv

// Instalar con Yarn
yarn add dotenv
```

Crea un archivo _`index.js`_ ejecutando el siguiente comando:

```bash
touch index.js
```

La estructura de tu proyecto debería ser algo así:

![](../.gitbook/assets/project\_directory.png)
{% endtab %}

{% tab title="Go" %}
Crea un archivo `hedera_examples.go` en el directorio raíz `hedera-go-examples`. Escribirás todo tu código en este archivo.

```bash
touch hedera_examples.go
```

Crea el archivo Go "module" ejecutando el comando de abajo. El archivo `go.mod` define las propiedades y dependencias del módulo y proporciona una manera de administrar el versionamiento para proyectos Go.

```go
go mod init hedera_examples.go
```

Instala el [Go SDK](https://github.com/hashgraph/hedera-sdk-go):

```go-module
ve a obtener github.com/hashgraph/hedera-sdk-go/v2@latest
```

Y el [paquete DotEnv](https://github.com/joho/godotenv):&#x20

```go-module
go get github.com/joho/godotenv
```

Importa los siguientes paquetes a tu archivo `hedera_examples.go`:

```go
paquete principal

import (
    "fmt"
    "os"

    "github.com/joho/godotenv"
    "github.com/hashgraph/hedera-sdk-go/v2"
)
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
_**Nota:** Se requiere **HBAR** de prueba para este siguiente paso. Por favor, sigue las instrucciones para crear una cuenta de Hedera en la_ [_portal_](https://docs.hedera.com/guides/getting-started/introduction) _antes de pasar al siguiente paso._
{% endhint %}

***

## Paso 3: \*\*Crea tu archivo .env \*\*

Crea el archivo `.env` en el directorio raíz de tu proyecto. El archivo `.env` almacena tus variables de entorno, como el ID de tu cuenta y la clave privada.&#x20

_**📣 Nota**: Si no has creado una cuenta, por favor hazlo_ [_here_](introducción.md) _antes de este paso._

{% tabs %}
{% tab title="Hedera Developer Portal" %}
Si creaste tu cuenta de testnet a través del portal del desarrollador, agarra el ID de cuenta de Hedera Testnet y la clave privada codificada en DER de tu [perfil de portal de Hedera](https://portal. edera.com/) (ver captura de pantalla abajo) y asignarlos a las variables de entorno `MY_ACCOUNT_ID` y `MY_PRIVATE_KEY` en tu archivo `.env`:&#x20

<figure><img src="../.gitbook/assets/DER portal (1).png" alt="" width="563"><figcaption><p>Portal de desarrollador de Hedera</p></figcaption></figure>

```markdown
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=302e020100300506032b657004220420ed5a93073.....
```

{% endtab %}

{% tab title="Hedera Faucet" %}
Alternativamente, si usaste el grifo para crear una cuenta de red de pruebas. agarrar su ID de cuenta de faucet y la clave privada (cómo exportar una clave privada desde MetaMask [here](https://support. etamask.io/hc/es-us/articles/360015289632-Cómo exportarlos-an-account-s-private-key)) y asignarlos a las variables de entorno `MY_ACCOUNT_ID` y `MY_PRIVATE_KEY` en tu archivo `.env`:

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

```
MY_ACCOUNT_ID=0.0.1234
MY_PRIVATE_KEY=0xfd154395435c81233b2fc906486f35e068...
```

{% endtab %}
{% endtabs %}

A continuación, cargará el ID de su cuenta y las variables clave privadas desde el archivo `.env` creado en el paso anterior.

{% tabs %}
{% tab title="Java" %}
Dentro del método _`main`_, añade el ID de tu cuenta de testnet y la clave privada desde el archivo de entorno.

{% code title="HederaExamples.java" %}

```java
public class HederaExamples {

    public static void main(String[] args) {

        //Grab your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));  
    }
}
```

{% endcode %}
{% endtab %}

{% tab title="JavaScript" %}
{% code title="index.js" %}

```javascript
const { Cliente, PrivateKey, AccountCreateTransaction, AccountBalanceQuery, Hbar, TransferTransaction } = require("@hashgraph/sdk");
require('dotenv'). onfig();

async function environmentSetup() {

    //Gcope su ID de cuenta testnet de Hedera y clave privada de su . nv file
    const myAccountId = process.env. Y_ACCOUNT_ID;
    const myPrivateKey = process.env. PLAYLIST_BTN

    // Si no fuimos capaces de agarrarlo, deberíamos lanzar un nuevo error
    si (! yAccountId || ! yPrivateKey) {
        arrojar nuevo Error("Las variables de entorno MY_ACCOUNT_ID y MY_PRIVATE_KEY deben estar presentes");
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

    //Carga el . archivo nv y arroja un error si no puede cargar correctamente las variables de ese archivo
    err := godotenv. oad(".env")
    if err != nil {
        panic(fmt. rrorf("No se pueden cargar las variables de entorno desde el archivo .env. Error:\n%v\n", err))
    }

    //Acoge tu ID de cuenta testnet y clave privada del . archivo nv
    myAccountId, err := hedera.AccountIDFromString(os. etenv("MY_ACCOUNT_ID"))
    if err ! nil {
        pánico (err)
    }

    myPrivateKey, err := hedera. rivateKeyFromString(os. etenv("MY_PRIVATE_KEY"))
    if err ! nil {
        panic(err)
    }

    //Imprime tu ID de cuenta testnet y tu clave privada en la consola para asegurarte de que no hubo ningún error
    fmt. rintf("El ID de cuenta es = %v\n", myAccountId)
    fmt. rintf("La clave privada es = %v\n", myPrivateKey)
}
```

{% endcode %}

En tu terminal, introduce el siguiente comando para crear tu archivo `go.mod`. Este módulo se utiliza para rastrear dependencias y es necesario.

```go-module
go mod init hedera_examples.go
```

Ejecute su código para ver su ID de cuenta testnet y su clave privada impresa en la consola.

```go-module
go run hedera_examples.go
```

{% endtab %}
{% endtabs %}

***

## Paso 4: Crea tu cliente de Hedera Testnet

Cree una red de Hedera Testnet [client](../support-and-community/glossary.md#client) y configure la información del operador utilizando el ID de la cuenta testnet y la clave privada para la autorización de las comisiones de transacción y consulta. El _operator_ es la cuenta por defecto que pagará las comisiones de transacción y consulta en HBAR. Necesitará firmar la transacción o consulta con la clave privada de esa cuenta para autorizar el pago. En este caso, el ID del operador es tu `ID de cuenta**.**` testnet y la clave privada del operador es la clave privada correspondiente de la cuenta testnet.

{% hint style="warning" %}
Para evitar encontrar el error **`INSUFFICIENT_TX_FEE`** mientras realizas transacciones, puedes ajustar el límite máximo de cuota de transacción a través del método **`.setDefaultMaxTransactionFee()`**. Del mismo modo, el pago máximo de la consulta puede ser ajustado utilizando el método **`.setDefaultMaxQueryPayment()`** .
{% endhint %}

<details>

<summary>🚨 How to resolve the <em>INSUFFIENT_TX_FEE</em> error</summary>

Para resolver este error, debe ajustar la tarifa máxima de transacción a un valor más alto adecuado para sus necesidades.

Aquí hay un sencillo ejemplo añadido a tu código:

```javascript
const maxTransactionFee = new Hbar(XX); // reemplaza XX con la comisión deseada en Hbar
```

En este ejemplo, puedes establecer `maxTransactionFee` a cualquier valor mayor que 5 HBAR (o 500,000, 00 tinybars) para evitar el error "_INSUFICIENT\_TX\_FEE_" para transacciones mayores a 5 HBAR. Por favor, reemplace `XX` con el valor deseado.

Para implementar esta nueva comisión máxima de transacción, utiliza el método `setDefaultMaxTransactionFee()` como se muestra a continuación:

```javascript
client.setDefaultMaxTransactionFee(maxTransactionFee);
```

</details>

{% tabs %}
{% tab title="Java" %}

```java
//Crear su cliente Hedera Testnet
Cliente cliente cliente = Client.forTestnet();

//Establecer su cuenta como el operador de cliente
cliente. etOperator(myAccountId, myPrivateKey);

//Establecer la tarifa máxima de transacción por defecto (en la barra de comandos)
cliente. etDefaultMaxTransactionFee(new Hbar(100));

//Establecer el pago máximo para consultas (en Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="JavaScript" %}

```javascript
//Create your Hedera Testnet client
const client = Client.forTestnet();

//Set your account as the client's operator
client. etOperator(myAccountId, myPrivateKey);

//Establecer la tarifa máxima de transacción por defecto (en la barra de comandos)
cliente. etDefaultMaxTransactionFee(new Hbar(100));

//Establecer el pago máximo para consultas (en Hbar)
client.setMaxQueryPayment(new Hbar(50));
```

{% endtab %}

{% tab title="Go" %}

```go
//Crear su cliente testnet
cliente := hedera.ClientForTestnet()
client.SetOperator(myAccountId, myPrivateKey)

// Establecer la cuota máxima de transacción predeterminada
cliente. etDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera.HbarUnits.Hbar))

// Establecer pago máximo de consulta
client.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
```

{% endtab %}
{% endtabs %}

**¡Tu entorno de proyecto ahora está configurado para enviar transacciones y consultas a la red de pruebas de Hedera con éxito!**

A continuación, aprenderás a [crear una cuenta](create-an-account.md).

## Comprobar código :white\_check\_mark:

***

<details>

<summary>Java</summary>

<pre class="language-java" data-title="HederaExamples.java"><code class="lang-java">importar com.hedera.hashgraph.sdk.Hbar;
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
import java.util.concurrent. imeoutExcepto;

clases públicas HederaExamples {

        public static void main(String[] args) {
                
        //Gaking your Hedera Testnet account ID and private key
        AccountId myAccountId = AccountId. romString(Dotenv.load().get("MY_ACCOUNT_ID"));+
        PrivateKey myPrivateKey = PrivateKey.fromString(Dotenv.load(). et("MY_PRIVATE_KEY"));
        //Crear su cliente Hedera Testnet
        
<strong>        Cliente = Cliente. orTestnet();
</strong>        clientes. etOperator(myAccountId, myPrivateKey);
        
        // Establecer tarifa máxima de transacción por defecto & máxima de pago de consulta
        cliente. etDefaultMaxTransactionFee(new Hbar(100)); 
        cliente. etMaxQueryPayment(new Hbar(50)); 
        
        System. ut.println("Configuración del cliente completa.");
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

async function environmentSetup() {
  //GTI su ID de cuenta testnet de Hedera y clave privada de su . nv file
  const myAccountId = process.env.MY_ACCOUNT_ID;
  const myPrivateKey = process.env. Y_PRIVATE_KEY;

  // Si no fuimos capaces de agarrarlo, deberíamos lanzar un nuevo error
  if (!myAccountId || ! yPrivateKey) {
    throw new Error(
      "Environment variables MY_ACCOUNT_ID and MY_PRIVATE_KEY must be present"
    );
  }
  
  //Crear su cliente Hedera Testnet
  const client = Cliente. orTestnet();

  //Configura tu cuenta como el operador del cliente
  cliente. etOperator(myAccountId, myPrivateKey);

  //Establecer la tarifa máxima de transacción por defecto (en la barra de comandos)
  cliente. etDefaultMaxTransactionFee(new Hbar(100));

  //Set the maximum payment for queries (in Hbar)
  client. etDefaultMaxQueryPayment(new Hbar(50));
  
  console.log("Configuración del cliente completa.");
}
environmentSetup();
```

{% endcode %}

</details>

<details>

<summary>Ir</summary>

{% code title="hedera_examples.go" %}

```go
paquete principal

import (
	"fmt"
	"os"

	"github.com/hashgraph/hedera-sdk-go/v2"
	"github. om/joho/godotenv"
)

func main() {

	//Carga el . archivo nv y arroja un error si no puede cargar correctamente las variables de ese archivo
	err := godotenv. oad(".env")
	if err != nil {
		panic(fmt.Errorf("Imposible cargar las variables de entorno desde el archivo .env. Error:\n%v\n", err))
	}

	//Acoge el ID de tu cuenta de testnet y la clave privada del . archivo nv
	myAccountId, err := hedera.AccountIDFromString(os. etenv("MY_ACCOUNT_ID"))
	if err != nil {
		panic(err)
	}

	myPrivateKey, err := hedera.PrivateKeyFromString(os. etenv("MY_PRIVATE_KEY"))
	if err != nil {
		panic(err)
	}

	//Crear su cliente testnet
	cliente := hedera.ClientForTestnet()
	cliente. etOperator(myAccountId, myPrivateKey)

	// Establecer la tarifa máxima de transacción por defecto y el pago máximo de la consulta
	cliente.SetDefaultMaxTransactionFee(hedera.HbarFrom(100, hedera. barUnits.Hbar))
	cliente.SetDefaultMaxQueryPayment(hedera.HbarFrom(50, hedera.HbarUnits.Hbar))
	
	fmt.Println(“Configuración del cliente completa.”)
}
```

{% endcode %}

</details>

{% hint style="info" %}
¿Tienes una pregunta? [Preguntarlo en StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph)
{% endhint %}

***

**Colaboradores:** [fabianstraubinger99](https://github.com/fabianstraubinger99)
