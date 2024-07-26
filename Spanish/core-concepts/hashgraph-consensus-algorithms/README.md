---
description: Algoritmo de consenso distribuido
---

# Algoritmo de consenso Hashgraph

El algoritmo de consenso del hashgraph permite el consenso distribuido de una manera innovadora y eficiente. Hashgraph es un algoritmo de consenso distribuido y estructura de datos que es rápido, justo y seguro. Esto crea indirectamente una comunidad de confianza, incluso cuando los miembros no confían necesariamente entre sí.

El [algoritmo de consenso de hashgraph ](./) y el código de plataforma son de código abierto bajo una licencia Apache 2.0.

{% embed url="https://www.youtube.com/watch?v=cje1vuVKhwY&t=5s" %}

## Rendimiento

### Costo

El hashgraph es barato, en el sentido de evitar la prueba. Los individuos y las organizaciones que tienen nodos hashgraph no necesitan comprar caras plataformas de minería personalizada. En lugar de ello, pueden funcionar fácilmente disponibles y rentables. El hashgraph es 100% eficiente, sin desperdiciar recursos en cálculos que lo ralentizan.

### Eficiencia

El hashgraph es 100% eficiente, ya que ese término se utiliza en la comunidad blockchain. En blockchain, el trabajo a veces se malgasta minando un bloque que más tarde se considera obsoleto y es descartado por la comunidad. En el hashgraph, el equivalente de un “bloque” nunca se vuelve stale. Hashgraph también es eficiente en su uso del ancho de banda. Sea cual sea la cantidad de ancho de banda requerida simplemente para informar a todos los nodos de una transacción determinada (incluso sin alcanzar un consenso sobre una marca de tiempo para esa transacción), hashgraph añade sólo una sobrecarga muy pequeña más allá de ese mínimo absoluto. Adicionalmente, el algoritmo de voto de hashgraph, no requiere que se envíe ningún mensaje adicional para que los nodos voten (o esos votos sean contados) más allá de aquellos mensajes por los que la comunidad aprendió de la transacción misma.

### Producto

El hashgraph es rápido. Sólo está limitado por el ancho de banda. Si cada miembro tiene suficiente ancho de banda para descargar y cargar un determinado número de transacciones por segundo, el sistema en su conjunto puede manejar cerca de ese muchos. Incluso una conexión a Internet doméstica rápida podría ser lo suficientemente rápida como para manejar todas las transacciones de toda la red de tarjetas VISA en todo el mundo.

### **Eficiencia del Estado**

Una vez que ocurra un evento, en pocos segundos todos en la comunidad sabrán dónde debe ser colocado en la historia con 100% de seguridad. Lo que es más importante, todo el mundo sabrá que todos los demás lo saben. En ese momento, sólo pueden incorporar los efectos de la transacción y, a menos que sea necesario para una futura auditoría o cumplimiento, entonces descartarla. Por lo tanto, en un sistema mínimo de criptomonedas, cada miembro solo necesitaría almacenar el saldo actual de cada cuenta que no está vacía. No necesitarían recordar toda la historia de las transacciones que resultaron en esos saldos hasta la ‘genesis’.

## Seguridad

### Tolerancia asincrónica de fallo bizantino

El algoritmo de consenso de hashgraph es el Tolerante de Byzantine Fault. Esto significa que ningún miembro (o grupo pequeño de miembros) puede impedir que la comunidad alcance un consenso. Tampoco pueden cambiar el consenso una vez alcanzado. Cada miembro llegará finalmente a un punto en el que sabrá con certeza que ha alcanzado un consenso. Blockchain no tiene una garantía de acuerdo Byzantine, porque un miembro nunca llega a la certeza de que se ha alcanzado un acuerdo (sólo hay una probabilidad que aumenta con el tiempo). Blockchain también no es Byzantine porque no se ocupa automáticamente de las particiones de red. Si un grupo de mineros está aislado del resto de Internet, que pueden permitir el crecimiento de múltiples cadenas, que entran en conflicto entre sí en el orden de las transacciones.

Vale la pena señalar que el término “Tolerante Byzantine Fault” (BFT) a veces se utiliza en un sentido más débil por otros algoritmos de consenso. Pero aquí, se utiliza en su sentido original y más fuerte de que (1) cada miembro sabe que se ha llegado a un consenso. (2) los atacantes pueden coludir, y (3) los atacantes incluso controlan la propia Internet (con algunos límites). Hashgraph es Byzantine, incluso por esta definición más fuerte.

Hay diferentes grados de BFT, dependiendo de las suposiciones hechas sobre la red y la transmisión de mensajes. La forma más fuerte de BFT es la BFT asíncrona, lo que significa que puede alcanzar un consenso incluso si los actores maliciosos son capaces de controlar la red y eliminar o ralentizar los mensajes de su elección. Las únicas suposiciones hechas son que más de 2 de 3 están siguiendo correctamente el protocolo y que si los mensajes se envían repetidamente de un nodo a otro a través de Internet, eventualmente uno llegará, y luego otra voluntad, y así sucesivamente. Algunos sistemas son parcialmente asíncronos, que son seguros sólo si los atacantes no tienen demasiado poder y no manipulan demasiado el momento de los mensajes. Por ejemplo, un sistema parcialmente asincrónico podría demostrar Byzantine bajo la suposición de que los mensajes se pasan por Internet en diez segundos. Esta suposición ignora la realidad de las redes de bots, los ataques de denegación de servicio distribuida y los muros de fuego maliciosos.

### Conformidad ACID

El hashgraph es compatible con ACID. ACID (Atomicidad, Consistencia, Isolación, Durabilidad) es un término de base de datos y se aplica al hashgraph cuando se utiliza como base de datos distribuida. Una comunidad de nodos lo usa para alcanzar un consenso sobre el orden en el que se produjeron las transacciones. Después de llegar al consenso, cada nodo alimenta esas transacciones a la copia local de la base de datos, enviando cada una en el orden de consenso. Si la base de datos local tiene todas las propiedades estándar de una base de datos (ACID), entonces se puede decir que la comunidad en su conjunto tiene una base de datos única y distribuida con esas mismas propiedades. En blockchain, nunca hay un momento en el que se sabe que se ha alcanzado el consenso, por lo que no sería compatible con ACID.

### Resistencia al ataque de denegación de servicio (DDoS) distribuida

Una forma de ataque de Denegación de Servicio (DoS) ocurre cuando un atacante es capaz de inundar un nodo honesto en una red con mensajes sin sentido, impidiendo que ese nodo desempeñe otras tareas y roles (válidos). Una Denegación de Servicio Distribuida (DDoS) utiliza servicios públicos o dispositivos para amplificar involuntariamente ese ataque de denegación de servicio - haciéndolos una amenaza aún mayor.

En un contador distribuido, un ataque de DDoS podría apuntar a los nodos que contribuyen a la definición de consenso y, potencialmente, impiden que se establezca ese consenso.

Hashgraph es resistente a DDoS ya que no da poder a ningún nodo ni a un pequeño número de nodos con derechos o responsabilidades especiales en el establecimiento de un consentimiento. Tanto Bitcoin como hashgraph se distribuyen de una manera que resiste los ataques DDoS. Un atacante podría inundar a un miembro o minero con paquetes, para desconectarlos temporalmente de Internet. Pero la comunidad en su conjunto seguirá funcionando normalmente. Un ataque al sistema en su conjunto exigiría inundar una gran parte de los miembros con paquetes, lo que es más difícil. Ha habido una serie de alternativas propuestas a la cadena de bloqueos basadas en líderes o redondos. Se han propuesto para evitar los costes de prueba de trabajo de Bitcoin. Pero tienen el inconveniente de ser sensibles a los ataques DDoS. Si el atacante ataca al líder actual, y cambia a atacar al nuevo líder tan pronto como sea elegido, entonces el atacante puede congelar todo el sistema mientras sigue atacando sólo una computadora a la vez. Hashgraph evita este problema, pero no necesita prueba de trabajo.

## Equidad

Hashgraph es justo porque no hay líder o minero con permisos especiales para determinar la marca de tiempo consensuada asignada a una transacción. En cambio, la marca de tiempo consensuada para las transacciones se calcula a través de un proceso de votación en el que los nodos establecen colectiva y democráticamente el consentimiento. Podemos distinguir entre tres aspectos de la justicia.

### Acceso justo

Hashgraph es fundamentalmente justo porque ningún individuo puede impedir que una transacción entre en el sistema, o incluso retrasarla mucho. Si uno (o pocos) nodos maliciosos intenta impedir que una transacción determinada se entregue al resto de la comunidad y se agregue así al consenso, entonces la naturaleza aleatoria del protocolo de chismes asegurará que la transacción fluya alrededor de ese bloqueo.

### Timestamps justos

Hashgraph le da a cada transacción una marca de tiempo consensuada que refleja cuando la mayoría de los miembros de la red recibieron esa transacción. Esta marca de tiempo de consenso es “justa”, porque no es posible que un nodo malicioso lo corrompa y haga que difiera mucho de ese tiempo. A cada transacción se le asigna un tiempo de consenso, que es la mediana de los tiempos en que cada miembro dice que lo recibió por primera vez. Recibido aquí se refiere al tiempo que un nodo dado fue pasado por primera vez la transacción desde otro nodo a través de chismes. Esto forma parte del consenso, al igual que todas las garantías de Byzantine. Si más de 2 de los miembros participantes son honestos y tienen relojes confiables en su computadora, entonces la marca de tiempo será honesta y confiable, porque es generado por un miembro honesto y confiable o cae entre dos veces que fueron generados por miembros honestos y fiables. Debido a que el hashgraph toma la mediana de todos estos tiempos, la marca de tiempo del consenso es robusta. Incluso si algunos de los relojes están un poco apagados o incluso si algunos de los nodos dan maliciosamente tiempos que están lejos, la marca de tiempo del consenso no tiene un impacto significativo.

Esta marca de tiempo de consenso es útil para cosas como la obligación legal de llevar a cabo alguna acción en un momento determinado. Habrá un consenso sobre si un acontecimiento se produjo por un plazo, y la marca de tiempo es resistente a la manipulación de un atacante. En un blockchain, cada bloque contiene una marca de tiempo, pero refleja sólo un solo reloj: el uno en la computadora del minero que minó ese bloque.

### Orden de transacción justa

Las transacciones se ponen en orden de acuerdo a sus marcas de tiempo. Debido a que las marcas de tiempo asignadas a las transacciones individuales son justas, también lo es el orden resultante. Esto es de vital importancia para algunos casos de uso. Por ejemplo, imagina un mercado de valores, donde Alice y Bob tratan de comprar la última parte disponible de un stock al mismo momento por el mismo precio. En una cadena de bloqueos, un minero podría poner ambas transacciones en un solo bloque y tener total libertad para elegir el orden que se producen. O el minero podría optar por incluir sólo la transacción de Alice y retrasar la de Bob a un bloque futuro. En hashgraph, no hay manera de que un individuo afecte indebidamente el orden consensuado de esas transacciones. Lo mejor que Alice puede hacer es invertir en una mejor conexión a Internet para que su transacción llegue a todos antes que Bob’s. Esa es la forma justa de competir.

## FAQ

<details>

<summary>What is the hashgraph consensus algorithm? ¿Cómo funciona?</summary>

El algoritmo de consenso del hashgraph es un mecanismo de consenso distribuido utilizado por Hedera. Utiliza una estructura de datos llamada [hashgraph](../../support-and-community/glossary.md#hashgraph), y un mecanismo de consenso llamado el protocolo Gossip. Esta combinación permite un consenso rápido, justo y seguro. El algoritmo trabaja por cada nodo en la red compartiendo información (o “chismes”) sobre las transacciones que conoce con otros nodos en orden aleatorio.

</details>

<details>

<summary>¿Qué tan seguro es el algoritmo de consenso del hashgraph ?</summary>

Hashgraph es seguro porque es un Tolerante asíncrono de Fault Byzantine (aBFT). Esto significa que ningún miembro o grupo pequeño de miembros puede impedir que la comunidad alcance un consenso o cambie el consenso una vez que se haya alcanzado. También es compatible con ACID cuando se usa como base de datos distribuida, y es resistente a los ataques de [Denegación de Servicio Distribuida (DDoS)](../../support-and-community/glossary.md#distributed-denial-of-service-ddos).

</details>

<details>

<summary>¿Qué es la votación virtual?</summary>

La votación virtual es una parte integral del algoritmo de consenso del hashgraph . Permite a los nodos saber lo que otros votarían sin necesidad de enviar votos reales a través de Internet. Esto se logra examinando la historia de los chismes (quién habló con quién y en qué orden) para determinar cómo votaría un nodo basándose en la información que probablemente tenga.

</details>
