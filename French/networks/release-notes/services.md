---
description: Informations sur la version des services Hedera
---

# Services Hedera

Veuillez visiter la [page de statut de Heder](https://status.hedera.com/) pour les dernières versions prises en charge sur chaque réseau.

## [v0.51](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)

{% hint style="info" %}
**MAINNET MISE À JOUR SCHEDULED: 17 JUILLE 2024**
{% endhint %}

### Relâcher les points forts

#### [HIP 206](https://hips.hedera.com/hip/hip-206)

**Fonctionnalité:**

- Définit une nouvelle fonction pour le contrat du système de service de jetons Hedera qui permet le transfert atomique des jetons HBAR, des jetons fongibles et des jetons non fongibles.
  - Transfert de la fonction (TransferList de transfert,TokenTransferList\[] Transfert de jetons)
- Expose un appel HAPI existant via des contrats intelligents.
- Le transfert respecte les quotas accordés. &#x20

**Avantages :**

- Active la prise en charge des redevances natives sur l'EVM puisque $hbar natif peut maintenant être transféré en utilisant des allocations de dépenses
- Interaction directe avec les jetons HBAR et HTS
- Élimine le besoin d'emballage des jetons.
- Améliore l'efficacité et réduit la complexité.
- Réduit les coûts en supprimant les étapes intermédiaires, c'est-à-dire en enveloppant les actifs pour interagir avec eux.
- Active le support des redevances natives sur l'EVM puisque le HBAR natif peut maintenant être transféré en utilisant des allocations de dépenses

#### [HIP 906](https://hips.hedera.com/hip/hip-906)

**Fonctionnalité:**

- Introduit un nouveau contrat de service de compte Hedera.
- Active la requête et l'approbation du HBAR à un compte de dépense à partir du code des contrats intelligents
  - hbarAllowance, hbarApprove
- Les développeurs n'ont pas besoin de mettre en contexte le code du smart contract

**Avantages :**

- Introduit un nouveau contrat de proxy de compte pour les allocations HBAR
- Active la subvention, la récupération et la gestion des quotas HBAR dans les contrats intelligents
  - Les développeurs n'ont pas à mettre en contexte le code des contrats intelligents
- Simplifie les flux de travail et améliore la sécurité
- Développe les cas d'utilisation potentielle, en particulier pour les marchés DeFi et de jetons

### [0.51.5](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)

{% hint style="success" %}
**MISE À JOUR TESTNET : 2 JUILLET 2024**
{% endhint %}

#### Ce qui a changé

- feat(reconnect) : introduisez l'interface ReconnectMapStats par [@anthony-swirldslabs](https://github.com/anthony-swirldslabs) dans [#13027](https://github.com/hashgraph/hedera-services/pull/13027)
- chore: annuler la suppression de l'outil de rapport CLI par [@lpetrovic05](https://github.com/lpetrovic05) dans [#13002](https://github.com/hashgraph/hedera-services/pull/13002)
- docs : ajoutez un document de conception pour le jeton de rejet HIP-904 par [@MiroslavGatsanoga](https://github.com/MiroslavGatsanoga) dans [#12786](https://github.com/hashgraph/hedera-services/pull/12786)
- feat: facade de gossip par [@cody-littley](https://github.com/cody-littley) dans [#12897](https://github.com/hashgraph/hedera-services/pull/12897)
- feat: ajoute la possibilité de désactiver l'événement en cours d'exécution par [@cody-littley](https://github.com/cody-littley) dans [#13083](https://github.com/hashgraph/hedera-services/pull/13083)
- Correction : ignore le statut d'expiration de jeton dans `TokenDissociate` par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13104](https://github.com/hashgraph/hedera-services/pull/13104)
- feat: add javadoc and diagram, delete dead code by [@tinker-michaelj](https://github.com/tinker-michaelj) in [#13070](https://github.com/hashgraph/hedera-services/pull/13070)
- correction : utilisez le payeur civil pour les variantes modifiées par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13020](https://github.com/hashgraph/hedera-services/pull/13020)
- correction : 12853: Fuite mémoire de MerkleDbDataSource.copyStatisticsFrom() par [@artemananiev](https://github.com/artemananiev) dans [#13097](https://github.com/hashgraph/hedera-services/pull/13097)
- feat: Mise à jour du code hedera-services pour supporter les changements DAB protobuf. par [@iwsimon](https://github.com/iwsimon) dans [#13090](https://github.com/hashgraph/hedera-services/pull/13090)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.51.5)**.**

## [v0.50](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 20 JUIN 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 5 JUIN 2024**
{% endhint %}

### [0.50.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.1)

#### Ce qui a changé

- chore: Cherry pick 13648 into release 0.50 branch by [@lukelee-sl](https://github.com/lukelee-sl) in [#13662](https://github.com/hashgraph/hedera-services/pull/13662)
- fix(ci): cerry pick milestone assignee checks rel 50 by [@rbarkerSL](https://github.com/rbarkerSL) in [#13712](https://github.com/hashgraph/hedera-services/pull/13712)
- correction : (cherry-pick) Utilisez la méthode de redémarrage de tous les schémas de jetons par [@Neeharika-Sompalli](https://github.com/Neeharika-Sompalli) dans [#13676](https://github.com/hashgraph/hedera-services/pull/13676)
- Correction : Activez tokens.balancesInQueries.enabled par [@netopyr](https://github.com/netopyr) dans [#13716](https://github.com/hashgraph/hedera-services/pull/13716)
- chore: Activez tokens.balancesInQueries en code par [@netopyr](https://github.com/netopyr) dans [#13769](https://github.com/hashgraph/hedera-services/pull/13769)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/compare/v0.50.0...v0.50.1)**.**

### [0.50.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)

#### Ce qui a changé

- feat: réorganiser le câblage ISS par [@alittley](https://github.com/alittley) dans [#11685](https://github.com/hashgraph/hedera-services/pull/11685)
- feat(diff-testing): Script (python) pour tirer des intervalles - jusqu'à une journée - de GCP par [@david-bakin-sl](https://github.com/david-bakin-sl) dans [#11409](https://github.com/hashgraph/hedera-services/pull/11409)
- correction : 11750 Correction de la synchronisation dans `BreakableDataSource.saveRecords` par [@imalygin](https://github.com/imalygin) dans [#11756](https://github.com/hashgraph/hedera-services/pull/11756)
- feat: Test différentiel : Améliorez le dumper de la boutique de compte pour gérer la représentation modulaire par [@vtronkov](https://github.com/vtronkov) dans [#11489](https://github.com/hashgraph/hedera-services/pull/11489)
- test : ajouter des tests de modèle de sécurité v2 pour un jeton associé par [@anastasiya-kovaliova](https://github.com/anastasiya-kovaliova) dans [#11327](https://github.com/hashgraph/hedera-services/pull/11327)
- Correction : arrête de vérifier si la partie minimale de naissance est jouée par [@cody-littley](https://github.com/cody-littley) dans [#11769](https://github.com/hashgraph/hedera-services/pull/11769)
- feat: rendre l'état compatible avec les rounds de naissance par [@cody-littley](https://github.com/cody-littley) dans [#11780](https://github.com/hashgraph/hedera-services/pull/11780)
- correction : FilteredLoggingMonitor par [@mxtartaglia-sl](https://github.com/mxtartaglia-sl) dans [#11754](https://github.com/hashgraph/hedera-services/pull/11754)
- feat: diagram tweaks by [@cody-littley](https://github.com/cody-littley) in [#11801](https://github.com/hashgraph/hedera-services/pull/11801)
- correction : attendez plus longtemps pour que la transaction de gel soit gérée par [@JeffreyDallas](https://github.com/JeffreyDallas) dans [#11790](https://github.com/hashgraph/hedera-services/pull/11790)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.50.0)**.**

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.50_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.49](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 22 MAI 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 14 MAI 2024**
{% endhint %}

### [0.49.7](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.7)

#### Ce qui a changé

- correction : support des clés d'administration crypto dans le contrat système `tokenCreate()` par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13148](https://github.com/hashgraph/hedera-services/pull/13148)
- correction : supprime la limite d'ajustement de la balance de l'enregistrement en état, utilisez `0` pour un instantané de gaz initial par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13185](https://github.com/hashgraph/hedera-services/pull/13185)

### [0.49.6](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.6)

#### Ce qui a changé

- correctif : gestion des tarifs de minuit au redémarrage ([#13071](https://github.com/hashgraph/hedera-services/pull/13071)) par [@povolev15](https://github.com/povolev15) dans [#13091](https://github.com/hashgraph/hedera-services/pull/13091)
- feat: auto-re-submit des opérations avec des modifications ([#12811](https://github.com/hashgraph/hedera-services/pull/12811)) par [@Neeharika-Sompalli](https://github.com/Neeharika-Sompalli) dans [#13088](https://github.com/hashgraph/hedera-services/pull/13088)
- Correction : ignore le statut d'expiration de jeton dans `TokenDissociate` par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13106](https://github.com/hashgraph/hedera-services/pull/13106)
- Correction : évitez les NPE lors de la migration de l'état genesis (non-prod) par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13123](https://github.com/hashgraph/hedera-services/pull/13123)

### [0.49.5](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.5)

#### Ce qui a changé

- correction : gestion des liens de stockage par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#13056](https://github.com/hashgraph/hedera-services/pull/13056)

### [0.49.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.1)

#### Ce qui a changé

- correction : gérez `StakingInfos` en redémarrant par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#12911](https://github.com/hashgraph/hedera-services/pull/12911)

### [0.49.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)

#### Ce qui a changé&#x20

- feat: problème de lecture froide dans ExtCodeHash par [@lukelee-sl](https://github.com/lukelee-sl) dans [#11323](https://github.com/hashgraph/hedera-services/pull/11323)
- correction : 11348 : La correction de 11231 ne couvre pas ParsedBucket par [@artemananiev](https://github.com/artemananiev) dans [#11349](https://github.com/hashgraph/hedera-services/pull/11349)
- chore: Créer un composant détecteur ISS par [@lpetrovic05](https://github.com/lpetrovic05) dans [#11075](https://github.com/hashgraph/hedera-services/pull/11075)
- chore: Ajoutez la méthode `orderedSolderTo` à OutputWire par [@poulok](https://github.com/poulok) dans [#11330](https://github.com/hashgraph/hedera-services/pull/11330)
- chore: remove hashgraph demo by [@lpetrovic05](https://github.com/lpetrovic05) in [#11352](https://github.com/hashgraph/hedera-services/pull/11352)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.49.0)**.**

### **Résultats de performances**

<figure><img src="../../.gitbook/assets/0.49_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.48](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 25 APRIL 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 18 AVRIL 2024**
{% endhint %}

### [0.48.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.1)

#### Ce qui a changé

- correction : supprime la limite d'ajustements par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [#12826](https://github.com/hashgraph/hedera-services/pull/12826)

### [0.48.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.48.0)

{% hint style="success" %}
**MISE À JOUR TESTNET : 11 APRIL 2024**
{% endhint %}

#### Ce qui a changé

- feat: Vérifiez le statut de la plateforme avant de la synchroniser ([#11429](https://github.com/hashgraph/hedera-services/pull/11429)) par [@alittley](https://github.com/alittley) dans [#12679](https://github.com/hashgraph/hedera-services/pull/12679)

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.48_Performance Measurement Results_Extract.001.png" alt=""><figcaption></figcaption></figure>

## [v0.47](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : APRIL 4, 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 28 MARS 2024**
{% endhint %}

### [0.47.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.4)

#### Ce qui a changé

- chore: cherry-pick unified CryptoCreate throttle reclamation ([#12339](https://github.com/hashgraph/hedera-services/pull/12339)).

### [0.47.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.3)

{% hint style="success" %}
**MISE À JOUR TESTNET : 20 MARS 2024**
{% endhint %}

#### Ce qui a changé

- chore: Configurer `maxAggregateRels` à 15 millions (toutes les envies) ([#12053](https://github.com/hashgraph/hedera-services/pull/12053)).

### [0.47.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.2)

#### Ce qui a changé

- Correction : Mettre à jour la configuration `hashesRamToDiskThreshold` à 0 dans `MerkleDbConfig`
- correction : Backport le correctif pour les vides de cartes virtuelles.

### [0.47.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.1)

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 29 FÉVRIER 2024**
{% endhint %}

#### Ce qui a changé

- correction : ne compare que le temps enfant créé par [@alittley](https://github.com/alittley) dans [#11673](https://github.com/hashgraph/hedera-services/pull/11673)
- chore: ajoute un fil de file d'attente de style ancien pour la prise par [@cody-littley](https://github.com/cody-littley) dans [#11671](https://github.com/hashgraph/hedera-services/pull/11671)
- correction : 11746: Backport la correction de [#11304](https://github.com/hashgraph/hedera-services/issues/11304) pour libérer la 0.47 par [@artemananiev](https://github.com/artemananiev) dans [#11747](https://github.com/hashgraph/hedera-services/pull/11747)

### [0.47.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)

#### Ce qui a changé

- correction : bug quand le noeud est supprimé par [@cody-littley](https://github.com/cody-littley) dans [#10687](https://github.com/hashgraph/hedera-services/pull/10687)
- correction : correspondance Fuzzy pour CreateOperationSuite et Create2OperationSuite 09431 par [@JivkoKelchev](https://github.com/JivkoKelchev) dans [#10185](https://github.com/hashgraph/hedera-services/pull/10185)
- Correction : recordCache pour valider les entrées ajoutées et implémenté correctement les éléments de la file d'attente par [@povolev15](https://github.com/povolev15) dans [#10523](https://github.com/hashgraph/hedera-services/pull/10523)
- Correction : Corrige et active tous les Schedule HapiTests par [@povolev15](https://github.com/povolev15) dans [#10551](https://github.com/hashgraph/hedera-services/pull/10551)
- correction : implémente les sidecars par [@JivkoKelchev](https://github.com/JivkoKelchev) dans [#9815](https://github.com/hashgraph/hedera-services/pull/9815)
- feat: ajoute le réglage du seuil de naissance ancien par [@cody-littley](https://github.com/cody-littley) dans [#10660](https://github.com/hashgraph/hedera-services/pull/10660)
- chore: drop chatter by [@cody-littley](https://github.com/cody-littley) in [#10670](https://github.com/hashgraph/hedera-services/pull/10670)
- chore: remove state info by [@cody-littley](https://github.com/cody-littley) in [#10685](https://github.com/hashgraph/hedera-services/pull/10685)
- chore: Renommer le contrat causant une régression de services en raison du nom long par [@stoqnkpL](https://github.com/stoqnkpL) dans [#10700](https://github.com/hashgraph/hedera-services/pull/10700)
- correction : état de fuite par [@cody-littley](https://github.com/cody-littley) dans [#10690](https://github.com/hashgraph/hedera-services/pull/10690)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.47.0)**.**

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.47_Performance Measurement Results_Extract.png" alt=""><figcaption></figcaption></figure>

## [v0.46](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 21 FÉVRIER 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 6 FÉVRIER 2024**
{% endhint %}

### [**0.46.3**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.3)

#### Ce qui a changé

- chore: bump HAPI proto version by [@tinker-michaelj](https://github.com/tinker-michaelj) in [#11232](https://github.com/hashgraph/hedera-services/pull/11232)

### [**0.46.2**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.2)

{% hint style="success" %}
**MISE À JOUR TESTNET : 30 JANUARY 2024**
{% endhint %}

#### Ce qui a changé

- correction : Assurez-vous que le personnalisateur de création en attente s'applique à l'adresse créée par [@lukelee-sl](https://github.com/lukelee-sl) dans [#11213](https://github.com/hashgraph/hedera-services/pull/11213)

### [**0.46.1**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.1)

#### Ce qui a changé

- chore: bump HAPI proto version by [@tinker-michaelj](https://github.com/tinker-michaelj) in [#11232](https://github.com/hashgraph/hedera-services/pull/11232)

### [0.46.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)

{% hint style="success" %}
**MISE À JOUR TESTNET : 23 JANUARY 2024**
{% endhint %}

#### Ce qui a changé

- feat: amélioration du diagramme par [@cody-littley](https://github.com/cody-littley) dans [#10233](https://github.com/hashgraph/hedera-services/pull/10233)
- chore: Changez `HashMap` en `LinkedHashMap` dans l'évaluation des frais personnalisés par [@Neeharika-Sompalli](https://github.com/Neeharika-Sompalli) dans [#10240](https://github.com/hashgraph/hedera-services/pull/10240)
- feat : ajoute une implémentation dans la facilité de limitation pour gérer le type de limitation N-Of-Unscaled par [@MiroslavGatsanoga](https://github.com/MiroslavGatsanoga) dans [#10142](https://github.com/hashgraph/hedera-services/pull/10142)
- build: ne publiez pas de correctifs de test par [@jjohannes](https://github.com/jjohannes) dans [#10147](https://github.com/hashgraph/hedera-services/pull/10147)
- build: patche tout ce que nous utilisons pour être un vrai module Java par [@jjohannes](https://github.com/jjohannes) dans [#10056](https://github.com/hashgraph/hedera-services/pull/10056)
- chore!: Plus de tests courants ont été déplacés vers le module correct par [@hendrikebbers](https://github.com/hendrikebbers) dans [#10133](https://github.com/hashgraph/hedera-services/pull/10133)
- feat: constantes de configuration créées et utilisées par [@hendrikebbers](https://github.com/hendrikebbers) dans [#10117](https://github.com/hashgraph/hedera-services/pull/10117)
- feat: script pour nettoyer les fichiers de construction par [@cody-littley](https://github.com/cody-littley) dans [#10190](https://github.com/hashgraph/hedera-services/pull/10190)
- correction : le dernier fichier PCES compact au démarrage par [@cody-littley](https://github.com/cody-littley) dans [#10257](https://github.com/hashgraph/hedera-services/pull/10257)
- feat: sync++- par [@cody-littley](https://github.com/cody-littley) dans [#10260](https://github.com/hashgraph/hedera-services/pull/10260)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.46.0)**.**

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.46_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.45](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 9 JANUARY 2024**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 28 DECEMBRE 2023**
{% endhint %}

### [0.45.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.2)

#### Ce qui a changé

- Correction : Ajout d'un flag de fonctionnalité qui est par défaut activé pour désactiver les tokenBalances et tokenRelationships dans les requêtes `getAccountInfo`, `getAccountBalance` et `getContractInfo`. [#10639](https://github.com/hashgraph/hedera-services/pull/10639)

### [0.45.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.45.0)

- Remplir la fonction evm si la transaction échoue par [@stoqnkpL](https://github.com/stoqnkpL) dans [#9453](https://github.com/hashgraph/hedera-services/pull/9453)
- Désactiver la compression. par [@cody-littley](https://github.com/cody-littley) dans [#9554](https://github.com/hashgraph/hedera-services/pull/9554)
- Corrige les tests dans une spécification unique de gestion de jetons par [@mhess-swl](https://github.com/mhess-swl) dans [#9537](https://github.com/hashgraph/hedera-services/pull/9537)
- a lancé un test de plus et supprimé l'autre qui n'est pas vraiment utilisé par [@povolev15](https://github.com/povolev15) dans [#9557](https://github.com/hashgraph/hedera-services/pull/9557)
- Activer les tests de CannotDeleteSystemEntitiesSuite par [@Ivo-Yankov](https://github.com/Ivo-Yankov) dans [#9440](https://github.com/hashgraph/hedera-services/pull/9440)
- Corrige les tests de ContractBurnHTSSuite par [@agadzhalov](https://github.com/agadzhalov) dans [#9572](https://github.com/hashgraph/hedera-services/pull/9572)
- Tune dependency scopes by [@jjohannes](https://github.com/jjohannes) in [#8455](https://github.com/hashgraph/hedera-services/pull/8455)
- appels inutiles vers swirlds-common supprimés par [@hendrikebbers](https://github.com/hendrikebbers) dans [#9003](https://github.com/hashgraph/hedera-services/pull/9003)
- Correction de CryptoRecordsSanityCheckSuite par [@iwsimon](https://github.com/iwsimon) dans [#9551](https://github.com/hashgraph/hedera-services/pull/9551)
- Activer le test depuis AssociatePrecompileSuite par [@mustafauzunn](https://github.com/mustafauzunn) dans [#9571](https://github.com/hashgraph/hedera-services/pull/9571)

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.45_Performance Measurement Results_Extract.png" alt=""><figcaption></figcaption></figure>

## [v0.44](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 19 DECEMBRE 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 12 DECEMBRE 2023**
{% endhint %}

### [0.44.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.3)

#### Ce qui a changé

- Forcer la reconnaissance NFT à vérifier la création automatique par [@tinker-michaelj](https://github.com/tinker-michaelj) dans [e69d0a9](https://github.com/hashgraph/hedera-services/commit/e69d0a917c1c0a9417a3f335129a74ac3004b7c9)

### [0.44.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.2)

#### Ce qui a changé

- Attrape une exception UncheckedIOException pendant la copie de fichier PCES. ([#10083](https://github.com/hashgraph/hedera-services/pull/10083)) par [@cody-littley](https://github.com/cody-littley) dans [#10087](https://github.com/hashgraph/hedera-services/pull/10087)

### [0.44.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.1)

#### Corrections de bugs

- Corriger les bugs de copie de PCES. ([#10057](https://github.com/hashgraph/hedera-services/pull/10057)) [#10062](https://github.com/hashgraph/hedera-services/pull/10062)

### [0.44.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)

#### Fonctionnalités

- Ré-ajouter le fichier bootstrap.properties pour maintenir les processus en aval et augmenter le nombre de comptes.maxNumber=20\_000\_000 [#8915](https://github.com/hashgraph/hedera-services/pull/8915)
- 8815 : triez les feuilles sales pendant la purge [#8981](https://github.com/hashgraph/hedera-services/pull/8981)
- Ajouter un paramètre pour désactiver le quorum critique. [#8961](https://github.com/hashgraph/hedera-services/pull/8961)
- Ajouter un doc pour tous les numéros d'entités système [#8993](https://github.com/hashgraph/hedera-services/pull/8993)
- 08566 - Valider les événements PCES lorsque le chargement de l'état sur un réseau différent [#8568](https://github.com/hashgraph/hedera-services/pull/8568)
- Moteur d'analyse de tests différentiels : le dumper de fichiers d'état sauvegarde maintenant des fichiers spéciaux [#8991](https://github.com/hashgraph/hedera-services/pull/8991)
- Ajout d'une amélioration de l'art ASCII de démarrage. [#9028](https://github.com/hashgraph/hedera-services/pull/9028)
- Caractériser les modes d'échec des identifiants invalides pour les appels HTS classiques [#9053](https://github.com/hashgraph/hedera-services/pull/9053)
- Ajouter des ordinals à l'état du diagramme et mettre à jour javadocs [#9108](https://github.com/hashgraph/hedera-services/pull/9108)
- 5552: Créer un tableau de bord de données Grafana pour afficher toutes les données existantes pertinentes [#8845](https://github.com/hashgraph/hedera-services/pull/8845)
- Mettre à jour Besu vers la version 23.10.0 [#9168](https://github.com/hashgraph/hedera-services/pull/9168)

**➡️ Voir la liste complète des changements** [**ici**](https://github.com/hashgraph/hedera-services/releases/tag/v0.44.0)**.**

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.44_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.43](https://github.com/hashgraph/hedera-services/releases/tag/v0.43.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 27 NOVEMBRE 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 2 NOVEMBRE 2023**
{% endhint %}

Services v0.43.0 ajoute les fonctionnalités suivantes :

- HIP-786 ([#8620](https://github.com/hashgraph/hedera-services/pull/8620))

#### Améliorations

Les services v0.43.0 ajoutent les améliorations suivantes :

- Mettre à jour Besu vers 23.10.0 - cherry pick ([#9199](https://github.com/hashgraph/hedera-services/pull/9199))
- Mettez à jour la bibliothèque Besu EVM en version 23.7.2 ([#8472](https://github.com/hashgraph/hedera-services/pull/8472))
- Le désastre du contrat "Productizing" ([#8563](https://github.com/hashgraph/hedera-services/pull/8563))
- Validations auto sidecar ([#8404](https://github.com/hashgraph/hedera-services/pull/8404))
- Créez des graisses jar avec les services CLI pour qu'il puisse être exécuté seul ([#8519](https://github.com/hashgraph/hedera-services/pull/8519))

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.43_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.42](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 24 OCTOBER 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 26 SEPTEMBRE 2023**
{% endhint %}

### [0.42.6](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.6)

Cette version met à jour la version de la plate-forme SDK de `0.42.0` à `0.42.6`, qui supprime `reconnect.asyncStreamTimeout` des fichiers de configuration. Cela garantit que cette propriété sera par défaut à la valeur spécifiée dans le code (300 secondes).

#### Changements

- Mise à jour de la plate-forme SDK ([#9224](https://github.com/hashgraph/hedera-services/pull/9224))

### [0.42.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.2)

#### Changements

- Test du solde de compte 0.42 ([#8866](https://github.com/hashgraph/hedera-services/pull/8866))
- Ré-ajouter le fichier bootstrap.properties et augmenter `accounts.maxNumber=20_000_000` ([#8928](https://github.com/hashgraph/hedera-services/pull/8928))

### [0.42.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.1)

#### Changements

- Chore: normalise les valeurs de configuration (release/0.42) ([#8668](https://github.com/hashgraph/hedera-services/pull/8668))
- 8751 : Aucune mesure de source de données pour les comptes, les NFL ou les jetons rels ([#8798](https://github.com/hashgraph/hedera-services/pull/8798))

### [0.42.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.42.0)

- Ajouter le support EIP 2930 à EthTXData ([#7696](https://github.com/hashgraph/hedera-services/pull/7696))
- Fournir des tableaux de bord d'entités et de gaz ([#7774](https://github.com/hashgraph/hedera-services/pull/7774))
- 07748 Collecte de signatures Postconsensus ([#7776](https://github.com/hashgraph/hedera-services/pull/7776))
- Activer les transactions EIP-2930 par défaut ([#7786](https://github.com/hashgraph/hedera-services/pull/7786))
- 7570 : Supprimer JasperDB ([#7803](https://github.com/hashgraph/hedera-services/pull/7803))
- Supprimer la prise en charge des ragots de synchronisation hérités. ([#8059](https://github.com/hashgraph/hedera-services/pull/8059))
- Désactiver les exportations du solde du compte ([#8272](https://github.com/hashgraph/hedera-services/pull/8272))
- Modifier la configuration pour prendre en charge l'état sur le disque par défaut ([#8510](https://github.com/hashgraph/hedera-services/pull/8510))

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.42_Performance Measurement Results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.41](https://github.com/hashgraph/hedera-services/releases/tag/v0.41.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 20 SEPTEMBRE 2023**
{% endhint %}

{% hint style="success" %}
**Mises à jour TESTNET : 22 août, 2023**
{% endhint %}

- La prise en charge du type de transaction Ethereum est étendue pour inclure les transactions de type 1 ([#7670](https://github.com/hashgraph/hedera-services/issues/7670)) qui suivent l'encodage EIP 2930 RLP. Cela augmente le nombre d’outils et de scénarios natifs de EVM pris en charge par le Service de Contrats Intelligents Hedera.
- Le prix de la menthe NFT est modifié à une échelle linéaire en fonction du nombre de séries frappées. En outre, la récolte d'une seule NFT dans la collection est modifiée pour coûter 0,02 $ de 0,05 $. [#7769](https://github.com/hashgraph/hedera-services/issues/7769)

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.41_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.40](https://github.com/hashgraph/hedera-services/releases/tag/v0.40.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 15 Aout 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 8 AUTS, 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 19 JUILLET 2023**
{% endhint %}

La version 0.40 de Hedera Services délivre [HIP-729 \~ "Externalisation des comptes de contrats Nonce "](https://hips.hedera.com/hip/hip-729). Les développeurs de contrats intelligents utilisant le serveur miroir public Hedera peuvent maintenant suivre les nonces de contrat comme sur Ethereum, par exemple. Les cas d'utilisation peuvent inclure le dépannage des appels de contrat échoués ou l'écriture de tests unitaires qui valident l'ordre des transactions en fonction des adresses `CREATE1` (lorsque celles-ci sont définies par défaut dans la version 0. 1+).

Les contributeurs Open Source au projet remarqueront des améliorations majeures dans la compilation Gradle, grâce à [@jjohannes](https://github.com/jjohannes)'s expert touch.

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.40_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.39](https://github.com/hashgraph/hedera-services/tags)

{% hint style="success" %}
**MISE À JOUR MAINNET : 11 JUILLET, 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 21 JUIN 2023**
{% endhint %}

Services v0.39.0 ajoute les fonctionnalités suivantes :

- Le constructeur VirtualRootNode crée un objet cache qui ne sera pas réutilisé [#6321](https://github.com/hashgraph/hedera-services/pull/6321)
- Implémenter la liste noire des adresses EVM [#5799](https://github.com/hashgraph/hedera-services/pull/5799)
- Optimiser la stratégie de vidage du cache des nœuds virtuels [#5568](https://github.com/hashgraph/hedera-services/pull/5568)
- HIP-721: 06026 - ajoute la version du logiciel aux événements [#6236](https://github.com/hashgraph/hedera-services/pull/6236)
- Implémenter la méthode CryptoCreate [#6112](https://github.com/hashgraph/hedera-services/pull/6112)
- UtilPrng gère l'implémentation [#6310](https://github.com/hashgraph/hedera-services/pull/6310)
- Ajouter une sous-commande PCLI pour signer les fichiers de flux de services [#6309](https://github.com/hashgraph/hedera-services/pull/6309)
- Implémenter la gestion des jetons gels [#6467](https://github.com/hashgraph/hedera-services/pull/6467)
- Implémenter token unfreeze handle() [#6502](https://github.com/hashgraph/hedera-services/pull/6502)
- Combiner les modules Admin et Network [#6511](https://github.com/hashgraph/hedera-services/pull/6511)
- Implémenter le flux de travail modulaire pré-géré [#6291](https://github.com/hashgraph/hedera-services/pull/6291)
- Déplacer les hashes hors du nœud de feuilles dans VirtualMap [#5825](https://github.com/hashgraph/hedera-services/pull/5825)
- Implémentation de TokenFeeScheduleUpdate handle() [#6582](https://github.com/hashgraph/hedera-services/pull/6582)
- Implémentation du service de fichiers de base [#6522](https://github.com/hashgraph/hedera-services/pull/6522)
- Implémenter l'association de jetons vers le compte [#6609](https://github.com/hashgraph/hedera-services/pull/6609)
- Implémentation du workflow de gestion [#6476](https://github.com/hashgraph/hedera-services/pull/6476)
- Implémenter le cache d'enregistrement modulaire [#6754](https://github.com/hashgraph/hedera-services/pull/6754)
- Implémentation du gestionnaire de CryptoDelete [#6694](https://github.com/hashgraph/hedera-services/pull/6694)

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.39_Performance Measurement.png" alt=""><figcaption></figcaption></figure>

## [v0.38](https://github.com/hashgraph/hedera-services/releases/tag/v0.38.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 8 JUIN 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 1 JUIN 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 18 MAI 2023**
{% endhint %}

- Mettez à niveau EVM vers Shanghai [#5964](https://github.com/hashgraph/hedera-services/pull/5964)
- Mise à jour et optimisation de la version EVM [#5962](https://github.com/hashgraph/hedera-services/pull/5962)
- Activez la version Shanghai de l'EVM dans previewnet [#6212](https://github.com/hashgraph/hedera-services/pull/6212)
- Mettre à jour la version hedera-protobufs-java vers la 0.38.10 [#6579](https://github.com/hashgraph/hedera-services/pull/6579)
- Ajouter une commande PCLI pour signer les fichiers de solde du compte [#6264](https://github.com/hashgraph/hedera-services/pull/6264)

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.38_Performance Measurement Results_Extract.001.jpeg" alt=""><figcaption></figcaption></figure>

## [v0.37](https://github.com/hashgraph/hedera-services/releases/tag/v0.37.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : MAI 17, 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 24 APRIL 2023**
{% endhint %}

### Fonctionnalités

- Implémenter le préhandle de suppression de sujet ([#5033](https://github.com/hashgraph/hedera-services/pull/5033))
- Générer les workflows activés et ajouter des ports de workflow ([#5032](https://github.com/hashgraph/hedera-services/pull/5032))
- Pré-gérer les améliorations ([#5056](https://github.com/hashgraph/hedera-services/pull/5056))
- Prise en charge des opérations de planification automatique par type dans une suite ([#5054](https://github.com/hashgraph/hedera-services/pull/5054))
- Ajouter des composants SPI et App prenant en charge TransactionDispatcher pour le HCS modularisé ([#5062](https://github.com/hashgraph/hedera-services/pull/5062))
- a ajouté la fonctionnalité manquante à FileSignTool ([#5100](https://github.com/hashgraph/hedera-services/pull/5100))
- Pré-handle de soumission de message de consensus ([#5059](https://github.com/hashgraph/hedera-services/pull/5059))
- Ajouter des adaptateurs mono IngestChecker pour signature et solvabilité ([#5098](https://github.com/hashgraph/hedera-services/pull/5098))
- \[HIP-583] Finaliser les comptes creux via toute signature requise dans un txn ([#4990](https://github.com/hashgraph/hedera-services/pull/4990))
- Supprimer la capacité CryptoCreate pour créer des comptes creux ([#4998](https://github.com/hashgraph/hedera-services/pull/4998))
- Remplissez l'adresse EVM dans CryptoTranscation ([#5010](https://github.com/hashgraph/hedera-services/pull/5010))
- Activer toutes les suites EVM E2E pour fonctionner avec Ethereum Calls ([#4375](https://github.com/hashgraph/hedera-services/pull/4375))

### Résultats de performance

<figure><img src="../../.gitbook/assets/0_37Performance Measurement Results_Extract.001.jpeg" alt=""><figcaption></figcaption></figure>

## [v0.36](https://github.com/hashgraph/hedera-services/releases/tag/v0.36.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 20 APRIL 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : APRIL 13, 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : APRIL 4, 2023**
{% endhint %}

#### Fonctionnalités

Services v0.36.0 ajoute les fonctionnalités suivantes :

- Ajouter le suivi des changements de propriété pour la réalisation du compte creux ([#4647](https://github.com/hashgraph/hedera-services/pull/4647))
- Ajout du support des Redirect Token Calls fro evm-module ([#4880](https://github.com/hashgraph/hedera-services/pull/4880))
- Mettre à jour FileSignTool ([#4988](https://github.com/hashgraph/hedera-services/pull/4988))
- Ajout de l'outil de numéro de bloc ([#4997](https://github.com/hashgraph/hedera-services/pull/4997))
- Ajoutez client.workflow.operations et testez avec les workflows ([#5053](https://github.com/hashgraph/hedera-services/pull/5053))
- mettre à jour les services hedera pour utiliser FSTS CLI au lieu des propriétés système
- 6166 : Migrer les données de la carte virtuelle de JasperDB vers les sources de données MerkleDb
- Implémentation de la fonctionnalité réseau actuelle dans une nouvelle architecture d'application modularisée : opérations de consensus, flux de travail de requêtes, et diverses implémentations de prégestion

### Mises à jour de sécurité : Changements du modèle de sécurité du service de Hedera Smart Contract Service

Des changements depuis les services v0.35.2 ont également été portés vers v0.36.0.

- Après l'incident de sécurité le 9 mars, les ingénieurs ont effectué une analyse approfondie du Service de Contrat Intelligent et des contrats du système de Token Service Hedera.
- Dans le cadre de cet exercice, nous n'avons pas trouvé de vulnérabilités supplémentaires qui pourraient entraîner une attaque que ce que nous avons vu le 9 mars.
- L'équipe a également cherché des disparités entre les attentes d'un développeur de contrats intelligents typique qui est utilisé pour travailler avec les API de jetons Ethereum (EVM) ou ERC et les comportements des API de contrats du système de jeton de Hedera. De telles différences de comportement pourraient être utilisées par un développeur de contrats intelligents malveillants de manière inattendue.
- Afin d'éliminer la possibilité que ces différences de comportement soient utilisées comme vecteurs d'attaque dans le futur, le logiciel de nœuds de consensus alignera les comportements des contrats du système de jetons de service de contrats intelligents Hedera avec ceux de EVM et les API de jetons typiques tels que ERC 20 et ERC 721.
- En conséquence, les modifications suivantes sont apportées depuis la version 0.35.2 du réseau principal le 31 mars :
  - Un EOA (compte appartenant à l'extérieur) devra fournir une approbation ou une provision explicites à un contrat s'il veut que le contrat transfère la valeur du solde de son compte.
  - Le comportement du contrat système `transferFrom` sera exactement le même que celui de la fonction `transferFrom` ERC 20 et ERC 721 spec `transferFrom`.
  - Pour les fonctionnalités de jetons spécifiques au HTS (par ex. Mettre en pause, congeler ou accorder KYC), un contrat ne sera autorisé à exécuter la fonction de gestion de jetons associée que si le ContractId est listé comme clé sur le jeton (i.e. Pause Key, Freeze Key, KYC Key respectivement).
  - Les API `transferToken` et `transferNFT` se comporteront comme `transfer` dans ERC20/721 si l'appelant possède la valeur en cours de transfert, sinon il dépendra des quotas de débiteurs du propriétaire du jeton.
  - Le modèle ci-dessus dictera les autorisations de l'entité (EOA et contrats) pendant l'exécution du contrat lors de la modification de l'état. Les contrats ne s'appuieront plus sur la présence de signature de la transaction Hedera, mais seront plutôt conformes aux modèles clés EVM, ERC et ContractId notés.
- Dans le cadre de ce communiqué, le réseau comprendra la logique du grand-père dans les contrats antérieurs.
  - Tous les contrats créés à partir de cette publication utiliseront le modèle de sécurité plus strict et, en tant que tel, n'auront pas de considération pour les signatures de premier niveau sur les transactions pour fournir des autorisations.
  - Les contrats existants déployés avant cette mise à niveau seront automatiquement mis à profit et continueront à utiliser l'ancien modèle qui était en place avant cette mise à jour pour une durée limitée afin de permettre aux modifications DApp/UX de travailler avec le nouveau modèle de sécurité.
  - La logique du grand-père sera maintenue pendant une période approximative de 3 mois à compter de cette publication. Dans une version ultérieure en juillet 2023, le réseau supprimera la logique du grand-père et tous les contrats suivront le nouveau modèle de sécurité.
  - Les développeurs sont encouragés à tester leurs DApps avec de nouveaux contrats et UX en utilisant le nouveau modèle de sécurité pour éviter des conséquences involontaires. Si aucun développeur DApp ne parvient à modifier ses applications ou à mettre à niveau ses contrats (selon le cas) pour adhérer au nouveau modèle de sécurité. ils peuvent rencontrer des problèmes dans leurs applications.

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.36_Performance Measurement Results_Extract.001 (1).png" alt=""><figcaption></figcaption></figure>

## [v0.35](https://github.com/hashgraph/hedera-services/releases)

{% hint style="success" %}
**MISE À JOUR MAINNET : 31 MARS 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 16 MARS 2023**
{% endhint %}

### [0.35.2 Modifications du modèle de sécurité du service de contrat intelligent Hedera](https://github.com/hashgraph/hedera-services/releases/tag/v0.35.2)

- Après l'incident de sécurité le 9 mars, les ingénieurs ont effectué une analyse approfondie du Service de Contrat Intelligent et des contrats du système de Token Service Hedera.
- Dans le cadre de cet exercice, nous n'avons pas trouvé de vulnérabilités supplémentaires qui pourraient entraîner une attaque que ce que nous avons vu le 9 mars.
- L'équipe a également cherché des disparités entre les attentes d'un développeur de contrats intelligents typique qui est utilisé pour travailler avec les API de jetons Ethereum (EVM) ou ERC et les comportements des API de contrats du système de jeton de Hedera. De telles différences de comportement pourraient être utilisées par un développeur de contrats intelligents malveillants de manière inattendue.
- Afin d'éliminer la possibilité que ces différences de comportement soient utilisées comme vecteurs d'attaque dans le futur, le logiciel de nœuds de consensus alignera les comportements des contrats du système de jetons de service de contrats intelligents Hedera avec ceux de EVM et les API de jetons typiques tels que ERC 20 et ERC 721.
- En conséquence, les modifications suivantes sont apportées depuis la version 0.35.2 du réseau principal le 31 mars :
  - Un EOA (compte appartenant à l'extérieur) devra fournir une approbation ou une provision explicites à un contrat s'il veut que le contrat transfère la valeur du solde de son compte.
  - Le comportement du contrat système `transferFrom` sera exactement le même que celui de la fonction `transferFrom` ERC 20 et ERC 721 spec `transferFrom`.
  - Pour les fonctionnalités de jetons spécifiques au HTS (par ex. Mettre en pause, congeler ou accorder KYC), un contrat ne sera autorisé à exécuter la fonction de gestion de jetons associée que si le ContractId est listé comme clé sur le jeton (i.e. Pause Key, Freeze Key, KYC Key respectivement).
  - Les API `transferToken` et `transferNFT` se comporteront comme `transfer` dans ERC20/721 si l'appelant possède la valeur en cours de transfert, sinon il dépendra des quotas de débiteurs du propriétaire du jeton.
  - Le modèle ci-dessus dictera les autorisations de l'entité (EOA et contrats) pendant l'exécution du contrat lors de la modification de l'état. Les contrats ne s'appuieront plus sur la présence de signature de la transaction Hedera, mais seront plutôt conformes aux modèles clés EVM, ERC et ContractId notés.
- Dans le cadre de ce communiqué, le réseau comprendra la logique du grand-père dans les contrats antérieurs.
  - Tous les contrats créés à partir de cette publication utiliseront le modèle de sécurité plus strict et, en tant que tel, n'auront pas de considération pour les signatures de premier niveau sur les transactions pour fournir des autorisations.
  - Les contrats existants déployés avant cette mise à niveau seront automatiquement mis à profit et continueront à utiliser l'ancien modèle qui était en place avant cette mise à jour pour une durée limitée afin de permettre aux modifications DApp/UX de travailler avec le nouveau modèle de sécurité.
  - La logique du grand-père sera maintenue pendant une période approximative de 3 mois à compter de cette publication. Dans une version ultérieure en juillet 2023, le réseau supprimera la logique du grand-père et tous les contrats suivront le nouveau modèle de sécurité.
  - Les développeurs sont encouragés à tester leurs DApps avec de nouveaux contrats et UX en utilisant le nouveau modèle de sécurité pour éviter des conséquences involontaires. Si aucun développeur DApp ne parvient à modifier ses applications ou à mettre à niveau ses contrats (selon le cas) pour adhérer au nouveau modèle de sécurité. ils peuvent rencontrer des problèmes dans leurs applications.

#### Fonctionnalités

- [HIP-583](https://hips.hedera.com/hip/hip-583) pour étendre le support des alias dans CryptoCreate & CryptoTransfer Transactions.

Cela inclut,

- CryptoTransfer vers un alias d'adresse EVM non existant, provoquant la création d'un compte creux.
- Finalisation d'un compte creux avec la signature du payeur dans une transaction entrante

Cas d'utilisation pour HIP-583 qui fonctionnent dans cette version :

1. En tant qu'utilisateur avec un compte ECDSA d'une autre chaîne, je peux avoir un nouveau compte Hedera créé sur la base de mes alias d'adresse evm.
2. En tant que développeur, je peux créer un nouveau compte en utilisant un alias d'adresse électronique via la transaction CryptoTransfert.
3. En tant que développeur, je peux transférer des HBAR ou des jetons vers un compte Hedera en utilisant leurs alias d'adresse evm.
4. En tant qu'utilisateur de Hedera avec un portefeuille natif d'Ethereum, je peux recevoir des HBAR ou des jetons dans mon compte en ne partageant que mes alias d'adresse evm.
5. En tant qu'utilisateur de Hedera avec un portefeuille natif de Hedera, je peux transférer des HBAR ou des jetons vers un autre compte en utilisant uniquement les alias d'adresse électronique du destinataire.

#### Changements de configuration

```
autoCreation.enabled=true
lazyCreation.enabled=true
cryptoCreateWithAliasAndEvmAddress.enabled=false
contracts.evm.version=v0.34
```

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.35_results.001.jpeg" alt=""><figcaption></figcaption></figure>

## [**v0.34**](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 9 FÉVRIER 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 24 JANUARY 2023**
{% endhint %}

### [0.34.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.3)

Utilisez `v0.34.3` SDK.

### [0.34.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.34.0)

Les services `v0.34.0` complètent l'implémentation de [HIP-583](https://hips.hedera.com/hip/hip-583).

Pour assurer une couverture de test complète de cette fonctionnalité complexe, elle sera d'abord activée **uniquement sur previewnet**.

Cette version n'activera pas la location de contrats intelligents.

### Résultats de performance

<figure><img src="../../.gitbook/assets/0.34.1.001.png" alt=""><figcaption></figcaption></figure>

## [v0.33](https://github.com/hashgraph/hedera-services/releases/tag/v0.33.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 12 JANUARY 2023**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 22 DÉCEMBRE 2022**
{% endhint %}

Services v0.33.0 ajoute les fonctionnalités suivantes :

- Hyperledger Besu EVM mis à jour en version 22.10.x
- sous-commande 'accounts send' ajoutée à yahcli pour supporter l'envoi d'unités de jetons HTS
- Mises à jour de la documentation des développeurs

<figure><img src="../../.gitbook/assets/Performance Measurement Results_033.001.png" alt=""><figcaption></figcaption></figure>

## [v0.31](https://github.com/hashgraph/hedera-services/releases/tag/v0.31.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 9 DECEMBRE 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : DÉCEMBRE 1, 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 11 NOVEMBRE 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 27 OCTOBER 2022**
{% endhint %}

Les services 0.31 complètent les fonctionnalités suivantes :

- [roadmap HIP-542](https://hips.hedera.com/hip/hip-542) pour avoir rendu payeur du sponsor `CryptoTransfer` pour `auto-creation`. Il permet également la création automatique avec les transferts de jetons en plus des transferts Hbar.
- [roadmap HIP-564](https://hips.hedera.com/hip/hip-564) pour permettre des transferts de jetons fongibles sans unité
- [feuille de route HIP-573](https://hips.hedera.com/hip/hip-573) pour permettre aux créateurs de jetons d'exempter _tous_ les collecteurs de frais de leur jeton d'un frais personnalisé.

En plus des fonctionnalités ci-dessus,

- Ajoute le support de la méthode ERC20/721 `transferFrom` pour les précompilations HTS à partir de la [roadmap HIP-514](https://hips.hedera.com/hip/hip-514).
- Active la traçabilité des contrats intelligents.
- Ajoute quelques modifications liées aux améliorations de testabilité.

<figure><img src="../../.gitbook/assets/0.31_results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.30](https://github.com/hashgraph/hedera-services/releases/tag/v0.30.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 21 OCTOBRE 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 19 OCTOBER 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 6 OCTOBER 2022**
{% endhint %}

Les services 0.30 complètent la [feuille de route HIP-514](https://hips.hedera.com/hip/hip-514) pour rendre les jetons natifs de Hedera gérables via des contrats intelligents. Il y a cinq nouveaux contrats de système: `getTokenExpiryInfo(address)`, `updateTokenExpiryInfo(address, Expiry)`, `isToken(address token)`, `getTokenType(address token)`, et `updateTokenInfo(address, HederaToken)`.

L'appel `updateTokenInfo(adresse, HederaToken)` est particulièrement puissant. Si la clé d'administration d'un jeton signe la transaction en appelant un contrat, ce contrat peut maintenant se faire le trésor du jeton, assumer le pouvoir de frapper, de brûler des unités ou des NFL, etc.

⚠️ Les auteurs de contrats devraient savoir que cette version initie le modèle Hedera [expiration et location de contrats](https://hedera.com/blog/smart-contract-rent-on-hedera-is-coming-what-you-need-to-know). Il y aura deux effets visibles immédiatement après la mise à niveau de la 0.30:

- Tous les contrats non supprimés auront leur expiration prolongée d'au moins 90 jours après la date de mise à niveau.
- Les contrats supprimés vont commencer à être purgés de l'état; donc une requête `getContractInfo` qui précédemment\
  a retourné `CONTRACT_DELETED` peut maintenant signaler `INVALID_CONTRACT_ID`.

Environ 90 jours après la mise à niveau 0.30, certains contrats vont commencer à expirer. Le réseau tentera de facturer automatiquement les frais de renouvellement (environ `$0.026` pendant 90 jours) au compte de renouvellement automatique du contrat expiré. Si un compte de renouvellement automatique a un solde nul, le réseau essaiera alors de facturer le contrat lui-même.

Un contrat incapable de payer les frais de renouvellement entrera dans une période de grâce d'une semaine au cours de laquelle il est inutilisable, sauf si son expiration est prolongée via `ContractUpdate` ou si elle reçoit un hbar. Après cette période de grâce, le contrat sera purgé de l'état.

Nous encourageons **fortement** tous les auteurs de contrats à créer un compte de renouvellement automatique de leur contrat. Cela permet d'isoler la logique contractuelle de l'existence du loyer.

Cette version apporte également deux améliorations périphériques :

1. Il sera possible de planifier une transaction `CryptoApproveAllowance`.
2. Les opérateurs de noeuds miroir seront en mesure d'utiliser l'exportation quotidienne de `NodeStakeUpdate` pour suivre les valeurs actuelles de [plusieurs propriétés de staking clés](https://github.com/hashgraph/hedera-protobufs/blob/main/services/node\_stake\_update.proto#L45). Veuillez consulter les commentaires liés au protobuf pour plus de détails sur ces propriétés.

<figure><img src="../../.gitbook/assets/0.30_results.001.png" alt=""><figcaption></figcaption></figure>

## [v0.29](https://github.com/hashgraph/hedera-services/releases/tag/v0.29.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 27 SEPTEMBRE 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : SEPTEMBRE 7, 2022**
{% endhint %}

{% hint style="success" %}
**Mises à jour TESTNET : 30 août, 2022**
{% endhint %}

### Jetons gérés par contrat 🪙

Dans les services 0.29, nous avons suivi la [feuille de route HIP-514](https://hips.hedera.com/hip/hip-514) pour donner aux auteurs de contrats de nombreuses nouvelles façons d'inspecter et de gérer les jetons HTS.

L'HIP énumère les voies ; des exemples incluent un contrat qui révoque le KYC d'un compte pour un jeton, ou supprime un jeton pour lequel il a des privilèges d'administrateur, ou même modifie la clé de fourniture d'un jeton basée sur les métadonnées dans un NFT!

Note : il y a quatre fonctions HIP-514 qui feront partie de la version 0.30, comme suit: `getTokenExpiryInfo(address)`, `updateTokenExpiryInfo(address, ExpiryInfo(address, Expiry)`, `updateTokenInfo(address, HederaToken)`, `isToken(address token)` et `getTokenType(address token)`.

[HIP-435 Record Stream v6](https://hips.hedera.com/hip/hip-435) sera activé sur testnet et mainnet dans cette version.

### Dépréciations

Veuillez noter cette [dépréciation importante](https://github.com/hashgraph/hedera-protobufs/blob/main/services/crypto\_get\_info.proto#L141) qui va changer la façon dont les clients récupèrent les associations de jetons et les soldes après la publication de novembre de cette année. À cette époque, les nœuds de miroir deviendront la source exclusive des métadonnées d'association de jetons. C'est parce que [HIP-367](https://hips.hedera. om/hip/hip/hip-367) a rendu les associations de jetons illimitées, donc à long terme il ne sera pas efficace pour les nœuds consensuels de servir cette information.

<figure><img src="../../.gitbook/assets/0.29.2.png" alt=""><figcaption></figcaption></figure>

## [v0.28](https://github.com/hashgraph/hedera-services/releases/tag/v0.28.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 25 Aout 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 29 JUILLET 2022**
{% endhint %}

Les services 0.28 donnent aux développeurs Hedera un nouveau bloc de construction dApp dans [HIP-351 (Pseudorandom Numbers)](https://hips.hedera.com/hip/hip-351). HAPI a un nouveau [`UtilService`](https://hashgraph.github.io/hedera-protobufs/#proto. tilService) avec une transaction `prng` qui génère un enregistrement avec soit une seed pseudorandom de 48 octets, soit un entier dans une plage demandée.

Les contrats intelligents peuvent également obtenir des valeurs pseudographiques en appelant un nouveau contrat système à l'adresse `0x169`, en utilisant l'interface [here](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contracts/PrngSystemContract/IPrngSystemContract.sol#L4) comme dans [cet exemple](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/PrngSystemContract/PrngSystemContract.sol). Les demandes peuvent comprendre des contrats avec la menthe de la PFN, des loteries, etc.

📝 Le texte HIP-351 ne reflète pas encore le changement de nom de `RandomGenerate` à `prng`, ou la spécification du contrat système. Il explique en détail comment `prng` dérive son entropie à partir du hachage des enregistrements de transactions générés par le réseau.

Cette version inclut également quelques corrections de bugs et de petites améliorations, notamment :

1. Supporte [`ContractCallLocal`](https://github.com/hashgraph/hedera-services/issues/3632) aux fonctions ERC-20 et ERC-721 `allowance`, `getApproved`, et `isApprovedForAll`.
2. Permet de mettre le staking à des comptes contractuels.

![](../../.gitbook/assets/0.28.0\_results.001.jpeg)

## [v0.27](https://github.com/hashgraph/hedera-services/releases)

### v0.27.7

{% hint style="success" %}
**MISE À JOUR MAINNET : 9 Aout 2022**
{% endhint %}

Tout livre qui passera à des milliards d'entités doit avoir un moyen efficace de supprimer les entités périmées. Dans le réseau Hedera, cela signifie conserver une liste des NFTs appartenant à un compte, afin que, lorsqu'un compte expire, nous puissions retourner ses FNL à leurs comptes de trésorerie respectifs.

Dans certaines conditions de la version 0.27.5, un bogue dans la logique qui maintient ces listes pourrait faire échouer les transferts NFT, sans frais de remboursement.

Nous apprécions la collaboration de la communauté Hedera sur cette question. Nous invitons tous les utilisateurs qui ont été affectés par ce bogue à contacter le support à support@hedera.com.

### v0.27.0

{% hint style="success" %}
**MISE À JOUR MAINNET : 21 JUILLET 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 1 JUILLE 2022**
{% endhint %}

La version 0.27 de Hedera Services initie la première phase de [HIP-406 (Staking)](https://hips.hedera.com/hip/hip-406). Nous apprécions profondément les commentaires de la communauté sur cette fonctionnalité critique!

Au fur et à mesure que les portefeuilles et les échanges déploient le support client, les utilisateurs auront maintenant le choix de mettre leur hbar sur un nœud. À mesure que les nœuds accumulent des enjeux, tant de la part des individus que des organismes, ils deviendront admissibles à payer des récompenses à leurs stakers. À ce stade, une fois que le solde du compte `0.800` a franchi un seuil à fixer par le comité de pièces du conseil, les récompenses seront activées de façon permanente.

Cela permettra de préparer le terrain pour la deuxième phase de mise en jeu, dans laquelle la contribution d’un nœud au consensus devient une fonction directe de son enjeu, et les nœuds de la communauté avec un enjeu suffisant peuvent commencer à participer au consensus. Veuillez noter que la nature décentralisée de ce processus rend difficile de prédire exactement quand chaque étape et phase sera atteinte. Les conséquences immédiatement visibles de la version 0.27 seront simples,

1. Les nœuds de consensus gèrent les transactions `CryptoCreate` et `CryptoUpdate` avec les élections de stating--même si tous les portefeuilles et les échanges ne sont pas mis à jour pour faire de ces élections pour le moment.

Les lecteurs observateurs se rappelleront qu'une [version alpha](https://github.com/hashgraph/hedera-services/releases/tag/v0.27.0-alpha.5) des Services 0.27 _also_ a activé [HIP-423 (Long Term Scheduled Transactions)](https://hips.hedera.com/hip/hip-423). Il s'agit d'une caractéristique complexe qui a des implications profondes, et nous avons décidé de différer une nouvelle version avant d'aller en production.

![](<../../.gitbook/assets/0.27.4\_results copy.001.jpeg>)

## [v0.26](https://github.com/hashgraph/hedera-services/releases)

{% hint style="success" %}
**MISE À JOUR MAINNET : 9 JUIN 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 25 MAI 2022**
{% endhint %}

Dans cette version, nous sommes heureux de déployer le support de [HIP-410 (Wrapping Ethereum Transaction Bytes in a Hedera Transaction)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-410.md). et [HIP-415 (Introduction Of Blocks)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-415.md).

HIP-410 ajoute un `EthereumTransaction` HAPI par lequel un compte qui était [auto-created](https://hips.hedera. om/hip/hip-32) avec une clé [ECDSA(secp256k1) ](https://hips.hedera.com/hip/hip-222) peut soumettre des transactions Ethereum à Hedera en signant avec sa clé ECDSA. (Les restrictions Ethereum standard sur la `nonce` de l'expéditeur s'appliquent.) Veuillez consulter HIP-410 pour plus de détails, y compris un résumé de certains cas d'utilisation très convaincants que le `EthereumTransaction` active--par exemple, "Je veux utiliser MetaMask pour créer une transaction pour transférer HBAR vers un autre compte".

HIP-415 anticipe également ces cas d'utilisation en normalisant le concept d'un "bloc" Hedera ; c'est important pour une implémentation complète de l'API [Ethereum JSON-RPC](https://eth. iki/json-rpc/API). La définition est simple : un _bloc_ est toutes les transactions dans un fichier de flux d'enregistrements. Le hachage _block _ est le préfixe de 32 octets de la transaction exécutant le hachage à la fin du fichier. Et le _numéro de bloc_ est l'index du fichier d'enregistrement dans l'historique complet du flux, où le premier fichier avait l'index `0`.

Hedera Services 0.26 implémente [HIP-376](https://hips.hedera.com/hip/hip-376), permettant aux développeurs de contrats intelligents d'utiliser le [EIP-20]familier (https://eips. thereum.org/EIPS/eip-20) et [EIP-721](https://eips.ethereum.org/EIPS/eip-721) "agrément de l'opérateur" avec des jetons HTS fongibles et non fongibles.

Les opérateurs agréés peuvent gérer les jetons d'un propriétaire en leur nom ; cela est nécessaire pour de nombreux cas d'utilisation des envois avec des courtiers/portefeuilles/ventes aux enchères.

Toutes les autorisations accordées dans un contrat via `approve()` ou `setApprovalForAll()` ont un équivalent HAPI `cryptoApproveAllowance` ou `cryptoDeleteAllowance` expression---et cette expression est externalisée en tant que HAPI `TransactionBody` dans le flux d'enregistrement. Autrement dit, les contrats système HIP-376 exposent un sous-ensemble des opérations HAPI natives, uniquement au sein de l'EVM.

![](<../../.gitbook/assets/image (2).png>)

## [v0.25](https://github.com/hashgraph/hedera-services/releases/tag/v0.25.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 19 MAI 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 26 APRIL 2022**
{% endhint %}

{% hint style="success" %}
**Mises à jour TESTNET : APRIL 21, 202**
{% endhint %}

Les services Hedera 0. La version 5 apporte de bonnes nouvelles aux utilisateurs HTS qui gèrent un grand nombre de types de jetons, car elle fournit [HIP-367 (Associations de jetons illimitées par compte)](https://hips. edera.com/hip/hip-367). En particulier, un seul compte peut désormais servir de trésorerie pour n'importe quel type de jeton. (Veuillez noter que les requêtes HAPI `CryptoService` ne retournent toujours des informations que pour les 1000 derniers jetons associés d'un compte ; les nœuds miroir restent la meilleure source pour toute l'histoire.)

Nous sommes également très heureux d'annoncer le support de [HIP-358 (Allow `TokenCreate` via Hedera Token Service Precompiled Contract)](https://hips.hedera.com/hip/hip-358). Ce HIP surcharge l'intégration du contrat, ce qui permet à un contrat intelligent de créer un nouveau jeton HTS---fungible ou non fongible, avec ou sans frais personnalisés. (Un développeur intéressé de Solidity pourrait consulter les exemples dans [ce contrat](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/solidity/FeeHelper.sol).)

Dans un préfixe de [support des précompilations HTS à venir](https://hips.hedera.com/hip/hip-376), cette version activera également [HIP-336 (API Approbation et Allowance pour Tokens)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-336.md). Les propriétaires de jetons peuvent maintenant approuver d'autres comptes pour gérer leurs jetons HTS ou NFTs, en analogie directe avec les mécanismes `approve()` et `transferFrom()` dans les jetons de style ERC-20 et ERC-721.

### Améliorations

- Implémentation HIP-336 [#2814](https://github.com/hashgraph/hedera-services/issues/2814)
- Implémentation HIP-358 [#3015](https://github.com/hashgraph/hedera-services/issues/3015)
- Implémentation HIP-367 [#2917](https://github.com/hashgraph/hedera-services/issues/2917)

### Corrections

- Les fonctions ERC `view` sont maintenant utilisables dans `ContractCallLocalQuery` [#3061](https://github.com/hashgraph/hedera-services/issues/3061)

![](<../../.gitbook/assets/image (11).png>)

## [v0.24](https://github.com/hashgraph/hedera-services/releases/tag/v0.24.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 15 AVRIL 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : APRIL 7, 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 31 MARS 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 24 MARS 2022**
{% endhint %}

Dans le 0. 4 versions de Hedera Services, nous sommes heureux de donner aux développeurs de contrats intelligents un nouveau niveau d'interopérabilité avec les jetons Hedera Token Service (HTS) natifs via [HIP-218 (interactions de contrats avec les comptes de jetons Hedera)](https://hips. edera.com/hip/hip-218). L'EVM d'Hedera expose désormais tous les jetons fongibles HTS comme jeton ERC-20 à l'adresse du jeton `0. .X` ID de l'entité; et analogiquement, chaque jeton HTS non fongible apparaît comme un jeton ERC-721. Cela signifie qu'un contrat intelligent peut rechercher son solde d'un jeton HTS fongible; ou modifier son comportement en fonction du propriétaire d'un HTS NFT. Veuillez consulter le HIP lié pour plus de détails.

Cette mise à niveau crée également deux nouveaux comptes système 0.0.800 et 0.0.801 qui contiendront des fonds de récompense.

Un changement à l'API Hedera (HAPI) est que nous avons maintenant assez de preuves pour conclure les requêtes expérimentales `getAccountNftInfos` et `getTokenNftInfos` n'ont pas de rapport coût/bénéfice favorable, et ces requêtes sont maintenant [désactivées en permanence](https://hashgraph. ithub.io/hedera-protobufs/#proto.TokenService).

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (4).jpeg>)

## [v0.23](https://github.com/hashgraph/hedera-services/releases/tag/v0.23.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : 10 MARS 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 17 FÉVRIER 2022**
{% endhint %}

Hedera Services 0.23 renforce notre service de contrats intelligents via l'implémentation de [HIP-329 (Support `CREATE2` opcode)](https://hips.hedera.com/hip/hip-329). Les développeurs de contrats intelligents sont maintenant libres d'utiliser l'opcode EVM `CREATE2`. Un cas d'utilisation typique est un échange distribué qui veut que ses contrats de paire aient des adresses déterministes basées sur les jetons dans la paire.

Veuillez noter que deux problèmes ont été résolus dans cette version. [First](https://github.com/hashgraph/hedera-services/issues/2841), dans la version 0.22, les nœuds ont retourné le `bytes ledger_id` stipulé par [HIP-33](https://hips.hedera.com/hip/hip-33) comme encodage UTF-8 d'une chaîne hexadécimale. Les octets retournés sont maintenant la représentation volumineuse de l'identifiant numérique du livre. [Second](https://github. om/hashgraph/hedera-services/issues/2857), avant cette version, l'enregistrement d'un `dissociateToken` d'un jeton supprimé ne listait pas le solde du compte dissocié si le trésor du jeton était manquant. Ce problème est maintenant résolu.

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (2).jpeg>)

## [v0.22](https://github.com/hashgraph/hedera-services/releases/tag/v0.22.1)

{% hint style="success" %}
**MISE À JOUR MAINNET : 3 FÉVRIER 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 20 JANUARY 2022**
{% endhint %}

La version 0.22 est un changement de paradigme pour Hedera Services, car nous livrons la prochaine étape majeure de nos Smart Contracts 2. feuille de route sur la force du protéan [HIP-25](https://hips.hedera. om/hip/hip-25), une base technique pour adapter l'état du monde de notre livre à des milliards d'entités _sans_ sacrifiant le haut TPS activé par l'algorithme de consensus du hashgraphe.

Les points forts de cette version comprennent :

- La capacité EVM du réseau est passée à 15M `gaz`-par seconde. (Veuillez consulter [HIP-185](https://hips.hedera.com/hip/hip-185) pour plus de détails.)
- Limite de gaz par `ContractCreate` ou `ContractCall` élevée à 4M.
- La capacité de stockage par contrat est passée à 10 Mo.
- Intégration de la solidité avec les jetons HTS natifs. (Veuillez consulter [HIP-206](https://hips.hedera.com/hip/hip-206) pour plus de détails.)

Nous attendons plus de progrès dans ces directions par rapport aux prochaines versions. Notez que l'utilisation de gaz des intégrations HTS est en constante évolution ; suivez [ce problème](https://github. om/hashgraph/hedera-services/issues/2786) pour suivre les frais de gaz finalisés menant à la sortie mainnet.

Il y a deux autres HIP inclus dans cette version qui ne sont pas liés au service de contrats intelligents. Tout d'abord, [HIP-33](https://hips.hedera.com/hip/hip-33) améliore les requêtes comme `CryptoGetInfo` avec un identifiant _ledger_ qui marque le réseau Hedera qui a répondu à la requête. Deuxièmement, [HIP-31](https://hips.hedera.com/hip/hip-31) permet à un client d'inclure les décimales attendues pour un jeton dans un `CryptoTransfer`. Cela signifie qu'un portefeuille matériel peut garantir que ses transactions de jetons auront la précision vue par l'utilisateur dans l'affichage de l'appareil.

Alors que nous gagnons en dynamisme dans notre feuille de route sur les contrats intelligents, nous nous engageons également à améliorer l'expérience des développeurs, et bienvenue des problèmes et des idées dans notre [dépôt GitHub](https://github. om/hashgraph/hedera-services) et [Discord](https://hedera.com/discord)!

![](<../../.gitbook/assets/Performance Measurement Results\_Extract.001 (1).jpeg>)

## [v0.21.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.21.0-rc.1)

{% hint style="success" %}
**MISE À JOUR MAINNET : 13 JANUARY 2022**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 21 DÉCEMBRE 2021**
{% endhint %}

Dans Hedera Services 0.21, nous sommes heureux d'annoncer la prise en charge des clés [ECDSA(secp256k1) ](https://hips.hedera.com/hip/hip-222) et [auto-account creation](https://hips.hedera.com/hip/hip-32).

Le réseau Ethereum utilise fortement la cryptographie ECDSA avec la courbe secp256k1, et en soutenant ces clés, nous facilitons l'expérience de développement de la migration d'un dApp vers Hedera. Partout où une clé Ed25519 peut être utilisée dans l'API Hedera, il est maintenant possible de remplacer une clé ECDSA(secp256k1).

La création de compte automatique permet à un nouvel utilisateur de recevoir <unk> via un `CryptoTransfer` _sans_ ayant déjà créé un id `0.0.X` sur le réseau. Le nouvel utilisateur n'a besoin que de fournir sa clé publique, et quand un compte sponsor envoie <unk> "à" sa clé via un nouveau [`AccountID. champ lias`](https://hashgraph.github.io/hedera-protobufs/#proto.AccountID), le réseau crée automatiquement un compte avec leur clé. Des transferts supplémentaires vers et depuis un compte auto-créé peuvent également utiliser son alias au lieu de l'identifiant du compte.

Un alias peut également être utilisé pour obtenir le solde du compte et les informations de compte du compte. (Notez qu'il y a un [problème connu](https://github.com/hashgraph/hedera-services/issues/2653) qui fait que la réponse à la requête `getAccountInfo` fait écho à l'alias du compte au lieu de son `0. .<num>` id ; cela sera corrigé dans la prochaine version. Veuillez utiliser la requête gratuite `getAccountBalance` pour vérifier l'id `0.0.<num>` qui correspond à un alias.) Vous pourrez utiliser l'alias dans toutes les autres transactions et requêtes dans une prochaine version.

Pendant ce temps, notre équipe continue à faire preuve de diligence raisonnable pour les contrats intelligents 2.0... 🚀

![](<../../.gitbook/assets/Performance Measurements.jpeg>)

## [v0.20.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.20.0)

{% hint style="success" %}
**MISE À JOUR MAINNET : DÉCEMBRE 2,2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 18 NOVEMBRE 2021**
{% endhint %}

Hedera Services 0. 0 est avant tout une version d'échafaudage, car notre équipe travaille d'arrache-pied pour fournir le Service de Contrat Intelligent actualisé avec une nouvelle dimension et une nouvelle performance massifs; ainsi que l'intégration de contrats intelligents avec des jetons natifs créés à l'aide du service de jetons Hedera. La portée de ce rafraîchissement est importante, et nous pensons qu'il vaudra la peine d'attendre.

Les principaux livrables de cette version sont une automatisation améliorée pour les opérateurs de nœuds à utiliser dans les mises à jour logicielles ; et quelques corrections de bugs mineurs, y compris pour [<mark style="color:purple;">#2432</mark>](https://github. om/hashgraph/hedera-services/issues/2432).

Veuillez également noter les dépréciations suivantes dans les protobufs de l'API Hedera :

- Le [<mark style="color:purple;">`ContractUpdateTransactionBody.fileID`</mark> <mark style="color:purple;">champ</mark>](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L82), qui est redondant étant donné l'existence du [<mark style="color:purple;">`ContractGetBytecode`</mark> <mark style="color:purple;">quer</mark>y](https://github.com/hashgraph/hedera-protobufs/blob/main/services/smart\_contract\_service.proto#L63).
- Le [<mark style="color:purple;">`ContractCallLocalQuery.maxResultSize`</mark> <mark style="color:purple;">champ</mark>](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_call\_local.proto#L136), car cette limite est maintenant simplement un effet secondaire de la limite de gaz donnée.

![](../../.gitbook/assets/Performance%20Mesure%20Résultats\_Extract. 01%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20%20 \\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\)%20\\(1\\). peg)

## [v0.19.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.19.4)

{% hint style="success" %}
**MISE À JOUR MAINNET : NOVEMBRE 4,2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET : 28 OCTOBER 2021**
{% endhint %}

## [v0.19.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.19.1)

{% hint style="success" %}
**MISE À JOUR TESTNET : 21 OCTOBRE 2021**
{% endhint %}

In Hedera Services 0.19, we are thrilled to announce migration of the Hedera smart contract service to the Hyperledger Besu EVM, as laid out in [HIP-26](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-26.md). Cela permet de supporter les derniers contrats de Solidity v0.8.9 et d’harmoniser notre calendrier de gaz avec celui de la fourche dure « Londres ». La migration de Besu ouvre également la voie à un changement progressif des performances des contrats intelligents en Hedera.

Deux autres HIPs ciblant le Hedera Token Service sont en ligne dans cette version. Tout d'abord, l' [HIP-23](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23. d) l'ensemble de fonctionnalités est maintenant activé, de sorte que tout compte qui a été configuré avec un `maxAutoAssociations` non zéro`peut recevoir des gouttes d'air (i. ., unités ou NFTs d'un type de jeton sans association explicite). Deuxièmement, nous avons également implémenté [HIP-24](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-24.md), qui fournit une mesure de sécurité pour les types de jetons créés avec une`pauseKey`. Si un `TokenPause`est soumis avec la signature de cette clé, toutes les opérations sur le jeton seront suspendues jusqu'à ce qu'un`TokenUnpause\` subséquent.

## [v0.18.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.18.1)

{% hint style="success" %}
**MISE À JOUR MAINNET : 7 OCTOBER 2021**
{% endhint %}

Dans Hedera Services 0.18.1, nous avons un nouveau profil d'évolutivité pour les NFL dans le Hedera Token Service (HTS). Jusqu’à cinquante millions de NFTs (50M), chacun avec 100 octets de métadonnées, peut maintenant être frappé. Bien sûr, nos opérations `CryptoTransfer` et `ConsensusSubmitMessage` sont toujours prises en charge à 10k TPS même avec cette échelle.

Dans cette version, nous avons également activé la reconnexion automatique. Cette fonctionnalité entre en jeu lorsqu'une partition réseau fait qu'un noeud se trouve à la traîne dans le protocole consensuel. Avec la reconnexion activée, le noeud peut utiliser une forme spéciale de rappels pour "rattraper" et reprendre la participation au réseau sans aucune intervention humaine. Cela fonctionne même lorsque le nœud a manqué plusieurs millions de transactions, et l'état du monde est très différent de celui de la dernière fois qu'il a été actif.

Nous sommes heureux d'annoncer également que les comptes peuvent être personnalisés pour profiter de la prochaine fonctionnalité [HIP-23 (Opt-in Token Associations)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md) à venir. C'est-à-dire, le propriétaire d'un compte peut maintenant "pre-payer" pour les associations de jetons via un [`CryptoCreate`](https://hashgraph.github.io/hedera-protobufs/#proto.CryptoCreateTransactionBody) ou [`CryptoUpdate`](https://hashgraph. transaction ithub.io/hedera-protobufs/#proto.CryptoUpdateTransactionBody), _sans_ sachant à l'avance quels types de jetons spécifiques ils utiliseront.

Une fois que HIP-23 est entièrement activé dans la version 0. 9, lorsque leur compte reçoit des unités ou des NFT d'un nouveau type de jeton via un `CryptoTransfer`, le réseau créera automatiquement l'association---aucune transaction explicite de `TokenAssociate` requise. Cela prend en charge plusieurs cas d'utilisation intéressants ; veuillez consulter le lien HIP-23 pour plus de détails.

Il y a trois autres points d'intérêt dans cette version.

Tout d'abord, nous avons supprimé les limitations HIP-18 notées dans la version précédente. La transaction `tokenFeeScheduleUpdate` a été réactivée, et plusieurs frais de redevances peuvent maintenant être facturés pour un type de jeton non fongible.

Deuxièmement, les carnets d'adresses dans les fichiers système `0.0.101` et `0.0.102` vont maintenant remplir leurs champs `ServiceEndpoint`. (Cependant, les champs obsolètes `ipAddress`, `portno` et `memo` ne seront plus remplis après la prochaine version.)

Troisièmement, veuillez noter que les requêtes `TokenService` `getTokenNftInfos` et `getAccountNftInfos` sont maintenant **dépréciées** et seront supprimées dans une prochaine version. Les meilleures réponses à de telles requêtes exigent un contexte historique que seuls les nœuds miroir ont ; donc ces requêtes et les requêtes connexes se déplaceront vers les API REST miroir.

Les développeurs apprécieront probablement deux autres éléments de la version 0.18.1. Tout d'abord, nous avons migré vers [Dagger2](https://dagger.dev/) pour l'injection de dépendance. Deuxièmement, il y a une nouvelle requête `getExecutionTime` dans le [`NetworkService`](https://hashgraph.github.io/hedera-protobufs/#proto.NetworkService) qui supporte les tests de performances granulaires dans les environnements de développement.

![](<../../.gitbook/assets/image (3).png>)

## v0.18.0

{% hint style="success" %}
**MISE À JOUR TESTNET : 23 SEPTEMBRE 2021**
{% endhint %}

Dans Hedera Services 0.18.0, nous sommes heureux d'annoncer le support de [HIP-23 (Opt-in Token Associations)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md). Cette fonctionnalité permet au propriétaire d'un compte Hedera de "pre-payer" pour les associations de jetons via un [`CryptoCreate`](https://hashgraph.github.io/hedera-protobufs/#proto. ryptoCreateTransactionBody) ou [`CryptoUpdate`](https://hashgraph.github.io/hedera-protobufs/#proto.CryptoUpdateTransactionBody) transaction, _sans_ sachant à l'avance quels types de jetons spécifiques ils utiliseront.

Ensuite, lorsque leur compte reçoit des unités ou des NFT d'un nouveau type de jeton via un `CryptoTransfer`, le réseau crée automatiquement l'association---aucune transaction explicite de `TokenAssociate` requise. Cela prend en charge plusieurs cas d'utilisation intéressants ; veuillez consulter le lien HIP-23 pour plus de détails.

Il y a trois autres points d'intérêt dans cette version.

Tout d'abord, nous avons supprimé les limitations HIP-18 notées dans la version précédente. La transaction `tokenFeeScheduleUpdate` a été réactivée, et plusieurs frais de redevances peuvent maintenant être facturés pour un type de jeton non fongible.

Deuxièmement, les carnets d'adresses dans les fichiers système `0.0.101` et `0.0.102` vont maintenant remplir leurs champs `ServiceEndpoint`. (Cependant, les champs obsolètes `ipAddress`, `portno`, et `memo` ne seront plus remplis après la prochaine version.)

Troisièmement, veuillez noter que les requêtes `TokenService` `getTokenNftInfos` et `getAccountNftInfos` sont maintenant **dépréciées** et seront supprimées dans une prochaine version. Les meilleures réponses à de telles requêtes exigent un contexte historique que seuls les nœuds miroir ont ; donc ces requêtes et les requêtes connexes se déplaceront vers les API REST miroir.

Les développeurs apprécieront probablement deux autres éléments de la version 0.18.0. Tout d'abord, nous avons migré vers [Dagger2](https://dagger.dev/) pour l'injection de dépendance. Deuxièmement, il y a une nouvelle requête `getExecutionTime` dans le [`NetworkService`](https://hashgraph.github.io/hedera-protobufs/#proto.NetworkService) qui supporte les tests de performances granulaires dans les environnements de développement.

**Résultats de la mesure des performances :**

![](<../../.gitbook/assets/Performance Measurement Results.jpeg>)

## [v0.17.4](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.3)

{% hint style="success" %}
**MISE À JOUR MAINNET : SEPTEMBRE 2, 2021**
{% endhint %}

{% hint style="success" %}
**Mises à jour TESTNET : 30 août, 2021**
{% endhint %}

Dans Hedera Services 0.17.2, nous sommes heureux d'annoncer le support de [HIP-17 (Non-fungible Tokens)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md), avec une extension complémentaire à [HIP-18 (Custom Hedera Token Service Fees)](https://github. om/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-18.md) qui permet à un créateur de NFT de fixer une redevance à facturer lorsque la valeur fongible est échangée contre une de leurs créations.

Les types de jetons uniques et les NFT montés sont plus naturels pour de nombreux cas d'utilisation que les types de jetons fongibles. Le service de jetons Hedera prend maintenant en charge les deux nativement, afin qu'une seule `CryptoTransfer` puisse effectuer des échanges atomiques avec n'importe quelle combinaison arbitraire de transferts fongibles, non fongibles et <unk> . (Veuillez noter que les requêtes "paged" `getAccountNftInfos` et `getTokenNftInfos` resteront désactivées jusqu'à la version 0.18.0, car plusieurs améliorations de grandes performances sont en attente.)

Dans cette version, nous avons rendu possible la dénomination d'un tarif fixe dans les unités du jeton auquel il est attaché (en supposant que le type de ce jeton soit `FUNGIBLE_COMMON`). Les frais fractionnaires personnalisés peuvent maintenant aussi être définis comme un « réseau de transfert ». Dans ce cas, le ou les destinataires de la liste de transfert reçoivent les montants indiqués, et les frais de cotisation sont imputés à l'expéditeur.

Il y a quelques derniers points d'intérêt plus spécialisé. Premièrement, les utilisateurs de la fonctionnalité de transaction planifiée peuvent maintenant également planifier les transactions `TokenBurn` et `TokenMint`. Deuxièmement, les administrateurs de réseau qui émettent une `CryptoUpdate` pour changer la clé du compte de trésorerie doivent maintenant signer avec la nouvelle clé de trésorerie. Troisièmement, les suites de chiffrement TLS supportées ont été mises à jour vers la liste suivante :

1. `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384` (TLS v1.2)
2. `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384` (TLS v1.2)
3. `TLS_AES_256_GCM_SHA384` (TLS v1.3)

⚠️ Il y a deux limitations temporaires à HIP-18 dans cette version. Tout d'abord, la transaction `tokenFeeScheduleUpdate` n'est pas disponible actuellement. Deuxièmement, une seule redevance sera facturée pour un type de jeton non fongible. Les deux limitations seront supprimées dans la version 0.18.0.

#### Résultats de la mesure des performances :

![](../../.gitbook/assets/0.17.png)

## [v0.17.3](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.3-rc.1)

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 24 Aout 2021**
{% endhint %}

Veuillez consulter les notes de version 0.17.4.

## [v0.17.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.17.2)

{% hint style="success" %}
**MISE À JOUR DU TESTNET : 19 AUTS, 2021**
{% endhint %}

Veuillez consulter les notes de version 0.17.4.

## [v0.16.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.16.1)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉE : 5 AUTOMATIQUE, 2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET TERMINÉ: 22 JUILLET 2021**
{% endhint %}

Dans Hedera Services 0.16.0, nous sommes heureux d'annoncer le support de [HIP-18 (frais de service de jeton de Hedera personnalisé)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-18.md).

Les jetons Hedera peuvent maintenant être créés avec un planning de 10 frais personnalisés, qui sont soit _fixes_ en unités de <unk> ou un autre jeton; ou _fractional_ et calculé dans les unités du jeton propriétaire. Le registre facture automatiquement des frais de douane sur les comptes lorsqu'ils envoient des unités d'un jeton fongible (ou propriétaire d'une NFT, voir ci-dessous) via un `CryptoTransfer`.

Lorsqu'un frais personnalisé ne peut pas être facturé, le `CryptoTransfer` échoue atomiquement, ne changeant aucun solde autre que les frais de réseau Hedera.

Les cinq études de cas de [ce document](https://github.com/hashgraph/hedera-services/blob/master/docs/fees/custom-fees-characterization.md) montrent les bases de la façon dont les frais de douane sont facturés et comment ils apparaissent dans les dossiers. Notez qu'au plus deux "niveaux" des frais HTS personnalisés sont autorisés, et les frais personnalisés ne peuvent pas nécessiter de changer plus de 20 soldes de compte.

⚠️ Il y a une variation des frais de douane qui nécessite un contournement dans cette version. Plus précisément, si des frais fixes doivent être perçus _dans les unités du jeton "parent" auquel il appartient_, puis dans la version 0.16. ceci doit être accompli en utilisant un `FractionalFee` comme décrit dans [ce problème](https://github.com/hashgraph/hedera-services/issues/1925). Dans la version 0.17.0, la configuration plus naturelle `FixedFee` sera disponible.

Dans cette version, nous avons également activé le support de [HIP-17 (Non-fungible Tokens)](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md). Les types de jetons uniques et les NFT montés sont plus naturels pour de nombreux cas d'utilisation que les types de jetons fongibles. Le service de jetons Hedera prendra bientôt en charge les deux nativement, afin qu'une seule `CryptoTransfer` puisse effectuer des échanges atomiques avec n'importe quelle combinaison arbitraire de transferts fongibles, non fongibles et <unk> .

Nous sommes très reconnaissants à la communauté d'utilisateurs de Hedera pour ces nouveaux ensembles de fonctionnalités intéressants et puissants.

#### Résultats de la mesure des performances :

![](../../.gitbook/assets/0.16.1.png)

## [v0.15.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.15.1)

{% hint style="success" %}
\*\*MISE À JOUR MAINNET TERMINÉE : 1 JUILLE 2021
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET TERMINÉE : JUNE 17, 2021**
{% endhint %}

Dans Hedera Services 0.15.1, nous avons amélioré les performances et nous avons intégré le dernier SDK de Platform pour permettre le support complet de la reconnexion du réseau.

Ces améliorations de performance nous permettent d'améliorer l'état du monde de Hedera avec les enregistrements de toutes les transactions traitées dans les trois minutes de temps de consensus. même lorsque vous gérez 10 000 transactions par seconde. La requête HAPI `GetAccountRecords` retourne maintenant, depuis l'état, tous les enregistrements pour lesquels le compte demandé était le compte du payeur.

Nous avons également finalisé la conception du support des jetons non fongibles (NFT) à ajouter au service de jetons Hedera (HTS) dans la version 0.16.0. Les protobufs pour les nouvelles opérations HAPI sont disponibles dans la balise 0.15.0 du dépôt GitHub [ hedera-protobufs](https://github.com/hashgraph/hedera-protobufs).
\
Pour simplifier le calcul des frais, il y a maintenant une durée de vie maximale d'une entité d'un siècle pour toute entité dont la durée de vie n'est pas \_déjà\_ contrainte par la période maximale de renouvellement automatique. Une transaction HAPI qui tente de définir une expiration supérieure à un siècle à partir de l'heure actuelle du consensus sera résolue à `INVALID_EXPIRATION_TIME`.

## [v0.14.0](https://github.com/hashgraph/hedera-services/releases/tag/0.14.0)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: 3 JUIN 2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET TERMINÉ: 20 MAI 2021**
{% endhint %}

Dans Hedera Services 0.14.0, nous avons implémenté le renouvellement automatique du compte selon les spécifications de [HIP-16](https://github.com/hashgraph/hedera-improvement-proposal). Cette fonctionnalité ne sera pas activée avant une date ultérieure, après avoir assuré la sensibilisation universelle à son impact dans la communauté des utilisateurs.

Cette version inclut des travaux d'infrastructure remarquables pour permettre l'utilisation de la fonction de reconnexion de la plate-forme. La reconnexion permet à un noeud qui a pris du retard dans les commérages consensuels de rattraper dynamiquement la reprise.

Une amélioration mineure de l'API Hedera est que la requête GetVersionInfo inclut maintenant la version de pré-publication optionnelle et les champs de métadonnées à partir de la spécification de Versioning sémantique (le cas échéant).

Afin de simplifier la vie des administrateurs système qui mettent à jour la clé d'un compte système, nous renonçons maintenant à l'obligation de signature pour la nouvelle clé du compte.

## [v0.13.2](https://github.com/hashgraph/hedera-services/releases/tag/v0.13.2)

{% hint style="success" %}
**MISE À JOUR MAINNET TERMINÉ: 6 MAI 2021**
{% endhint %}

{% hint style="success" %}
\*\*MISE À JOUR TESTNET TERMINÉ: 29 APRIL 2021 \[v0.13.2]
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET TERMINÉE : 22 AVRIL \[v0.13.0]**
{% endhint %}

Dans Hedera Services v0.13.0, nous avons [redesigned](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md) opérations de planification. Le nouveau design donne aux nœuds collaborateurs un flux de travail bien défini s'ils planifient des transactions identiques, _even if_ ils utilisent différentes bibliothèques clients gRPC (par exemple, Go et JavaScript). Le nouveau design réduit également le nombre de signatures requises pour soumettre une transaction valide `ScheduleSign` dans de nombreux cas d'utilisation courants. Les utilisateurs seront en mesure de planifier les transactions `CryptoTransfer` et `ConsensusSubmitMessage` dans cette version. D'autres types de transactions seront introduits dans les versions futures.

{% hint style="warning" %}
**Remarque :** La fonctionnalité des transactions planifiées ne sera pas activée dans cette version ; il devrait être activé sur testnet dans une version ultérieure. 3.2 mise à jour le 29 avril. Cette fonctionnalité est activée sur previewnet.
{% endhint %}

Cette version déprécie trois champs dans la [protobuf](https://hashgraph.github.io/hedera-protobufs/#proto.NodeAddress) pour les fichiers système `0.0.101` et `0.0.102`. Les trois champs obsolètes sont `ipAddress`, `portno` et `memo`. Lorsque nous comptons sur ces champs, nous ne pouvons pas représenter de manière concise un noeud avec plusieurs adresses IP. Par exemple, prenez le noeud principal 0 (compte `0.0.3`), qui, à partir de cette écriture, a des IP proxy `13.82.40.153`, `34.239.82.6`, et `35.237.200.180`. Le fichier `0.0.101` du réseau principal doit inclure une entrée `NodeAddress` pour chaque proxy, ce qui signifie dupliquer les champs comme `nodeCertHash`.

Le nouveau protobuf évite cette duplication, nous permettant de représenter le noeud 0 dans un protobuf équivalent de

```
{
    "nodeId" : 0,
    "certHash" : "337390d8fea144afc12e81254a28dac6ea82893836ac072effd85e0a7748580ef28096648c5a7f8dbb4ce81476815137",
    "nodeAccount" : "0. .3",
    "serviceEndpoints" : [ {
      "ipAddressV4" : "13.82. 0.153",
      "port" : 50211
    }, {
      "ipAddressV4" : "34. 39.82. ",
      "port" : 50211
    }, {
      "ipAddressV4" : "35. 37.200.180",
      "port" : 50211
    } ]
}
```

Cependant, Services continuera à remplir les champs obsolètes dans les entrées dupliquées pendant six mois, pour donner à tous les consommateurs des fichiers `0. .101` et `0.0.102` pour se préparer à une utilisation exclusive du nouveau format. Après six mois, nous éliminerons la duplication et les champs `ipAddress`, `portno`, et `memo` resteront vides. (Les champs ne seront jamais supprimés pour s'assurer qu'il reste possible d'analyser les versions antérieures de ces fichiers système.)

Dans un point mineur, Services rejette maintenant tout champ protobuf `string` dont l'encodage UTF-8 inclut le caractère zéro-octet, c'est-à-dire le point de code Unicode 0, `NUL`. Les bases de données (par exemple, PostgreSQL) réservent généralement ce caractère comme un délimiteur dans leurs formats internes, pour que cela puisse se produire dans les champs d'entités peut rendre la vie plus difficile pour les opérateurs de noeuds miroirs.

Pour simplifier les tâches des administrateurs réseau, nous avons également rationalisé les conditions de signature pour les mises à jour des comptes système, et a introduit un utilitaire basé sur Docker appelé "yahcli" pour les actions d'administration telles que la mise à jour des fichiers système.

## [v0.12.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.12.0-rc.2)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: 12 MARS 2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET TERMINÉ: 26 FÉVRIER 2021**
{% endhint %}

Dans Hedera Services v0.12.0, nous avons terminé l'implémentation MVP du service de transaction planifiée Hedera (HSTS) comme détaillé dans [this](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) document. Ce service découplera _what_ devrait s'exécuter sur le grand livre à partir de _when_ il devrait s'exécuter, offrant une nouvelle flexibilité et une nouvelle programmabilité aux utilisateurs. Notez que les opérations HSTS sont activées sur Previewnet, mais restent désactivées sur Testnet et Mainnet pour le moment.

Nous avons donné aux utilisateurs du Hedera Token Service (HTS) plus de contrôle sur le cycle de vie de leurs associations de jetons. Dans la v0.11.0, les jetons supprimés ont été immédiatement dissociés de tous les comptes. Cette dissociation automatique ne se produit plus. Si le compte `X` est associé au jeton `Y`, alors même si le jeton `Y` est marqué pour suppression, une requête `getAccountInfo` pour `X` continuera à montrer l'association avec `Y` \_until\_it est explicitement supprimée via une transaction `tokenDissociateFromAccount`. Notez que pour plus de commodité, les requêtes qui retournent des soldes de jetons retournent désormais la valeur `decimals` pour le jeton correspondant. Cela permet à un utilisateur d'interpréter par exemple `balance=10050` comme des jetons `100.50` donnés `decimals=2`.

Dans un changement final de l'API Hedera (HAPI), nous avons étendu le champ `memo` présent sur les entités contractuelles et thématiques au compte, fichier, jeton et entités de transaction planifiée. (Notez que ce `mémo` est distinct du `mémo` de courte durée qui peut être donné à n'importe quel `TransactionBody`pour inclusion dans la `TransactionRecord`.) Toutes ces modifications de HAPI sont maintenant plus facilement consultées via les pages GitHub [here](https://hashgraph.github. o/hedera-protobufs/); le nouveau dépôt [`hashgraph/hedera-protobufs`](https://github.com/hashgraph/hedera-protobufs) est maintenant la source officielle des fichiers protobuf définissant HAPI.

En dehors de ces améliorations à HAPI, les « streams » consommables par les opérateurs de nœuds miroir incluent maintenant une version alpha d'un fichier protobuf qui contient les mêmes informations que les « _Balances ». Fichiers sv`. Le type de ce fichier est [`AllAccountBalances\`](https://hashgraph.github.io/hedera-protobufs/#proto.AllAccountBalances).

## [v0.11.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.11.0)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: 4 FÉVRIER 2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET TERMINÉ: 26 JANUARY 2021**
{% endhint %}

Dans Hedera Services v0.11.0, nous avons mis à jour le format de flux d'enregistrements de v2 à v5 et le format de flux d'événements de v3 à v5. Ces changements sont décrits en détail dans les formats de fichier "Record and Event Stream File Formats" [article](https://docs.hedera.com/guides/docs/record-and-event-stream-file-formats).

Nous avons également mis à jour le code de démarrage pour que le nombre de comptes système dans les réseaux de développement et de pré-production corresponde au nombre de comptes système sur le réseau principal, [creating](https://github. om/hashgraph/hedera-services/issues/784) numéros de compte `900-1000` au démarrage s'ils n'existent pas.

## [v0.10.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.10.0)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: JANUARY 7, 2021**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DE TESTNET TERMINÉ: DÉCEMBRE 17, 2020**
{% endhint %}

Dans Hedera Services v0.10. , nous avons amélioré l'utilisation du Hedera Token Service (HTS) avec un champ `newTotalSupply` dans les reçus des transactions `TokenMint` et `TokenBurn`. Sans ce champ, un client doit suivre la totalité du flux d'enregistrement des modifications de l'approvisionnement d'un jeton pour être certain de sa fourniture à l'horodatage du consensus dans la réception. (Notez que les opérations HTS sont maintenant activées sur Previewnet et Testnet, mais restent désactivées sur le réseau principal pour le moment. Veuillez consulter la [documentation SDK](https://docs.hedera.com/452354233115445331/token-service) pour la sémantique HTS.)

Aussi pour le HTS, nous avons ajouté une propriété `fees.tokenTransferUsageMultiplier` qui met à l'échelle l'utilisation de la ressource assignée à un `CryptoTransfer` qui modifie les soldes de jetons. Ce facteur de mise à l'échelle devrait être défini de sorte que le coût d'un `CryptoTransfer` qui change deux balances de jetons soit environ 10x le coût d'un `CryptoTransfer` qui ne change que deux balances de barres.

En dehors de HTS, cette publication supprime une restriction sur quels comptes de payeur peuvent être utilisés pour les transactions `CryptoUpdate` qui ciblent les comptes système. (Autrement dit, les comptes dont les nombres ne sont pas supérieurs à `hedera.numReservedSystemEntities`.) Dans les versions précédentes, seuls trois payeurs ont été acceptés : le compte cible lui-même, le compte administrateur du système ou le compte de trésorerie. Les autres payeurs ont abouti à un statut de `AUTHORIZATION_FAILED`. Cette restriction entière est supprimée, à une exception près, la trésorerie doit payer pour une `CryptoUpdate` ciblant la trésorerie.

Mis à part ces modifications fonctionnelles, nous avons corrigé un changement involontaire dans le nommage du fichier de crypto-balances CSV, et a amélioré l'utilité des clients sous _tests-clients/_ pour tester les scénarios de reconnexion.

## [v0.9.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0-rc.1)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: DÉCEMBRE 3, 2020**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DU TESTNET TERMINÉ: 19 NOVEMBRE 2020**
{% endhint %}

Dans Hedera Services v0.9.0, nous avons terminé l'implémentation alpha du Hedera Token Service (HTS). Notez que toutes les opérations HTS sont activées sur Previewnet, mais restent désactivées sur Testnet et Mainnet. Veuillez consulter la [documentation SDK](https://docs.hedera.com/452354233115445331/token-service) pour la sémantique HTS.

Nous avons apporté plusieurs modifications au protobuf HAPI. Tout d'abord, nous avons supprimé le type de message obsolète `SignatureList`. Deuxièmement, nous avons ajouté un champ `signedTransactionBytes` de premier niveau au message `Transaction` pour assurer des hashs de transaction déterministes dans différentes bibliothèques clientes; les champs `bodyBytes` et `sigMap` de niveau supérieur sont maintenant obsolètes et le champ `body` déjà obsolète est supprimé. Troisièmement, nous avons déprécié tous les champs liés aux enregistrements non payants, y compris les seuils d'envoi et de réception du compte. Cela a été suivi de la suppression effective des registres des non-payeurs dans la v0.8.1.

Pour la même raison, la sémantique des requêtes `CryptoGetRecords` et `ContractGetRecords` ont également changé. Les seuls enregistrements interrogeables sont maintenant ceux accordés au payeur effectif d'une transaction qui a été traitée alors que la propriété réseau `ledger.keepRecordsInState=true`. Ces enregistrements ont une expiration de 180 secondes. Il est important de noter que parce qu'un compte de contrat ne peut jamais être le payeur effectif d'une transaction, toute requête `ContractGetRecords` retournera toujours une liste d'enregistrements vide, et nous avons déprécié la requête.

## [v0.8.1](https://github.com/hashgraph/hedera-services/releases/tag/v0.8.1-rc1)

{% hint style="success" %}
**MAINNET MISE À JOUR TERMINÉ: 22 OCTOBER 2020**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR TESTNET TERMINÉE : 7 OCTOBRE 2020**
{% endhint %}

La version principale inclut les mises à jour de la version 0.8.0.

## [v0.8.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.8.0-rc1)

{% hint style="success" %}
**MISE À JOUR TESTNET TERMINÉE : SEPTEMBRE 17, 2020**
{% endhint %}

Dans Hedera Services v0.8.0, nous avons fait plusieurs corrections et améliorations mineures. Cette balise inclut également des implémentations pré-publiées de plusieurs opérations pour un service de jetons Hedera (HTS).

**REMARQUE :** Les opérations HTS resteront désactivées dans des environnements non de développement pendant un certain temps. Ces opérations sont en cours de développement actif; veuillez consulter `master` pour la sémantique à jour.

### Améliorations

- Champs obsolètes liés aux seuils dans HAPI protobuf [#506](https://github.com/hashgraph/hedera-services/issues/506)
- Mettre à jour la propriété de reçu pour associer chaque état à NodeID - Le reçu n'est supprimé que lorsque la dernière (duplique) transaction expire. L'API `getTxRecord` continuera de retourner TOUS les enregistrements avec l'ID de la transaction.
- Les premières ébauches de `tokenCreate`, `tokenUpdate`, `tokenDelete`, `tokenTransfer`, `tokenFreeze`, `tokenUnfreeze`, `tokenGrantKyc`, `tokenRevokeYc`, `tokenWipe`, et `getTokenInfo` opérations HAPI. [#505](https://github.com/hashgraph/hedera-services/pull/505) et [#522](https://github.com/hashgraph/hedera-services/pull/522)

## [v0.7.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.7.0-alpha1)

{% hint style="success" %}
**MISE À JOUR MAINNET TERMINÉ: 8 SEPTEMBRE 2020**
{% endhint %}

{% hint style="success" %}
**MISE À JOUR DE TESTNET TERMINÉE : 20 AOUT 2020**
{% endhint %}

Dans Hedera Services v0.7.0, nous sommes passés à la version de Swirlds SDK `0.7.3` qui permet aux nœuds zéro enjeu de faire partie d'un réseau sans affecter le consensus. Hedera Services v0.7.0 a migré vers de nouvelles interfaces et méthodes fournies dans cette version du SDK Swirlds. Les hachages en cours d'exécution du HCS sont maintenant calculés, y compris l'identifiant du compte du payeur. La version inclut d'autres corrections et améliorations mineures.

**Améliorations**

- Migration vers Swirlds SDK version `0.7.3` avec les paramètres appropriés et les configurations de journalisation [#347](https://github.com/hashgraph/hedera-services/issues/347), [#427](https://github.com/hashgraph/hedera-services/issues/427)
- Mettre à jour le hash du sujet HCS en cours d'exécution pour inclure l'id du compte payeur [#88](https://github.com/hashgraph/hedera-services/issues/88)
- Ajouter une fonctionnalité de nœud zéro enjeu [#274](https://github.com/hashgraph/hedera-services/issues/274)
- Ajoute de nouvelles statistiques pour la taille moyenne des messages envoyés par HCS qui ont été traités et pour compter le nombre de transactions non créées par seconde [#316](https://github. om/hashgraph/hedera-services/issues/316), [#334](https://github.com/hashgraph/hedera-services/issues/334)
- Changer gRPC CipherSuite pour être conforme à la CNSA [#215](https://github.com/hashgraph/hedera-services/issues/215)
- Rendre recordLogPeriod dynamique avec une valeur par défaut de 2 secondes [#315](https://github.com/hashgraph/hedera-services/issues/315)
- Ajouter un enregistrement avec une expiration de 3 minutes au compte payeur effectif après avoir géré la transaction [#348](https://github.com/hashgraph/hedera-services/issues/348)
- Améliorations pour l'open source [#378](https://github.com/hashgraph/hedera-services/issues/378), [#379](https://github.com/hashgraph/hedera-services/issues/379)

**Changements de documentation**

- Clarifiez l'interprétation des codes de réponse `UNKNOWN` et `PLATFORM_TRANSACTION_NOT_CREATED` [#314](https://github.com/hashgraph/hedera-services/issues/314), [#394](https://github.com/hashgraph/hedera-services/issues/394)

**Corrections de bugs**

- Empêcher les transactions `CryptoCreate` et `CryptoUpdate` de donner à un compte une clé vide [#58](https://github.com/hashgraph/hedera-services/issues/58), [#60](https://github.com/hashgraph/hedera-services/issues/60)
- Corriger le nombre incorrect de transactions de contrats intelligents soumis [#371](https://github.com/hashgraph/hedera-services/issues/371)
- Validez le solde total du registre avant de démarrer Services [#258](https://github.com/hashgraph/hedera-services/issues/258)
- Ajouter un nouveau fichier roulant pour enregistrer toutes les requêtes avec un taux maximum contrôlé [#59](https://github.com/hashgraph/hedera-services/issues/59)
- Autres bugs mineurs [#373](https://github.com/hashgraph/hedera-services/issues/373)

## [v0.6.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.6.0)

{% hint style="success" %}
**MAINNET SUPPLÉMENTAIRE COMPLÉTÉ : 6 AUTÉS 2020**
{% endhint %}

{% hint style="success" %}
**Mise à jour TESTNET TERMINÉE : 16 JUILLET 2020**
{% endhint %}

Dans Hedera Services v0.6.0, nous avons amélioré le service de consensus Hedera en soutenant [HCS Topic Fragmentation](https://github.com/hashgraph/hedera-services/issues/53). Nous avons ajouté, dans le `ConsensusSubmitMessageTransactionBody`, un champ facultatif pour les informations actuelles du chunk. Pour chaque chunk, le compte payeur qui fait partie du `initialTransactionID` doit correspondre au compte Payer de cette transaction. L'entier `initialTransactionID` devrait correspondre au `transactionID` du premier chunk, mais ceci n'est pas vérifié ou imposé par Hedera, sauf lorsque le numéro de chunk est 1.

**Améliorations**

- Ajouter la prise en charge de la fragmentation des sujets HCS

**Changements de documentation**

- Protobuf v0.6.0 avec la mise à jour du doc HAPI pour prendre en charge la fragmentation des sujets HCS

## [**v0.5.8**](https://github.com/hashgraph/hedera-services/releases/tag/oa-release-r5-rc8)

{% hint style="success" %}
**MAINNET SUPPLÉMENTAIRE COMPLÉTÉ : 18 JUIN 2020**

v0.5.8 inclut toutes les mises à jour trouvées dans [v0.5.0](services.md#v-0-5-0)
{% endhint %}

{% hint style="success" %}
**Mise à jour TESTNET TERMINÉE : 8 JUIN 2020**
{% endhint %}

La version 0.5.8 inclut un correctif qui traite de la résilience du réseau peer-to-peer dans la plate-forme de consensus hashgraph.

## **v0.5.0**

{% hint style="success" %}
**Mise à jour TESTNET TERMINÉE : 5 MAI 2020**
{% endhint %}

Dans Hedera Services v0.5.0, nous avons ajouté TLS pour une communication fiable avec des nœuds sur le réseau Hedera. Pour une meilleure sécurité, seules les suites de chiffrement TLS v1.2 et v1.3 avec TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384 et TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 sont autorisées.

Nous avons ajouté de nouvelles métadonnées dans Hedera NodeAddressBook, accessibles dans le fichier système 0.0.101. Les versions du logiciel de nœud et de l'API Hedera de gRPC (HAPI) sont maintenant interrogeables via GetVersionInfo sous le nouveau NetworkService pour les opérations de nœud et de réseau.

Pour le service de consensus Hedera, nous avons mis à jour le calcul du hachage du sujet en cours pour utiliser le hachage SHA-384 du message soumis plutôt que le message lui-même. Cela réduit les exigences de stockage nécessaires pour valider le hachage d'un sujet. L'enregistrement d'une transaction ConsensusSubmitMessage qui utilise le nouveau schéma de hachage aura un nouveau champ topicRunningHashVersion dans son reçu. La valeur du champ sera 2.

Hedera File Service a également plusieurs corrections de note. Tout d'abord, nous avons activé des fichiers immuables. Deuxièmement, nous avons assoupli les exigences de signature pour une transaction FileDelete afin de correspondre à la sémantique d'un service de révocation. Troisièmement, nous avons corrigé un bogue de calcul des frais qui a surchargé certaines transactions FileUpdate.

Pour le service de Contrat Intelligent Hedera, nous avons amélioré la visibilité dans les transactions qui créent des contrats enfants en utilisant le nouveau mot clé en plaçant les ids créés dans l'enregistrement de la transaction; et nous propageons maintenant les métadonnées du contrat parent aux enfants créés.

Enfin, si vous utilisez les propriétés de gaz dans le fichier système 0.0. 21 pour estimer les limites de performance du réseau, vous serez également intéressé par un nouveau format standardisé de ces propriétés. Les listes ci-dessous contiennent ces mises à jour mineures, corrections de bogues et modifications de la documentation.

**Améliorations**

- Ajouter le support pour TLS
- Développer les métadonnées du carnet d'adresses
- Renvoyer tous les identifiants de contrat créés
- Métadonnées du contrat de créateur de propagation
- Introduire la requête GetVersionInfo
- Normalisation de la configuration des gaz
- Forcer file.encoding=utf-8 au démarrage
- Rendre les propriétés de durée inclues pour la lisibilité

**Corrections de bugs**

- Utiliser le hachage SHA-384 dans le hachage en cours d'exécution
- Activer les fichiers immuables
- Relax FileDelete exigences de signature
- Corriger le calcul sbh dans FileUpdate
- Renvoyer les métadonnées pour les fichiers supprimés
- Forcer les exigences de signature du récepteur lors de l'exécution du contrat
- Rejeter CryptoGetInfo invalide
- Rejeter CryptoCreate avec une clé vide
- Renvoyer NOT\_SUPPORTED pour les requêtes de preuve d'état
- Frais de dispense pour 0.0.57 mise à jour de 0.0.111
- Exigences d'annulation de signature pour 0.0.55 mise à jour 0.0.121/0.0.122
- Abandonner tous les frais pour 0,0.2
- Ne pas réduire les comptes du système de gaz

**Changements de documentation**

- Remplacer « claim» par « livehash» comme il convient
- Standardisez et clarifiez la documentation HAPI

## v0.4.1

- La mise à jour du logiciel inclut la possibilité pour Hedera de régler dynamiquement les limites des types de transactions réseau.
- Les limites suivantes seraient mises à jour à : 1000 messages par seconde et 5 créations par seconde.
- Réassignation des nouveaux nœuds membres du Conseil

## v0.4.0

- Dites bonjour au service de consensus Hedera ! Cette version est la première à inclure HCS, permettant un horodatage vérifiable et l'ordre des messages de l'application.
- La tarification du réseau a été mise à jour pour inclure les transactions et les requêtes NCS
- L'accélérateur réseau pour le NCS a été fixé à 1000 tps pour l'envoi de messages, et 100 tps pour chacune des autres opérations NCS.
- Amélioration de la fin des tests.
- Nettoyage et refactorisation du code général.
- ContractCall - La réponse de la TransactionReceipt à ContractCall n'inclut plus l'ID du contrat appelé
- CryptoUpdate - La réponse TransactionReceipt à CryptoUpdate ne comprend plus le compte ID mis à jour
- CryptoTransfer – Les transactions CryptoTransfer ayant pour résultat INSUFFICIENT\_ACCOUNT\_BALANCE erreur ne listent plus les transferts dans la liste de transfert des TransactionRecord qui n'ont pas été appliqués

### Divers

### SDK

- Java SDK a été mis à jour pour supporter le service de consensus Hedera
- JavaScript/Typescript SDK a atteint la version 1.0.0, prenant en charge les quatre services du réseau principal
- JavaScript/Typescript SDK supporte à la fois l'exécution dans le navigateur (avec Envoy Proxy) et dans Node.
- Go SDK supporte maintenant les quatre services du réseau principal.

**Frais**

- La liste des transferts dans les registres des transactions ne montre plus qu'un seul montant net dans ou à l'extérieur pour chaque compte, reflétant à la fois les transferts et les frais payés.
- Correction d'un bug dans le calendrier des frais qui avait entraîné des frais pour les requêtes ContractCallLocal, ContractGetBytecode et getVersion étant souscrites par \~33%
- Vous pouvez obtenir plus d'informations concernant les frais d'enregistrement des transactions [here](https://docs.hedera.com/guides/mainnet/fees/transaction-records).

### Composants d'extension SDK

- Les composants d'extension SDK Hedera (SXC) est un ensemble open source de composants précompilés qui ont pour but de fournir des fonctionnalités supplémentaires en plus du HCS pour faciliter et accélérer le développement d'applications en particulier s'ils ont besoin de communications sécurisées entre les participants.
- Les composants utilisent le SDK Java Hedera pour communiquer avec le service de consensus Hedera.
- En savoir plus sur Hedera SXC [here](https://github.com/hashgraph/hedera-hcs-sxc).
