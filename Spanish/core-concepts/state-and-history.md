# Estado e Historia

### Maquinas Estatales

Una "[máquina de estado](../support-and-community/glossary.md#state-machine)" representa un enfoque conceptual de cómo funciona un programa: mantiene un "estado" y modifica este estado en respuesta a "transacciones". En una "máquina estatal replicada", el deber y la responsabilidad de manejar este estado en evolución se distribuyen a través de varias computadoras, ofreciendo tolerancia a faltas.

Hedera permite una máquina de estado replicado. Numerosos nodos, incluso potencialmente opuestos, pueden mantener consistentemente el estado de un conjunto de datos. Por ejemplo, la cantidad HBAR a través de un grupo de cuentas. Como se detalla anteriormente, las transacciones se envían a la red, y de forma constante, la [hashgraph](.. el algoritmo support-and-community/glossary.md#hashgraph) les asigna una marca de tiempo de consenso y una posición en la secuencia de consenso. Una vez que todos los nodos llegan a un acuerdo sobre la secuencia de transacciones, los aplican secuencialmente al estado. Este procedimiento asegura que la copia de estado de cada nodo siga siendo consistente. Cada nodo aplica (por ejemplo, ajusta el saldo del pagador y del destinatario para un pago HBAR) las transacciones al estado siguiendo la secuencia mutuamente acordada, preservando así un estado uniforme con otros nodos en cualquier momento específico.

<figure><img src="../.gitbook/assets/hh-consensus-service-whitepaper-icons.png" alt=""><figcaption></figcaption></figure>

### Estado vs. Historial

El último estado (p. ej. los saldos de HBAR de cada cuenta) y el historial de las transacciones que alteraron ese estado son dos estructuras de datos diferentes con diferentes propiedades. El estado es mutable por definición, cambiando constantemente a medida que las transacciones se aplican a él. En contraste, la historia de las transacciones se considera generalmente inmutable e irreversible. El estado y la historia presentan cargas de almacenamiento muy diferentes. Con el gran avance que puede apoyar Hedera, la historia crecerá muy rápidamente, lo que aumentará la carga de almacenarla. El estado también crecerá a medida que se creen nuevas cuentas, archivos y contratos inteligentes, pero a un ritmo más lento.

### Nodos de Tecnología Distribuida (DLT)

Hay tres funciones en su mayoría independientes que un nodo [distribuido ledger technology (DLT)](../support-and-community/glossary.md#distributed-ledger-technology-dlt) puede realizar:

- Contribuir a [consensus](../support-and-community/glossary.md#consensus)
- Persistir historial de transacciones
- Estado persistente

Como los nodos tienen recursos limitados, suele darse el caso de que un nodo no puede desempeñar de forma óptima todos los roles –y se deben tomar decisiones sobre qué funciones priorizar.

### Prioridades de nodos de red principal de Hedera

Para los nodos de Hedera Mainnet, las prioridades contribuyen al consenso y persisten en el estado. El hashgraph, que contiene todas las transacciones que cambian el estado, es constantemente podado después de que las transacciones se asignan un lugar en orden de consenso. Los nodos Mainnet pueden eliminar porciones antiguas del hashgraph porque el algoritmo entrega finalidad - una vez que una transacción ha sido asignada una marca de tiempo, ordenado, y luego aplicado al Estado, no hay posibilidad de revesar. En consecuencia, no hay necesidad de mantener transacciones históricas alrededor en caso de que sean necesarias para aplicarlas en un orden diferente. Para evitar que tales transacciones históricas llenen el almacenamiento del nodo, los nodos principales eliminan las transacciones históricas.

Pero hay valor en la historia que persiste, aunque no sea por nodos mainnet. Un auditor podría querer determinar las identidades de las partes que enviaron HBAR a una cuenta determinada o los tiempos de esas transferencias, ninguna de las cuales estaría disponible en el estado (e. ., los saldos de las cuentas).

### Funciones de los nodos de ira

[Nodos de espejo](mirror-nodes/) en la arquitectura Hedera, además de mantener estado, también puede almacenar el historial de transacciones. Un espejo en particular puede elegir si almacenar toda la historia, ninguna historia o posiblemente sólo una fracción de la historia, tal vez sólo para tipos de transacción en particular, cuentas en particular, etc. Además de la historia, los nodos espejos almacenan información que les permite demostrar que su historial es correcto, incluso para algunos tipos de historias parciales. Esto evita que un nodo espejo malicioso miente sobre lo que está almacenando. Una [client](../support-and-community/glossary.md#client) buscando una transacción del pasado consultaría una réplica apropiada para el registro de esa transacción. Como la carga del almacenamiento de la historia recae en los espejos y no en los nodos mainnet, estos últimos pueden ser optimizados para el papel más fundamental del consenso y el almacenamiento estatal.

## FAQ

<details>

<summary>What is the concept of <code>state</code> in the Hedera Network?</summary>

El estado en la Red Hedera es el estado actual de todos los datos, como la cantidad de HBAR en un conjunto de cuentas. Se mantiene a través de múltiples nodos en una representación consistente, proporcionando tolerancia a fallos. El estado cambia constantemente a medida que se aplican las transacciones.

</details>

<details>

<summary>¿Cómo maneja el historial de Hedera?</summary>

El historial de transacciones se mantiene como una estructura de datos independiente del estado. Proporciona un registro de transacciones que han cambiado el estado con el paso del tiempo. Normalmente está previsto como inmutable e irreversible. Los nodos de espejo en la arquitectura Hedera almacenan el historial de transacciones, mientras que los nodos mainnet se centran en el consenso y el almacenamiento del estado.

</details>

<details>

<summary>What are the roles of mainnet nodes and mirror nodes?</summary>

Los nodos Mainnet priorizan la contribución al consenso y al estado persistente. Eliminan transacciones históricas después de que se les asigne un lugar en el orden de consenso. Los nodos de espejo, por otra parte, almacenan el historial de transacciones y mantienen el estado, proporcionando un registro de transacciones pasadas para fines de auditoría.

</details>
