# Chisma sobre chismes

El consenso de Hashgraph utiliza un **protocolo de chismes**. Esto significa que un miembro como Alice elegirá a otro miembro al azar, como Bob, y entonces Alice le dirá a Bob toda la información que conoce hasta ahora. Alice luego se repite con un miembro aleatorio diferente. Bob hace repetidamente lo mismo y todos los demás miembros hacen lo mismo. De esta manera, si un solo miembro tiene conocimiento de la nueva información, se extenderá exponencialmente rápidamente a través de la comunidad hasta que cada miembro sea consciente de ello.

La sincronización de la información entre dos miembros a través del protocolo de chismes se llama una **sincronización de chismes**. Al finalizar la sincronización de chismes, cada miembro participante conmemora la sincronización de chismes con un evento. Un **evento** se almacena en memoria como una estructura de datos compuesta por una marca de tiempo, un array de cero o más transacciones, dos hash padre, y una firma criptográfica. Los dos hash padre son el hash del último evento creado por el auto padre antes de la sincronización de chismes y el hash del último evento creado por el otro padre antes de la sincronización de chismes. Por ejemplo, si Alice y Bob realizan una sincronización de chismes, Alice crearía un nuevo evento que conmemore la sincronización de chismes, donde el hash auto-padre sería el hash del último evento creado por Alice antes de la sincronización de chismes y el hash del otro padre sería el hash del último Bob de eventos creado antes de la sincronización de chismes. Bob también crearía un evento conmemorando la sincronización de chismes, pero el hash auto-padre sería el hash del último evento que creó antes de la sincronización de chismes y el hash de otro padre sería el hash del último evento creado por Alice antes de la sincronización de chismes. Los chismes continúan hasta que todos los miembros hayan recibido el evento recién creado.

## Chisma sobre chismes

La historia de cómo estos eventos están relacionados entre sí a través de sus hash padres se llama **chismes sobre los chismes**. Esta historia se expresa como un tipo de gráfica acíclica dirigida \(DAG\), una gráfica de hashes, o un hashgraph. El hashgraph registra la historia de cómo se comunicaron los miembros. Crece directamente con el tiempo a medida que se producen más sincronizaciones de chismes y se crean eventos. Todos los miembros mantienen una copia local del hashgraph que continúa actualizándose a medida que los miembros se sincronizan entre sí.

Estos hashgraphs pueden ser ligeramente diferentes en cualquier momento, pero siempre serán coherentes. Consistente significa que si \[Alice\] y \[Bob\] contienen el evento x, ambos contendrán exactamente el mismo conjunto de antepasados para x, y ambos contendrán exactamente el mismo conjunto de bordes entre esos antepasados.

Cada evento contiene lo siguiente:

- Timestamp
- Dos hash de dos eventos a continuación
  - Auto-padre
  - Otro-padre
- Transacciones
- Firma digital

| Objeto                               | Descripción                                                                                            |
| :----------------------------------- | :----------------------------------------------------------------------------------------------------- |
| **Marca de tiempo:** | Marca de tiempo del momento en que el miembro creó el evento conmemorando la sincronización de chismes |
| **Transacciones:**   | El evento puede contener cero o más transacciones                                                      |
| **Hash 1:**          | Hashh auto-padre                                                                                       |
| **Hash 2:**          | Hash Otro-padre                                                                                        |
| **Firma digital:**   | Criptográficamente firmado por el creador del evento                                                   |
