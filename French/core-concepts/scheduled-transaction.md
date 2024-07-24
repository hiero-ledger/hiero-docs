# Planifier la transaction

## Aperçu

Une **transaction programmée** est une transaction ayant la possibilité de collecter les signatures requises sur un réseau Hedera en préparation de son exécution. Contrairement aux autres transactions Hedera, cela vous permet de mettre en file d'attente une transaction pour l'exécution dans le cas où vous ne possédez pas toutes les signatures requises pour le réseau pour traiter immédiatement la transaction. Une opération planifiée est utilisée pour créer une opération planifiée. Cette fonctionnalité est idéale pour les transactions qui nécessitent plusieurs signatures.

Lorsqu'un utilisateur crée une opération planifiée, le réseau crée une entité planifiée. L'entité planifiée reçoit un ID d'entité tout comme les comptes, jetons, etc appelés un ID de planification. L'ID de la planification est utilisé pour référencer la transaction planifiée qui a été créée. La transaction en cours de planification est référencée par un ID de transaction planifiée.&#x20

Les signatures sont ajoutées à la transaction planifiée en soumettant une transaction `ScheduleSign`. La transaction `ScheduleSign` nécessite l'ID du planning de la transaction planifiée à laquelle les signatures seront ajoutées. Dans sa conception actuelle, une transaction planifiée a 30 minutes pour collecter toutes les signatures nécessaires avant que la transaction planifiée puisse être exécutée ou sera supprimée du réseau. Vous pouvez supprimer une transaction planifiée en définissant une clé d'administration pour supprimer une transaction planifiée avant qu'elle ne soit exécutée ou supprimée par le réseau.

Vous pouvez demander l'état actuel d'une transaction planifiée en interrogeant le réseau sur `ScheduleGetInfo`. La demande retournera les informations suivantes :

- ID de l'horaire
- ID du compte client qui a créé la transaction planifiée
- ID du compte client qui a payé pour la création de la transaction planifiée
- Corps de la transaction interne
- ID de la transaction interne
- Liste actuelle des signatures
- Clé admin (le cas échéant)
- Délai d'expiration
- L'horodatage de la suppression de la transaction, si vrai

Le document de conception de cette fonctionnalité peut être référencé [here](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md).

**Identification de la transaction programmée**

Les ID de transaction Hedera sont composés de l'ID du compte qui soumet la transaction et de l'heure de début valide de la transaction en secondes.nanosecondes (`0.0.1234@1615422161.673238162`). L'ID de la transaction pour une transaction planifiée inclura "`? chedule`" à la fin de l'identifiant de la transaction qui identifie la transaction comme une opération planifiée i. . `0.0.1234@1615422161.673238162?scheduled`. L'ID de l'opération planifiée (intérieure) hérite de l'heure de début de la transaction valide et de l'ID du compte de la transaction planifiée (extérieure).

**Planifier les reçus de transactions**

Le reçu de transaction pour un calendrier qui a été créé contient le nouvel ID de l'entité planifiée et l'ID de l'opération planifiée. L'ID de la transaction planifiée est utilisé pour demander des enregistrements pour la transaction intérieure une fois l'exécution réussie.

**Planifier les enregistrements de transactions**

Les enregistrements de la transaction sont créés lorsque l'opération planifiée est créée, pour chaque signature qui a été ajoutée, lorsque la transaction planifiée est exécutée, et si la transaction planifiée a été supprimée par un utilisateur. L'enregistrement d'une opération planifiée comprend une propriété de référence de calendrier, qui est l'ID de la planification à laquelle l'enregistrement est associé. Pour obtenir l'enregistrement de la transaction interne après l'exécution réussie, vous pouvez faire ce qui suit :

1. Sondage sur le réseau pour l'ID de transaction planifiée spécifiée. Une fois que l'opération planifiée exécute l'opération planifiée avec succès, demandez l'enregistrement de l'opération planifiée en utilisant l'ID de l'opération planifiée.
2. Interroger un noeud miroir Hedera pour l'ID de transaction planifiée.
3. Exécutez votre propre nœud miroir et requête pour l'ID de transaction planifiée.

## Foire Aux Questions

<details>

<summary>What is the difference between a schedule transaction and scheduled transaction?</summary>

Une transaction _**planifiée**_ est une transaction qui peut planifier n'importe quelle transaction Hedera avec la possibilité de collecter les signatures requises sur le réseau Hedera en préparation de son exécution.

Une _**transaction planifiée**_ est une transaction qui a déjà été planifiée.

</details>

<details>

<summary>Is there an entity ID assigned to a schedule transaction?</summary>

Oui, l'ID de l'entité est désigné comme l'ID de l'horaire qui est retourné à la réception de la transaction ScheduleCreate.

</details>

<details>

<summary>Quelles transactions peuvent être planifiées ?</summary>

Lors de sa première itération, un petit sous-ensemble de transactions sera planifiable. Vous consultez la page [this](../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md) pour une liste des types de transactions qui sont pris en charge aujourd'hui. Tous les autres types de transaction seront disponibles pour les prochaines versions. La liste complète des transactions que les utilisateurs peuvent planifier à l'avenir peut être trouvée ici.

</details>

<details>

<summary>How can I find a schedule transaction that requires my signature?</summary>

- Le créateur de la transaction planifiée peut vous fournir un ID de planification que vous spécifiez dans la transaction ScheduleSign pour soumettre votre signature.

<!---->

- Vous pouvez interroger un noeud miroir pour retourner toutes les transactions de planification qui ont votre clé publique associée. Cette option n'est pas disponible aujourd'hui, mais est prévue pour l'avenir.

</details>

<details>

<summary>What happens if the scheduled transaction does not have sufficient balance?</summary>

Si le payeur de frais de l'opération planifiée (opération intérieure) n'a pas le solde suffisant, l'opération intérieure échouera alors que l'opération planifiée (opération externe) sera réussie.

</details>

<details>

<summary>Pouvez-vous retarder une transaction une fois qu'elle a été planifiée ?</summary>

Non, vous ne pouvez pas retarder ou modifier une transaction planifiée une fois qu'elle a été soumise à un réseau. Vous devrez supprimer l'opération planifiée et en créer une nouvelle avec les modifications.

</details>

<details>

<summary>What happens if multiple users create the same schedule transaction?</summary>

- La première transaction à atteindre un consensus créera la transaction planifiée et fournira l'ID de l'entité planifiée
- Les autres utilisateurs recevront l'ID de l'horaire lors de la réception de la transaction qui a été soumise. Le statut du reçu résultera en `IDENTICAL_SCHEDULE_ALREADY_CREATED`. Ces utilisateurs devront soumettre une transaction ScheduleSign pour ajouter leurs signatures à la transaction planifiée.

</details>

<details>

<summary>When does the scheduled transaction execute?</summary>

La transaction planifiée s'exécute lorsque la dernière signature est reçue.

</details>

<details>

<summary>How often does the network check to see if the scheduled transaction (inner transaction) has met the signature requirement?</summary>

Chaque fois que la transaction du calendrier est signée.

</details>

<details>

<summary>How do you get information about a schedule transaction?</summary>

Vous pouvez soumettre une [requête d'information sur le programme](../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) au réseau.

</details>

<details>

<summary>When does a scheduled transaction expire?</summary>

Une transaction planifiée expire dans 30 minutes. Dans les implémentations futures, nous permettrons à l'utilisateur de définir l'heure à laquelle la transaction planifiée doit s'exécuter, et la transaction expirera à ce moment-là.

</details>

<details>

<summary>What does a schedule transaction receipt contain?</summary>

Le reçu de transaction pour un calendrier qui a été créé contient le nouvel ID de l'entité planifiée et l'ID de l'opération planifiée.

</details>
