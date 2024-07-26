# Introducción

A través de esta corta serie _Getting Started_ aprenderá los conceptos básicos de crear una cuenta, transfiriendo HBAR, firmando y enviando transacciones a Hedera Testnet. La red de pruebas de Hedera te permite jugar con las APIs de Hedera en un entorno no productivo. Verás lo fácil que es empezar con uno de nuestros [SDKs de Hedera](../sdks-and-apis/) en el lenguaje de programación de tu elección. A continuación se muestran dos rutas para crear cuentas de red de pruebas y recibir HBAR para construir, probar y desplegar dApps de Hedera:

**➡️** [**Fauceto de Hedera**](introduction.md#hedera-faucet)

**➡️** [**Portal de Desarrollador de Hedera**](introduction.md#hedera-developer-portal-profile)

***

### Hedera Faucet

El grifo de Hedera le permite recibir de forma anónima HBAR red de pruebas sin el problema de crear una cuenta de portal de desarrolladores. Para usar el grifo anónimo visita [Hedera faucet](https://portal.hedera. om/faucet) y conecte su cartera, o introduzca una dirección de cartera EVM o el ID de cuenta de Hedera para iniciar el proceso.

<figure><img src="../.gitbook/assets/faucet-receive-hbar.png" alt=""><figcaption></figcaption></figure>

Introducir una dirección EVM facilitará la creación de una cuenta usando el [flujo de creación de cuenta automático](../core-concepts/accounts/auto-account-creation.md#auto-account-creation-evm-address-alias). Copia y guarda el nuevo ID de cuenta de Hedera y la clave privada que estás manejando para la configuración de tu entorno de codificación en la página siguiente.

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt=""><figcaption></figcaption></figure>

El grifo tiene un límite máximo de **100 HBAR** cada 24 horas.

<figure><img src="../.gitbook/assets/faucet-wallet-timer.png" alt=""><figcaption></figcaption></figure>

Recibirá un mensaje de error y se le pedirá que vuelva cuando su cuenta sea elegible para un relleno si lo intenta más de una vez dentro de un período de 24 horas.

<figure><img src="../.gitbook/assets/faucet-receive-error.png" alt=""><figcaption></figcaption></figure>

***

### Perfil del Portal de Desarrolladores de Hedera

El portal de desarrolladores de Hedera te permite crear una cuenta de testnet para recibir HBAR después de su creación. Visita el [portal de desarrollo de Hedera](https://portal.hedera.com/register) y sigue las instrucciones para crear una cuenta.

<figure><img src="../.gitbook/assets/portal testnet account.png" alt="Screenshot of the Hedera Developer portal (portal.hedera.com/register) account creation page."><figcaption></figcaption></figure>

Tras la creación de la cuenta, tu cuenta de portal testnet recibirá automáticamente \*\*1000 HBAR, \* y verás tu ID de cuenta y par de llaves desde el panel de control del portal (ver imagen más abajo). Copie el ID de su cuenta y la clave privada codificada en DER para el paso de configuración del entorno de codificación.

<figure><img src="../.gitbook/assets/faucet-der-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
**Nota**: Las cuentas de Testnet en el portal del desarrollador están sujetas a un límite diario de recarga de 1000 HBAR. Las cuentas _**no**_ se recargan automáticamente. Para mantener su saldo, debe solicitar manualmente un relleno a través del panel de control del portal cada 24 horas.

Para mayor claridad, la recarga no añade 1000 HBAR a su saldo de cuenta cada vez que se rellena. En su lugar, el saldo de su cuenta se repone hasta 1000 HBAR si cae por debajo de este umbral. Por ejemplo, si el saldo de su cuenta es 500 HBAR, el rellenado sólo añadirá HBAR suficiente para llevar su saldo a 1000 HBAR, no un extra de 1000 HBAR.&#x20;
{% endhint %}
