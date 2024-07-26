# Propiedades de la cuenta

## ID de cuenta

El ID de la cuenta es el ID de la entidad de la cuenta en la red Hedera. The account ID includes the **shard number**, **realm number**, and an **account** <mark style="color:purple;">`<shardNum>.<realmNum>.<account>`</mark>**.** The account ID is used to specify the account in all Hedera transactions and queries. Puede haber más de un ID de cuenta que puede representar una cuenta.\

<details>

<summary>Número</summary>

Formato: **`shardNum`**`.realmNum.account`

El número del fragmento es el número del fragmento en el que existe la cuenta. Un fragmento es una partición de los datos recibidos por los nodos que participan en un fragmento dado.  Hoy en día, Hedera opera en un solo fragmento. Este valor permanecerá cero hasta que Hedera opere en más de un fragmento. Este valor no es negativo y es 8 bytes.\
\
Predeterminado: `0`

</details>

<details>

<summary>Número de realm</summary>

Formato: `shardNum.`**`realmNum`**`.account`\
\
El número del reino es el número del reino que existe en un fragmento dado. Hoy en día, Hedera opera en un solo reino. Este valor permanecerá cero hasta que Hedera opere en más de un fragmento. Este valor no es negativo y es de 8 bytes. La cuenta sólo puede pertenecer a un reino precisamente. El ID del reino puede ser reutilizado en otros fragmentos. \
\
Predeterminado: `0`

</details>

<details>

<summary><strong>Cuenta</strong></summary>

Formato: `shardNum.realmNum.`**`account`**

La `cuenta` puede ser una de las siguientes:\
\
:black\_circle: [Número de cuenta](account-properties.md#account-number) \
:black\_circle: [Alias de cuenta](account-properties.md#account-alias)

</details>

### **Número de cuenta**

Cada cuenta de Hedera tiene un **número de cuenta** proporcionado por el sistema cuando se crea la cuenta.  Un número de cuenta es un número no negativo de 8 bytes. Puede utilizar el número de cuenta para especificar la cuenta en todas las transacciones de Hedera y solicitudes de consultas. Los números de cuenta son únicos e inmutables. El número de cuenta para una cuenta recién creada se devuelve en el recibo de transacción o registro de transacción para el ID de transacción que creó la cuenta. El ID del número de cuenta tiene el siguiente formato  <mark style="color:purple;">`<shardNum>.<realmNum>.<accountNum>`</mark><mark style="color:blue;">.</mark>

<table><thead><tr><th width="199">ID de número de cuenta</th><th width="523.3333333333333">Descripción</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>El número 10 de cuenta en formato de ID de número de cuenta.</td></tr></tbody></table>

#### Número de cuenta alias

Todas las cuentas pueden tener un \*\*alias de número de cuenta. \* Un alias de número de cuenta es una forma codificada por hex del número de cuenta con 20 bytes de ceros. Es una dirección compatible con EVM que hace referencia a la cuenta de Hedera. El alias del número de cuenta no contiene el ID del fragmento y el ID del reino.&#x20

Esta propiedad de cuenta no está almacenada en estado de nodo de consenso. You will not see this value returned when querying the consensus nodes for the account object and inspecting the account alias field.\
\
The mirror node will calculate the account number alias from the account number. El alias de número de cuenta es calculado y devuelto en la cuenta API REST sólo cuando la cuenta no tiene un alias de cuenta existente. Por ejemplo, si la cuenta fue creada a través del flujo de [creación de cuenta automática](auto-account-creation.md) usando un alias de cuenta, el alias de número de cuenta no será rellenado. Si la cuenta fue creada normalmente entonces el campo alias de la cuenta almacenará el alias de la cuenta.

<table><thead><tr><th width="175">ID de cuenta</th><th>Ejemplo de alias de número de cuenta</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>El valor de codificación hexadecimal para 10 es "0a."<br><br><code>0000000000000000000000000000000000000000000000000a</code></td></tr></tbody></table>

### Alias de cuenta

Algunas cuentas de Hedera tendrán un **alias de cuenta**. Los alias de la cuenta son un puntero al objeto de la cuenta, además de ser identificados por el número de cuenta. Los alias de la cuenta se asignan a la cuenta cuando se crea la cuenta mediante el flujo [creación de cuenta automática](./#auto-account-creation). La red no genera el alias de la cuenta; en su lugar, el usuario especifica el alias de la cuenta al crear la cuenta. Esta propiedad será nula, si se crea una cuenta a través del flujo normal de creación de la cuenta. Los alias de la cuenta son únicos e inmutables. El ID de alias de cuenta tiene el siguiente formato  <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark><mark style="color:blue;">.</mark>

Este formato sólo es aceptable cuando se especifica en `TransferTransaction`, `AccountInfoQuery` y `AccountBalanceQuery`. Si este formato se utiliza para hacer referencia a una cuenta en cualquier otro tipo de transacción la transacción no tendrá éxito.&#x20

El `alias` puede ser uno de los siguientes tipos de alias:

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>               Clave pública</td><td><a href="account-properties.md#public-key-account-alias">#public-key-account-alias</a></td></tr><tr><td>             Dirección EVM</td><td><a href="account-properties.md#public-key-account-alias">#public-key-account-alias</a></td></tr></tbody></table>

#### Cuenta de clave pública alias

La clave pública alias de cuenta es la clave pública de un tipo de clave ECDSA secp256k1 o ED25519. El formato de ID de la clave pública es <mark style="color:blue;">`<shardNum>.<realmNum>.<alias>`</mark> donde <mark style="color:blue;">`alias`</mark> es la clave pública. Este formato se crea usando los bytes de una simple clave pública criptográfica soportada por Hedera. El alias de la cuenta de clave pública no es necesario para que coincida con la [clave pública](account-properties. d#keys) usados para determinar la firma criptográfica necesaria para firmar transacciones para la cuenta.

<details>

<summary>Example Public Key Alias Account ID</summary>

`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`

</details>

#### **Alias de la cuenta EVM**

Un alias de cuenta de dirección EVM es el más derecho de 20 bytes del hash `Keccak-256` de 32 bytes de la clave pública `ECDSA` de la cuenta. Este cálculo está en la forma descrita por el [Paper Amarillo Ethereum](https://ethereum.github.io/yellowpaper/paper.pdf). Tenga en cuenta que el id de recuperación no es formalmente parte de la clave pública y no está incluido en el hash. Esto se calcula en los nodos de consenso usando la clave `ECDSA` proporcionada en el flujo de [creación de cuenta automática](auto-account-creation.md.  La dirección EVM también se conoce comúnmente como la dirección pública. El formato de ID de la cuenta EVM es <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> donde <mark style="color:purple;">`alias`</mark> es la dirección EVM.

La cuenta de direcciones EVM y el [alias de número de cuenta](account-properties.md#account-number-alias) son valores de 20 bytes. Pueden diferenciarse porque el alias de número de cuenta siempre tiene 12 bytes. El alias de la cuenta de dirección EVM se utiliza comúnmente en carteras y herramientas para representar direcciones de cuenta.&#x20

<details>

<summary>EVM Address Account Alias Account ID Example</summary>

El número de fragmentos y el número de reinos son 0 seguidos por la dirección EVM.&#x20

\
**Ejemplo**\
\
Dirección EVM: `b794f5ea0ba39494ce839613fffba74279579268`\
\
Dirección EVM codificada: `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
ID de la cuenta EVM Alias: \
\
`0. .b794f5ea0ba39494ce839613fffba74279579268`

</details>

Propuesta de Mejora de Referencia de Hedera: [HIP-583](https://hips.hedera.com/hip/hip-583)

## Memo de cuenta

Un memo es como una nota corta que vive con el objeto de la cuenta en el estado del contador y puede ser visto en un explorador de red cuando busca la cuenta. Este memo de cuenta está limitado a 100 caracteres. El memo de la cuenta es mutable y se puede actualizar o eliminar de la cuenta en cualquier momento. La clave de cuenta es necesaria para firmar la transacción para facilitar cualquier cambio en esta propiedad.

{% hint style="warning" %}
No publique ninguna información privada en el campo memo de la cuenta. Este campo es visible para todos los participantes en la red.&#x20;
{% endhint %}

## Cuenta Nonce

Las cuentas en Hedera pueden enviar tipos de `EthereumTransaction` procesados por la máquina virtual Ethereum (EVM) en un nodo de consenso. El nonce en la cuenta representa un número secuencialmente incrementado de las transacciones enviadas por una cuenta a través del tipo `EthereumTransaction`. El valor nonce por defecto de la cuenta se establece en cero.

Propuesta de Mejora de Referencia de Hedera: [HIP-410](https://hips.hedera.com/hip/hip-410)

## Asociaciones de fichas automáticas

Las cuentas de Hedera generalmente deben aprobar fichas personalizadas antes de transferirlas a la cuenta receptora. La cuenta receptora debe firmar la transacción que asociará los tokens, permitiendo que los tokens especificados sean depositados en su cuenta. La función de asociación automática de tokens permite a la cuenta eludir manualmente asociar el token personalizado antes de transferirlo a la cuenta.&#x20

Las cuentas pueden aprobar automáticamente hasta 5.000 fichas sin preautorizar manualmente cada ficha personalizada. Supongamos que una cuenta necesita mantener un saldo para fichas personalizadas mayores de 5.000. En ese caso, la cuenta debe aprobar manualmente cada token adicional usando la transacción para asociar los tokens. No hay límite en el número total de tokens que puede contener una cuenta. Esta propiedad es mutable y se puede cambiar después de que se haya establecido.

Propuesta de Mejora de Referencia de Hedera: [HIP-23](https://hips.hedera.com/hip/hip-23)

## Saldos

Cuando se crea una nueva cuenta, puede especificar un saldo inicial de HBAR para la cuenta. El saldo inicial de HBAR para el token es deducido de la cuenta que está pagando para crear la nueva cuenta. Crear una cuenta con un saldo inicial es opcional.&#x20

Una cuenta de Hedera puede mantener un balance de HBAR y fichas personalizadas fungibles y no fungibles (NFT). Los saldos de cuentas pueden ser vistos en un [Explorador de red](../../networks/community-mirror-nodes.md) y consultados desde APIs REST de nodo réplica o nodos de consenso.

| Tipo de token                                  | Descripción                                                                                                            | Ejemplo de ID de Token                                                                                                       |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **HBAR**                                       | El símbolo fungible nativo de Hedera utilizado para pagar comisiones de transacción y asegurar la red. | Ninguna                                                                                                                      |
| **Token Funcionable**                          | Tokens fungibles personalizados creados en Hedera.                                                     | <p>El identificador fungible del token está representado como <code>0.0.tokenNum</code> <br><br>ex: <code>0.0.100</code></p> |
| **Token no Fungible (NFT)** | Tokens personalizados no fungibles (NFTs) creados en Hedera.                        | <p>NFT ID se representa como <code>0.0.tokenNum-serialNum</code>.<br></p><p>ej: <code>0.0.0.101-1</code></p>                 |

## Teclas

Cada cuenta es requerida para tener al menos una clave al crearla. Si no se suministra una clave en el momento de crear una cuenta, la red rechazará la transacción. Los individuo(s) que tienen acceso a las claves privadas de la cuenta tienen acceso para autorizar la transferencia de tokens dentro o fuera de la cuenta y están obligados a firmar las transacciones que modifican la cuenta. Modificar la cuenta incluye cambiar cualquier propiedad, como el saldo, llaves, memo, etc.

Las cuentas pueden tener opcionalmente más de una clave asociada a ellas. Estos tipos de cuentas son cuentas multifirma lo que significa que necesitarás más de una clave para firmar la transacción para cambiar una propiedad en una cuenta o débito HBARs. Los requisitos de firma de una cuenta multifirma dependen de la estructura clave elegida por la cuenta. Para soporte de estructuras clave y tipos de clave, siga el siguiente enlace.

{% content-ref url="../keys-and-signatures.md" %}
[keys-and-signatures.md](../keys-and-signatures.md)
{% endcontent-ref %}

{% hint style="danger" %}
Advertencia: Las llave(s) privadas asociadas con la cuenta no deben ser compartidas con nadie, ya que permitirán que otros autoricen transacciones de tu cuenta en tu nombre. Compartir tu clave privada es como compartir la contraseña de tu cuenta bancaria. Por favor, asegúrese de que sus claves privadas están almacenadas en una cartera segura.
{% endhint %}

## Firma del receptor requerida

Las cuentas pueden requerir opcionalmente que la cuenta firme cualquier ficha de depósito en la cuenta. Esta característica se establece en falso por defecto. Si esta función está configurada como verdadera, la cuenta será requerida para firmar todas las transacciones que el depósito tokens en la cuenta. Esta propiedad es mutable y se puede actualizar después de crear la cuenta.

## Tomando

Staking in Hedera está teniendo una cuenta y asociando el balance HBAR a un nodo en la red. Los balances de tokens personalizados fungibles o no fungibles que contiene una cuenta no contribuyen a apostar en la red. El propósito de apostar cuentas a un nodo en la red es fortalecer la seguridad de la red. Para contribuir a la seguridad de la red, las cuentas apuestadas pueden ganar recompensas en HBAR. Por favor, consulta esta [guide](https://docs.hedera.com/hedera/core-concept/staking) para obtener información adicional sobre el programa de recompensas. Los contratos también pueden apostar sus cuentas para ganar recompensas.

{% hint style="info" %}
Una cuenta sólo puede apostar a un nodo o a una cuenta en un momento dado.
{% endhint %}

<details>

<summary>ID de nodo tomado</summary>

Una cuenta puede optar por apostar su HBAR a un nodo de la red Hedera. El ID del nodo apostado es el nodo al que puede apostar una cuenta. El saldo completo de la cuenta es apostado al nodo. No confunda el ID del nodo con el ID de la cuenta del nodo. Si participas en el ID de la cuenta del nodo, tu cuenta no ganará recompensas acumuladas. \
\
El saldo de cuenta apuesto es líquido en todo momento. Esto significa que puedes transferir tokens HBAR dentro y fuera de la cuenta, y su cuenta continuará siendo apostada al nodo sin interrupción. \
\
There is no lock-up period. Esto significa que los tokens de HBAR en tu cuenta no se mantienen por un período de tiempo antes de que puedas usarlos. \
\
El ID de un nodo puede encontrarse en [here](https://docs.hedera.com/hedera/networks/mainnet/mainnet-nodes) o puede ser consultado desde la [API REST de los nodos](https://testnet. irrornode.hedera.com/api/v1/docs/#/network/getNetworkNodes).\
\
Ejemplo:\
\
ID del nodo: `1`\

</details>

<details>

<summary>ID de cuenta tomada</summary>

Una cuenta puede optar por apostar su HBAR a otra cuenta en la red Hedera. Esto se conoce como **apuestas indirectas**. El ID de la cuenta apuestada es el ID de la cuenta a la que apostar. El saldo total de la cuenta es apostado a la cuenta especificada. \
\
No hay un período de bloqueo y el balance es siempre líquido como apostar a un nodo. \
\
Las cuentas que estén en otra cuenta no ganan las recompensas correspondientes. Por ejemplo, si la cuenta A es apostada a la cuenta B, la cuenta B tendrá que ser apostada a un nodo para contribuir a la seguridad de la red y ganar ganancias acumuladas. La cuenta B ganará las recompensas por apostar al apostar a un nodo para ambos saldos de HBAR en la Cuenta A + Cuenta B. Cuenta A no ganará recompensas por apuestas. \
\
Ejemplo:\
\
ID de la cuenta: `0.0.10`

</details>

<details>

<summary>Rechaza la ganancia de Recompensas</summary>

Las cuentas pueden declinar para ganar recompensas cuando se juegan a un nodo o una cuenta. La cuenta apuestada todavía contribuye al peso de apuesta del nodo, pero no gana recompensas o se calcula como parte del pago de las recompensas a las otras cuentas que han elegido para ganar recompensas. Por defecto, todas las cuentas apuestadas ganarán recompensas a menos que esta bandera booleana sea verdadera. Esta elección se puede cambiar actualizando las propiedades de la cuenta. Hedera treasury accounts enable this flag to decline earning staking rewards.\
\
Default: `true` (all accounts accept earning staking rewards if the account is staked)

</details>

### Información de Staking

La red almacena los metadatos activos para una cuenta y cuentas contractuales. Esta información se devuelve en solicitudes de consulta de información de cuenta (`AccountInfoQuery` o `ContractInfoQuery`). Los metadatos de apuesta para una cuenta incluyen la siguiente información:

- **declinación\_recompensa:** si la cuenta rechazó o no ganar recompensas
- **stake\_period\_start:** El período de apuesta durante el cual la configuración de apuesta de esta cuenta o contrato cambió (como comenzar a apostar o cambiar staked\_node\_id) o la recompensa más reciente, lo que sea más tarde. Si esta cuenta o contrato no está apostada actualmente a un nodo, entonces este campo no está establecido. El periodo de apuestas es de 24 horas, a partir de la medianoche.
- **Pendiente\_reward:** La cantidad en tinybars que será recibida en el próximo pago de recompensa
- **staked\_to\_me:** El saldo total tinybar de todas las cuentas apostadas a esta cuenta
- **apuesta\_id:** ID de la cuenta o nodo al que esta cuenta o contrato está apostando
  - **staked\_account\_id:** La cuenta a la que esta cuenta o contrato está apostando
  - **staked\_node\_id:** La ID del nodo a la que esta cuenta o contrato es apuestada

Propuesta de Mejora de Referencia de Hedera: [HIP-406](https://hips.hedera.com/hip/hip-406)

## Renovación automática y **expiración**

{% hint style="warning" %}
Auto-renovación y expiración (renta) no están habilitados actualmente.
{% endhint %}

Al igual que las otras entidades de Hedera, las cuentas ocupan almacenamiento de red. Para cubrir el costo de almacenar una cuenta, se cobrará una cuota de renovación por el almacenamiento utilizado en la red. Esta característica no está habilitada en la red hoy; Sin embargo, en el futuro, cuando esté habilitada, la cuenta debe tener fondos suficientes para pagar las tasas de renovación. 20 x

El importe de la renovación se cargará cada período predeterminado en segundos. El intervalo de tiempo que se cargará la cuenta es el período de renovación automática. El sistema cargará automáticamente los gastos de renovación de la cuenta. Si la cuenta no tiene un saldo HBAR, se suspenderá una semana antes de que se elimine del contador. Puede renovar una cuenta durante el período de suspensión.

<details>

<summary> Tiempo de caducidad</summary>

La marca de tiempo de consenso efectiva en (y después) que la entidad está programada para expirar.

</details>

<details>

<summary><strong>Renovar automáticamente la cuenta</strong></summary>

La cuenta de renovación automática es la que se utilizará para pagar los honorarios de renovación de automóviles. Si no se especifica una cuenta de renovación automática, se cargarán en la cuenta los honorarios de renovación de autos.

</details>

<details>

<summary><strong>Auto Renovar Periodo</strong></summary>

El intervalo en el que se cobrará a esta cuenta los honorarios de renovación de automóviles. El período máximo de renovación automática para una cuenta está limitado a 3 meses (8000001 segundos seg). El período mínimo de renovación automática es de 30 días. El período de renovación automática es mutable y se puede actualizar en cualquier momento. Si no hay fondos suficientes, entonces se extiende todo el tiempo posible. Si está vacío cuando caduca, entonces se elimina.

</details>

Propuesta de Mejora de Referencia de Hedera: [HIP-16](https://hips.hedera.com/hip/hip-16)
