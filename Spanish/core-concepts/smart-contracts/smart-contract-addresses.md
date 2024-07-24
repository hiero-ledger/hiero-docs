# Direcciones de Contrato Inteligente

Después de que se implemente un contrato inteligente en Hedera, se asocia con una dirección de contrato inteligente única. Hay dos tipos de direcciones a las que puede hacer referencia un contrato inteligente en el sistema:

**➡️** [**Dirección EVM Contrato Inteligente**](smart-contract-addresses.md#evm-address)

**➡️** [**Smart Contract ID**](smart-contract-addresses.md#contract-id)

***

### Dirección EVM

La dirección estándar de contrato inteligente EVM es la dirección que es compatible con EVM. La dirección del contrato EVM es devuelta por el sistema una vez que el contrato es desplegado. Este es el formato de dirección que se utiliza comúnmente en el ecosistema Ethereum. Puede utilizar la dirección EVM del contrato inteligente para hacer referencia a contratos inteligentes en herramientas Ethereum Ecosystem como [Hardhat](../../support-and-community/glossary.md#hardhat) y [MetaMask](../../support-and-community/glossary.md#metamask).

Ejemplo de Contrato EVM Dirección hexadecimal codificado ID del contrato: `0x00000000000000000000000000002cd37f`

{% hint style="info" %}
_**Nota:** Los contratos desplegados usando las transacciones Hedera API `ContractCreate` tendrán este formulario (por ejemplo, usando ContractCreateTransaction en los SDKs). Todos los demás casos de despliegue estarán en la dirección estándar de EVM, post_ [_HIP-729_](https://hips.hedera.com/hip/hip-729)_._
{% endhint %}

Ejemplo de Contrato EVM Dirección: [`0x86ecca95fecdb515d068975b75eac4357contractd6e86c5`](https://hashscan.io/mainnet/contract/0.0.2958097?p=1\\&k=1685819177.474035003)

***

### ID del Contrato

En la Red Hedera, los contratos inteligentes también se pueden identificar mediante un ID de contrato inteligente. Un ID de contrato inteligente es un identificador de contrato nativo de la red Hedera. Tanto la dirección del contrato inteligente EVM como el ID del contrato inteligente son identificadores aceptados para un contrato inteligente al interactuar con el contrato en Hedera utilizando las transacciones de Hedera.

Ejemplo de ID del contrato: `0.0.123`&#x20

En algunos casos, la dirección EVM es el formato codificado por hex del contrato ID.

El ID del contrato inteligente **no es un formato de dirección** aceptado o conocido en el ecosistema de Ethereum. Por ejemplo, si utiliza MetaMask, no especificará el contrato por su ID de contrato y en su lugar utilizará su dirección EVM.

Al ver la información del contrato, puede ver ambos tipos de direcciones anotadas en Exploradores de Red de Hedera como [HashScan](https://hashscan.io/).

<figure><img src="../../.gitbook/assets/contract-hashscan-evm-address-id-example.png" alt=""><figcaption><p>Dirección EVM & ejemplo de ID de contrato en HashScan</p></figcaption></figure>

***

### Cuentas de Contratos Inteligentes

Similar a [Ethereum](../../support-and-community/glossary.md#ethereum), las entidades Smart Contract también son un tipo de cuenta. Un contrato inteligente implementado en Hedera puede contener [HBAR](../../support-and-community/glossary.md#hbar), [fungible](../../support-and-community/glossary.md#fungible-token), y [tokens no fungibles](../../support-and-community/glossary.md#non-fungible-token-nft).

<table><thead><tr><th width="289">Propiedad del contrato inteligente</th><th>Ejemplo</th></tr></thead><tbody><tr><td><strong>ID del contrato inteligente</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0.0.2940467</a></td></tr><tr><td><strong>Dirección EVM de contrato inteligente</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0xde2b7414e2918a393b59fc130bceb75c3ee52493</a></td></tr><tr><td><strong>Contrato Hex Codificado con un contrato inteligente</strong></td><td>0x00000000000000000000000000000000002cff73<br>*<em>This is only present if the contract was NOT deployed via an EVM tool and instead the Hedera SDKs.</em></td></tr><tr><td><strong>ID de cuenta de contrato inteligente</strong></td><td><a href="https://hashscan.io/mainnet/account/0.0.2940467?app=false&#x26;ph=1&#x26;pt=1&#x26;p2=1&#x26;p1=1">0.0.2940467</a></td></tr></tbody></table>
