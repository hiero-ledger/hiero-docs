# Location de contrats intelligents

{% hint style="danger" %}
🚨 **HEDERA COUNCIL N'A PAS ACTIVÉ DE location SUR LES CONTRACTS DE SMART YET. Les locations sont payantes pour l’UTILISATION EN COURS DES RESSOURCES UTILISÉES PAR LE CONTRAT DE SMART. HEDERA INTENDS POUR ACTIVER LES LOCATIONS DU FUTURE, COMME DÉCRIBÉ DANS CETTE SECTION. PLUS DE DÉTAILS COMMENTAIRES BIENTÔT... 🚨**
{% endhint %}

La location de contrats intelligents est un mécanisme de paiement récurrent conçu pour maintenir l'allocation des ressources et est nécessaire pour que les contrats restent actifs sur le réseau. Pour les contrats, le loyer est composé de deux composantes principales:

**➡️** [**Auto-Renouvellement**](smart-contract-rent.md#contract-auto-renewal)

**➡️** [**Paiements de stockage**](smart-contract-rent.md#storage-payment)

***

## Renouvellement automatique du contrat

Le renouvellement automatique est une fonctionnalité qui renouvelle automatiquement la durée de vie des contrats intelligents non supprimés d'au moins 90 jours. Les auteurs de contrats sont encouragés à créer un compte de renouvellement automatique spécifiquement à cette fin.&#x20

Le réseau tentera de facturer automatiquement le **paiement de renouvellement** au compte de renouvellement automatique du contrat expiré. Le réseau tentera de facturer le contrat si un compte de renouvellement automatique a un solde nul.&#x20

Si le compte manque de fonds suffisants pour le renouvellement, le contrat entrera en période de grâce d’une semaine. Pendant ce temps, le contrat est inopérant à moins que des fonds ne soient ajoutés, son expiration est prolongée (via `ContractUpdate`), ou il reçoit HBAR. En cas de non-renouvellement, le contrat sera purgé de l'État.

***

## Paiements de stockage

Les paiements de stockage contractuels sur Hedera seront activés une fois que **100 millions de paires clés** seront stockées cumulativement sur le réseau. Le comité économique de Hedera Coin devrait fixer un taux de **0,02 $ par paire de valeur clé par an**. Ceci s'applique à tous les contrats sur Hedera, quel que soit le contrat en cours de création avant ou après le paiement du loyer en direct.

Une fois que les paiements de stockage sont activés sur Hedera, chaque contrat a **100 paires clés-valeurs** gratuites de stockage. Puis, une fois qu'un contrat dépasse les 100 premières paires clé-valeur, il doit payer des frais de stockage.&#x20

> _Les frais de stockage feront partie du paiement du loyer perçu lorsqu'un contrat est renouvelé automatiquement. Les fenêtres de renouvellement valides sont entre \~30 et \~92 jours (voir_ [_HIP-372_](https://hips.hedera.com/hip/hip-372)_)._

Si un seuil d'utilisation assez élevé est atteint, \*\*prix d'encombrement s'applique. \* Dans ce cas, les prix facturés seront inversement proportionnels à la capacité du réseau restante (la capacité restante inférieure signifie une tarification plus élevée). Cela s'applique à toutes les transactions.

***

## Smart Contract Rent - Foire Aux Questions (FAQ)

<details>

<summary>Why do contracts have to pay rent on Hedera?</summary>

Les réseaux distribués comme Hedera ont une quantité limitée de ressources informatiques. Lorsque des entités comme les contrats intelligents sont déployés sur un réseau décentralisé, une partie de ces ressources est consommée. Il est donc impossible de maintenir un nombre illimité d'entités pour un temps infini sur des ressources limitées. La résolution de ce problème est nécessaire, et c'est un sujet de discussion clé par Leemon et [others](https://www.coindesk.com/markets/2018/03/27/vitalik-wants-you-to-pay-to-slow-ethereums-growth/) dans l'espace réseau de la couche 1.

Le loyer des contrats est une approche économiquement et techniquement viable pour gérer les entités des contrats intelligents et le stockage des États.

</details>

<details>

<summary>Do all entities on Hedera have to pay rent or just contracts?</summary>

Toutes les autres entités du réseau (par exemple, jetons, comptes, sujets et fichiers) paieront également le loyer. Cependant, le calendrier pour le loyer n'est pas encore défini. Un délai et un avis suffisants seront fournis à la communauté avant de permettre la location d'autres entités.

</details>

<details>

<summary>What charges are included in contract rent?</summary>

Le loyer est défini comme le paiement récurrent requis pour que les contrats (et, éventuellement, toutes les autres entités de Hedera) restent actifs sur le réseau. Pour les contrats, le loyer est composé de paiements de **renouvellement automatique** et de **stockage** :

- **Paiements de renouvellement automatique** Les frais de renouvellement automatique pour un contrat sont de 0,026 $ US par 90 jours.
- **Les paiements de stockage** commenceront une fois qu'un total de **100 millions de paires clés-valeurs** sont stockés cumulativement sur le réseau. Ces frais d’entreposage feront partie du paiement du loyer perçu lors du renouvellement automatique d’un contrat. Les frais de stockage sont de 0,02 $ par paire de valeur clé par année.

<img src="../../.gitbook/assets/smart-contracts-rent-storage-payments.png" alt="" data-size="original">

</details>

<details>

<summary>What are the steps in the renewal process? Et que se passe-t-il si un contrat ne paie pas le loyer ?</summary>

Chaque entité sur Hedera a les champs `expirationTime`, `autorenewPeriod`, et `autorenewAccount`.

1. Quand le `expirationTime` d'un contrat est atteint, le réseau essaiera d'abord de facturer le loyer sur le `autoRenewAccount` du contrat
   - Si le renouvellement réussit, alors le contrat reste actif sur le réseau
   - Si le renouvellement échoue, alors le contrat est marqué comme `expiré`
2. Une entité `expiré` reçoit un délai de grâce avant d'être retirée du réseau. Pendant la période de grâce, l'entité (contrat) est inactive, et toutes les transactions qui l'impliquent échoueront, à l'exception d'une transaction de mise à jour pour prolonger le `expirationTime`
   - Un contrat dans la période de grâce peut être immédiatement "réactivé" en lui envoyant un HBAR ou en étendant manuellement son `expirationTime` via une transaction de mise à jour du contrat
3. À la fin de la période de grâce, le contrat est définitivement retiré du registre si :
   - Le contrat et son `autoRenewAccount` ont toujours un solde HBAR zéro à la fin de la période de grâce, OU
   - Le contrat n'est pas prolongé manuellement pendant la période de grâce

Notez que le numéro d'identification d'une entité retirée n'est pas réutilisé. En outre, si une entité a été marquée comme `supprimé`, alors elle ne peut pas avoir son `expirationTime` prolongé. Ni une transaction de mise à jour ni un renouvellement automatique ne seront en mesure de l'étendre.

Voir le diagramme ci-dessous et [HIP-16](https://hips.hedera.com/hip/hip-16) pour plus de détails.

<img src="../../.gitbook/assets/Untitled.png" alt="" data-size="original">

</details>

<details>

<summary>How long is the grace period for expired contracts?</summary>

La période de grâce entre l'expiration de l'entité et la suppression est de 30 jours.

</details>

<details>

<summary>Who pays for the contract’s renewal and storage fees?</summary>

Les contrats intelligents sur Hedera peuvent payer la location de deux façons : des fonds externes ou des fonds contractuels.

Lorsque le `expirationTime` d'un contrat est atteint, le réseau essaiera d'abord de facturer le loyer au `autoRenewAccount` du contrat :

- Si le `autoRenewAccount` a suffisamment de HBAR pour payer pour la `autoRenewPeriod`, alors le contrat est renouvelé avec succès
- Si le `autoRenewAccount` a quelques HBAR mais pas assez pour se permettre la `autoRenewPeriod` complète, puis le contrat est prolongé le plus longtemps possible (par exemple, 1 semaine au lieu de 90 jours). Une fois cette extension (1 semaine) s'écoule, si le `autoRenewAccount` n'a pas été réfinancé pour couvrir la `autoRenewPeriod`, alors le compte de contrat lui-même sera débité pour le loyer
- Si le `autoRenewAccount` a un solde HBAR zéro, alors le contrat lui-même est facturé
- Si le `autoRenewAccount` et le contrat ont tous deux un solde de HBAR zéro au moment où les frais de renouvellement sont exigibles, le contrat est marqué comme `expiré`

</details>

<details>

<summary>What happens if I call a contract that is expired?</summary>

Appeler un contrat `expiré` va se résoudre à `CONTRACT_EXPIRED_AND_AWAITING_REMOVAL`.

</details>

<details>

<summary>When a contract is expired and deleted from the network, what happens to its account and assets?</summary>

Si un contrat expiré qui contient les jetons du Hedera Service (HTS) natif atteint l'étape de la suppression. puis les actifs détenus par ce contrat sont restitués à leurs comptes de trésorerie respectifs.

Si le contrat supprimé est utilisé comme clé spécifique pour un jeton HTS, alors ce champ clé fera référence à un contrat qui n'existe plus. Cette clé spécifique peut être modifiée, tant qu'une clé admin a été spécifiée lors de la création de jetons. Si le jeton est immuable (aucune clé d'administrateur), la clé spécifique ne peut pas être changée.

Les contrats qui sont la trésorerie des jetons HTS n'expirent pas à ce moment (sous réserve de changement à l'avenir).

</details>

<details>

<summary>For how long can I renew my contract?</summary>

La période de renouvellement minimum possible est de 2 592 000 secondes (\~30 jours) et le maximum est de 8 000 001 secondes (\~92 jours).

Voir les détails dans [HIP-372: Entity Auto-Renewals and Expiry Window](https://hips.hedera.com/hip/hip-372).

</details>

<details>

<summary>If I change the <code>autoRenewPeriod</code> of my contract from 30 to 90 days, what will the cost of my transaction be?</summary>

Le coût du loyer est à peu près linéaire avec la durée de la période de renouvellement. Ainsi, un renouvellement qui paie 90 jours coûtera \~3 fois plus cher qu'un renouvellement qui paie 30 jours.

</details>

<details>

<summary>Where can I seen when a contract will expire?</summary>

Les noeuds miroir fournissent le temps d'expiration des contrats. Vous pouvez obtenir ces informations en utilisant l'API REST du noeud miroir (montrer comme `expiration_time`) et les explorateurs de réseau comme HashScan (l'affiche comme `Expires at`).

</details>

<details>

<summary>Where do the auto-renewal transactions appear? Peut-on les voir sur des explorateurs de réseau comme HashScan ?</summary>

Selon [HIP-16 : Entity Auto-Renewal](https://hips.hedera.com/hip/hip-16), les enregistrements de frais de renouvellement automatique apparaîtront comme des `actions` dans le flux d'enregistrement, et seront disponibles via les nœuds miroirs. En outre, la ventilation des frais est fournie dans les explorateurs de réseau comme HashScan pour la transaction de mise à jour du contrat. Aucun reçu ou enregistrement pour les actions de renouvellement automatique ne sera disponible via les requêtes HAPI.

[HIP-449](https://hips.hedera.com/hip/hip-449) provides technical details on how information for expiring contracts is included in the record stream.

</details>

<details>

<summary>Est-ce que le <code>autoRenewAccount</code> pour un contrat peut être défini sur un autre ID de contrat ?</summary>

Oui, c'est possible pour les contrats.

</details>

<details>

<summary>What are the key-value pair thresholds that I should be aware of that impact the size of the storage payment?</summary>

- Les paiements de stockage pour les contrats ne commenceront à être facturés que si **100 millions de paires clé** sont atteints cumulativement à travers le réseau
- Après cela, chaque contrat a **100 paires clé-valeur gratuites** de stockage disponibles. Une fois qu'un contrat dépasse les 100 premières paires clé-valeur gratuites, il doit payer des frais de stockage

</details>

<details>

<summary>For smart contracts created via <code>CREATE2</code>, how can I specify rent-related properties like<code>autorenewAccount</code> and <code>autorenewPeriod</code>?</summary>

Les contrats créés via `CREATE2` à l'intérieur de l'EVM hériteront des `autorenewaccount` et `autorenewPeriod`de l'adresse `expéditeur`.

Par exemple, si vous appelez le contrat `0xab...cd` qui a `autorenewAccount` `0.0.X` et `autorenewPeriod` de 45 jours, et ce contrat déploie un nouveau contrat `0xcd. .ef`, alors le nouveau contrat aura également `autorenewAccount` `0.0.X`et `autorenewPeriod` de 45 jours.

Rappelez-vous également que le loyer peut être couvert par le solde du HBAR d'un contrat. Ainsi, les développeurs peuvent envoyer HBAR au contrat ou configurer le contrat pour facturer aux utilisateurs un montant HBAR spécifique lors de l'exécution d'opérations.

</details>
