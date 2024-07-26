---
description: Un aperçu des transactions et des requêtes de l'API Hedera
---

# Transactions et requêtes

## Transactions

Les **Transactions** sont des requêtes envoyées par un client à un noeud avec l'attente qu'elles soient soumises au réseau pour être traitées dans l'ordre de consensus et l'application subséquente à état. Chaque transaction (par exemple `TokenCreateTransaction()`) a des frais de transaction associés qui compensent le réseau Hedera pour ce traitement et la maintenance subséquente dans un état de consensus. Les utilisateurs peuvent définir des frais de transaction maximum pour le montant que l'utilisateur est prêt à dépenser. L’utilisateur est seulement facturé les frais de transaction réels.

**Identifiant de la transaction**

Chaque transaction a un ID de transaction unique. L'ID de la transaction est utilisé pour les éléments suivants :

- Obtention des reçus, des dossiers, des preuves d'état
- En interne par le réseau pour détecter quand des transactions en double sont soumises

L'ID de la transaction est composé en utilisant l'heure de début valide de la transaction et l'ID du compte du compte qui paie pour l'opération. L'heure de début valide de la transaction est l'heure à laquelle la transaction commence à être traitée sur le réseau. L'heure de début valide de la transaction peut être définie à une date ou heure ultérieure. Un ID de transaction ressemble à `0.0.9401@1598924675.82525000`où `0.0.9401` est l'identifiant du payeur des frais de transaction et `1598924675.82525000` est l'horodatage en `secondes.nanosecondes`.

Les transactions ont une durée valide allant jusqu'à 180 secondes et commencent à l'heure de début valide de la transaction. Cela signifie que la transaction a jusqu'à 180 secondes pour être acceptée par l'un des nœuds du réseau. Si la transaction n'est pas acceptée dans ce délai, la transaction expirera. La transaction devra être créée, signée et soumise à nouveau.

Une **transaction** inclut généralement les éléments suivants :

- **Compte de nœud**: le compte du nœud auquel la transaction est envoyée (par exemple `0.0.3`)
- **ID de transaction** : l'identifiant d'une transaction a deux composants, l'ID du compte payant et l'heure de début valide de la transaction
- **Frais de transaction**: les frais maximaux que le compte payant est prêt à payer pour la transaction
- **Durée valide** : le nombre de secondes pour lesquelles le client souhaite que la transaction soit considérée comme valide, à partir de l'heure de début valide de la transaction
- **Memo** : une chaîne de texte jusqu'à 100 octets de données (facultatif)
- **Transaction** : type de requête, par exemple, un transfert HBAR ou un appel smart contract
- **Signatures**: au minimum, le compte payant signera la transaction comme autorisation. D'autres signatures peuvent également être présentes.

Le cycle de vie d'une transaction dans l'écosystème Hedera commence quand un client crée une transaction. Une fois la transaction créée, elle est signée cryptographiquement au minimum par le compte payant les frais associés à la transaction. Des signatures supplémentaires peuvent être requises en fonction des propriétés définies pour le compte, le sujet ou le jeton. Le client est en mesure de préciser les frais maximaux qu'il est prêt à payer pour le traitement de la transaction et, pour une opération de contrat intelligent, la quantité maximale de gaz. Une fois que les signatures requises sont appliquées à la transaction, le client soumet la transaction à n'importe quel noeud du réseau Hedera.

Le noeud récepteur valide (par exemple, confirme que le compte payant a un solde suffisant pour payer les frais) la transaction et, si la validation réussit, soumet la transaction au réseau Hedera pour consensus en ajoutant la transaction à un événement et en racontant cet événement à un autre noeud. Rapidement, cet événement s'écoule vers tous les autres nœuds. Le réseau reçoit cette transaction exponentiellement rapidement via le [protocole gossip à propos de gossip](hashgraph-consensus-algorithms/gossip-about-gossip.md). L'horodatage du consensus pour un événement (et donc les transactions à l'intérieur) est calculé par chaque noeud indépendamment calculant la médiane des temps que les nœuds du réseau ont reçus cet événement. Vous trouverez plus d'informations sur la façon dont l'horodatage du consensus est calculé [here](https://docs.hedera.com/docs/hashgraph-overview#section-fair-timestamps). L'algorithme de hashgraphe fournit la finalité du consensus. Une fois assignée un timestamp de consensus, la transaction est alors appliquée à l'état de consensus dans l'ordre déterminé par l'horodatage de consensus de chaque transaction. À ce moment-là, les frais de la transaction sont également traités. De cette façon, chaque noeud du réseau maintient un état de consensus parce qu'il applique tous les mêmes transactions dans le même ordre. Chaque nœud crée et stocke temporairement les reçus/enregistrements à l'appui du client qui demande ensuite le statut d'une transaction.

### Transactions imbriquées

Une **transaction imbriquée** déclenche les transactions suivantes après avoir exécuté une transaction de premier niveau. La transaction de premier niveau qu'un utilisateur soumet est une **transaction parente**. Chaque transaction subséquente que la transaction mère déclenche suite à l'exécution de la transaction mère est une **transaction enfant**. Un exemple de transaction imbriquée est quand un utilisateur soumet la transaction de transfert de premier niveau à un alias de compte qui déclenche une transaction de création de compte en coulisses. Cette relation de transaction parent/enfant est également observée avec les contrats Hedera qui interagissent avec la précompilation HTS. Une transaction mère prend en charge jusqu'à 999 transactions enfants puisque la plateforme réserve 1000 nanosecondes par transaction soumise par l'utilisateur.

**IDs de transaction**

Les transactions des parents et des enfants partagent l'identifiant du compte du payeur et l'horodatage valide de la transaction. Les identifiants de transaction fils ont une valeur **nonce** supplémentaire qui représente l'ordre dans lequel les opérations enfants ont été exécutées. La transaction mère a une valeur nonce de 0. La valeur nonce des incréments de transactions fils par 1 pour chaque transaction enfant exécutée en conséquence de la transaction mère.

ID de transaction parent : <mark style="color:red;">payerAccountId</mark>@<mark style="color:blue;">transactionValidStart</mark>

ID de transaction enfant : <mark style="color:red;">payerAccountId</mark>@<mark style="color:blue;">transactionValidStart</mark>/<mark style="color:green;">nonce</mark>

Exemple:

- ID de transaction parent : <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>
- ID de transaction enfant 1 : <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>/<mark style="color:green;">1</mark>
- ID de transaction enfant 2 : <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>/<mark style="color:green;">2</mark>

**Enregistrements de transactions**

Les enregistrements de transactions imbriquées sont retournés en demandant l'enregistrement pour la transaction parent et en définissant le paramètre `setIncludeChildren(<value>)` à true. Cette option renvoie les enregistrements de toutes les transactions enfants associées à la transaction mère. Les enregistrements de transactions enfants comprennent l'horodatage du consensus parent et l'identifiant de la transaction enfant.

Le champ timestamp du consensus parent dans un enregistrement de transaction fils n'est pas rempli dans les cas où la transaction fils a été déclenchée **avant** la transaction parente. Un exemple de ce cas est la création d'un compte à l'aide d'un alias de compte. L'utilisateur soumet la transaction de transfert pour créer et financer le nouveau compte en utilisant l'alias du compte. La transaction de transfert (parent) déclenche la transaction de création de compte (enfant). Cependant, la transaction enfant se produit avant l'opération parente, de sorte que le nouveau compte est créé avant de compléter le transfert. L'horodatage du consensus parent n'est pas renseigné dans ce cas.

**Reçus de transaction**

Les reçus de transaction imbriqués peuvent être retournés en demandant le reçu de transaction parent et en définissant la valeur booléenne égale à true pour retourner tous les reçus de transaction enfant.

**Frais de transaction enfants**

Les frais de transaction pour la transaction enfant sont inclus dans le dossier de la transaction mère. Les frais de transaction retourneront zéro dans la transaction enfant.

## Requêtes

Les **requêtes** ne sont traitées que par le seul nœud vers lequel elles sont envoyées. Les clients envoient des requêtes pour récupérer un aspect de l'état actuel du consensus, comme le solde d'un compte. Certaines requêtes sont gratuites, mais en général, les requêtes sont soumises à des frais. La liste complète des requêtes peut être trouvée [here](../sdks-and-apis/sdks/queries.md).

Une requête inclut un en-tête qui inclut une transaction de transfert HBAR normale qui servira de moyen par lequel le client paie au noeud les frais appropriés. Il n'y a aucun moyen de verser un paiement partiel à un noeud pour traiter la requête, ce qui signifie que si un utilisateur surpayé pour la requête ne recevra pas de remboursement. Le noeud traitant la requête soumettra cette transaction de paiement au réseau pour la traiter dans une déclaration de consensus afin de recevoir ses frais.

Un client peut déterminer les frais appropriés pour une requête en demandant d'abord à un noeud le coût et non les données réelles. Une telle requête COST\_ANSWER est libre pour le client.

Pour plus d'informations sur les frais de requête, veuillez consulter les frais de l'API Hedera [overview](https://www.hedera.com/fees).

### Rappeler :

Rappel {% hint style="info" %}

Hedera n'a pas de **mineurs** ou un groupe spécial de nœuds qui sont chargés d'ajouter des transactions au grand livre comme des solutions alternatives distribuées pour les livres sterling. L'influence de chaque noeud à la détermination de l'horodatage du consensus pour un événement est proportionnelle à son intérêt dans HBAR.
{% endhint %}

![](../.gitbook/assets/transaction-flow.png)

Une fois qu'une transaction a été soumise au réseau, les clients peuvent demander la confirmation qu'elle a été traitée avec succès. Il existe plusieurs méthodes de confirmation - selon le niveau d'information fourni, la durée pour laquelle la confirmation est disponible, le degré de confiance et le coût correspondant.

![](../.gitbook/assets/query-confirmation.png)

## Confirmations

- **Recettes :** Les reçus fournissent un minimum d'information - simplement si la transaction a été traitée avec succès ou non dans un état de consensus. Les reçus sont générés par défaut et sont maintenus pendant 3 minutes. Les reçus sont gratuits.
- **Enregistrements :** Les enregistrements fournissent plus de détails sur la transaction que les reçus — comme l'horodatage du consensus qu'il a reçu ou les résultats d'un appel de fonction de contrat intelligent. Les enregistrements sont générés par défaut, mais sont maintenus pendant 3 minutes.
- **Preofs d'état (à venir) :** Lors d'une requête pour un enregistrement, un client peut éventuellement indiquer qu'il désire que le réseau renvoie une preuve d'état en plus de l'enregistrement. Une preuve d'état, documents réseau consensus sur le contenu de cet enregistrement dans l'état de consensus — cette assertion collective comprend des signatures de la plupart des nœuds du réseau. Parce que les preuves d'état sont signées cryptographiquement par une grande majorité du réseau, elles sont sûres et potentiellement recevables devant un tribunal.

{% hint style="info" %}
Une version préliminaire d'une preuve d'état, l'alpha état, est maintenant disponible. Veuillez consulter la section API REST de Mirror Node pour commencer.
{% endhint %}

{% content-ref url="../sdks-and-apis/rest-api.md" %}
[rest-api.md](../sdks-and-apis/rest-api.md)
{% endcontent-ref %}

Pour une revue plus détaillée des méthodes de confirmation, veuillez consulter ce [billet de blog](https://www.hedera.com/blog/transaction-confirmation-methods-in-hedera).

## Foire Aux Questions

<details>

<summary>What are the transaction and query fees associated with using Hedera?</summary>

Vous pouvez vous référer à la page des frais sur le site Hedera pour une répartition détaillée des frais de transaction et des frais de requête. Si vous recherchez un outil d'estimation, vous pouvez utiliser l'[estimation des frais Heder](https://hedera.com/fees).

</details>

<details>

<summary>What are transactions?</summary>

Les transactions sont des requêtes envoyées par un client à un noeud avec l'attente qu'il soit soumis au réseau pour traiter en ordre consensuel et l'application subséquente à état. Chaque transaction a un ID unique de transaction composé de l'heure de début valide de la transaction et de l'ID du compte qui paie pour l'opération. Cet identifiant est utilisé pour obtenir des reçus, des enregistrements et des preuves d'état et pour détecter lorsque des opérations en double sont soumises.

</details>

<details>

<summary>What are queries?</summary>

Les requêtes ne sont traitées que par le seul noeud vers lequel elles sont envoyées. [Clients](../support-and-community/glossary.md#client) envoie des requêtes pour récupérer certains aspects de l'état actuel du consensus, comme le solde d'un compte. Certaines requêtes sont gratuites, mais en général, les requêtes sont soumises à des frais.

</details>

<details>

<summary>What is the difference between receipts, records, and state proofs?</summary>

Les reçus fournissent un minimum d'information - que la transaction ait été traitée avec succès ou non dans un état de consensus. Les enregistrements fournissent plus de détails sur la transaction que les reçus, tels que l'horodatage du consensus qu'il a reçu ou les résultats d'un appel de fonction de contrat intelligent. Les preuves d'état sont une fonctionnalité qui permettra à un client d'indiquer éventuellement qu'il souhaite que le réseau renvoie une preuve d'état en plus de l'enregistrement.

</details>
