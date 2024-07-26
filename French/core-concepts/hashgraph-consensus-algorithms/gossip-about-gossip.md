# Ragots à propos de Gossip

Le consensus de Hashgraph utilise un **protocole de commutation**. Cela signifie qu'un membre tel qu'Alice choisira un autre membre au hasard, comme Bob, puis Alice dira à Bob toutes les informations qu'elle sait jusqu'ici. Alice se répète ensuite avec un membre aléatoire différent. Bob fait la même chose à plusieurs reprises, et tous les autres membres font de même. De cette façon, si un seul membre prend connaissance de nouvelles informations, il se répandra exponentiellement vite à travers la communauté jusqu'à ce que chaque membre en soit conscient.

La synchronisation des informations entre deux membres via le protocole gossip est appelée **synchro de ragots**. Une fois la synchronisation des ragots terminée, chaque membre participant commémore la synchronisation des ragots avec un événement. Un **événement** est stocké en mémoire sous la forme d'une structure de données composée d'un timestamp, un tableau de zéro ou plus de transactions, deux hachages parents, et une signature cryptographique. Les deux hashs parents sont le hachage du dernier événement créé par le parent avant la synchronisation des ragots et le hachage du dernier événement créé par l'autre parent avant la synchronisation des ragots. Par exemple, si Alice et Bob effectuent une synchronisation de commutations, Alice créerait un nouvel événement commémorant la synchronisation des commérages où le hash auto-parent serait le hash du dernier événement Alice créé avant la synchronisation des commérages et le hash des autres parents serait le hash du dernier événement Bob créé avant la synchronisation des commérages. Bob créerait également un événement commémorant la synchronisation des commérats, mais le hash auto-parent serait le hash du dernier événement qu'il a créé avant la synchronisation des commutateurs et l'autre hash parent serait le hash du dernier événement créé par Alice avant la synchronisation des commérages. Gossip continue jusqu'à ce que tous les membres aient reçu l'événement nouvellement créé.

## Ragots à propos de Gossip

L'histoire de la manière dont ces événements sont liés les uns aux autres par le biais de leurs hashes parents est appelée **commémorer sur les ragots**. Cette histoire s'exprime comme un type de graphique acyclique orienté \\(DAG\\), un graphe de hachage, ou un hashgraph. Le hashgraph enregistre l'historique de la communication des membres. Il grandit de façon directe, avec le temps, au fur et à mesure que de nouvelles synchro ont lieu et que des événements sont créés. Tous les membres conservent une copie locale du hashgraph qui continue à se mettre à jour au fur et à mesure que les membres se synchronisent.

Ces hashgraphes peuvent être légèrement différents à un moment donné, mais ils seront toujours cohérents. Conformément signifie que si \[Alice\] et \[Bob\] tous les deux contiennent un événement x, ils contiendront tous les deux exactement le même ensemble d'ancêtres pour x, et contiendra tous les deux exactement le même ensemble de bords entre ces ancêtres.

Chaque événement contient les éléments suivants :

- Horodatage
- Deux hashs de deux événements en-dessous de lui-même
  - Auto-parent
  - Autre parent
- Transactions
- Signature numérique

| Élément                                  | Libellé                                                                                                      |
| :--------------------------------------- | :----------------------------------------------------------------------------------------------------------- |
| **Horodatage :**         | L'horodatage de la date à laquelle le membre a créé l'événement en commémorant la synchronisation des ragots |
| **Transactions:**        | L'événement peut contenir zéro ou plus de transactions                                                       |
| **Hachage 1:**           | Hachage auto-parent                                                                                          |
| **Hachage 2:**           | Hachage autre parent                                                                                         |
| **Signature numérique:** | Signé cryptographiquement par le créateur de l'événement                                                     |
