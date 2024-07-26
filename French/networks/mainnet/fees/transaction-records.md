# Enregistrements de transaction

## Comprendre les enregistrements de transactions – Remises & Frais

Une fois qu'une transaction a été traitée avec succès par le réseau Hedera dans un état de consensus ou non, les nœuds de réseau créent respectivement un "enregistrement" ou un "reçu" pour cette transaction, en indiquant son statut et son impact.

Un élément clé de l'information dans un enregistrement pour une transaction est la façon dont la transaction a changé les soldes des comptes Hedera qui ont été impliqués. Le solde d’un compte peut changer en raison d’une transaction soit en raison de frais (payés ou reçus) ou de tout autre transfert intentionnel – que nous appelons ici « transfert de fonds ».

### Soldes du compte

Chaque transaction soumise à Hedera entraînera une modification des soldes d'un ensemble de comptes – soit parce que

1. La transaction a directement orienté des soldes spécifiques à changer, par exemple Alice a envoyé 1000 barres de har à Bob avec un CryptoTransfer.
2. La transaction a indirectement entraîné des changements de soldes, par exemple l'exécution d'un ContractCall a fait envoyer des HBAR depuis le compte du Smart Contract à d'autres.
3. Des frais sont payés pour le traitement de la transaction en un état de consensus et la persistance de cet état changé.
4. Des frais sont payés pour la persistance d’un « enregistrement » pour cette transaction pendant une période de temps plus longue que le défaut.

Une transaction peut entraîner une modification du solde d'un compte pour l'une des raisons ci-dessus ou, plus généralement, pour une combinaison des raisons ci-dessus.

Il y a en général 5 types de comptes associés à une transaction :

1. Expéditeurs – les comptes à partir desquels les HBAR sont envoyés
2. Recevants – les comptes vers lesquels les HBAR sont envoyés
3. Payeur – le compte qui paie les frais associés à la transaction.
4. Réseau – le compte Hedera qui reçoit la composante des frais qui compensent le réseau pour le traitement de la transaction.
5. Noeud – le compte de n'importe quel nœud soumet la transaction au réseau pour consensus

**Notes**

- Pour toute transaction, la somme des transferts sur tous les comptes sera toujours égale à la somme de tous les transferts sur tous les comptes.
- En général, le payeur est différent de l'expéditeur ou du destinataire. Néanmoins, un cas typique est que l'expéditeur sera également le payeur.
- Toutes les transactions n'ont pas d'expéditeur ou de destinataire car il n'y a pas d'aspect de transfert de la transaction, e. . une transaction FileCreate ou ConsensusSubmitMessage aura des frais mais aucun transfert associé.
- Un seul CryptoTransfer peut avoir plusieurs expéditeurs et plusieurs récepteurs.
- Un versement peut être le nombre de HBARs qu'un CryptoTransfer dirige être déplacé, le montant des HBARs une CryptoCreate directs être financés sur le nouveau compte, ou le montant des HBAR dans un compte à supprimer, avec ces fonds transférés vers un autre compte.
- Un versement devra être autorisé par le propriétaire de ces HBARs.
- Le propriétaire d'un compte peut spécifier des seuils pour les transferts dans et hors de ce compte. Si une transaction provoque le seuil d'un compte, alors l'enregistrement de cette transaction persistera pendant 25 heures et non pas les 3 minutes par défaut.
- Le propriétaire du compte qui a spécifié le seuil paiera des frais de seuil – distincts des frais pour la transaction elle-même – pour cette période de stockage supplémentaire.
- Il s'agit de 0,0. 8 qui reçoit le composant des frais de transaction qui compense tous les nœuds pour leur travail dans le traitement de la transaction en consensus
- 0.0.98 perçoit également tous les frais de l'enregistrement seuil
- Au début de février 2024, il y a 31 nœuds dont le numéro de compte est 0.0.3-0.0.4698971.
- Tant que les comptes sont de 0,0. 8 et les comptes de nœud sont spéciaux en ce qui concerne la réception des frais, ils peuvent également envoyer et recevoir des HBAR et, en tant que tels, peuvent être l'expéditeur ou le destinataire d'une transaction.

### Scenarios

Nous explorons les scénarios ci-dessous et comment les HBAR circulent entre les comptes pour chacun.

#### Caisse 1 - Générique

Dans le cas le plus générique, un expéditeur effectue un versement à un récepteur et un compte séparé paie les frais associés. La taille des frais dépendra de la nature de la transaction – télécharger un fichier volumineux coûtera plus cher qu'un simple transfert de cryptomonnaie.

Le montant de ces frais est réparti entre le compte 0,0. 8 (un compte spécial Hedera qui représente le réseau) et le nœud spécifique qui a soumis la transaction.

![](../../../.gitbook/assets/transaction\_records\_1.png)

#### Cas 2 - Frais seulement

De nombreuses transactions ne permettent pas un versement explicite, par exemple, un FileCreate ou un ConsensusSubmitMessage. Pour de telles transactions, les seules modifications apportées au solde du compte seront dues aux frais de cette transaction.

Comme précédemment, les frais pour la transaction sont répartis entre 0.0.98 et un nœud.

![](../../../.gitbook/assets/transaction\_records\_2.png)

#### Cas 3 - Compte expéditeur paye des frais

Il arrive souvent que les frais pour un CryptoTransfer envoyant un versement d'un expéditeur à un destinataire soient payés par l'expéditeur. Dans ce cas, le solde de l’expéditeur diminuera de la somme du versement et des frais.

En principe, le récepteur pourrait également payer les frais.

![](../../../.gitbook/assets/transaction\_records\_3.png)

#### Cas 4 - Le compte expéditeur a un seuil qui est franchi

Les propriétaires du compte peuvent spécifier des seuils pour leurs comptes afin que tout transfert dans/hors du compte qui dépasse ces seuils provoque la persistance de l'enregistrement créé pendant 25 heures et non pas les 3 minutes par défaut. Le compte qui a stipulé le seuil paiera un seuil pour ce stockage prolongé du dossier.

Dans cet exemple, le seuil est spécifié sur le compte d'envoi, et donc ce sera ce compte qui paiera ce seuil. Le solde de ce compte diminue par conséquent de la somme des versements et des droits de seuil.

Compte 98 reçoit la somme du réseau, du service, et aussi de ce seuil de frais.

Non affiché ici, mais si l’expéditeur payait les frais de transaction (comme ci-dessus) alors le solde du compte de l’expéditeur diminuerait de la somme de l’envoi, les frais de transaction et ce seuil de frais.

![](../../../.gitbook/assets/transaction\_records\_4.png)

#### Cas 5 - Le compte du destinataire a un seuil qui est franchi

Le compte récepteur peut également avoir un seuil fixé à cela, si elle est dépassée par le montant des ARS transférés dans le compte, fera que le dossier sera stocké pendant 25 heures.

Les enregistrements de seuil peuvent être particulièrement utiles pour les récepteurs car un récepteur peut ne pas être au courant des envois qui leur sont envoyés (car ils ne sont pas nécessairement impliqués dans la signature de la transaction, comme c'est le cas pour l'expéditeur). Les seuils de plus longue durée permettent au propriétaire du compte de demander quotidiennement les dossiers pour tous les envois qu’ils ont reçus au cours des 24 dernières heures et dont ils pourraient ne pas avoir connaissance autrement.

Le compte récepteur paiera les frais de seuil associés pour cette période de stockage plus longue du dossier.

Le changement de solde net du compte récepteur sera donc le versement moins les frais de seuil.

Compte 98 reçoit la somme du réseau, du service et des frais de seuil.

Les frais de transaction ne sont pas affectés par les frais de seuil payés.

Si la valeur de la remise est inférieure au seuil des frais, la transaction échouera.

![](../../../.gitbook/assets/transaction\_records\_5.png)

#### Cas 6 - Compte Node récepteur

Les nœuds peuvent recevoir des envois comme tout autre compte Hedera.

À titre d'exemple spécifique, les clients compensent les nœuds pour répondre à une requête en incluant dans la requête un CryptoTransfer que, Lorsqu'il est soumis au réseau, compense ce noeud par un envoi approprié.

Dans ce scénario, le solde du compte du nœud augmentera de la somme des frais de nœud qu'il reçoit pour le traitement du CryptoTransfer plus la valeur du versement réel qui paie le noeud pour la réponse de la requête.

![](../../../.gitbook/assets/transaction\_records\_6.png)

### Enregistrements de transaction

Après que les nœuds du réseau principal d'Hedera traitent une transaction en état de consensus, les détails sont publiés dans le monde extérieur dans un ‘enregistrement de transaction’ que les nœuds créent et rendent disponible. Les clients récupèrent les enregistrements et analysent les données à l'intérieur pour vérifier les conséquences des transactions, par exemple, l'horodatage du consensus qui a été assigné et la façon dont les soldes du compte associé ont changé à la suite de la transaction.

Lorsqu'elle est récupérée à partir d'un noeud miroir et non du réseau principal, la transaction qui a abouti à un enregistrement donné sera également disponible. C'est donc cette structure de données combinée qui fournit l'ensemble d'informations le plus riche pour l'analyse et la différenciation entre les transferts de fonds et les redevances.

Le flux d'information est affiché ci-dessous:

![](../../../.gitbook/assets/transaction\_records\_7.png)

Un client qui récupère la paire d'une transaction et son enregistrement associé peut vouloir distinguer entre les versements et les composantes de frais pour la transaction - c'est-à-dire quelle partie du changement de solde d'un compte était due aux frais de transaction, quelle partie est due à un montant de seuil et quelle partie est due à un envoi.

Il y a suffisamment d'informations dans la combinaison de la transaction et de l'enregistrement correspondant pour permettre à un client de faire une telle distinction sans ambiguïté pour chaque compte.

Un enregistrement de transaction a une structure de données de liste de transfert qui décrit comment les HBARs ont déplacé entre comptes à la suite de la transaction.

Dans la R3 (version antérieure à la mise à jour du 10 février 2020) version du logiciel de nœud, il peut y avoir plusieurs transferts pour chaque compte impliqué dans l'opération. Par exemple, il pourrait y avoir des transferts distincts indiquant les frais de réception du compte 0,98, qui se sont additionnés au montant total exact des frais.

De plus, en R3

1. La détermination du dépassement d'un seuil pour chaque compte a été effectuée pour chaque transfert. Par conséquent, un seul compte payant à la fois un versement et des frais pourrait payer des enregistrements à plusieurs seuils si le seuil était très bas.
2. L'ordre des transferts au format R3 n'était pas prévisible.

Nous avons modifié le format de la liste des transferts de dossiers dans la R4 (la sortie du 10 février, 2020) logiciel de nœud pour résoudre les problèmes ci-dessus et être plus cohérent et plus concis.

Dans le communiqué R4, la liste des transferts d'enregistrements présente, pour tous les types d'opérations, un seul transfert net pour chaque compte pertinent.

La comparaison entre les transferts entrant/sortant et les seuils d'un compte est maintenant faite sur ce transfert net, et non sur les transferts constituants qui se sont résumés au net. Par conséquent, tout compte payera une seule fois pour un seul seuil de frais et non pour plusieurs fois. C'est moins cher pour l'utilisateur.

Le changement entre R3 et R4 est indiqué ci-dessous, pour une opération représentative dans laquelle le compte est 0,0. 002 envoie un versement de 10 000 tinybars au compte 0.0.1001 et l'expéditeur et le récepteur ont tous deux des seuils de 1 000 tinybars fixés sur leur compte.

Comme la valeur du versement dépasse ces seuils, l'expéditeur et le récepteur paieront des frais de seuil.

**R3**

![](../../../.gitbook/assets/r3.jpg)

**R4**

![](../../../.gitbook/assets/r4.jpg)

Au format R4, la liste des transferts d'enregistrements n'a plus de multiples transferts pour les différents comptes – chaque compte n'a qu'un seul transfert avec une valeur qui reflète la somme des différents transferts qui ont eu un impact sur chaque compte.

Alors que le format R4 est plus concis que le format R3, certains clients peuvent vouloir déterminer les transferts de composants - à savoir briser les envois de fonds, les frais de nœud, les frais de seuil et les autres frais de transaction. Pour faciliter cette analyse, Hedera prévoit d'ajouter le support à l'API REST du noeud miroir pour permettre à un client de demander soit la liste de transfert agrégée par défaut, ou plutôt une liste détaillée de transferts (similaire au format R3).
