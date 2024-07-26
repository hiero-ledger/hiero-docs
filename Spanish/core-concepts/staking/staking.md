# Programa Staking

La función de apuestas se desplegará en cuatro fases. Las dos primeras fases se describen a continuación y las dos últimas estarán disponibles al inicio de la Fase I.

## **Fase I: Disponibilidad técnica \[Complete]**

La funcionalidad de apuesta ahora está disponible y en vivo tanto en Hedera Testnet como en Mainnet a partir del 21 de julio de 2022. En la fase I, los usuarios serán técnicamente capaces de colocar su cuenta en nodos de red principal, pero esto no contribuirá al peso consensual del nodo (poder de voto). Este lanzamiento inicial de disponibilidad técnica no premia a los participantes por apuestas, sino que permite una igualdad de condiciones para que todos los participantes del mercado tengan la posibilidad de participar en el programa de apuestas, y evita dar una ventaja injusta a los primeros que están en juego.

## **Fase II: Desarrollo del ecosistema \[Complete]**

Durante esta fase, las bolsas y billeteras soportadas serán capaces de integrar la funcionalidad de apuesta para proporcionar a los titulares de cuentas una forma fácil de apostar su HBAR, pero no distribuirá recompensas. Además, es probable que las aplicaciones web para delegar la participación se construyan para su utilización por el ecosistema minorista. Durante esta fase, habrá visibilidad de la apuesta por nodo, y apostar a un nodo afectará su peso consensuado (poder de voto) con actualizaciones mensuales.

## **Fase III: Recompensas del programa de lanzamiento \[Complete]**

El Consejo de Gobierno de Hedera determinará cuándo el ecosistema de Hedera ha alcanzado un conjunto mínimo viable de integraciones que permitan obtener recompensas. Una vez que esto se determine, el Consejo (a través de CoinCom) votará para actualizar la tasa de recompensa, y de forma constante, el mainnet se actualizará con la tarifa de recompensa acordada. La última tasa de recompensa de apuesta votada por CoinComm se puede encontrar [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).
\
Una vez actualizada, la cuenta de recompensa de apuesta (0.0. 00) será elegible para distribuir recompensas ganadas por los interesados, una vez que el límite de recompensas de 250M total de HBAR haya sido alcanzado. Las recompensas continuarán siendo distribuidas incluso si, después de este tiempo, el saldo de la cuenta 0.0.800 va por debajo de 250M.

## Fase IV: Implementación completa de consumo

En esta fase, las actualizaciones de 24 horas serán publicadas para que sean visibles en la apuesta por nodo, y la función de actualización del nodo será lanzada. Esto significa que en lugar de actualizar la visibilidad de la apuesta del nodo sobre una base mensual, la visibilidad de la apuesta del nodo se actualizará en un intervalo epoch de 24 horas. Cuando la función de tiempo de actividad entra en vigor, las cuentas apuestadas no ganarán recompensas cuando los nodos no puedan participar en el consenso (no disponible o sin conexión).

## **Nodos de Tomando**

{% hint style="info" %}
El Consejo de Gobierno de Hedera votó para cambiar el valor mínimo de la estaca de la mitad del valor máximo de la apuesta del nodo a 1/4 del valor máximo de la estaca del nodo.
{% endhint %}

Todos los nodos de consenso dirigidos por el Consejo de Gobierno de la Hedera distribuyen recompensas a las cuentas que les interesan. Puede encontrar información sobre cada nodo en la red visitando uno de los exploradores de la red Hedera o obteniendo la [libreta de direcciones](../../sdks-and-apis/rest-api.md#api-v1-network-nodes). En el futuro, la participación en la red se abrirá a los nodos comunitarios y, eventualmente, al público como parte de los esfuerzos de descentralización de Hedera.

Los nodos tienen una **apuesta mínima** y una **apuesta máxima**. La apuesta mínima del nodo debe ser cumplida para que las cuentas apostadas a ese nodo sean elegibles para ganar recompensas apuestas. Los tokens tomados que superen la apuesta máxima ya no afectarán a la proporción de recompensas devueltas. El umbral máximo de apuesta para cada nodo será el número total de HBAR dividido por el número total de nodos en la red. El valor umbral mínimo de la apuesta del nodo será 1/4 del valor máximo de la apuesta del nodo. Estos valores cambiarán a medida que se añadan más nodos a la red o pueden cambiar por voto del Consejo de Gobierno de Hedera.

#### Ejemplo:

Toma mínima: 50.000.000 hbars\*(1/26nodes)\*(1/4)

Toma máxima: 50.000.000 hbars\*(1/26nodes)

## **Periodo de bloqueo**

No hay **período de bloqueo** cuando las cuentas son apostadas a un nodo. Los interesados no necesitan elegir una cantidad de HBAR para apostar desde su cuenta. El saldo completo de la cuenta se apuestan automáticamente al nodo o cuenta seleccionada. No hay ningún concepto de “vinculación” o “recortar” de sus fichas. El saldo de cuenta apuestado es líquido en todo momento.

## **Cuenta de Recompensa**

La cuenta de recompensa apuestada distribuye recompensas a cuentas apostadas elegibles. El ID de la cuenta de recompensa apuestada es [0.0.800](https://hashscan.io/#/mainnet/account/0.0.800?type=) en mainnet. Cualquiera en la comunidad puede contribuir al fondo de recompensas transfiriendo HBAR a esa cuenta. Esta cuenta no tiene llaves, y por lo tanto, cualquier HBAR transferido a esta cuenta no puede ser devuelto al propietario. Si eliges contribuir al fondo de recompensas, por favor asegúrate de revisar los detalles de tu transacción de transferencia.

La cuenta de recompensa apuestada necesita alcanzar un saldo mínimo antes de que las recompensas puedan comenzar a distribuir recompensas ganadas a las cuentas apostadas elegibles. El umbral mínimo de saldo de HBAR para la cuenta de recompensa es de 250 millones de HBAR votados por el Consejo de Gobierno de Hedera. Si este saldo no se cumple, las recompensas de apuesta no se distribuirán. Puede ver el saldo de esta cuenta visitando cualquiera de los exploradores de la red Hedera.

Una vez alcanzado el umbral mínimo, las recompensas continuarán distribuidas a las cuentas apuestadas siempre y cuando haya un saldo en la cuenta de recompensas incluso si cae por debajo del umbral mínimo inicial. La tasa de recompensa se establecerá inicialmente en cero. El Consejo de Gobierno de Hedera votará y actualizará la tasa de recompensa cuando el Programa de Recompensas de Hedera salga en vivo. La última tasa de recompensa puede encontrarse [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).&#x20

## **Recompensas de recogida**

En la Fase I, la tasa de recompensa inicial será cero. El Consejo de Gobierno de Hedera determinará cuándo el ecosistema de Hedera ha alcanzado un conjunto mínimo viable de integraciones que permitan obtener recompensas. Una vez que esto se determine, el Consejo (a través de CoinCom) votará para actualizar la tasa de recompensa, y de forma constante, el mainnet se actualizará con la tarifa de recompensa acordada.

Cualquier cuenta puede optar por apostar a un nodo u otra cuenta. El **periodo mínimo de apuesta** es la cantidad mínima de tiempo que una cuenta necesita ser apostada a un nodo de consenso antes de que la cuenta sea elegible para ganar recompensas. El periodo mínimo de apuesta es **un día (24 horas).** El periodo de apuestas comienza a medianoche UTC y termina a medianoche UTC. El Consejo de Gobierno de la Hedera define el período de apuestas. Las recompensas ganadas no se transfieren a la cuenta apuestada inmediatamente después de que una cuenta haya sido apostada por un período completo de apuesta. Por favor vea la sección Distribución de Recompensas de Toma para ver qué escenarios activan el pago de una recompensa.

Las cuentas apostadas por menos del período de apuesta mínimo definido no son elegibles para ganar recompensas por ese período. Los nodos y las cuentas acumulan apuestas y recompensas por cada HBAR completo. Las fracciones se redondean hacia abajo.

Para que una cuenta apuestada sea elegible para ganar recompensas, lo siguiente debe ser cierto:

- La cuenta de recompensa de apuesta debe haber alcanzado el saldo de umbral inicial de HBAR
  - Una vez que se ha alcanzado el valor umbral mínimo, la cuenta de recompensas continuará recompensando las cuentas apostadas incluso si el saldo cae por debajo del umbral inicial
- La cuenta que el nodo está apostado para cumplir con el valor umbral mínimo de la apuesta del nodo
- La cuenta debe ser apostada por el período mínimo de apuesta
- La tasa de recompensa es votada por el Consejo de Gobierno de Hedera y actualizada en la red principal

Las recompensas continuarán ganándose cuando un nodo esté caído o inactivo en la primera fase. El Consejo (a través de CoinCom) ha votado a favor de implementar un límite máximo de [2,5% anual de tasa de recompensa](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm). La tarifa de recompensa real variará dependiendo de la cantidad de HBAR que se apuesten para recompensas, pero la tasa no excederá el límite. En el futuro, cuando los nodos estén abajo o inactivos, la cuenta apuestada no será elegible para ganar recompensas.

Este sistema de apuestas ofrece una funcionalidad única adicional: **apuestas indirectas**. Si la cuenta A apuesta al nodo N, entonces la apuesta aumenta el peso consensuado de N, y la cuenta A es recompensada por cada periodo de 24 horas que apuesta. Si la cuenta A apuesta a la cuenta B, y la cuenta B apuesta al nodo N, entonces la apuesta tanto de A como de B aumentará el peso consensuado de N, pero las recompensas tanto para A como B serán recibidas por B.

Una cuenta puede declinar opcionalmente para ganar recompensas cuando se apuesta. La cuenta seguirá contándose para cumplir con el valor mínimo de apuesta del nodo.

\*\*📣 Si estás interesado en revisar las billeteras y los intercambios de soporte para staking HBAR, dirígete a la página \*\* [**Tomar HBAR**](stake-hbar.md) \*\*. \*\*
