# Votación virtual

No basta con garantizar que todos los miembros conozcan todos los acontecimientos. It is also\
necessary to agree on a linear ordering of the events, and thus of the transactions\
recorded inside the events. La mayoría de los protocolos de tolerancia a defectos byzantinos sin un líder\
dependen de que los miembros se envíen uno al otro. .Algunos de estos protocolos\
requieren recibos de votos enviados a todos. Y ellos\
pueden requerir múltiples rondas de votación, lo que incrementa aún más el número de mensajes de voto\
enviados.

Este enfoque puro de voto se convierte en prohibitivo y poco práctico de ancho de banda en una red de cualquier tamaño significativo, pero tiene las propiedades de ser el método más justo y seguro de alcanzar el consentimiento. El algoritmo hashgraph implementa votaciones que logran las mismas propiedades justas y seguras pero también es muy rápido y práctico. Lo logra mediante **votación virtual**.

El algoritmo hashgraph no requiere que se envíe ningún voto a través de la red para calcular los votos de cada miembro. Los miembros pueden calcular los votos de todos los demás miembros examinando internamente cada una de sus copias del hashgraph y aplicando el algoritmo de votación virtual. Los votos se calculan localmente en función de los antepasados de un evento determinado.

Esta votación virtual tiene varios beneficios. Además de ahorrar ancho de banda, asegura que los miembros siempre calculen sus votos de acuerdo a las reglas. Si Alice es honesto, calculará votos virtuales para el Bob virtual que son honestos. Incluso si el verdadero Bob es un estafador, no puede atacar a Alicia haciendo que el virtual Bob vote incorrectamente.

Con este algoritmo de votación virtual, el acuerdo Byzantine está garantizado.

La votación virtual ocurre en 3 pasos:

1. Dividir Rondas
2. Decidir fama
3. Buscar pedido

## Dividir Rondas

Para comenzar el proceso de votación virtual, primero debemos definir rondas y testigos. En la historia del hashgraph, el primer evento para el nodo de un miembro es el primer **testigo**. El primer testigo es el comienzo de la primera ronda \(r\) para ese nodo. Todos los acontecimientos posteriores forman parte de esa primera ronda hasta que se descubre un nuevo testigo. Un testigo se descubre cuando un nodo crea un nuevo evento que **puede ver fuertemente** 2.3 de los testigos en la ronda actual. Por ejemplo, event w puede ver fuertemente el evento x cuando w puede rastrear su ascendencia a través de las relaciones de los padres que pasan a través de otros eventos que residen en al menos 2 de los nodos de los miembros. Cuando un evento está determinado a ver fuertemente 2 º 3 del testigo de la ronda actual, ese evento se considera el siguiente testigo para ese nodo. Ese nuevo testigo es el primer evento en la siguiente ronda \(r+1\) para ese nodo. A cada evento se le asigna una ronda mientras el evento se añade al hashgraph.

```text
procedure divideRounds

for each event x
    r <- max round of parents of x (or 1 if none exist)
    if x can strongly see more than 2n/3 round r witnesses
        x. pulido <- r+1
    else
      x.round <- r
    x. itness <- (x no tiene autopadre)
                o (x. encontrado > x.selfParent.round)
```

## Decidir fama

El siguiente paso es decidir si un testigo es un testigo famoso o no. Un testigo es famoso si muchos de los testigos de la siguiente ronda pueden verlo, y no es famoso si muchos no pueden. El evento A puede **ver** el evento B si el evento B es un antepasado del evento A. A la hora de decidir la fama de Witness A, debemos ver los testigos de la siguiente ronda. Si los testigos de la siguiente ronda pueden ver a Witness A, cuentan como un voto a favor de la fama de Witness A. De la misma manera, si un testigo en la próxima ronda no puede ver a Witness A, entonces el voto de ese testigo es que el Testigo A no es famoso. Para que el Testigo A sea considerado famoso, un futuro testigo debe ser capaz de ver con fuerza que al menos 2 de los testigos de los votos han votado a favor de que el Testigo A sea famoso. Si 2 de los testigos de las votaciones han votado que el Testigo A no es famoso, entonces el Testigo A se decidirá a no ser famoso.

## Buscar pedido

Ahora que hemos calculado todos los testigos de una ronda para ser famosos o no famosos, podemos determinar el orden de los acontecimientos que ocurrieron antes de los famosos testigos. Esto se hace calculando:

1. La **ronda recibida** para todos los eventos que aún deben ser ordenados y que han ocurrido antes de una ronda en la que se ha decidido la fama de todos los testigos. La ronda del evento recibida es la primera ronda donde todos los testigos famosos de esa ronda pueden ver \(o son descendientes de\) el evento en cuestión.
2. La **marca de tiempo** para cada evento. Esto se hace reuniendo a los primeros antepasados de los famosos testigos de la ronda recibida que también son descendientes del evento en cuestión. y tomando el timestamp mediano de esos eventos reunidos.
3. El **orden de los eventos** primero por: ronda recibida, marca de tiempo de consenso, luego firma.
