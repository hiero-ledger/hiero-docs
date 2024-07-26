---
description: Algorithme de consensus distribué
---

# Algorithme de consensus Hashgraph

L'algorithme de consensus hashgraph permet un consensus distribué de manière innovatrice et efficace. Hashgraph est un algorithme de consensus distribué et la structure de données qui est rapide, équitable et sécurisé. Cela crée indirectement une communauté de confiance, même lorsque les membres ne se font pas nécessairement confiance.

Le [algorithme de consensus hashgraph](./) et le code de la plate-forme sont open-source sous une licence Apache 2.0.

{% embed url="https://www.youtube.com/watch?v=cje1vuVKhwY&t=5s" %}

## Performances

### Coûts

Le hashgraphe est peu coûteux, dans le sens où il évite des preuves de travail. Les individus et les organisations qui utilisent des nœuds hashgraph n’ont pas besoin d’acheter des plates-formes minières sur mesure coûteuses. Au lieu de cela, ils peuvent fonctionner facilement et à moindre coût. Le hashgraph est 100% efficace, ne gaspillant aucune ressource sur les calculs qui le ralentissent.

### Efficacité

Le hashgraph est 100% efficace, car ce terme est utilisé dans la communauté blockchain. Dans la blockchain, le travail est parfois gaspillé à miner un bloc qui plus tard est considéré comme obsolète et est jeté par la communauté. Dans le hashgraph, l’équivalent d’un « bloc» ne devient jamais dépassé. Hashgraph est également efficace dans son utilisation de la bande passante. Quelle que soit la quantité de bande passante requise pour simplement informer tous les nœuds d'une transaction donnée (même sans parvenir à un consensus sur un timestamp pour cette transaction), hashgraph n'ajoute qu'une très petite surcharge au-delà de ce minimum absolu. De plus, l’algorithme de vote du hashgraphe ne nécessite pas d’envoi de messages supplémentaires pour que les nœuds puissent voter (ou ces votes à compter) au-delà des messages par lesquels la communauté a appris la transaction elle-même.

### Débit

Le hashgraphe est rapide. Il est limité uniquement par la bande passante. Si chaque membre a suffisamment de bande passante pour télécharger et télécharger un nombre donné de transactions par seconde, le système dans son ensemble peut gérer près de cela beaucoup. Même une connexion Internet à domicile rapide pourrait être assez rapide pour gérer toutes les transactions de l'ensemble du réseau de cartes VISA dans le monde entier.

### **Efficacité d'État**

Une fois qu'un événement se produit, en quelques secondes, tout le monde dans la communauté saura où il devrait être placé dans l'historique avec une certitude totale. Plus important encore, tout le monde saura que tout le monde le sait. À ce moment-là, ils peuvent simplement incorporer les effets de la transaction et, à moins que cela ne soit nécessaire pour la vérification ou la conformité future, puis les écarter. Ainsi, dans un système minimal de cryptomonnaies, chaque membre n’aura besoin que de stocker le solde actuel de chaque compte qui n’est pas vide. Ils n’auraient pas besoin de se souvenir de l’histoire complète des transactions qui ont abouti à ces soldes jusqu’à la « genèse ».

## Sécurité

### Tolérance de Défaut Byzantine asynchrone

L'algorithme de consensus de hashgraph est asynchrone Byzantine Fault Tolerant. Cela signifie qu’aucun membre (ou petit groupe de membres) ne peut empêcher la communauté d’obtenir un consensus. Ils ne peuvent pas non plus changer le consensus une fois qu'il aura été atteint. Chaque membre finira par arriver à un point où il sait avec certitude qu'il est parvenu à un consensus. La Blockchain n'a pas de garantie de l'accord Byzantin, parce qu’un membre n’obtient jamais la certitude qu’un accord a été conclu (il y a juste une probabilité qui augmente au fil du temps). La Blockchain est également non-Byzantine car elle ne gère pas automatiquement les partitions réseau. Si un groupe de mineurs est isolé du reste de l'Internet, qui peuvent permettre à plusieurs chaînes de grandir, ce qui entre en conflit sur l'ordre des transactions.

Il convient de noter que le terme « Byzantine Fault Tolerant» (BFT) est parfois utilisé dans un sens plus faible par d’autres algorithmes de consensus. Mais ici, il est utilisé dans son sens original, plus fort que (1) chaque membre sait que le consensus a été atteint, (2) des attaquants peuvent se heurter et (3) des attaquants contrôlent même l'internet lui-même (avec quelques limites). Hashgraph est Byzantine, même par cette définition plus forte.

Il y a différents degrés de BFT, selon les hypothèses faites au sujet du réseau et de la transmission des messages. La forme la plus puissante de BFT est une BFT asynchrone, ce qui signifie qu'elle peut atteindre un consensus même si les acteurs malveillants sont capables de contrôler le réseau et de supprimer ou de ralentir les messages de leur choix. Les seules hypothèses faites sont que plus de 2<unk> 3 suivent correctement le protocole et que si les messages sont envoyés à plusieurs reprises d'un nœud à un autre sur Internet, finalement, on finira par passer, et ensuite par une autre volonté, et ainsi de suite. Certains systèmes sont partiellement asynchrones, qui ne sont sécurisés que si les attaquants n'ont pas trop de puissance et ne manipulent pas trop le moment des messages. Par exemple, un système partiellement asynchrone pourrait s'avérer Byzantin en supposant que les messages sont passés sur Internet en dix secondes. Cette hypothèse ignore la réalité des botnets, des attaques par déni distribué de service et des pare-feu malveillants.

### Conformité ACID

Le hashgraph est conforme à l'ACID. L'ACID (Atomicité, Consistencie, Isolation, Durabilité) est un terme de base de données et s'applique au hashgraphe lorsqu'il est utilisé comme base de données distribuée. Une communauté de noeuds l'utilise pour parvenir à un consensus sur l'ordre dans lequel les transactions se sont produites. Après avoir atteint un consensus, chaque nœud envoie ces transactions à la copie locale de la base de données de ce nœud, en envoyant chacun dans l'ordre de consensus. Si la base de données locale a toutes les propriétés standards d'une base de données (ACID), alors on peut dire que la communauté dans son ensemble a une base de données unique et distribuée avec ces mêmes propriétés. Dans la blockchain, il n'y a jamais un moment où vous savez que le consensus a été atteint, donc il ne serait pas conforme à l'ACID.

### Résistance d'attaque (DDoS) déni de service distribué

Une forme d'attaque de déni de service (DoS) se produit quand un attaquant est capable d'inonder un noeud honnête sur un réseau avec des messages insignifiants empêchant ce noeud d'effectuer d'autres tâches et rôles (valides). Un déni de service distribué (DDoS) utilise des services ou des dispositifs publics pour amplifier involontairement cette attaque de DoS, ce qui en fait une menace encore plus grande.

Dans un livre distribué, une attaque DDoS pourrait cibler les nœuds qui contribuent à la définition du consensus et, empêcher éventuellement l'établissement de ce consensus.

Hashgraph est DDoS résistant car il n'autorise aucun noeud ou un petit nombre de nœuds ayant des droits ou des responsabilités spéciaux dans l'établissement d'un consensus. Bitcoin et hashgraph sont distribués d'une manière qui résiste aux attaques DDoS. Un attaquant pourrait inonder un membre ou un mineur de paquets, pour le déconnecter temporairement de l'internet. Mais la communauté dans son ensemble continuera à fonctionner normalement. Une attaque sur le système dans son ensemble nécessiterait d'inonder une grande partie des membres avec des paquets, ce qui est plus difficile. Il y a eu un certain nombre d'alternatives proposées à la blockchain basée sur les leaders ou le round-robin. Celles-ci ont été proposées pour éviter les coûts de preuve de travail du Bitcoin. Mais ils ont l'inconvénient d'être sensibles aux attaques DDoS. Si l'attaquant attaque le chef actuel, et passe à attaquer le nouveau chef dès qu'il est choisi, alors l'attaquant peut geler tout le système tout en n'attaquant qu'un seul ordinateur à la fois. Hashgraph évite ce problème, mais n'a toujours pas besoin de preuves de travail.

## Équité

Hashgraph est juste parce qu'il n'y a pas de leader ou de mineur donné des autorisations spéciales pour déterminer l'horodatage du consensus attribué à une transaction. Au lieu de cela, l'horodatage du consensus pour les transactions est calculé par un processus de vote dans lequel les nœuds établissent collectivement et démocratiquement le consensus. Nous pouvons distinguer trois aspects de l'équité.

### Accès équitable

Hashgraph est fondamentalement juste car aucun individu ne peut empêcher une transaction d'entrer dans le système, ou même le retarder beaucoup. Si un (ou plusieurs) nœuds malveillants tente d'empêcher une transaction donnée d'être livrée au reste de la communauté et ainsi être ajoutée à un consensus, alors la nature aléatoire du protocole de ragot assurera que la transaction coule autour de ce blocage.

### Horodatages équitables

Hashgraph donne à chaque transaction un timestamp de consensus qui reflète lorsque la majorité des membres du réseau ont reçu cette transaction. Cet horodatage du consensus est « juste », parce qu'il n'est pas possible pour un nœud malveillant de le corrompre et de le rendre très différent à partir de ce moment-là. Chaque transaction est assignée à un temps de consensus, qui est la médiane des moments où chaque membre l'indique pour la première fois. Reçu ici fait référence au moment où un noeud donné a été passé pour la première fois la transaction à partir d'un autre noeud à travers des commutations. Cela fait partie du consensus, de même que toutes les garanties d'être Byzantine. Si plus de 2 <unk> 3 des membres participants sont honnêtes et ont des horloges fiables sur leur ordinateur, alors l'horodatage lui-même sera honnête et fiable, parce qu'elle est générée par un membre honnête et fiable ou tombe entre deux fois qui ont été générés par des membres honnêtes et fiables. Parce que le hashgraph prend la médiane de tous ces temps, l'horodatage du consensus est robuste. Même si quelques horloges sont un peu éteintes, ou même si certains nœuds donnent de manière malveillante des temps qui sont éloignés, le timestamp du consensus n'est pas significativement impacté.

Cet horodatage consensuel est utile pour des choses telles qu'une obligation légale d'exécuter une action à un moment donné. Il y aura un consensus sur la question de savoir si un événement s'est produit par un délai, et le timestamp est résistant à la manipulation par un attaquant. Dans une blockchain, chaque bloc contient un horodatage, mais il ne reflète qu'une seule horloge: celle sur l'ordinateur du mineur qui a miné ce bloc.

### Commande de transaction équitable

Les transactions sont passées en ordre selon leur horodatage. Parce que les horodatages attribués à des transactions individuelles sont justes, la commande qui en résulte aussi. C'est extrêmement important pour certains cas d'utilisation. Par exemple, imaginez un marché boursier, où Alice et Bob essaient tous deux d'acheter la dernière action disponible au même moment pour le même prix. Dans une blockchain, un mineur pourrait mettre ces deux transactions en un seul bloc, et avoir une liberté totale pour choisir l'ordre dans lequel elles se produisent. Ou bien le mineur pourrait choisir d’inclure uniquement la transaction d’Alice, et de retarder le passage de Bob’s à un bloc futur. En ce qui concerne le hashgraphe, il n'y a aucun moyen pour un individu d'affecter indûment l'ordre de consensus de ces transactions. Le mieux que Alice puisse faire est d’investir dans une meilleure connexion Internet afin que sa transaction atteigne tout le monde avant Bob’s. C’est la manière juste de rivaliser.

## Foire Aux Questions

<details>

<summary>What is the hashgraph consensus algorithm? Comment ça marche ?</summary>

L'algorithme de consensus hashgraph est un mécanisme de consensus distribué utilisé par Hedera. Il utilise une structure de données appelée [hashgraph](../../support-and-community/glossary.md#hashgraph) et un mécanisme de consensus appelé protocole Gossip. Cette combinaison permet un consensus rapide, juste et sûr. L'algorithme fonctionne par chaque nœud du réseau partageant des informations (ou « commutations ») sur les transactions qu'il connaît avec d'autres nœuds dans un ordre aléatoire.

</details>

<details>

<summary>Quelle est la sécurité de l'algorithme de consensus hashgraph ?</summary>

Hashgraph est sécurisé car il est asynchrone Fault Tolerant (aBFT). Cela signifie qu’aucun membre ou petit groupe de membres ne peut empêcher la communauté de parvenir à un consensus ou de modifier le consensus une fois qu’il a été atteint. Il est également conforme à l'ACID lorsqu'il est utilisé comme une base de données distribuée, et il est résistant aux attaques [DDoS déni de service distribué](../../support-and-community/glossary.md#distributed-denial-of-service-ddos).

</details>

<details>

<summary>What is virtual voting?</summary>

Le vote virtuel fait partie intégrante de l'algorithme de consensus du hashgraphe. Il permet aux nœuds de savoir pour quoi les autres voteraient sans avoir besoin de véritables votes envoyés sur Internet. Ceci est accompli en examinant l'histoire des commérages (qui ont parlé à qui et dans quel ordre) pour déterminer comment un nœud voterait en fonction des informations qu'il est susceptible de avoir.

</details>
