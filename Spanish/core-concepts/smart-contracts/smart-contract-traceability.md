# Rastreabilidad del Contrato Inteligente

Después de que se hayan desplegado contratos, es posible que desee investigar más a fondo la ejecución de una llamada a la función de contrato inteligente. Las trazas proporcionan una visión completa de la secuencia de operaciones y sus efectos, permitiendo el análisis, depuración y auditoría del comportamiento inteligente de los contratos. Los dos tipos de trazas útiles:

**➡️** [**Traza de llamadas**](smart-contract-traceability.md#call-trace)

**➡️** [**Tráfico de Estado**](smart-contract-traceability.md#state-trace)

***

## Traza de llamada

La información de **seguimiento de llamadas** del contrato captura los detalles de entrada, salida y gas de todas las funciones anidadas de contratos inteligentes ejecutadas en una transacción. En Ethereum, se llaman transacciones internas de vez en cuando pero simplemente capturan instantáneas de la consideración de frame de mensaje que el EVM encuentra al procesar una ejecución de contrato inteligente en cada profundidad para todas las funciones involucradas.

<table data-header-hidden><thead><tr><th width="173"></th><th></th></tr></thead><tbody><tr><td><strong>Input Data</strong></td><td>Registra los datos de entrada o parámetros proporcionados al llamar a una función particular dentro de un contrato inteligente. Estos datos de entrada son esencialmente la forma codificada de la firma de la función y sus argumentos.</td></tr><tr><td><strong>datos de salida</strong></td><td>Después de ejecutar la función, la información de seguimiento incluye los datos de salida devueltos por esa función. Esto puede ser el resultado del cálculo de la función o cualquier dato que genere como parte de su ejecución.</td></tr><tr><td><strong>Detalles de gas</strong></td><td>Registros de información sobre el gas consumido por cada llamada de función. Cada operación dentro de una función consume una cierta cantidad de gas, y esta información se rastrea para calcular el coste total de la transacción.</td></tr></tbody></table>

Esta información puede ser consultada usando el ID de transacción o el hash de transacción Ethereum.

i Información detallada para la traza de llamada puede encontrarse en la Hedera [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_action.proto) e incluye:

<table><thead><tr><th width="178">Datos de rastreo de llamadas</th><th>Descripción</th></tr></thead><tbody><tr><td><strong>tipo de operación de llamada</strong></td><td><p>Tipo específico de operación realizada durante la ejecución de un contrato inteligente o una transacción en la EVM. Ejemplo: “CALL” es un tipo de operación utilizado cuando una transacción invoca una función dentro de un contrato inteligente. Ejecuta la función y potencialmente puede modificar el estado del contrato.</p><pre><code>OP_UNKNOWN = 0;
OP_CALL = 1;
OP_CALLCODE = 2;
OP_DELEGATECALL = 3;
OP_STATICCALL = 4;
OP_CREATE = 5;
OP_CREATE2 = 6;
</code></pre></td></tr><tr><td><strong>Datos de resultado</strong></td><td>Los datos resultantes son los valores de salida o retorno generados por la ejecución de una función o acción de contrato inteligente. Cuando se ejecuta una llamada de función, puede producir datos como resultado, tales como valores calculados, indicadores de estado, razón de revertir contrato si cualquiera y el error si la transacción misma falló sin un explícito <code>REVERTER</code></td></tr><tr><td><strong>Tipo de datos de resultado</strong></td><td>El "tipo de datos de resultado" se refiere al tipo de datos del valor devuelto por la función o método. Por ejemplo, si tienes una función <code>add(a, b)</code> que suma dos números y devuelve el resultado, el tipo de datos del resultado puede ser un entero si devuelve la suma de los números.</td></tr><tr><td><strong>Llamar a Profundidad</strong></td><td>El nivel o profundidad de la llamada de función actual dentro de la pila de llamadas. Proporciona información sobre la naturaleza anidada de las llamadas a funciones y ayuda a rastrear la secuencia y jerarquía de las invocaciones de funciones durante la ejecución de un contrato inteligente. <br><br>The caller depth indicates how many functions have been called before the current function in the call stack. Se inicia en 0 para la invocación inicial de la función y incrementos por 1 para cada llamada de función posterior. <br><br>For example, the parent transaction would be represented as call depth 1 and first child would be at call depth 1.1 and child transaction 2 would be at call depth 1.2. La transacción hijo a profundidad 1.2 tiene dos padres.</td></tr><tr><td><strong>Llamador</strong></td><td>La persona que llama puede ser el ID de la cuenta llamando al contrato o el ID de otro contrato inteligente que llame al contrato. <br><br>The first action in the tree can only come from an account. El resto de las acciones en el árbol de llamadas provienen del contrato. <br><br>When a smart contract function is invoked, either by an external account or by another contract, the caller address is recorded in the trace to identify the source of the function call. La dirección de llamada puede ser útil para entender el contexto de la ejecución y determinar el origen de la transacción o mensaje que desencadenó la llamada a la función.</td></tr><tr><td><strong>destinatario</strong></td><td>La dirección del contrato inteligente o cuenta que recibe una llamada o transacción específica. Representa el destino o objetivo de la interacción dentro de la EVM. La acción del contrato puede dirigirse a uno de los siguientes: <br><br>• Cuenta: El ID de la cuenta del destinatario si el destinatario es una cuenta. Sólo los HBARs serán transferidos. <br>• Contract: The contract ID if the recipient is a smart contract <br>• EVM address : If the contract action was directed to an invalid solidity address, what that address was</td></tr><tr><td><strong>de</strong></td><td>El contrato de Hedera llamando al siguiente contrato.</td></tr><tr><td><strong>a</strong></td><td>El contrato que recibe la llamada o se está creando.</td></tr><tr><td><strong>Valor/Cantidad</strong></td><td>La cantidad de hbars transferidos dentro de esta llamada.</td></tr><tr><td><strong>Límite de gas</strong></td><td>El gas se define como el límite superior de gas que esta llamada por contrato puede gastar.</td></tr><tr><td><strong>Gas Utilizadas</strong></td><td>La cantidad de gas que se utilizó para la llamada al contrato.</td></tr><tr><td><strong>Input</strong></td><td>Bytes pasados como datos de entrada a esta llamada de contrato</td></tr></tbody></table>

**Ejemplo**:

<figure><img src="../../.gitbook/assets/smart-contracts-core-concepts-call-trace.png" alt=""><figcaption><p>Ejemplo de seguimiento de llamadas en HashScan</p></figcaption></figure>

***

## Traza de Estado

El estado de Smart Contract cambiará ahora cuando una transacción de contrato inteligente modifique el estado del contrato. Esto permitirá a los desarrolladores tener un rastro de papel de los cambios de estado que ocurrieron para un contrato desde el momento en que se creó el contrato. Los cambios de estado que serán rastreados incluyen cada vez que un valor es leído o escrito en el contrato inteligente. La ranura de almacenamiento representa el orden en el que se lee o escribe el estado del contrato inteligente.

El valor leído refleja el valor de almacenamiento antes de la ejecución de la transacción de contrato inteligente. El valor escrito, si está presente, representa el valor actualizado final de la ranura de almacenamiento después de completar la llamada de contrato inteligente. Los estados transversales entre el inicio y la finalización del contrato no se almacenan en el registro de transacción.

la información detallada sobre el seguimiento de estado puede encontrarse en la [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_state\_change.proto) e incluye:&#x20

<table><thead><tr><th width="205">Datos de seguimiento de estado</th><th>Descripción</th></tr></thead><tbody><tr><td>Dirección</td><td><p>La dirección EVM del contrato inteligente.</p><p>Ex: <code>0000000000000000000000000000000000001f41</code></p></td></tr><tr><td>ID del Contrato</td><td><p>El ID del contrato inteligente.</p><p>Ex: <code>0.0.1234</code></p></td></tr><tr><td>Ranura</td><td>Se refiere a una ubicación de almacenamiento donde los datos se almacenan dentro del estado del contrato. También puede ser considerado como una variable o una unidad de almacenamiento que tiene un valor específico.</td></tr><tr><td>Valor leído</td><td>Los valores actuales de variables o estructuras de datos antes de realizar modificaciones. Estos valores pueden ser usados para validar condiciones, realizar cálculos, o disparar acciones específicas dentro del código del contrato.</td></tr><tr><td>Valor escrito</td><td>Las variables o estructuras de datos escritas o cambiadas después de la modificación.</td></tr></tbody></table>

### Nodo de consenso

Los nodos de consenso almacenan registros sidecar llamados `ContractStateChanges`. Cada vez que un estado de contrato inteligente cambie, se producirá un nuevo registro que conmemore los cambios de estado para el contrato que tuvo lugar.

### Nodo de Mirror

El [nodo espejo](../../support-and-community/glossary.md#mirror-nodes) de Hedera soporta dos API de resto que devuelven información sobre los cambios de estado del contrato inteligente. Esto incluye:

- `/api/v1/contracts/{id}/results/{timestamp}`
- `/api/v1/contracts/resultados/{transactionIdOrHash}`

**Ejemplo:**

```
    "state_changes": [
      {
        "address": "0000000000000000000000000000000000001f41",
        "contract_id": "0.1.2",
        "slot": "0x00000000000000000000000000000000000000000000000000000000000000fa",
        "value_read": "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
        "value_written": "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
      }
    ]
```

### Explorador de nodos de ira de Hedera

El seguimiento del estado se puede ver en un explorador de red de Hedera soportado.

<figure><img src="../../.gitbook/assets/smart-contracts-network-explorer-example.png" alt=""><figcaption><p>Ejemplo de seguimiento de estado en HashScan</p></figcaption></figure>
