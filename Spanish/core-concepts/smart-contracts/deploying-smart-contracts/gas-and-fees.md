# Gastos y tarifas

## Gas

Al ejecutar contratos inteligentes, la EVM requiere que la cantidad de trabajo se pague en gas. El “trabajo” incluye computación, transiciones de estado y almacenamiento. El gas es la unidad de medida utilizada para cobrar un cargo por código que es ejecutado por el EVM. Cada código tiene un coste de gas definido. El gas refleja el costo necesario para pagar los recursos computacionales utilizados para procesar las transacciones.

### **Weibar**

La EVM devuelve la información del gas en Weibar (introducido en [HIP-410](https://hips.hedera.com/hip/hip-410)). Un weibar es 10^-18º HBAR, que se traduce a 1 tinybar es 10^10 weibars. Como se señala en HIP-410, se trata de maximizar la compatibilidad con herramientas de terceros que esperan que las unidades de éter se operen en fracciones de 10^18, también conocido como Wei.

### **Gas Schedule and Fees**

Los gastos de gas pagados por las transacciones de EVM en Hedera pueden estar compuestos por tres tipos diferentes de costes de gas:

- Gas intrínsecas
- Gas opcode EVM
- Contrato de Gas del Sistema de Hedera

<table><thead><tr><th width="163">Tipo de tarifa de gas</th><th>Descripción</th></tr></thead><tbody><tr><td><strong>Gas intrínsecas</strong></td><td>La cantidad mínima de gas necesaria para ejecutar una transacción. Es un coste de gas fijo que es independiente de las operaciones o cálculos específicos realizados dentro de la transacción.<br><br>Coste de gas intrínseco: 21.000 gas</td></tr><tr><td><strong>Código de Operación EVM</strong></td><td><p>El gas necesario para ejecutar el código de operación definido para la llamada de contrato inteligente.</p><ul><li><strong>Coste de ejecución fija de opcode</strong>: Cada codigo tiene un costo fijo que debe pagarse a la ejecución, medido en gas. Este costo es el mismo para todas las ejecuciones, aunque esto está sujeto a cambios en nuevos tenedores duros.</li></ul><ul><li><strong>Opcode Dynamic Execution Coste</strong>: Algunas instrucciones realizan más trabajo que otras, dependiendo de sus parámetros. Debido a esto, además de los costes fijos, algunas instrucciones tienen costos dinámicos. Estos costos dinámicos dependen de varios factores (que varían de la horquilla dura a la horquilla dura). </li></ul><p>Vea la <a href="https://www.evm.codes/">referencia</a> para aprender sobre los costos específicos por código y fork.</p></td></tr><tr><td><strong>Transacción de Contrato del Sistema de Hedera</strong></td><td><p>El gas requerido que está asociado con una transacción definida por Hedera como el uso del contrato de Servicio de Token de Hedera que le permite quemar (<code>TokenBurnTransaction</code>) o la menta (<code>TokenMintTransaction</code>) un token.</p><p></p><p>Si no está utilizando un contrato de sistema que se mapee a uno de los servicios nativos de Hedera, no necesita aplicar esta cuota.</p><p></p><p>El cálculo de gas de transacción de Hedera es: Costo de la transacción en USD x gas conversión gas/USD + 20%</p><p>Ejemplo de contratos del sistema:</p><ul><li>Servicio de Token de Hedera</li><li>Generador de números aleatorios (PRNG)</li><li>Tipo de cambio</li></ul></td></tr></tbody></table>

### Límite de gas

El límite de gas es la cantidad máxima de gas que está dispuesto a pagar por una operación.

Las tarifas de gas de opcode actuales reflejan la [versión del Servicio de Hedera 0.22](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22).

| Operación                                                                    | Coste de cancelación (Gas)                                                                          | Hedera actual (Gas)                                                                                 |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Depósito de código                                                           | 200 \* bytes                                                                                                           | 200 \* bytes                                                                                                           |
| <p><code>BALANCE</code><br>(cuenta fría)</p>                                 | 2600                                                                                                                   | 2600                                                                                                                   |
| <p><code>BALANCE</code><br>(cuenta cálida)</p>                               | 100                                                                                                                    | 100                                                                                                                    |
| `EXP`                                                                        | 10 + 50/byte                                                                                                           | 10 + 50/byte                                                                                                           |
| <p><code>EXTCODECOPY</code><br>(cuenta fría)</p>                             | 2600 + Mem                                                                                                             | 2600 + Mem                                                                                                             |
| <p><code>EXTCODECOPY</code><br>(cuenta cálida)</p>                           | 100 + Mem                                                                                                              | 100 + Mem                                                                                                              |
| <p><code>EXTCODEHASH</code><br>(cuenta fría)</p>                             | 2600                                                                                                                   | 2600                                                                                                                   |
| <p><code>EXTCODEHASH</code><br>(cuenta cálida)</p>                           | 100                                                                                                                    | 100                                                                                                                    |
| <p><code>EXTCODESIZE</code><br>(cuenta fría)</p>                             | 2600                                                                                                                   | 2600                                                                                                                   |
| <p><code>EXTCODESIZE</code><br>(cuenta cálida)</p>                           | 100                                                                                                                    | 100                                                                                                                    |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>             | <p>375 + 375*tópicos<br>+ data Mem</p>                                                                                 | <p>375 + 375*tópicos<br>+ data Mem</p>                                                                                 |
| <p><code>SLOAD</code><br>(ranura fría)</p>                                   | 2100                                                                                                                   | 2100                                                                                                                   |
| <p><code>SLOAD</code><br>(ranura cálida)</p>                                 | 100                                                                                                                    | 100                                                                                                                    |
| <p><code>TORRE</code><br>(nueva ranura)</p>                                  | 22,100                                                                                                                 | 22,100                                                                                                                 |
| <p><code>TORRE</code><br>(ranura existente,<br>acceso frío)</p>              | 2,900                                                                                                                  | 2,900                                                                                                                  |
| <p><code>TORRE</code><br>(ranura existente,<br>acceso cálido)</p>            | 100                                                                                                                    | 100                                                                                                                    |
| <p><code>TORRE</code><br>reembolso</p>                                       | Como especifica la EVM                                                                                                 | Como especifica la EVM                                                                                                 |
| <p><code>TALAR</code> <em>et al</em>.<br>(destinatario frío)</p>             | 2,600                                                                                                                  | 2,600                                                                                                                  |
| <p><code>TALAR</code> <em>et al</em>.<br>(receptor cálido)</p>               | 100                                                                                                                    | 100                                                                                                                    |
| <p><code>TALAR</code> <em>et al</em>.<br>Sobrecarga de Hbar/Eth Transfer</p> | 9,000                                                                                                                  | 9,000                                                                                                                  |
| <p><code>SELFDESTRUCT</code><br>(frío beneficiario)</p>                      | 2600                                                                                                                   | 2600                                                                                                                   |
| <p><code>SELFDESTRUCT</code><br>(cálido beneficiario)</p>                    | 0                                                                                                                      | 0                                                                                                                      |
| `TSTORE`                                                                     | 100                                                                                                                    | 100                                                                                                                    |
| `TLOAD`                                                                      | 100                                                                                                                    | 100                                                                                                                    |
| `MCOPY`                                                                      | 3 + 3\*palabras\_copiadas + memoria\_expansion\_cost | 3 + 3\*palabras\_copiadas + memoria\_expansion\_cost |

Los términos "caliente" y "frío" en la tabla anterior corresponden con si la cuenta o la ranura de almacenamiento se ha leído o escrito en la transacción actual de contrato inteligente, incluso si dentro de un marco de llamada hijo.

'`CALL` _et al._' incluye con limitación: `CALL`, `CALLCODE`, `DELEGATECALL`, y `STATICALL`

Referencia: [HIP-206](https://hips.hedera.com/hip/hip-206), [HIP-865](https://hips.hedera.com/hip/hip-865)

### Gas por segundo lanzamiento

La mayoría de las redes compatibles con EVM colocan un límite de gas por bloque para administrar la asignación de recursos. Esto se hace para poner un límite en la cantidad de tiempo invertido en la validación de bloques para que los nodos mineros puedan producir nuevos nodos rápidamente. Mientras que Hedera no tiene bloques o mineros, en el contexto de cómo un [Nakamoto consensus](https://golden. om/wiki/Nakamoto\_consensus-AMB5VWM) sistema lo usaría, estamos limitados por la física del tiempo en cuanto a cuántos bloques podemos procesar.

Para las transacciones de contratos inteligentes, el gas es una medida mejor de la complejidad de la transacción EVM que contar todas las transacciones iguales, para medir los límites del gas proporciona un límite más razonable al consumo de recursos.

Para permitir una mayor flexibilidad en qué transacciones aceptamos y para reflejar el comportamiento de Ethereum Mainnet, los límites de las transacciones se calcularán sobre una base por gas para llamadas de contratos inteligentes (`ContractCreate`, `ContractCall`, `ContractCallLocalQuery`) además de un límite por transacción. Este enfoque dual permite una mejor gestión de recursos, proporcionando una manera matizada de regular las ejecuciones de contratos inteligentes.&#x20

La red Hedera ha implementado un acelerador de gas del sistema de **15 millones de gas por segundo** en el lanzamiento del Servicio Hedera [0.22](../../../networks/release-notes/services.md#v0.22).&#x20

### Reembolso de gas y reserva de gas no utilizada

Hedera ahoga las transacciones antes del consentimiento, y los nodos limitan el número de transacciones que pueden enviar a la red. Luego, en un momento de consenso, si el número máximo de transacciones es superado, el exceso de transacciones no se evalúa y se cancelan con un estado ocupado. Lanzar por cantidades de gas variable proporciona algunos desafíos a este sistema, donde los nodos sólo envían una parte de su límite de transacción.

Para hacer frente a esto, el acelerador se basará en un sistema de medición de gas de dos niveles: preconsenso y posconsenso. El acelerador de pre-consenso utilizará el campo 'gasLimit' especificado en la transacción. Después del consenso usará la cantidad real evaluada de gas consumido por la transacción, permitiendo ajustes dinámicos en el sistema. Es imposible saber el _actual_ evaluado antes de consenso de gas porque el estado de la red puede afectar directamente al flujo de la transacción, que es por lo que pre-consenso utiliza el campo `gasLimit` y será referido como la **reserva de gas**.

Las solicitudes de consulta por contrato son únicas y se saltan completamente el escenario de consenso. Estas peticiones se ejecutan únicamente en el nodo local que las recibe y sólo influye en el acelerador de precheck de ese nodo específico. Por otra parte, las transacciones contractuales estándar atraviesan las etapas de prechequeo y consenso y están sujetas a ambos conjuntos de límites de velocidad. Los límites de limitación de prechequeo y consenso pueden establecerse a diferentes valores.

Para asegurar que las transacciones puedan ejecutarse correctamente, es común establecer una reserva de gas más alta que la que se consumirá por la ejecución. En Ethereum Mainnet, toda la reserva se cargará a la cuenta antes de la ejecución, y la parte no utilizada de la reserva será acreditada de vuelta. Sin embargo, Ethereum utiliza un grupo de memoria ([mempool](../../../support-and-community/glossary. d#mempool)) y hace pedidos de transacciones en tiempo de producción de bloques, permitiendo que el límite de bloque se base sólo en gas usado y no reservado.

Para ayudar a prevenir la sobrereservación, Hedera limita la cantidad de gas no utilizado que se puede devolver a un máximo del 20% de la reserva de gas original. Esto significa que se cobrará a los usuarios por al menos el 80% de su reserva inicial, independientemente del uso real. Esta regla está diseñada para aumentar la visibilidad de los usuarios para hacer estimaciones de gas más precisas.

Por ejemplo, si reservamos inicialmente 5 millones de unidades de gas para crear un contrato inteligente pero terminamos utilizando sólo 2 millones. Hedera le devolverá 1 millón de unidades de gas, i. ., 20% de su reserva inicial. Esta configuración apunta a equilibrar la gestión de recursos de la red mientras que la mitigación de los usuarios para ser lo más precisa posible en sus estimaciones de gas.

### Máximo gas por transacción

Debido a que la ejecución del tiempo de consenso ahora está limitada por el gas real utilizado y no basado en un recuento de transacciones, aumentar el límite de gas disponible para cada transacción es seguro. Antes de la medición a base de gas, sería posible que cada transacción consumiera el máximo gas por transacción sin tener en cuenta las demás transacciones, así que los límites se basaban en este peor escenario. Ahora que el acelerador es el gas agregado utilizado, podemos permitir que cada transacción consuma grandes cantidades de gas sin preocuparse por un aumento extremo.

Cuando una transacción es enviada a un nodo con un 'gasLimit' que es mayor que el límite de gas por transacción, la transacción debe ser rechazada durante la preverificación con un código de respuesta de `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`. La transacción no debe enviarse al consenso.

El acelerador de gas por contrato y el contrato crean **15 millones de gas por segundo**.

Referencia: [HIP-185](https://hips.hedera.com/hip/hip-185)
