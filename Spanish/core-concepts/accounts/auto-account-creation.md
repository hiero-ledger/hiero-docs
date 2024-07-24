# Creación de cuenta automática

La creación de cuenta automática es un flujo único en el que las aplicaciones, como las carteras y los intercambios, pueden crear "cuentas" de usuario al instante, incluso sin una conexión a Internet. Las aplicaciones pueden hacer esto generando un \*\*alias de cuentas. \* El formato de ID de cuenta de alias utilizado para especificar el alias de cuenta en las transacciones de Hedera incluye el ID del fragmento, ID del reino, y alias de cuenta <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark>. Este es un identificador de cuenta alternativo comparado con el formato estándar de número de cuenta <mark style="color:purple;">`<shardId>.<realmId>.<accountNum>`</mark><mark style="color:blue;">.</mark>

El alias de la cuenta puede ser uno de los tipos soportados:

<details>

<summary>Llave pública</summary>

El alias de clave pública puede ser un tipo de clave pública ED25519 o ECDSA secp256k1. \
\
**Example**\
\
ECDSA secp256k1 Public Key:\
`02d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
DER Encoded ECDSA secp256k1 Public Key Alias:\
`302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
ECDSA secp256k1 Public Key Alias Account ID: \
`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`
\
\
\
\
EDDSA ED25519 Public Key:\
`1a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
DER Encoded EDDSA ED25519 Public Key Alias:\
`302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
EDDSA ED25519 Public Key Alias Account ID: \
`0.0.302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`

</details>

<details>

<summary>Dirección EVM</summary>

El alias de dirección EVM se crea mediante el uso de los 20 bytes más adecuados del hash `Keccak-256` de 32 bytes de una clave pública `ECDSA secp256k1`. Este cálculo está en la forma descrita por el [Paper Amarillo Ethereum](https://ethereum.github.io/yellowpaper/paper.pdf). La dirección EVM no es equivalente a la clave pública ECDSA. \
\
El formato aceptable para las transacciones de Hedera es el ID de la cuenta de alias de la dirección EVM. El formato aceptable para direcciones públicas de Ethereum para denotar una dirección de cuenta es la dirección pública codificada hexadecimalmente. \
\
**Ejemplo**\
\
Dirección EVM: `b794f5ea0ba39494ce839613fffba74279579268`\
\
Dirección EVM codificada: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
ID de la cuenta EVM: `0. .b794f5ea0ba39494ce839613fffba74279579268`

</details>

El formato <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> solo es aceptable cuando se especifica en los tipos de transacción `TransferTransaction`, `AccountInfoQuery` y `AccountBalanceQuery`. Si este formato se utiliza para especificar una cuenta en cualquier otro tipo de transacción, la transacción no tendrá éxito.&#x20

Propuesta de Mejora de Referencia de Hedera: [HIP-583](https://hips.hedera.com/hip/hip-583)

## **Auto cuenta Creation Flow**

### **1. Crear un alias de cuenta**

Crea un alias de cuenta y conviértalo al formato de ID de cuenta de alias. El formato de ID de cuenta de alias requiere añadir el número de fragmento y los números de reino a los alias de la cuenta. Esta forma de cuenta es puramente una cuenta local, es decir, no está registrada en la red Hedera.&#x20

### **2. Depositar fichas a la cuenta alias ID de alias de cuenta**

Una vez creado el ID de cuenta de alias, las aplicaciones pueden crear una transacción para transferir tokens al ID de cuenta de alias para los usuarios. Los usuarios pueden transferir fichas HBAR, fungibles o no fungibles personalizadas a la cuenta de alias ID. Esto activará la creación de la cuenta oficial de Hedera. Cuando se utiliza el flujo de creación de cuenta automática, el primer token transferido al ID de cuenta de alias se asocia automáticamente a la cuenta.

La transferencia inicial de tokens al ID de cuenta alias hará algunas cosas:

1. El sistema creará primero una transacción iniciada por el sistema para crear una nueva cuenta en Hedera. Esto es para crear una nueva cuenta en Hedera oficialmente. Esta transacción ocurre un nanosegundo antes de la transacción de transferencia de tokens.&#x20
2. Después de crear la nueva cuenta, el sistema asignará un nuevo número de cuenta y el alias de la cuenta se almacenará con la cuenta en el campo alias en el estado. La nueva cuenta tendrá una "cuenta creada automáticamente" establecida en el campo memo de la cuenta.
   - Para cuentas creadas usando el alias de la clave pública, la cuenta será asignada a la clave pública de la cuenta que coincida con la clave pública del alia.&#x20
   - Para una cuenta creada a través de un alias de una dirección EVM, la cuenta no tendrá una clave pública de la cuenta, creando una [cuenta vacía](auto-account-creation.md#auto-account-creation-evm-address-alias).
3. Una vez creada oficialmente la nueva cuenta, la transacción de transferencia de token instanciada por el usuario transferirá los tokens a la nueva cuenta.&#x20
4. La cuenta especificada para pagar por las comisiones de transacción de transferencia de token también se cobrará las comisiones de transacción de la cuenta en tinybar.&#x20

Las interacciones anteriores introducen el concepto de [transacciones padre e hijo](../transactions-and-queries.md#nested-transactions). La transacción padre es la que representa la transferencia de tokens desde la cuenta del remitente a la cuenta de destino. La transacción hijo es la transacción que el sistema inició para crear la cuenta. Este concepto es importante ya que el registro de transacción padre o el recibo no devolverán el nuevo número de cuenta ID. Debe obtener el registro de la transacción o la recepción de la transacción hija. Las transacciones padre e hijo compartirán el mismo ID de la transacción, excepto que la transacción hijo tenga un valor añadido nonce añadido. &#x20

{% hint style="info" %}
**transacción padre**: la transacción responsable de transferir los tokens a la cuenta de destino ID de la cuenta de alias

**transacción secundaria**: la transacción que creó la nueva cuenta
{% endhint %}

### **3. Obtener el nuevo número de cuenta**

Puede obtener el nuevo número de cuenta de cualquiera de las siguientes maneras:

- Solicitar el registro o recibo de transacción padre y establecer el registro booleano de transacción secundaria igual a verdad.&#x20
- Solicitar el recibo de la transacción o el registro de la cuenta crea la transacción usando el ID de transacción de la transacción padre e incrementando el valor nonce de 0 a 1.
- Especifique el alias de cuenta ID en una solicitud de transacción `AccountInfoQuery`. La respuesta devolverá el ID del número de cuenta de la cuenta.
- Inspeccionar la lista de transacciones de transacción padre para la cuenta con una transferencia igual al valor de transferencia de token.

## Creación de cuenta automática: alias de dirección EVM

Las cuentas creadas con el flujo de creación de cuenta automática usando un [alias de dirección EVM](account-properties.md#account-alias-evm-address) dan como resultado la creación de una **cuenta vacía**. Las cuentas vacías son cuentas incompletas con un número de cuenta y alias pero no con una clave de cuenta. La cuenta vacía puede aceptar transferencias de fichas a la cuenta en este estado. No puede transferir fichas desde la cuenta o modificar las propiedades de cualquier cuenta hasta que la clave de cuenta haya sido añadida y la cuenta esté completa.

Para actualizar la cuenta hueca en una cuenta completa, la cuenta vacía debe especificarse como una cuenta de pago de transacción para una transacción de Hedera. También debe firmar la transacción con la clave privada ECDSA correspondiente al alias de la dirección EVM. Cuando la cuenta vacía se convierte en una cuenta completa, puede usar la cuenta para pagar comisiones de transacción o actualizar propiedades de la cuenta como lo haría con cuentas de Hedera regulares.

## Ejemplos

<details>

<summary>Auto-crear una cuenta usando un alias de clave pública</summary>

:black\_circle: [Java](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AccountAliasExample.java) \
:black\_circle: [JavaScript](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/account-alias.js) \
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/alias\_id\_example/main.go) &#x20

</details>

<details>

<summary>Auto-crear una cuenta usando una dirección EVM (dirección pública) alias</summary>

:black\_circle: [Java ](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AutoCreateAccountTransactionExample.java)\
:black\_circle: [JavaScript ](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/transfer-using-evm-address.js)\
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/account\_create\_token\_transfer/main.go)

</details>
