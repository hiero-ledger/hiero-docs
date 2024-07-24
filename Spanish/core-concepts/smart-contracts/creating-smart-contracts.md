# Creando Contratos Inteligentes

Un [contrato inteligente](../../support-and-community/glossary.md#smart-contract) es un programa inmutable que consiste en un conjunto de lógica (variables de estado, funciones, controladores de eventos, etc.) o reglas que pueden ser desplegadas, almacenadas y accedidas en una [tecnología distribuida de ledger](../../support-and-community/glossary.md#distributed-ledger-technology-dlt) como Hedera. Las funciones contenidas en un contrato inteligente pueden actualizar y gestionar el estado del contrato y leer los datos del contrato desplegado. También pueden crear y llamar a otras funciones de contratos inteligentes en la red. Los contratos inteligentes son seguros, resistentes a la manipulación y transparentes, ofreciendo un nuevo nivel de confianza y eficiencia.

Hedera soporta cualquier idioma que compila al Mainnet de Ethereum. Esto incluye [Solidity](../../support-and-community/glossary.md#solidity) y [Vyper](../../support-and-community/glossary.md#vyper). Estos lenguajes de programación compilan código y producen [bytecode](../../support-and-community/glossary.md#bytecode) que [Ethereum Virtual Machine (EVM)](../../support-and-community/glossary.md#ethereum-virtual-machine-evm) puede interpretar y entender.

- Para obtener más información sobre el lenguaje de programación Solididad, consulte la documentación mantenida por el equipo de Solididad [here](https://docs.soliditylang.org/en/v0.8.19/).
- Para obtener más información sobre Vyper, revisa la documentación mantenida por el equipo de Vyper [here](https://docs.vyperlang.org/en/stable/).

Además, hay muchas herramientas disponibles para escribir y compilar contratos inteligentes, incluyendo el popular [Remix IDE](../../support-and-community/glossary.md#remix-ide) y [Hardhat](../../support-and-community/glossary.md#hardhat). El IDE Remix es una plataforma fácil de usar que le permite escribir y compilar fácilmente sus contratos inteligentes y realizar otras tareas como depuración y pruebas. Usando estas herramientas, usted puede crear contratos inteligentes potentes y seguros que pueden ser usados para varios propósitos, de simples transferencias de fichas a instrumentos financieros complejos.

**Ejemplo**

El siguiente es un ejemplo muy simple de un contrato inteligente escrito en el lenguaje de programación Solididad. El contrato inteligente define las variables de estado `owner` y `message`, junto con funciones como `set_message` (que modifica los detalles de estado por escrito) y `get_message`(que lee los detalles de estado).

```solidity
pragma solidity >=0.7.0 <0.8.9;

contract HelloHedera {
    // the contract's owner, set in the constructor
    address owner;

    // the message we're storing
    string message;

    constructor(string memory message_) {
        // set the owner of the contract for `kill()`
        owner = msg.sender;
        message = message_;
    }

    function set_message(string memory message_) public {
        // only allow the owner to update the message
        if (msg.sender != owner) return;
        message = message_;
    }

    // return a string
    function get_message() public view returns (string memory) {
        return message;
    }
}
```

***

## Cosas que debes tener en cuenta al crear un contrato

**Asociaciones de Token Automáticos**

Una ranura de asociación automática es una o más ranuras que usted aprueba que permiten enviar tokens a su contrato sin autorización explícita para cada tipo de token. Si esta propiedad no está definida, debes aprobar cada token antes de que sea transferido al contrato para que la transferencia tenga éxito a través de la `TokenAssociateTransaction` en los SDKs. Obtenga más información sobre las asociaciones de auto-token [here](../accounts/account-properties.md#automatic-token-associations).

Esta funcionalidad es exclusivamente accesible cuando se configura una API `ContractCreateTransaction` a través de los SDK de Hedera. Si está desplegando un contrato en Hedera usando herramientas EVM como Hardhat y el Relé RPC de Hedera JSON, ten en cuenta que esta propiedad no puede ser configurada, ya que las herramientas EVM carecen de compatibilidad con las características únicas de Hedera.

**Clave de Admin**

Los contratos tienen la opción de tener una [clave de administrador](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_create.proto#L117). Este concepto es nativo de los contratos de Hedera y permite actualizar las propiedades de la cuenta del contrato. Tenga en cuenta que esto no afecta al contrato [bytecode](../../support-and-community/glossary.md#bytecode) y no se relaciona con la actualizabilidad. Si no se establece la clave de administración, no podrá actualizar las siguientes propiedades nativas de Hedera (anotadas en [ContractUpdateTransactionBody](https://github. om/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto) para su contrato una vez que esté desplegado:

- [`autoRenewPeriod`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L78)
- [`memoField`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L88)
- [`max_automatic_token_associations`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L105)
- [`auto_renew_account_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L111)
- [`staked_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L116)
- [`decline_reward`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L134)

No se puede establecer el campo de clave de administración si se implementa un contrato a través de herramientas como Hardhat. Este campo puede establecerse si se desea implementando un contrato usando uno de los Hedera [SDKs](../../sdks-and-apis/sdks/).&#x20

**Tamaño máximo de almacenamiento del contrato**

Los contratos en Hedera tienen un límite de tamaño de almacenamiento de 16,384,000 pares de valor clave (\~100MB).&#x20

**Alquiler**

Mientras que el alquiler no está habilitado para contratos desplegados en Hedera hoy, querrá estar familiarizado con el concepto de alquiler, ya que puede afectar potencialmente a los costos de mantener su estado contractual en la red. Consulte la documentación [here](smart-contract-rent.md).

**Gastos de Transacción y Gas**

Hay comisiones de transacción de Hedera y comisiones EVM asociadas con el despliegue de un contrato. Para ver la lista de las comisiones base, consulte la página de comisiones [here](../../networks/mainnet/fees/) y la calculadora de gastos [here](https://hedera.com/fees).

***

## Preguntas frecuentes sobre contratos inteligentes

<details>

<summary>¿Qué es un contrato inteligente?</summary>

Un contrato inteligente es un programa que está escrito en un idioma que puede ser interpretado por la EVM. Por favor, consulte la [glossary](../../support-and-community/glossary.md) para más palabras clave y definiciones.

</details>

<details>

<summary>What programming language does Hedera support for smart contracts?</summary>

Hedera apoya Solididad y Vyper.

</details>

<details>

<summary>Can I write and compile my smart contracts using Remix IDE or other Ethereum ecosystem tools? </summary>

Puede utilizar Remix IDE u otras herramientas de ecosistema de Ethereum para escribir, compilar e implementar su contrato inteligente en Hedera. Revisa nuestras herramientas compatibles con EVM [here](../../#evm-compatible-tools).&#x20

</details>

<details>

<summary>Where can I find the smart contracts that are deployed to each Hedera network (previewnet, testnet, mainnet)?</summary>

En tu Explorador de Bloques de confianza favorito (también llamado Explorador de Nodos de Mirror en Hedera). Para ver exploradores alojados en la comunidad, revisa la página de herramientas del explorador de red [here](../../networks/community-mirror-nodes.md).&#x20

</details>

<details>

<summary>¿Cuáles estándares de token ERC son compatibles con Hedera?</summary>

Hedera soporta los estándares ERC y ERC-721 y puede encontrar la lista completa de estándares soportados [here](tokens-managed-by-smart-contracts/).

</details>
