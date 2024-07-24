---
description: Stocker l'historique et les données de requête économique
---

# Nœuds miroir

Les nœuds miroir fournissent un moyen de stocker et d'interroger efficacement les données historiques du registre public tout en minimisant l'utilisation des ressources du réseau Hedera. Les nœuds miroir prennent en charge les services réseau Hedera actuellement disponibles et peuvent être utilisés pour récupérer les informations suivantes :

- Transactions et enregistrements
- Fichiers d'événements
- Balance des fichiers

## Comprendre les noeuds miroir

Les nœuds miroir Hedera reçoivent des informations provenant des nœuds de consensus du réseau Hedera, soit du réseau principal, soit du réseau testnet, et fournissent un moyen plus efficace pour effectuer :

- Requêtes
- Analyses
- Support de l'audit
- Surveillance

Bien que les nœuds miroir reçoivent des informations des nœuds de consensus, ils ne contribuent pas à un consensus eux-mêmes. La confiance de Hedera est dérivée du consensus atteint par les points nodaux de consensus. Cette confiance est transférée aux noeuds miroir en utilisant des signatures, des chaînes de hachages et des preuves d'état.

Pour faciliter les déploiements initiaux, les noeuds miroir s'efforcent d'éliminer le fardeau de l'exécution d'un noeud Hedera complet par la création de fichiers périodiques qui contiennent des informations traitées (comme des soldes de comptes ou des enregistrements de transactions) et ont la confiance totale des noeuds de consensus Hedera. Le logiciel de nœud miroir réduit la charge de traitement en recevant des fichiers préconstruits du réseau, en les validant et en remplissant une base de données et en fournissant des API REST.

![](../../.gitbook/assets/betamirrornode-overview.jpg)

Les nœuds miroir fonctionnent en validant les fichiers de signature associés à l'enregistrement, l'équilibre, et les fichiers d'événements des nœuds consensuels qui ont été envoyés vers une solution de stockage cloud depuis le réseau.

Au fur et à mesure que les transactions atteignent un consensus sur le réseau Hedera, soit le réseau principal, soit le réseau de testnet, Les nœuds de consensus Hedera ajoutent la transaction et les enregistrements associés à un fichier d'enregistrement. Un fichier d'enregistrement contient une série d'opérations commandées et leurs enregistrements associés. Après un certain laps de temps, un fichier d'enregistrement est fermé et un nouveau fichier est créé. Ce processus se répète lorsque le réseau continue de recevoir des transactions.

Une fois qu'un fichier d'enregistrement est fermé, les nœuds de consensus génèrent un fichier de signature. Le fichier de signature contient une signature pour le hachage du fichier d'enregistrement correspondant. Avec le fichier de signature du nœud de consensus, le fichier d'enregistrement contient également le hachage du fichier d'enregistrement précédent. L'ancien fichier d'enregistrement peut maintenant être vérifié en correspondant au hachage du fichier précédent de l'enregistrement.

Les nœuds de consensus Hedera poussent de nouveaux fichiers d'enregistrement et de signature vers le fournisseur de stockage cloud – actuellement AWS S3 et Google File Storage sont pris en charge. Les nœuds miroir téléchargent ces fichiers, vérifient leurs signatures en fonction de leurs hachages, puis les mettent à disposition pour être traités.

### Journaux synthétiques Smart Contract

Commence par [v0.79](../../networks/release-notes/mirror-node.md#v0. 9) de la version de Hedera Mirror Node, les journaux d'événements synthétiques pour les transactions de jetons Hedera Token Service (HTS) ont été introduits pour imiter le comportement des jetons de smart contract tokens. Les événements synthétiques sont générés pour les transactions telles que:

- `CryptoTransfer`
- `CryptoApproveAllowance`
- `CryptoDeleteAllowance`
- `TokenMint`
- `TokenWipe`
- `TokenBurn`

Cette fonctionnalité permet aux développeurs de surveiller efficacement les activités de jetons HTS comme s'ils étaient des jetons de contrat intelligents. Un exemple d'implémentation de code démontrant l'utilisation de ethers.js pour écouter des événements synthétiques peut être trouvé [here](https://github.com/ed-marquez/hedera-example-hts-synthetic-events-sdk-ethers).&#x20

### API REST de Hedera

Hedera fournit des API REST pour facilement interroger un noeud miroir qui est hébergé par Hedera, ce qui élimine la complexité d'avoir à exécuter le vôtre. Consultez les docs REST API du noeud miroir ci-dessous.

{% content-ref url="../../sdks-and-apis/rest-api.md" %}
[rest-api.md](../../sdks-and-apis/rest-api.md)
{% endcontent-ref %}

### Exécuter un noeud miroir

N'importe qui peut exécuter un nœud miroir Hedera en téléchargeant et en configurant le logiciel sur son ordinateur. En exécutant un nœud miroir, vous êtes en mesure de vous connecter au stockage nuagique approprié et de stocker les fichiers de solde du compte, enregistrer les fichiers et les fichiers d'événements comme décrit ci-dessus. Veuillez consulter les liens ci-dessous pour savoir comment commencer.

{% content-ref url="run-your-own-beta-mirror-node/" %}
[run-your-own-beta-mirror-node](run-your-own-beta-mirror-node/)
{% endcontent-ref %}

{% content-ref url="one-click-mirror-node-deployment.md" %}
[one-click-mirror-node-deployment.md](un click-mirror-node-deployment.md)
{% endcontent-ref %}

## Foire Aux Questions

<details>

<summary>How is data stored in a Hedera Mirror Node? S'agit-il d'un type spécifique de base de données, ou utilise-t-elle une structure de données unique ?</summary>

Les nœuds miroir Hedera utilisent les bases de données [PostgreSQL](../../support-and-community/glossary.md#postgresql) pour stocker les données de transaction et d'événement organisées dans une structure qui reflète le réseau Hedera. Une fois que le nœud miroir reçoit des fichiers d'enregistrement des nœuds du Consensus de Hedera, les données sont validées et chargées dans la base de données.&#x20

</details>

<details>

<summary>How do I run my own Hedera Mirror Node? Quelles sont les exigences matérielles et logicielles ?</summary>

La mise en place d'un nœud miroir Hedera implique à la fois des composants matériels et logiciels. Les exigences peuvent être trouvées [here](run-your-own-beta-mirror-node/).

Pour exécuter votre nœud miroir, suivez les étapes du guide "[Exécutez votre propre nœud miroir](run-your-own-beta-mirror-node/)".

</details>

<details>

<summary>Y a-t-il des coûts associés à l'exécution d'un nœud miroir ?</summary>

Non, Hedera ne charge pas pour l'exécution d'un noeud miroir. Cependant, il y a des coûts associés à l'achat du matériel, de la connexion à Internet et des frais potentiels de service dans le cloud. Les exigences matérielles et logicielles peuvent être trouvées [here](run-your-own-beta-mirror-node/).

</details>

<details>

<summary>How do I configure a mirror node and query data?</summary>

Vous pouvez configurer votre propre noeud miroir Hedera en suivant les instructions pas à pas fournies dans la section "[Comment configurer un noeud miroir et les données de requête](. /../tutorials/more-tutorials/how-to-configure-a-mirror-node-and-query-specific-data.md)" guide. Le guide fournit des instructions sur les prérequis, la configuration des nœuds, la configuration et la requête du noeud. De plus, vous trouverez plus de détails sur la rétention et le filtrage des transactions et des entités dans le guide.

</details>

<details>

<summary>How can I provide feedback or create an issue to log errors?</summary>

Pour fournir des commentaires ou des erreurs de log, veuillez vous référer au [Guide de contribution](../../support-and-community/contributing-guide.md) et soumettre un problème dans le [dépôt GitHub](https://github.com/hashgraph/hedera-json-rpc-relay/issues).

</details>
