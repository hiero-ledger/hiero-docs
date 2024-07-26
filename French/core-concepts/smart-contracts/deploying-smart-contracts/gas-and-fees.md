# Gaz et frais

## Gaz

Lors de l’exécution de contrats intelligents, le MVV exige que le montant du travail soit payé en gaz. Le « travail » comprend le calcul, les transitions d'état et le stockage. Le gaz est l'unité de mesure utilisée pour facturer des frais par opcode qui est exécuté par l'EVM. Chaque code opcode a un coût de gaz défini. Le gaz reflète le coût nécessaire pour payer les ressources informatiques utilisées pour le traitement des transactions.

### **Weibar**

L'EVM renvoie des informations gazeuses à Weibar (introduit en [HIP-410](https://hips.hedera.com/hip/hip-410)). Un weibar est 10^-18 HBAR, qui se traduit par 1 tinybar est 10^10 weibars. Comme il est noté dans HIP-410, il s'agit de maximiser la compatibilité avec les outils tiers qui s'attendent à ce que les unités éther soient exploitées en fractions de 10^18, aussi connu sous le nom de Wei.

### **Calendrier et frais de gaz**

Les frais de gaz payés pour les transactions EVM sur Hedera peuvent être composés de trois types différents de coûts de gaz:

- Gaz intrinsèque
- EVM opcode Gaz
- Contrat de gaz du système Hedera

<table><thead><tr><th width="163">Type de frais de gaz</th><th>Libellé</th></tr></thead><tbody><tr><td><strong>Gaz intrinsèque</strong></td><td>La quantité minimale de gaz requise pour exécuter une transaction. Il s'agit d'un coût fixe de gaz qui est indépendant des opérations ou des calculs spécifiques effectués au cours de la transaction.<br><br>Coûts de gaz intrinsèques : 21 000 gaz</td></tr><tr><td><strong>EVM Operation Code</strong></td><td><p>Le gaz nécessaire pour exécuter le(s) code(s) d'opération définie(s) pour l'appel de contrat intelligent.</p><ul><li><strong>Opcode Frais d'Exécution Fixe</strong>: Chaque opcode a un coût fixe à payer lors de l'exécution, mesuré en gaz. Ce coût est le même pour toutes les exécutions, bien que cela soit sujet à des changements dans de nouveaux forks durs.</li></ul><ul><li><strong>Coût d'exécution dynamique opcode</strong>: Certaines instructions conduisent plus de travail que d'autres, en fonction de leurs paramètres. Pour cette raison, en plus des coûts fixes, certaines instructions ont des coûts dynamiques. Ces coûts dynamiques dépendent de plusieurs facteurs (qui varient de la fourche dure à la fourche dur). </li></ul><p>See the <a href="https://www.evm.codes/">reference</a> to learn about the specific costs per opcode and fork.</p></td></tr><tr><td><strong>Hedera System Contract Transaction</strong></td><td><p>Le gaz requis qui est associé à une transaction définie par Hedera, comme l'utilisation du contrat du système de service de jetons Hedera qui vous permet de brûler (<code>TokenBurnTransaction</code>) ou de mettre à menthe (<code>TokenMintTransaction</code>) un jeton.</p><p></p><p>Si vous n'utilisez pas un contrat de système qui correspond à l'un des services Hedera natifs, vous n'avez pas besoin d'appliquer ces frais.</p><p></p><p>Le calcul du gaz de la transaction Hedera est : Coût de la transaction en USD x Gaz Conversion gaz/USD + 20%</p><p>Exemples de contrats de système:</p><ul><li>Service de jetons Hedera</li><li>Générateur de nombres aléatoires de pseudo (PRNG)</li><li>Taux de change</li></ul></td></tr></tbody></table>

### Limite de gaz

La limite de gaz est la quantité maximale de gaz que vous êtes prêt à payer pour une opération.

Les frais de gaz d'opcode actuels reflètent la [version du service Hedera 0.22](https://docs.hedera.com/hedera/networks/release-notes/services#v0.22).

| Opération                                                                    | Coût de Cancun (Gas)                                                                          | Hedera actuelle (Gas)                                                                         |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Dépôt de code                                                                | 200 \* bytes                                                                                                     | 200 \* bytes                                                                                                     |
| <p><code>BALANCE</code><br>(compte froid)</p>                                | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>BALANCE</code><br>(compte chaud)</p>                                | 100                                                                                                              | 100                                                                                                              |
| `EXP`                                                                        | 10 + 50/octets                                                                                                   | 10 + 50/octets                                                                                                   |
| <p><code>EXTCODECOPY</code><br>(compte froid)</p>                            | 2600 + Mem                                                                                                       | 2600 + Mem                                                                                                       |
| <p><code>EXTCODECOPY</code><br>(compte chaud)</p>                            | 100 + Mem                                                                                                        | 100 + Mem                                                                                                        |
| <p><code>EXTCODEHASH</code><br>(compte froid)</p>                            | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>EXTCODEHASH</code><br>(compte chaud)</p>                            | 100                                                                                                              | 100                                                                                                              |
| <p><code>EXTCODESIZE</code><br>(compte froid)</p>                            | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>EXTCODESIZE</code><br>(compte chaud)</p>                            | 100                                                                                                              | 100                                                                                                              |
| <p><code>LOG0, LOG1, LOG2,</code><br><code>LOG3, LOG4</code></p>             | <p>375 + 375*topics<br>+ data mem</p>                                                                            | <p>375 + 375*topics<br>+ data mem</p>                                                                            |
| <p><code>SLOAD</code><br>(emplacement froid)</p>                             | 2100                                                                                                             | 2100                                                                                                             |
| <p><code>SLOAD</code><br>(emplacement chaud)</p>                             | 100                                                                                                              | 100                                                                                                              |
| <p><code>STORE</code><br>(nouvel emplacement)</p>                            | 22,100                                                                                                           | 22,100                                                                                                           |
| <p><code>SSTORE</code><br>(emplacement existant,<br>accès froid)</p>         | 2,900                                                                                                            | 2,900                                                                                                            |
| <p><code>SSTORE</code><br>(emplacement existant,<br>accès chaud)</p>         | 100                                                                                                              | 100                                                                                                              |
| <p><code>SSTORE</code><br>remboursement</p>                                  | Tel que spécifié par l'EVM                                                                                       | Tel que spécifié par l'EVM                                                                                       |
| <p><code>APPELER</code> <em>et</em>.<br>(destinataire froid)</p>             | 2,600                                                                                                            | 2,600                                                                                                            |
| <p><code>APPELER</code> <em>et</em>.<br>(destinataire chaud)</p>             | 100                                                                                                              | 100                                                                                                              |
| <p><code>APPEL</code> <em>et al</em>.<br>Surcharge de transfert Hbar/Eth</p> | 9,000                                                                                                            | 9,000                                                                                                            |
| <p><code>SELFDESTRUCT</code><br>(bénéficiaire froid)</p>                     | 2600                                                                                                             | 2600                                                                                                             |
| <p><code>SELFDESTRUCT</code><br>(bénéficiaire chaud)</p>                     | 0                                                                                                                | 0                                                                                                                |
| `TSTORE`                                                                     | 100                                                                                                              | 100                                                                                                              |
| `TLOAD`                                                                      | 100                                                                                                              | 100                                                                                                              |
| `MCOPY`                                                                      | 3 + 3\*words\_copied + memory\_expansion\_cost | 3 + 3\*words\_copied + memory\_expansion\_cost |

Les termes « chaud » et « froid » dans la table ci-dessus correspondent à la question de savoir si le compte ou l'emplacement de stockage a été lu ou écrit dans la transaction smart contract actuelle, même dans un cadre d'appel enfant.

'`CALL` _et al._' inclut avec limitation : `CALL`, `CALLCODE`, `DELEGATECALL`, et `STATICCALL`

Référence : [HIP-206](https://hips.hedera.com/hip/hip-206), [HIP-865](https://hips.hedera.com/hip/hip-865)

### Gaz par seconde cuisson

La plupart des réseaux compatibles EVM placent une limite de gaz par bloc pour gérer l’allocation des ressources. Ceci est fait pour limiter le temps passé en validation de blocs afin que les nœuds mineurs puissent produire rapidement de nouveaux nœuds. Bien que Hedera n'ait pas de blocs ou de mineurs, dans le contexte de la manière dont un [consensus Nakamoto](https://golden. om/wiki/Nakamoto\_consensus-AMB5VWM) système l'utiliserait, nous sommes contraints par la physique du temps quant au nombre de blocs que nous pouvons traiter.

Pour les transactions de contrats intelligents, le gaz est une meilleure mesure de la complexité de la transaction EVM que le calcul de toutes les transactions identiques, de façon à limiter les limites de gaz donne une limite plus raisonnable à la consommation de ressources.

Pour permettre une plus grande flexibilité dans les transactions que nous acceptons et pour refléter le comportement du réseau principal d'Ethereum, les limites de transaction seront calculées sur une base gazeuse pour les appels de contrats intelligents (`ContractCreate`, `ContractCallLocalQuery`) en plus d'une limite de transactions. Cette double approche permet une meilleure gestion des ressources, fournissant une manière nuancée de réglementer les exécutions de contrats intelligents.&#x20

Le réseau Hedera a implémenté un système de gaz de **15 millions de gaz par seconde** dans la version A du service Hedera [0.22](../../networks/release-notes/services.md#v0.22).&#x20

### Réservation de gaz et remboursement de gaz inutilisé

Hedera limite les transactions avant le consensus, et les nœuds limitent le nombre de transactions qu'ils peuvent soumettre au réseau. Ensuite, au moment du consensus, si le nombre maximal de transactions est dépassé, les opérations excédentaires ne sont pas évaluées et sont annulées avec un état occupé. La réduction par gaz variable fournit quelques défis à ce système, où les nœuds ne soumettent qu'une part de leur limite de transactions.

Pour y remédier, la limitation sera basée sur un système de mesure du gaz à deux niveaux: le pré-consensus et le post-consensus. La limitation de pré-consensus utilisera le champ `gasLimit` spécifié dans la transaction. Après consensus, on utilisera la quantité de gaz réellement évaluée consommée par la transaction, ce qui permettra des ajustements dynamiques dans le système. Il est impossible de connaître le pré-consensus gazier évalué car l'état du réseau peut affecter directement le flux de la transaction, c'est pourquoi le pré-consensus utilise le champ `gasLimit` et sera appelé **réservation de gaz**.

Les demandes de requête de contrat sont uniques et contournent tout à fait l'étape du consensus. Ces requêtes sont exécutées uniquement sur le noeud local qui les reçoit et n'influent que sur la limitation du précheck de ce noeud. D'un autre côté, les transactions contractuelles standard passent par les étapes de prévérification et de consensus et sont soumises aux deux ensembles de limites d'accélération. Les limites de l'accélérateur pour le précontrôle et le consensus peuvent être définies à des valeurs différentes.

Afin de s'assurer que les transactions peuvent s'exécuter correctement, il est courant de définir une réservation de gaz plus élevée que celle qui sera consommée par l'exécution. Sur Ethereum Mainnet, la totalité de la réservation est prélevée sur le compte avant l'exécution, et la partie inutilisée de la réservation est créditée. Cependant, Ethereum utilise un pool de mémoire ([mempool](../../../support-and-community/glossary. d#mempool)) et effectue des transactions en ordre au moment de la production du bloc, permettant que la limite du bloc soit basée uniquement sur l'utilisation et non sur le gaz réservé.

Afin de prévenir la surréservation, Hedera limite la quantité de gaz non utilisés qui peut être remboursée à un maximum de 20 % de la réservation de gaz initiale. Cela signifie en effet que les utilisateurs seront facturés pour au moins 80 % de leur réservation initiale, quelle que soit leur utilisation. Cette règle est conçue pour inciter les utilisateurs à faire des estimations gazières plus précises.

Par exemple, si vous réservez initialement 5 millions d'unités de gaz pour la création d'un contrat intelligent mais que vous finissez par n'utiliser que 2 millions, Hedera vous remboursera 1 million d'unités, i. ., 20% de votre réservation initiale. Cette configuration vise à équilibrer la gestion des ressources du réseau tout en incitant les utilisateurs à être aussi précis que possible dans leurs estimations de gaz.

### Gaz maximum par transaction

Parce que l'exécution du temps de consensus est maintenant limitée par le gaz réel utilisé et non basé sur le nombre de transactions, augmenter la limite de gaz disponible pour chaque transaction est sûr. Avant la mesure basée sur le gaz, il serait possible pour chaque transaction de consommer le gaz maximum par transaction sans tenir compte des autres transactions, donc les limites étaient basées sur ce scénario le plus grave. Maintenant que la limitation est le gaz agrégé utilisé, nous pouvons permettre à chaque transaction de consommer de grandes quantités de gaz sans se soucier d'une poussée extrême.

Lorsqu'une transaction est soumise à un nœud avec une `gasLimit` qui est plus grande que la limite de gaz par transaction, la transaction doit être rejetée lors de la prévérification avec un code de réponse de `INDIVIDUAL_TX_GAS_LIMIT_EXCEEDED`. La transaction ne doit pas être soumise à un consensus.

Les gaz gaz par contrat et les contrats créent **15 millions de gaz par seconde**.

Référence : [HIP-185](https://hips.hedera.com/hip/hip-185)
