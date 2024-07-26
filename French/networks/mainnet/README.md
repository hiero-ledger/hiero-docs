---
description: Rejoindre le réseau principal de Hedera
---

# Mainnet

## Aperçu

Le réseau principal Hedera (abréviation du réseau principal) est l'endroit où des applications sont exécutées en production, avec des frais de transaction payés en [HBAR](https://www.hedera.com/hbar). Les transactions sont soumises au réseau principal de Hedera par n'importe quelle application ou par un utilisateur au détail ; elles sont automatiquement acceptées par consensus et commandées de façon équitable.&#x20

Les données associées aux services de Hedera et stockées sur la chaîne peuvent être consultées par n'importe quel compte Hedera. Chaque transaction nécessite un paiement sous la forme d'un **frais de transaction** libellé en _**tinybars**_ (100 000 000 t<unk> = 1 <unk> ). Vous pouvez en savoir plus sur les frais de transaction et estimer les coûts de votre application [here](https://www.hedera.com/fees).&#x20

Si vous souhaitez tester votre application (ou simplement expérimenter), veuillez visiter [Testnet Access](../testnet/testnet-access.md). Le réseau de test Hedera permet aux développeurs de prototyper et de tester des applications dans un environnement simulé qui utilise le test _HBAR_ pour payer les frais de transaction.

{% hint style="warning" %}
**Throttles**\
Les transactions sur le réseau principal de Hedera sont actuellement limitées. Vous recevrez une réponse `"BUSY"` si le nombre de transactions soumises au réseau dépasse la valeur seuil.
{% endhint %}

#### Grottles réseau

| Types de requête réseau       | Gaz (tps)                                                                                                                                                                                           |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Transactions en cryptomonnaie | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: unlimited</p><p>TransferTransaction (inc. tokens): 10 000 tps<br>Autre : 10 000 tps</p>                                                                  |
| Transactions de consensus     | <p>TopicCreateTransaction: 5 tps</p><p>Autre : 10 000 tps</p>                                                                                                                                                          |
| Transactions de jeton         | <p>TokenMint:</p><ul><li>125 TPS pour la menthe fungible</li><li>50 TPS pour la menthe NFT</li></ul><p>TokenAssociateTransaction: 100 tps<br>TransferTransaction (inc. jetons): 10 000 tps</p><p>Autre : 3 000 tps</p> |
| Planifier les transactions    | <p>Transaction ScheduleSign: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                                                                         |
| Transactions de fichier       | 10 Tps                                                                                                                                                                                                                 |
| Transactions Smart Contract   | <p>ContractExecuteTransaction: 350 tps<br>ContractCreateTransaction: 350 tps</p>                                                                                                                                       |
| Requêtes                      | <p>ContractGetInfo : 700 tps<br>ContractGetBytecode : 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo : 700 tps<br>FileGetContents: 700 tps<br><br>Autres: 10, 00 tps</p>                                     |
| Reçus                         | illimité (sans limite)                                                                                                                                                                              |
