# Desplegando Contratos Inteligentes

Después de compilar su contrato inteligente, puede desplegarlo en la red Hedera. El "_init code_" del constructor incluye todo el bytecode del contrato. Al desplegar, se espera que la EVM sea suministrada con el contrato inteligente [bytecode](../../.. support-and-community/glossary.md#bytecode) y el gas necesario para ejecutar y desplegar el contrato. Post-deployment, el constructor es removido, dejando sólo el `runtime_bytecode` para futuras interacciones contractuales.

**➡️** [**Hyperledger Besu EVM**](./#hyperledger-besu-evm-on-hedera)

**➡️** [**Cancun Hard Fork**](./#cancun-hard-fork)

**➡️** [**Variables de Solidez y Opcodes**](./#solidity-variables-and-opcodes)

***

## Máquina virtual de Ethereum (EVM)

La [máquina virtual de Ethereum (EVM)](../../../support-and-community/glossary.md#ethereum-virtual-machine-evm) es un entorno de ejecución para la ejecución de contratos inteligentes escritos en lenguajes de programación nativos de EVM, como Solididad. El código fuente debe ser compilado en bytecode para la EVM para ejecutar un contrato inteligente determinado.

En Hedera, los usuarios pueden interactuar con el entorno compatible con EVM de varias maneras. Pueden enviar `ContractCreate`, `EthereumTransaction`, o hacer llamadas RPC `eth_sendRawTransaction` directamente con el bytecode del contrato. Estas diversas rutas permiten a los desarrolladores implementar y gestionar contratos inteligentes de manera eficiente.

Cuando la EVM recibe el bytecode, se dividirá en códigos de operación ([opcodes](../../../support-and-community/glossary.md#opcodes)). Los opcodes EVM representan las instrucciones específicas que puede realizar. Cada código es un byte y tiene su propio coste de gas asociado con él. El costo por código para el tenedor duro de Ethereum Cancun se puede encontrar [here](https://www.evm.codes/?fork=cancun).

#### Ejemplo de Opcode de contrato inteligente

```solidity
PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x40 MLOAD PUSH2 0x558 CODESIZE SUB DUP1 PUSH2 0x558 DUP4 CODECOPY DUP2 DUP2 ADD PUSH1 0x40 MSTORE PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x33 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 MLOAD PUSH1 0x40 MLOAD SWAP4 SWAP3 SWAP2 SWAP1 DUP5 PUSH5 0x100000000 DUP3 GT ISZERO PUSH2 0x53 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP4 DUP3 ADD SWAP2 POP PUSH1 0x20 DUP3 ADD DUP6 DUP2 GT ISZERO PUSH2 0x69 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 MLOAD DUP7 PUSH1 0x1 DUP3 MUL DUP4 ADD GT PUSH5 0x100000000 DUP3 GT OR ISZERO PUSH2 0x86 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 DUP4 MSTORE PUSH1 0x20 DUP4 ADD SWAP3 POP POP POP SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0xBA JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x9F JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xE7 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP PUSH1 0x40 MSTORE POP POP POP CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x144 SWAP3 SWAP2 SWAP1 PUSH2 0x14B JUMP JUMPDEST POP POP PUSH2 0x1E8 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x18C JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x1BA JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x1BA JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x1B9 JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x19E JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x1C7 SWAP2 SWAP1 PUSH2 0x1CB JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x1E4 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x1CC JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST PUSH2 0x361 DUP1 PUSH2 0x1F7 PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER
```

Referencia: [https://ethervm.io/](https://ethervm.io/)

***

## Hyperledger Besu EVM en Hedera

Los nodos de red de Hedera utilizan el [HyperLedger Besu EVM ](../../../support-and-community/glossary.md#hyperledger-besu-evm)Cliente escrito en Java como una capa de ejecución para las transacciones tipo Ethereum. El código base está actualizado con los tenedores de discos duros de Ethereum Mainnet. La biblioteca cliente Besu EVM se utiliza sin ganchos para las funciones de consenso, red y almacenamiento de Ethereum. En su lugar, Hedera engancha en sus propios componentes consensos Hashgraph y Gossip y [Virtual Merkle Trees](../../../support-and-community/glossary.md#virtual-merkle-tree) para mayor tolerancia, finalidad y escalabilidad.&#x20

A partir del lanzamiento Hedera Mainnet [`0.50.0`](../../networks/release-notes/services.md#v0. 0), el cliente EVM Besu está configurado para soportar el fork duro Cancun del Mainnet Ethereum, con algunas modificaciones.

### **Cancelar tenedor difícil**

La plataforma de contrato inteligente ha sido actualizada para soportar los cambios visibles de EVM introducidos en el fork duro [Cancun](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/cancun.md) . Esto incluye añadir nuevos opcodes para almacenamiento transitorio y copia de memoria, actualizaciones semánticas para opcodes introdujeron ciertas operaciones introducidas en la [Shanghai](https://github. om/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/shanghai.md), [London](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london. d), [Istanbul](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md), y [Berlin](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md) hard forks, excepto aquellos con cambios en la producción de bloques, serialización de datos y doble mercado.&#x20

A partir de los Servicios de Hedera [0.22](../../../networks/release-notes/services.md#v0.22) se cargan los costos de datos de gas y entrada. La cantidad de gas intrínseco consumido es una carga constante que ocurre antes de que se ejecute cualquier código. El coste del gas intrínseco es de 21.000. El costo asociado de los datos de entrada es 16 gas para cada byte de datos que no es cero y 4 gas para cada byte de datos que es cero. La cantidad de gas intrínseco consumido se carga en relación con los datos suministrados al hacer una llamada de contrato a los parámetros de función de los contratos externos. La programación del gas y la tabla de tarifas se pueden encontrar en la sección de gas de esta página de documentación.

<figure><img src="../../../.gitbook/assets/cancun-blob-graphic-onchaintimes.jpeg" alt=""><figcaption><p><strong>EIP-4844 Desvelado: allanando el camino para Proto-Danksharding en Ethereum</strong></p></figcaption></figure>

#### Proto-Danksharding

Como solución provisional a la fragmentación completa, introducida en el tenedor duro de Cancun, el proto-danksharding ofrece algunas de las ventajas de la fragmentación con menor complejidad y cambios de infraestructura que forman parte de una implementación de fragmentación. Esto, a su vez, abre las puertas para añadir "negros" de datos para añadir a los bloques para aumentar aún más la disponibilidad de datos y permitir una mayor eficiencia de procesamiento.

Los blobs son objetos de datos grandes dentro de los bloques. Estos pueden ser utilizados para almacenar rollos (soluciones de capa 2) y diferentes tipos de aplicaciones que requieren grandes objetos de datos para ser almacenados de manera eficiente. Esto es datos fuera de cadena para los validadores y requiere un procesamiento mínimo por su parte. Reduce la carga computacional en la red y por lo tanto reduce la tasa de gas de transacción.

#### ❌ ¿Bloques soportados en Hedera?

Hedera no proporciona bloques bajo [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844). [HIP-866](https://hips.hedera.com/hip/hip-866) define cómo se comporta Hedera sin soporte de blob. Para preservar la compatibilidad y el futuro espacio de diseño, Hedera actuará como si no se añadieran manchas. Esto permite que los contratos existentes que dependen del comportamiento de los blob funcionen sin manchas. Se impedirá que los blobs entren en el sistema prohibiendo las transacciones "Tipo 3", que permiten los blobs. Esto evitará que las imperfecciones se mantengan al margen de la preocupación de la EVM sin afectar a otras interacciones deseables en Hedera.

### Variables de solidez y Opcodes

La siguiente tabla define el mapeo de variables de Solidez y códigos de operación a Hedera. La lista completa de Opcodes soportados para el fork duro de Cancun se puede encontrar [here](https://www.evm.codes/).&#x20

<table><thead><tr><th width="245" align="center">Solidez</th><th width="170" align="center">Opcode</th><th>Hedera</th></tr></thead><tbody><tr><td align="center"><code>dirección</code></td><td align="center"></td><td>La dirección es un mapeo de shard.realm.number (0.0.10) en una dirección de solidez de 20 bytes. La dirección puede ser un ID de cuenta de Hedera o un ID de contrato en formato Solididad.</td></tr><tr><td align="center"><code>bloquear.basefee</code></td><td align="center"><code>BASE</code></td><td>El código <code>BASEFEE</code> devolverá cero. Hedera no utiliza el mecanismo de Mercado de Cuotas que está diseñado para apoyar.</td></tr><tr><td align="center"><code>block.chainId</code></td><td align="center"><code>CHAINID</code></td><td>El opcode <code>CHAINID</code> devolverá 295(hex <code>0x0127</code>) para mainnet, 296( hexadecimal <code>0x0128</code>) para redes de prueba, 297( hexadecimal <code>0x0129</code>) para previewnet, y 298 (<code>0x12A</code>) para redes de desarrollo.</td></tr><tr><td align="center"><code>block.coinbase</code></td><td align="center"><code>COINBASE</code></td><td>La operación <code>COINBASE</code> devolverá la cuenta de fondos (comisión por transacción de edera recopilando cuenta <code>0.0.98</code>).</td></tr><tr><td align="center"><code>block.number</code></td><td align="center"></td><td>El índice del archivo de registro (no recomendado, usa <code>block.timestamp</code>).</td></tr><tr><td align="center"><code>block.timestamp</code></td><td align="center"></td><td>La marca de tiempo del consenso de transacción.</td></tr><tr><td align="center"><code>block.difficulty</code></td><td align="center"></td><td>Siempre cero.</td></tr><tr><td align="center"><code>block.gaslimit</code></td><td align="center"><code>GASLIMIT</code></td><td>La operación <code>GASLIMIT</code> devolverá el <code>límite de gas</code> de la transacción. La transacción <code>límite de gas</code> será el más bajo del límite de gas solicitado en la transacción o un límite de gas superior global configurado para todos los contratos inteligentes.</td></tr><tr><td align="center"><code>msg.sender</code></td><td align="center"></td><td>La dirección del contrato de Hedera o ID de cuenta en formato de Solidez que llamó este contrato. Para el nivel de la raíz o para las cadenas de delegados que van a la raíz, es el ID de cuenta que paga la transacción.</td></tr><tr><td align="center"><code>msg.value</code></td><td align="center"></td><td>El valor asociado a la transacción asociada en tinybar.</td></tr><tr><td align="center"><code>tx.origin</code></td><td align="center"></td><td>El ID de la cuenta paga por la transacción, sin importar la profundidad.</td></tr><tr><td align="center"><code>tx.gasprice</code></td><td align="center"></td><td>Fijado (varía con el programa global de comisiones y el tipo de cambio).</td></tr><tr><td align="center"><p><code>selfdestruct</code></p><p><code>(dirección de destinatario a pagar)</code></p></td><td align="center"><code>SELFDESTRUCT</code></td><td>La dirección no será reutilizable debido a las políticas de numeración de cuentas de Hedera. En <code>SELFDESTRUCT</code> los contratos HBAR y HTS tokens son transferidos a los destinatarios. Si el destinatario no existe o no tiene una autorización para cualquiera de los tokens HTS, este opcode fallará. </td></tr><tr><td align="center"><code><dirección>.code</code></td><td align="center"></td><td>Las direcciones del contrato de precompilación no reportarán ningún código, incluido el contrato del sistema HTS.</td></tr><tr><td align="center"><code><address>.codehash</code></td><td align="center"></td><td>La precompilación de direcciones del contrato reportará el código hash vacío.</td></tr><tr><td align="center"></td><td align="center"><code>PRNGSEED</code></td><td>Este opcode devuelve un número aleatorio basado en el registro n-3 ejecutando hash.</td></tr><tr><td align="center"><code>delegar llamada</code></td><td align="center"></td><td>Los contratos ya no pueden usar <code>delegateCall()</code> para invocar contratos del sistema. En su lugar, los contratos deben utilizar el método <code>call()</code>.</td></tr><tr><td align="center"><code>blobVersionedHashesAtIndex</code></td><td align="center"><code>BLOBHASH</code></td><td>La operación <code>BLOBHASH</code> devolverá todos los ceros en todo momento.</td></tr><tr><td align="center"><code>comisión baseCarga</code></td><td align="center"><code>BLOBASEFEAR</code></td><td><p>La operación <code>BLOBBASEFEE</code> retornará</p><p><code>1</code> en todo momento.</p></td></tr></tbody></table>

Referencia: [HIP-866](https://hips.hedera.com/hip/hip-866), [HIP-868](https://hips.hedera.com/hip/hip-868)

***

### Limitación en `fallback()` / `receive()` Funciones en Contratos Inteligentes de Hedera <a href="#limitation-on-fallback-receive-functions-in-hedera-smart-contracts" id="limitation-on-fallback-receive-functions-in-hedera-smart-contracts"></a>

Al desarrollar contratos inteligentes en Hedera, es importante entender que las funciones `fallback()` y `receive()` **no** se activan cuando un contrato recibe HBAR a través de una transferencia de criptomonedas.

En Ethereum, estas funciones actúan como mecanismos "catch-all" cuando un contrato recibe Ether. En Hedera, sin embargo, los saldos contractuales pueden cambiar a través de operaciones nativas de HAPI, independientemente de las llamadas de mensajes EVM, haciendo imposible mantener invariantes relacionados con el saldo con sólo los métodos `fallback()` o `receive()`.

#### Variables impactadas

- **`msg.sender`:** La dirección que inicia la llamada al contrato.
- **`msg.value`:** La cantidad de HBAR enviado junto con la llamada.

#### Puntos clave

- Los desarrolladores deben implementar funciones explícitas para manejar transferencias HBAR.
- Para desactivar completamente las operaciones nativas, considere enviar una [Hedera Improvement Proposal (HIP)](https://hips.hedera.com/).

Entender estas diferencias es crucial para cualquiera que desarrolle contratos inteligentes en Hedera, en particular aquellos que están familiarizados con Ethereum.

***

## Desplegar su contrato inteligente

**SDK**

Puedes usar un [SDK de Hedera](../../sdks-and-apis/sdks/) para desplegar tu bytecode de contrato inteligente en la red. Este enfoque no requiere el uso de herramientas EVM como Hardhat o una instancia del Relé Hedera JSON-RPC.

{% content-ref url="../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md" %}
[deploy-your-first-smart-contract.md](../../tutorials/smart-contracts/deploy-your-smart-contract.md)
{% endcontent-ref %}

**Hardhat**

Hardhat puede utilizarse para desplegar su contrato inteligente apuntando a un [Relé JSON-RPC](json-rpc-relay.md). Sin embargo, las herramientas EVM no soportan características nativas de contratos inteligentes de Hedera como:

- Clave Admin
- Contrato de Nota
- Asociaciones de fichas automáticas
- Auto Renovar ID de cuenta
- ID de nodo Staking o ID de cuenta
- Rechazar Recompensas de Toma

Si necesita establecer cualquiera de las propiedades anteriores para su contrato, tendrás que llamar a la API `ContractCreateTransaction` usando uno de los [SDK de Hedera. (../../../sdks-and-apis/sdks/)

{% content-ref url="../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md" %}
[deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md](../../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md)
{% endcontent-ref %}

***

## FAQs

<details>

<summary><strong>Can I use Solidity functions directly with the Hedera EVM?</strong></summary>

Sí, puede utilizar funciones de Solidez directamente con la Hedera EVM. Sin embargo, consulte la tabla [Solidity Variables and Opcodes](./#solidity-variables-and-opcodes) para entender cualquier modificación a las descripciones de opcode que mejor reflejen su comportamiento en la red Hedera.

</details>

<details>

<summary><strong>¿Qué debo hacer si mi contrato se basa en códigos blob?</strong></summary>

Si su contrato se basa en los códigos de opacidad que se introducen en el tenedor duro de Cancun, todavía puede desplegarlo en Hedera. Los opcodes relacionados con los blobs **no** fallarán. Devolverán valores predeterminados como [especificado por el EVM](https://www.evm.codes/?fork=cancun).&#x20

</details>

<details>

<summary><strong>¿Hay alguna consideración especial para el uso de códigos de opciónes EVM actualizados en Hedera?</strong></summary>

Sí, mientras que el Hedera EVM soporta los opcodes actualizados del fork duro de Cancun, debería conocer los costos intrínsecos de gas y los cargos de datos de entrada específicos de Hedera. Vaya a la tabla [tarifas y horarios de gas](gas-and-fees.md) para más información.

</details>
