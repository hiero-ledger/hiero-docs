# Cuentas

Las cuentas son el punto de partida central al interactuar con la red Hedera y utilizar los servicios de Hedera. Una cuenta de Hedera es una entidad, un tipo de objeto distinto, almacenado en el contador, que contiene fichas. Las cuentas pueden contener el símbolo fungible nativo de Hedera (HBAR), fungible personalizado y tokens no fungibles personalizados (NFTs) creados en la red Hedera.&#x20

El token nativo HBAR de Hedera es un token de utilidad que se utiliza principalmente para pagar transacciones y comisiones de consulta al interactuar con la red. El símbolo HBAR está representado como "A".  Las aplicaciones pueden referirse a HBAR como la denominación topográfica; sin embargo, la red devuelve información en tinybars (t�rculas), una denominaci�n de HBAR. 100.000.000 de toneladas equivalentes a 1 de diámetro. Esto incluye cosas como comisiones de transacción o cuentas de saldos HBAR.&#x20

Usted interactúa con la red enviando transacciones que modifican el estado del contador o enviando solicitudes de consulta que lean datos del contador. La mayoría de las transacciones y consultas tienen una [cuota de transacción](../../networks/mainnet/fees/) que se cobra en HBAR. A diferencia de los usuarios de tokens personalizados crean en la red Hedera, ningún identificador de token representa el token HBAR nativo.&#x20

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><a href="account-creation.md">         <strong>Creación de cuenta</strong></a></td><td><a href="account-creation.md">account-creation.md</a></td></tr><tr><td>   <a href="auto-account-creation.md"><strong>Creación automática de cuenta</strong></a></td><td><a href="auto-account-creation.md">auto-account-creation.md</a></td></tr><tr><td><a href="account-properties.md">     <strong>Propiedades de la cuenta</strong></a></td><td><a href="account-properties.md">account-properties.md</a></td></tr></tbody></table>

## FAQ

<details>

<summary>What is a Hedera account?</summary>

Una cuenta de Hedera es una entidad única en la Red Hedera que puede contener fichas. Estos pueden ser el símbolo fungible nativo de Hedera (HBAR), fungible personalizado, o [tokens no fungibles (NFTs)](../../support-and-community/glossary.md#non-fungible-token-nft).

</details>

<details>

<summary>How are new accounts created on Hedera?</summary>

Las nuevas cuentas se crean al enviar una transacción a la red y pagar la cuota de transacción. Necesitará acceso a una cuenta existente con suficiente HBAR para cubrir esta cuota. Si no tienes acceso a una cuenta existente, puedes usar una cartera soportada, visita el [Portal de Desarrolladores de Hedera](https://portal. edera.com/), o utilice la función "Creación de cuenta automática" para aplicaciones.

</details>

<details>

<summary>¿Qué es la función 'Creación de cuenta automática'?</summary>

[Creación de cuenta automática](auto-account-creation.md) permite a las aplicaciones generar cuentas de usuario gratuitas al instante, incluso sin conexión a internet, creando un alias de cuenta.&#x20

</details>

<details>

<summary>¿Qué es una cuenta "hueca"?</summary>

Si una cuenta es creada con un alias [Dirección EVM](auto-account-creation.md#evm-address) a través de [Creación de Cuenta Automática](auto-account-creation.md), resultará en una cuenta "vacía". Esta cuenta tiene un número de cuenta y alias pero no tiene una clave de cuenta. Puede aceptar transferencias de tokens pero no puede transferir fichas o modificar las propiedades de la cuenta hasta que la clave de cuenta haya sido añadida, completando la cuenta.

</details>
