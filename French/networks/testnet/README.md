---
description: Rejoindre un réseau de test Hedera
---

# Testnets

## Aperçu

Les réseaux de test Hedera permettent aux développeurs d'accéder à un environnement de test gratuit pour les services réseau Hedera. Les réseaux de test simulent l'environnement de développement exact comme vous vous y attendez pour le réseau principal. Cela inclut les frais de transaction, les limites, les services disponibles, etc. Pour créer un compte Testnet Hedera ou Previewnet, vous pouvez visiter le [portail développeur Heder](https://portal.hedera.com/login).

Une fois que votre application a été construite et testée dans cet environnement de test, vous pouvez vous attendre à migrer votre application décentralisée (dApp) vers le réseau principal sans aucune modification.

<table><thead><tr><th width="324">Tester les réseaux</th><th>Libellé</th></tr></thead><tbody><tr><td><strong>Testnet</strong></td><td>Testnet exécute le même code que le réseau principal Hedera, conçu pour fournir un environnement de pré-production aux développeurs sur le point de passer au réseau principal. Vous pouvez trouver des SDKs compatibles <a href="../../sdks-and-apis/sdks/#hedera-supported-sdks">ici</a>.</td></tr><tr><td><strong>Aperçu net</strong></td><td><p>Code en cours de développement par l'équipe Hedera et susceptible d'être utilisé dans une prochaine version conçue pour permettre aux développeurs de découvrir rapidement les fonctionnalités qui descendent du tuyau. Les mises à jour du réseau sont faites fréquemment. Il n'y a aucune garantie qu'un SDK supportera facilement les fonctionnalités à venir.</p><p><strong>Note:</strong> Les mises à jour de ce réseau sont déclenchées par une nouvelle version et sont fréquentes. Ces mises à jour ne seront pas répercutées sur la page d'état.</p></td></tr></tbody></table>

<table><thead><tr><th width="325">Service réseau</th><th>Disponibilité</th></tr></thead><tbody><tr><td><strong>Crypto-monnaie</strong></td><td>Limité</td></tr><tr><td><strong>Service de Consensus</strong></td><td>Limité</td></tr><tr><td><strong>Service de fichiers</strong></td><td>Limité</td></tr><tr><td><strong>Smart Contract Service</strong></td><td>Limité</td></tr><tr><td><strong>Token Service</strong></td><td>Limité</td></tr></tbody></table>

### Tester la réinitialisation du réseau

Le nœud miroir et le réseau de test de nœuds consensuels sont programmés pour être réinitialisés une fois par trimestre. Lorsqu'une réinitialisation du réseau de test se produit, tous les comptes, jetons, contrats, sujets, calendriers et données de fichiers sont effacés.

Les développeurs n'auront plus accès aux données d'état depuis les nœuds de consensus du réseau de test. Par exemple, vous ne serez pas en mesure d'effectuer des transactions ou des requêtes sur un compte qui existait avant la réinitialisation.&#x20

Le noeud miroir testnet sera disponible pour les développeurs pour stocker toutes les données avant que l'accès ne soit complètement supprimé pendant deux semaines après la date de la réinitialisation. Vous pourrez interroger les anciennes informations du réseau de test pour la période de deux semaines si elles sont disponibles.

**Ce que tu dois faire:**

- Prenez note des dates de réinitialisation à venir.
- Ayez la possibilité de recréer des données de test pour votre application afin de minimiser les interruptions.
- Après la réinitialisation, vous devrez visiter le [Portail du Développeur Heder](https://portal.hedera.com/register) pour obtenir votre nouvel ID de compte testnet.
  - La paire de clés publique et privée restera la même après les réinitialisations.
- Abonnez-vous à la [page de statut de Heder](https://status.hedera.com/) pour recevoir des notifications de réinitialisation.
- Les opérateurs de nœuds miroir peuvent référencer les instructions [here](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) pour configurer votre nœud miroir
  - Segments GCP GCS et AWS S3 : `hedera-testnet-streams-2023-01`

Si vous avez des questions ou des préoccupations, veuillez nous contacter via [Discord](https://hedera.com/discord).

**Réinitialiser les données :**

**2024**

- 1 février 2024 - Terminé
- 25 avril 2024
- 25 juillet 2024
- 31 octobre 2024

**2023**

- 26 janvier 2023 - Terminé&#x20
- 27 avril 2023 - Ignoré&#x20
- 27 juillet 2023 - Terminé
- 26 octobre 2023 - Ignoré

### Tester les Grottles réseau

{% hint style="warning" %}
**Support Limité**\
Les transactions sont actuellement limitées pour les réseaux de test. Vous recevrez une réponse **`BUSY`** si le nombre de transactions soumises au réseau dépasse la valeur seuil.
{% endhint %}

<table><thead><tr><th width="322">Type de requête de réseau</th><th>Gaz (tps)</th></tr></thead><tbody><tr><td><strong>Transactions en cryptomonnaies</strong></td><td><p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>AccountBalanceQuery</code>: illimité<br><code>TransferTransaction</code> (jetons inc.) : 10 000 tps</p><p><code>Autres</code>: 10 000 tps</p></td></tr><tr><td><strong>Transactions de Consensus</strong></td><td><p><code>TopicCreateTransaction</code>: 5 tps</p><p><code>Autres</code>: 10 000 tps</p></td></tr><tr><td><strong>Opérations de jeton</strong></td><td><p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS pour menthe fongible</li><li>50 TPS pour menthe NFT</li></ul><p><code>TokenAssociateTransaction</code>: 100 tps<br><code>TransferTransaction</code> (tokens inclus) : 10 000 tps</p><p><code>Autres</code>: 3 000 tps</p></td></tr><tr><td><strong>Planifier les transactions</strong></td><td><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</td></tr><tr><td><strong>Transactions de fichier</strong></td><td>10 Tps</td></tr><tr><td><strong>Smart Contract Transactions</strong></td><td><code>ContractExecuteTransaction</code>: 350 tps<br><code>ContractCreateTransaction</code>: 350 tps</td></tr><tr><td><strong>Queries</strong></td><td><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetContents</code>: 700 tps<br><br><code>Other</code>: 10, 00tps</td></tr><tr><td><strong>Reçoit</strong></td><td>illimité (sans limite)</td></tr></tbody></table>
