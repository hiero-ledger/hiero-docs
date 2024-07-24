# Etat et Historique

### Comprendre les Machines d'Etat

Une "[machine d'état](../support-and-community/glossary.md#state-machine)" représente une approche conceptuelle du fonctionnement d'un programme : elle maintient un "état" et modifie cet état en réponse à des "transactions" spécifiques. Dans une « machine d'état reproduite », le devoir et la responsabilité de gérer cet état évolutif sont répartis sur plusieurs ordinateurs, offrant une tolérance aux défauts.

Hedera permet une machine d'état répliquée. De nombreux nœuds, même potentiellement opposés, peuvent maintenir en permanence l'état d'un jeu de données. Par exemple, la quantité HBAR à travers un groupe de comptes. Comme détaillé plus tôt, les transactions sont soumises au réseau, et par la suite, le [hashgraph](.. support-and-community/glossary.md#hashgraph) leur attribue un timestamp de consensus et une position dans la séquence de consensus. Une fois que tous les nœuds sont parvenus à un accord sur la séquence de transaction, ils les appliquent successivement à l'état. Cette procédure garantit la cohérence de la copie d'état de chaque noeud. Chaque nœud applique (par exemple, ajuste les soldes du payeur et du bénéficiaire pour un paiement HBAR) les transactions à l'État suivant la séquence convenue mutuellement, préservant ainsi un état uniforme avec d'autres noeuds à tout moment précis.

<figure><img src="../.gitbook/assets/hh-consensus-service-whitepaper-icons.png" alt=""><figcaption></figcaption></figure>

### Etat vs historique

L'état le plus récent (par ex. les soldes HBAR de chaque compte) et l'historique des transactions qui ont modifié cet état sont deux structures de données distinctes avec des propriétés différentes. L'état est mutable par définition, en constante évolution au fur et à mesure que les transactions y sont appliquées. En revanche, l'histoire des transactions est généralement considérée comme immuable et irréversible. L'état et l'histoire présentent des charges de stockage très différentes. À la vitesse élevée que Hedera peut soutenir, l'histoire va se développer très rapidement, ce qui augmentera la charge de stockage. L'État va également croître à mesure que de nouveaux comptes, dossiers et contrats intelligents seront créés, mais à un rythme plus lent.

### Noeud de technologie distribuée (DLT)

Il y a trois fonctions pour la plupart indépendantes qu'une [technologie distribuée de ledger (DLT)](../support-and-community/glossary.md#distributed-ledger-technology-dlt) peut effectuer :

- Contribuer à [consensus](../support-and-community/glossary.md#consensus)
- Persister l'historique des transactions
- Etat persistant

Comme les nœuds ont des ressources limitées, Il est généralement vrai qu'un nœud ne peut pas remplir de manière optimale tous les rôles – et des choix doivent être faits quant aux fonctions à prioriser.

### Priorités des nœuds principaux d'Hedera

Pour les nœuds du réseau principal d'Hedera, les priorités contribuent au consensus et persévèrent dans l'état. Le hashgraph, qui contient toutes les transactions qui changent l'état, est constamment élagué après que les transactions soient assignées à un lieu de consensus. Les nœuds du réseau principal peuvent supprimer des parties plus anciennes du hashgraphe parce que l'algorithme fournit la finalité - une fois qu'une transaction a été assignée à un horodatage, commandé, puis appliqué à l'état, il n'y a aucune chance d'inverse. Par conséquent, il n'est pas nécessaire de conserver les transactions historiques au cas où elles seraient nécessaires pour les appliquer dans un autre ordre. Pour éviter que de telles transactions historiques ne remplissent le stockage du nœud, les nœuds principaux suppriment les transactions historiques.

Mais il y a une valeur dans l'histoire qui persiste, même si ce n'est pas par les nœuds du réseau principal. Un vérificateur pourrait vouloir déterminer l'identité des parties qui ont envoyé un HBAR à un compte donné ou le moment de ces transferts, dont l'un ou l'autre serait disponible à l'État (e. ., les soldes des comptes) seuls.

### Rôles de noeuds miroir

Les [noeuds miroirs](mirror-nodes/) dans l'architecture Hedera, en plus de maintenir l'état, peuvent également stocker l'historique des transactions. Un miroir particulier peut choisir de stocker toute l'histoire, pas d'histoire, ou peut-être seulement une fraction de l'histoire, peut-être uniquement pour des types de transactions particulières, des comptes particuliers, etc. En plus de l'histoire, les nœuds miroir stockent des informations qui leur permettent de prouver que leur historique est correct, même pour certains types d'histoires partielles. Cela empêche un nœud miroir malveillant de mentir sur ce qu'il stocke. Un [client](../support-and-community/glossary.md#client) recherchant une transaction passée interroge un miroir approprié pour l'enregistrement de cette transaction. Comme la charge du stockage de l'histoire est supportée par les miroirs et non par les nœuds principaux, ce dernier peut être optimisé pour le rôle plus fondamental du consensus et du stockage de l'État.

## Foire Aux Questions

<details>

<summary>What is the concept of <code>state</code> in the Hedera Network?</summary>

L'état dans le réseau Hedera est l'état actuel de toutes les données, comme le montant de HBAR dans un ensemble de comptes. Il est maintenu à travers plusieurs nœuds dans une représentation cohérente, fournissant une tolérance aux défauts. L'état change constamment à mesure que les transactions lui sont appliquées.

</details>

<details>

<summary>How does Hedera handle history?</summary>

L'histoire des transactions est maintenue comme une structure de données distincte de l'état. Il fournit un enregistrement des transactions qui ont changé l'état au fil du temps. Elle est généralement envisagée comme immuable et irréversible. Les nœuds miroir de l'architecture Hedera stockent l'historique des transactions, tandis que les nœuds principaux se concentrent sur le consensus et le stockage d'état.

</details>

<details>

<summary>Quels sont les rôles des noeuds principaux et des noeuds miroir ?</summary>

Les nœuds principaux donnent la priorité à la contribution au consensus et à l'état persistant. Ils suppriment les transactions historiques après qu'on leur ait assigné un lieu dans l'ordre de consensus. Les noeuds miroirs, par contre, enregistrent l'historique de la transaction et conservent l'état, fournissant un enregistrement des transactions passées à des fins d'audit.

</details>
