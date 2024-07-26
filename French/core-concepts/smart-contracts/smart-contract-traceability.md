# Traceability Smart Contract

Une fois que les contrats ont été déployés, vous voudrez peut-être approfondir la recherche sur l'exécution d'un appel de fonction de contrat intelligent. Les traces fournissent une vue d'ensemble de la séquence des opérations et de leurs effets, ce qui permet d'analyser, de déboguer et d'auditer les comportements intelligents des contrats. Les deux types de traces utiles :

**➡️** [**Trace d'appels**](smart-contract-traceability.md#call-trace)

**➡️** [**State Trace**](smart-contract-traceability.md#state-trace)

***

## Trace d'appel

Les informations du contrat **call trace** saisissent les détails de l'intrant, de la sortie et du gaz de toutes les fonctions imbriquées de contrats intelligents exécutées dans une transaction. Sur Ethereum, Ces opérations sont parfois appelées opérations intérieures, mais elles ne font que capturer des instantanés de la trame de message en tenant compte de la rencontre EVM lors du traitement d'une exécution intelligente de contrat à chaque profondeur pour toutes les fonctions concernées.

<table data-header-hidden><thead><tr><th width="173"></th><th></th></tr></thead><tbody><tr><td><strong>Input Data</strong></td><td>Il enregistre les données ou paramètres des intrants fournis lors de l'appel d'une fonction particulière dans un contrat intelligent. Ces données d'entrée sont essentiellement la forme codée de la signature de la fonction et de ses arguments.</td></tr><tr><td><strong>Données de sortie</strong></td><td>Après l'exécution de la fonction, les informations de trace incluent les données de sortie retournées par cette fonction. Cela peut être le résultat du calcul de la fonction ou de toute donnée qu'elle génère dans le cadre de son exécution.</td></tr><tr><td><strong>Détails Gaz</strong></td><td>Enregistre les informations sur le gaz consommé par chaque appel de fonction. Chaque opération au sein d'une fonction consomme une certaine quantité de gaz, et cette information est suivie pour calculer le coût global de la transaction.</td></tr></tbody></table>

Ces informations peuvent être consultées en utilisant l'ID de la transaction ou le hash de la transaction Ethereum.

i Des informations détaillées pour la trace des appels peuvent être trouvées dans la Hedera [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_action.proto) et inclut:

<table><thead><tr><th width="178">Données de suivi d'appel</th><th>Libellé</th></tr></thead><tbody><tr><td><strong>Call Operation Type</strong></td><td><p>Type d'opération spécifique effectuée lors de l'exécution d'un contrat intelligent ou d'une transaction dans l'EVM. Exemple: “CALL” est un type d'opération utilisé lorsqu'une transaction invoque une fonction dans un contrat intelligent. Il exécute la fonction et peut potentiellement modifier l'état du contrat.</p><pre><code>OP_UNKNOWN = 0;
OP_CALL = 1;
OP_CALLCODE = 2;
OP_DELEGATECALL = 3;
OP_STATICCALL = 4;
OP_CREATE = 5;
OP_CREATE2 = 6;
</code></pre></td></tr><tr><td><strong>Result Data</strong></td><td>Les données résultantes sont les valeurs de sortie ou de retour générées par l'exécution d'une fonction ou d'une action de contrat intelligent. Lorsqu'un appel de fonction est exécuté, il peut produire des données comme des valeurs calculées, des indicateurs de statut, le contrat renvoie la raison si l'une et l'erreur si la transaction elle-même a échoué sans un <code>REVERT</code> explicite</td></tr><tr><td><strong>Type de données de résultat</strong></td><td>Le "type de données de résultat" fait référence au type de données de la valeur retournée par la fonction ou la méthode. Par exemple, si vous avez une fonction <code>add(a, b)</code> qui ajoute deux nombres et retourne le résultat, le type de données de résultat peut être un entier s'il retourne la somme des nombres.</td></tr><tr><td><strong>Profondeur d'appel</strong></td><td>Le niveau ou la profondeur de l'appel de la fonction courante dans la pile d'appels. Il fournit des informations sur la nature imbriquée des appels de fonctions et aide à suivre la séquence et la hiérarchie des invocations de fonctions pendant l'exécution d'un contrat intelligent. <br><br>La profondeur de l'appelant indique combien de fonctions ont été appelées avant la fonction courante dans la pile d'appels. Il commence à 0 pour l'invocation et l'incrémentation de la fonction initiale par 1 pour chaque appel de fonction suivante. <br><br>Par exemple, la transaction parent serait représentée comme la profondeur d’appel 1 et le premier enfant serait à la profondeur d’appel 1. et la transaction enfant 2 serait à la profondeur d'appel 1.2. La transaction enfant à la profondeur 1.2 a deux parents.</td></tr><tr><td><strong>Appeler</strong></td><td>L'appelant peut être l'ID du compte appelant le contrat ou l'ID d'un autre contrat intelligent appelant le contrat. <br><br>La première action dans l'arbre ne peut provenir que d'un compte. Le reste des actions dans l'arbre d'appel provient du contrat. <br><br>Lorsqu'une fonction de contrat intelligent est invoquée, soit par un compte externe, soit par un autre contrat, l'adresse de l'appelant est enregistrée dans la trace pour identifier la source de l'appel de fonction. L'adresse de l'appelant peut être utile pour comprendre le contexte de l'exécution et déterminer l'origine de la transaction ou du message qui a déclenché l'appel de fonction.</td></tr><tr><td><strong>Destinataire</strong></td><td>L'adresse du contrat ou du compte intelligent qui reçoit un appel ou une transaction spécifique. Il représente la destination ou la cible de l’interaction au sein de l’EVM. L'action contractuelle peut être dirigée vers l'une des actions suivantes : <br><br>• Compte : l'ID du compte du destinataire si le destinataire est un compte. Seuls les ARH seront transférés. <br>• Contract: The contract ID if the recipient is a smart contract <br>• EVM address : If the contract action was directed to an invalid solidity address, what that address was</td></tr><tr><td><strong>From</strong></td><td>Le contrat de Hedera appelant le prochain contrat.</td></tr><tr><td><strong>To</strong></td><td>Le contrat recevant l'appel ou en cours de création.</td></tr><tr><td><strong>Valeur/Montant</strong></td><td>Le nombre de barres de navigation transférées dans cet appel.</td></tr><tr><td><strong>Limite Gaz</strong></td><td>Le gaz est défini comme le gaz maximal que cet appel contractuel peut dépenser.</td></tr><tr><td><strong>Gaz utilisé</strong></td><td>La quantité de gaz qui a été utilisée pour l'appel de contrat.</td></tr><tr><td><strong>Input</strong></td><td>Octets passés en tant que données d'entrée à cet appel de contrat</td></tr></tbody></table>

**Exemple** :

<figure><img src="../../.gitbook/assets/smart-contracts-core-concepts-call-trace.png" alt=""><figcaption><p>Exemple de trace d'appel sur HashScan</p></figcaption></figure>

***

## Extrait d'Etat

Les changements d'état de Smart Contract seront désormais suivis chaque fois qu'une transaction de contrat intelligent modifie l'état du contrat. Cela permettra aux développeurs d'avoir une trace papier des changements d'état survenus pour un contrat depuis la création du contrat. Les changements d'état qui seront suivis incluent chaque fois qu'une valeur est lue ou écrite sur le contrat intelligent. La fente de stockage représente l'ordre dans lequel l'état du contrat intelligent est lu ou écrit.

La valeur lue reflète la valeur de stockage avant l'exécution de la transaction de contrat intelligent. La valeur écrite, si elle est présente, représente la valeur mise à jour finale du slot de stockage après la fin de l'appel du contrat intelligent. Les états transitoires entre le début et la fin du contrat ne sont pas stockés dans le dossier de transaction.

i Informations détaillées sur la trace d'état peuvent être trouvées dans le [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/contract\_state\_change.proto) et inclut:&#x20

<table><thead><tr><th width="205">Données de suivi d'état</th><th>Libellé</th></tr></thead><tbody><tr><td>Adresses</td><td><p>L'adresse EVM du contrat intelligent.</p><p>Ex: <code>0000000000000000000000000000000000001f41</code></p></td></tr><tr><td>ID du contrat</td><td><p>L'ID du contrat intelligent.</p><p>Ex: <code>0.0.1234</code></p></td></tr><tr><td>Emplacement</td><td>Se réfère à un emplacement de stockage où les données sont stockées dans l'état du contrat. Il peut également être considéré comme une variable ou une unité de stockage qui contient une valeur spécifique.</td></tr><tr><td>Valeur lue</td><td>Les valeurs courantes des variables ou des structures de données avant de faire des modifications. Ces valeurs peuvent être utilisées pour valider les conditions, effectuer des calculs ou déclencher des actions spécifiques dans le code du contrat.</td></tr><tr><td>Valeur écrite</td><td>Les variables ou structures de données écrites ou modifiées après la modification.</td></tr></tbody></table>

### Noeud de consensus

Les nœuds de consensus stockent les enregistrements sidecar appelés `ContractStateChanges`. Chaque fois qu'un contrat intelligent change, un nouveau record sera produit pour commémorer les changements d'état du contrat qui ont eu lieu.

### Noeud miroir

Le [nœud miroir](../../support-and-community/glossary.md#mirror-nodes) supporte deux API restantes qui retournent des informations sur les changements d'état du contrat intelligent. Cela comprend :

- `/api/v1/contracts/{id}/results/{timestamp}`
- `/api/v1/contracts/results/{transactionIdOrHash}`

**Exemple :**

```
    "state_changes": [
      {
        "address": "000000000000000000000000000000000000000000001f41",
        "contract_id": "0.1.2",
        "slot": "0x0000000000000000000000000000000000000000000000000000000000fa",
        "value_read": "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20640ba24ce69750",
        "value_written": "0x8c5be1e5d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
      }

```

### Explorateur de nœud miroir Hedera

La trace d'état peut être consultée sur un explorateur réseau Hedera pris en charge.

<figure><img src="../../.gitbook/assets/smart-contracts-network-explorer-example.png" alt=""><figcaption><p>exemple de trace d'état sur HashScan</p></figcaption></figure>
