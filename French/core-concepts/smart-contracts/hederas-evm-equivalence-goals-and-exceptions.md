# Comprendre Hedera pour les développeurs EVM

## Hedera vs Ethereum

Alors qu'Hedera aspire à l'équivalence EVM, il est important de reconnaître certains aspects uniques et des différences fondamentales dans son architecture et ses opérations de réseau. comme la gestion des structures de données d'état, les algorithmes de hachage et la gestion des comptes et des transactions. Ces distinctions dans les comportements de réseau sont des choix intentionnels de conception faits pour s'aligner avec les normes EVM, ce qui permet d'atteindre la compatibilité EVM. Cette approche garantit que si Hedera s'aligne de près sur Ethereum, elle maintient également ses caractéristiques et optimisation.

### Différences de réseau et de sécurité

<table><thead><tr><th width="211">Fonction</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Structure de données d'état réseau</strong></td><td>Arbre virtuel de Merkle</td><td>Merkle Patricia Trie</td></tr><tr><td><strong>Algorithme de hachage</strong></td><td>SHA-384</td><td>Keccak-256</td></tr><tr><td><strong>Sécurité</strong></td><td>Haute sécurité avec aBFT</td><td>Sécuriser avec un réseau PoS décentralisé</td></tr></tbody></table>

### Différences de compte et d'autorisation

<table><thead><tr><th width="202.33333333333331">Fonction</th><th width="296">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Signatures d'autorisation</strong></td><td>Utilisé pour l'autorisation de transaction en dehors des contrats intelligents</td><td>Généralement utilisé dans les contrats intelligents</td></tr><tr><td><strong>Comptes système spéciaux</strong></td><td>Disponible avec des propriétés uniques</td><td>Indisponible</td></tr><tr><td><strong>Comptes non-ECDSA</strong></td><td><p>Les comptes non-ECDSA (tels que ED ou multi-clé) sont pris en charge par Hedera et</p><p>Les comptes ECDSA sont entièrement compatibles</p></td><td>Les comptes ECDSA sont pris en charge par Ethereum et les comptes non-ECDSA ne sont pas supportés/compatibles</td></tr><tr><td><strong>Suppression du compte</strong></td><td>Possible</td><td>Inpossible</td></tr></tbody></table>

### Différences de contrat et de gaz

<table><thead><tr><th width="210.33333333333331">Fonction</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Retour de données sur les appels statiques</strong></td><td>La récupération des données doit être effectuée via le relais</td><td>Données retournées directement</td></tr><tr><td><strong>Frais Gaz</strong></td><td>Frais au moins 80% des frais de gaz quel que soit le résultat de la transaction</td><td>Les frais de gaz dépendent des résultats de la transaction, mais généralement 100 % des frais de gaz sont facturés et la portion inutilisée est créditée</td></tr><tr><td><strong>Contract Lifecycle</strong></td><td>Les entités contractuelles peuvent expirer, les frais de location peuvent s'appliquer</td><td>Pas de frais d'expiration ou de location</td></tr></tbody></table>

### Différences de transactions et de requêtes

<table><thead><tr><th width="212.33333333333331">Fonction</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Taille de transaction limite</strong></td><td>6 Ko</td><td>Aucune limite</td></tr><tr><td><strong>Réduction de la transaction</strong></td><td><a href="deploying-smart-contracts/#gas-limit">Les transactions peuvent être limitées par les limites de gaz</a></td><td>Transactions en attente jusqu'à la soumission future</td></tr><tr><td><strong>Coûts de requête</strong></td><td>Non libre, peut utiliser un noeud miroir pour des requêtes gratuites</td><td>Appels gratuits en lecture seule</td></tr><tr><td><strong>Mempools</strong></td><td>No <a href="../../support-and-community/glossary.md#mempool">mempools</a></td><td>Pools de mémoire disponibles</td></tr><tr><td><strong>Coût</strong></td><td>Frais bas et prévisibles (fraction d'un cent)</td><td>Variable, souvent des frais de gaz élevés</td></tr></tbody></table>

### Différences de point de terminaison et de communication RPC

<table><thead><tr><th width="259">Fonction</th><th width="244">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>RPC Block Requests</strong> (ex: <code>eth_getBlockByHash*</code> & <code>eth_getBlockByNumber</code>)</td><td>Renvoie la valeur hexadécimale de zéro 32 octets pour la <code>stateRoot</code></td><td>Retourne la valeur hexadécimale <code>stateRoot</code> de la tentative d'état final du bloc</td></tr><tr><td><strong>Communication</strong></td><td>Nécessite une communication avec les nœuds de consensus et miroir</td><td>Communication directe avec les nœuds</td></tr></tbody></table>

{% hint style="info" %}
**Note** : Le Consensus Hedera et les nœuds miroir ne fournissent pas de terminaux d'API RPC Ethereum.
{% endhint %}

### Différences de jetons et de frais

<table><thead><tr><th width="232.33333333333331">Fonction</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td> <strong>Native Tokens</strong></td><td>Supporte les jetons natifs en plus des normes de jetons <a href="tokens-managed-by-smart-contracts/">ERC-20 et ERC-721</a></td><td>Toutes les normes de jetons ERC mais principalement les jetons ERC-20 et ERC-721.</td></tr><tr><td><strong>Frais Structure</strong></td><td><a href="deploying-smart-contracts/#gas-schedule-and-fees">Complex with two different gas prices</a></td><td>Prix du gaz unique</td></tr><tr><td><strong>Association de jetons**</strong></td><td><a href="../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md">Concept de l'association de jetons </a></td><td>Aucun concept d'association de jetons</td></tr><tr><td><strong>Keys for Token Functionality</strong></td><td>Les clés contrôlent l'accès aux jetons (<code>KYC</code>, <code>FREEZE</code>, <code>WIPE</code>, fourniture, frais et <code>PAUSE</code>)</td><td>Aucune fonctionnalité <em>native</em> équivalente</td></tr></tbody></table>

{% hint style="info" %}
**\*\*Note :** L'association de jetons ne s'applique qu'aux jetons HTS _native_ et n'affecte pas les jetons ERC-20/721.
{% endhint %}

### Autres différences

<table><thead><tr><th width="238">Fonction</th><th width="274.3333333333333">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Precheck Failures</strong></td><td><a href="../../sdks-and-apis/hedera-api/miscellaneous/responsecode.md">Multiple precheck failure reasons</a></td><td>Raison de l'échec généralement simple</td></tr><tr><td><strong>HBAR Précision décimale</strong></td><td>8 ou 18<a href="../../sdks-and-apis/sdks/hbars.md#hbar-decimal-places"> (varie entre les API Hedera)</a></td><td>Précision décimale cohérente de 18 points</td></tr></tbody></table>
