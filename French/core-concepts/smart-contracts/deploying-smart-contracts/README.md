# Déploiement de contrats intelligents

Après avoir compilé votre contrat intelligent, vous pouvez le déployer sur le réseau Hedera. Le "_init code_" du constructeur inclut le bytecode entier du contrat. Lors du déploiement, l'EVM devrait être fourni avec les deux contrats intelligents [bytecode](../../.. support-and-community/glossary.md#bytecode) et le gaz nécessaire pour exécuter et déployer le contrat. Post-déploiement, le constructeur est supprimé, ne laissant que le `runtime_bytecode` pour les futures interactions de contrat.

**➡️** [**EVM de Besu d'Hyperledger**](./#hyperledger-besu-evm-on-hedera)

**➡️** [**Fork dur de Cancun**](./#cancun-hard-fork)

**➡️** [**Variables Solidity et Opcodes**](./#solidity-variables-and-opcodes)

***

## Machine virtuelle Ethereum (EVM)

La [Machine Virtuelle Ethereum (EVM)](../../../support-and-community/glossary.md#ethereum-virtual-machine-evm) est un environnement d'exécution pour exécuter des contrats intelligents écrits en langage de programmation natifs EVM, comme Solidity. Le code source doit être compilé en bytecode pour que l'EVM exécute un contrat intelligent.

Sur Hedera, les utilisateurs peuvent interagir avec l'environnement compatible EVM de plusieurs façons. Ils peuvent soumettre `ContractCreate`, `EthereumTransaction`, ou faire des appels RPC `eth_sendRawTransaction` avec le bytecode du contrat directement. Ces différents chemins permettent aux développeurs de déployer et de gérer efficacement les contrats intelligents.

Lorsque l'EVM reçoit le bytecode, il sera ensuite décomposé en codes de fonctionnement ([opcodes](../../../support-and-community/glossary.md#opcodes)). Les opcodes EVM représentent les instructions spécifiques qu'il peut effectuer. Chaque opcode est un octet et a son propre coût de gaz. Le coût par opcode pour le fork dur Ethereum Cancun peut être trouvé [here](https://www.evm.codes/?fork=cancun).

#### Exemple d'Opcode du Smart Contract

```solidity
PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x40 MLOAD PUSH2 0x558 CODESIZE SUB DUP1 PUSH2 0x558 DUP4 CODECOPY DUP2 DUP2 ADD PUSH1 0x40 MSTORE PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x33 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 MLOAD PUSH1 0x40 MLOAD SWAP4 SWAP3 SWAP2 SWAP1 DUP5 PUSH5 0x100000000 DUP3 GT ISZERO PUSH2 0x53 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP4 DUP3 ADD SWAP2 POP PUSH1 0x20 DUP3 ADD DUP6 DUP2 GT ISZERO PUSH2 0x69 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 MLOAD DUP7 PUSH1 0x1 DUP3 MUL DUP4 ADD GT PUSH5 0x100000000 DUP3 GT OR ISZERO PUSH2 0x86 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 DUP4 MSTORE PUSH1 0x20 DUP4 ADD SWAP3 POP POP POP SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0xBA JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x9F JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xE7 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP PUSH1 0x40 MSTORE POP POP POP CALLER PUSH1 0x0 DUP1 PUSH2 0x100 EXP DUP2 SLOAD DUP2 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF MUL NOT AND SWAP1 DUP4 PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND MUL OR SWAP1 SSTORE POP DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x144 SWAP3 SWAP2 SWAP1 PUSH2 0x14B JUMP JUMPDEST POP POP PUSH2 0x1E8 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x18C JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x1BA JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x1BA JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x1B9 JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x19E JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x1C7 SWAP2 SWAP1 PUSH2 0x1CB JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x1E4 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x1CC JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST PUSH2 0x361 DUP1 PUSH2 0x1F7 PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x4 CALLDATASIZE LT PUSH2 0x36 JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E982602 EQ PUSH2 0x3B JUMPI DUP1 PUSH4 0x32AF2EDB EQ PUSH2 0xF6 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST PUSH2 0xF4 PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH2 0x51 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH5 0x100000000 DUP2 GT ISZERO PUSH2 0x6E JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP3 ADD DUP4 PUSH1 0x20 DUP3 ADD GT ISZERO PUSH2 0x80 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP2 DUP5 PUSH1 0x1 DUP4 MUL DUP5 ADD GT PUSH5 0x100000000 DUP4 GT OR ISZERO PUSH2 0xA2 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST SWAP2 SWAP1 DUP1 DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP4 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP4 DUP4 DUP1 DUP3 DUP5 CALLDATACOPY PUSH1 0x0 DUP2 DUP5 ADD MSTORE PUSH1 0x1F NOT PUSH1 0x1F DUP3 ADD AND SWAP1 POP DUP1 DUP4 ADD SWAP3 POP POP POP POP POP POP POP SWAP2 SWAP3 SWAP2 SWAP3 SWAP1 POP POP POP PUSH2 0x179 JUMP JUMPDEST STOP JUMPDEST PUSH2 0xFE PUSH2 0x1EC JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x13E JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x123 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0x16B JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x0 DUP1 SLOAD SWAP1 PUSH2 0x100 EXP SWAP1 DIV PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND EQ PUSH2 0x1D1 JUMPI PUSH2 0x1E9 JUMP JUMPDEST DUP1 PUSH1 0x1 SWAP1 DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 PUSH2 0x1E7 SWAP3 SWAP2 SWAP1 PUSH2 0x28E JUMP JUMPDEST POP JUMPDEST POP JUMP JUMPDEST PUSH1 0x60 PUSH1 0x1 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 PUSH1 0x1F ADD PUSH1 0x20 DUP1 SWAP2 DIV MUL PUSH1 0x20 ADD PUSH1 0x40 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 SWAP3 SWAP2 SWAP1 DUP2 DUP2 MSTORE PUSH1 0x20 ADD DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV DUP1 ISZERO PUSH2 0x284 JUMPI DUP1 PUSH1 0x1F LT PUSH2 0x259 JUMPI PUSH2 0x100 DUP1 DUP4 SLOAD DIV MUL DUP4 MSTORE SWAP2 PUSH1 0x20 ADD SWAP2 PUSH2 0x284 JUMP JUMPDEST DUP3 ADD SWAP2 SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 JUMPDEST DUP2 SLOAD DUP2 MSTORE SWAP1 PUSH1 0x1 ADD SWAP1 PUSH1 0x20 ADD DUP1 DUP4 GT PUSH2 0x267 JUMPI DUP3 SWAP1 SUB PUSH1 0x1F AND DUP3 ADD SWAP2 JUMPDEST POP POP POP POP POP SWAP1 POP SWAP1 JUMP JUMPDEST DUP3 DUP1 SLOAD PUSH1 0x1 DUP2 PUSH1 0x1 AND ISZERO PUSH2 0x100 MUL SUB AND PUSH1 0x2 SWAP1 DIV SWAP1 PUSH1 0x0 MSTORE PUSH1 0x20 PUSH1 0x0 KECCAK256 SWAP1 PUSH1 0x1F ADD PUSH1 0x20 SWAP1 DIV DUP2 ADD SWAP3 DUP3 PUSH1 0x1F LT PUSH2 0x2CF JUMPI DUP1 MLOAD PUSH1 0xFF NOT AND DUP4 DUP1 ADD OR DUP6 SSTORE PUSH2 0x2FD JUMP JUMPDEST DUP3 DUP1 ADD PUSH1 0x1 ADD DUP6 SSTORE DUP3 ISZERO PUSH2 0x2FD JUMPI SWAP2 DUP3 ADD JUMPDEST DUP3 DUP2 GT ISZERO PUSH2 0x2FC JUMPI DUP3 MLOAD DUP3 SSTORE SWAP2 PUSH1 0x20 ADD SWAP2 SWAP1 PUSH1 0x1 ADD SWAP1 PUSH2 0x2E1 JUMP JUMPDEST JUMPDEST POP SWAP1 POP PUSH2 0x30A SWAP2 SWAP1 PUSH2 0x30E JUMP JUMPDEST POP SWAP1 JUMP JUMPDEST JUMPDEST DUP1 DUP3 GT ISZERO PUSH2 0x327 JUMPI PUSH1 0x0 DUP2 PUSH1 0x0 SWAP1 SSTORE POP PUSH1 0x1 ADD PUSH2 0x30F JUMP JUMPDEST POP SWAP1 JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 AND DIFFICULTY CHAINID 0x5F 0x5F PUSH20 0xDFD73A518B57770F5ADB27F025842235980D7A0F 0x4E ISZERO 0xB1 0xAC 0xB1 DUP15 PUSH5 0x736F6C6343 STOP SMOD STOP STOP CALLER
```

Référence : [https://ethervm.io/](https://ethervm.io/)

***

## Hyperledger Besu EVM sur Hedera

Les nœuds réseau Hedera utilisent le [EVM HyperLedger Besu ](../../../support-and-community/glossary.md#hyperledger-besu-evm)Client écrit en Java comme couche d'exécution pour les transactions de type Ethereum. La base de code est à jour avec les forks durs Ethereum principaux. La bibliothèque client Besu EVM est utilisée sans crochets pour le consensus, le réseautage et le stockage d'Ethereum. Au lieu de cela, Hedera s'accroche à son propre consensus sur le Hashgraphe, la communication de Gossip et les [arbres virtuels de Merkle](../../../support-and-community/glossary.md#virtual-merkle-tree) pour une plus grande tolérance aux défauts, la finalité et la scalabilité.&#x20

Depuis la version principale de Hedera [`0.50.0`](../../../networks/release-notes/services.md#v0. 0), le client Besu EVM est configuré pour supporter le fork dur de Cancun du réseau principal Ethereum, avec quelques modifications.

### **Fork dur de Cancun**

La plateforme de contrat intelligent a été mise à jour pour supporter les changements EVM visibles introduits dans le fork dur [Cancun](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/cancun.md) . Cela inclut l'ajout de nouveaux opcodes pour le stockage transitoire et la copie de mémoire, les mises à jour sémantiques pour les opcodes introduits dans le [Shanghai](https://github. om/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/shanghai.md), [London](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london. d), [Istanbul](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md), et [Berlin](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md) forks durs, sauf ceux ayant des changements dans la production de blocs, la sérialisation des données et le marché du double frais.&#x20

Depuis la sortie des services Hedera [0.22](../../../networks/release-notes/services.md#v0.22), les coûts des gaz et des données d'entrée sont facturés. La quantité de gaz intrinsèque consommée est une charge constante qui se produit avant l'exécution de n'importe quel code. Le coût intrinsèque du gaz est de 21 000. Le coût associé des données d'entrée est de 16 gaz pour chaque octet de données qui n'est pas zéro et de 4 gaz pour chaque octet de données qui est de zéro. La quantité de gaz intrinsèque consommée est facturée en relation avec les données fournies lors de l'appel d'un contrat aux paramètres de fonction des contrats externes. Le calendrier de l'essence et le tableau des honoraires se trouvent dans la section gaz de cette page documentaire.

<figure><img src="../../../.gitbook/assets/cancun-blob-graphic-onchaintimes.jpeg" alt=""><figcaption><p><strong>EIP-4844 Dévoiré: Paving the Way for Proto-Danksharding in Ethereum</strong></p></figcaption></figure>

#### Proto-Danksharding

En tant que solution provisoire au déchiquetage complet, introduite dans l'assiette dure de Cancun, le proto-danksharding offre certains des avantages du bombardement avec une complexité réduite et des changements d'infrastructure qui font partie d'une mise en œuvre du bombardement. Ceci, à son tour, ouvre les portes pour ajouter des "blobs" de données à ajouter aux blocs pour augmenter davantage la disponibilité des données et permettre une plus grande efficacité de traitement.

Les lobs sont de gros objets de données dans les blocs. Celles-ci peuvent être utilisées pour stocker des rollups (solutions de calque 2) et différents types d'applications nécessitant que les gros objets de données soient stockés de manière efficace. Il s'agit de données hors chaîne pour les validateurs et qui nécessitent un traitement minimal de leur part. Il réduit la charge de calcul sur le réseau et réduit ainsi les frais de gaz de transaction.

#### ❌ Blobs pris en charge sur Hedera?

Hedera ne fournit pas de blobs sous [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844). [HIP-866](https://hips.hedera.com/hip/hip-866) définit comment Hedera se comporte sans support blob. Pour préserver la compatibilité et l'espace de design futur, Hedera agira comme si les blobs ne sont pas ajoutés. Cela permet aux contrats existants dépendants du comportement de blob de fonctionner sans blobs. Les Blobs seront empêchés d'entrer dans le système en interdisant les transactions de type 3, qui activent les blobs. Cela empêchera les blobs de se préoccuper de l'EVM sans affecter d'autres interactions souhaitables en Hedera.

### Variables Solidity et Opcodes

Le tableau ci-dessous définit le mappage des variables Solidity et des codes d'opération vers Hedera. La liste complète des opcodes pris en charge pour le fork dur de Cancun peut être trouvée [here](https://www.evm.codes/).&#x20

<table><thead><tr><th width="245" align="center">Solidité</th><th width="170" align="center">Opcode</th><th>Hedera</th></tr></thead><tbody><tr><td align="center">Adresse <code></code></td><td align="center"></td><td>L'adresse est un mappage de shard.realm.number (0.0.10) en une adresse Solidity de 20 octets. L'adresse peut être un ID de compte Hedera ou un ID de contrat au format Solidity.</td></tr><tr><td align="center"><code>block.basefee</code></td><td align="center"><code>BASEFEE</code></td><td>L'opcode <code>BASEFEE</code> retournera zéro. Hedera n'utilise pas le mécanisme du marché de la tarification qu'il est conçu pour soutenir.</td></tr><tr><td align="center"><code>block.chainId</code></td><td align="center"><code>CHAINID</code></td><td>L'opcode <code>CHAINID</code> retournera 295(hexadécimal <code>0x0127</code>) pour le réseau principal, 296 ( hexadécimal <code>0x0128</code>) pour testnet, 297 ( hexadécimal <code>0x0129</code>) pour previewnet et 298 (<code>0x12A</code>) pour les réseaux de développement.</td></tr><tr><td align="center"><code>block.coinbase</code></td><td align="center"><code>COINBASE</code></td><td>L'opération <code>COINBASE</code> retournera le compte de financement (frais de transaction Hedera percevant le compte <code>0.0.98</code>).</td></tr><tr><td align="center"><code>block.number</code></td><td align="center"></td><td>L'index du fichier d'enregistrement (non recommandé, utilisez <code>block.timestamp</code>).</td></tr><tr><td align="center"><code>block.timestamp</code></td><td align="center"></td><td>L'horodatage du consensus de la transaction.</td></tr><tr><td align="center"><code>block.difficulty</code></td><td align="center"></td><td>Toujours zéro.</td></tr><tr><td align="center"><code>block.gaslimit</code></td><td align="center"><code>GASLIMIT</code></td><td>L'opération <code>GASLIMIT</code> retournera la <code>limite</code> de la transaction. La transaction <code>la limite de gaz</code> sera la plus basse de la limite de gaz demandée dans la transaction ou une limite de gaz supérieure globale configurée pour tous les contrats intelligents.</td></tr><tr><td align="center"><code>msg.sender</code></td><td align="center"></td><td>L'adresse de l'ID du contrat Hedera ou de l'ID du compte au format Solidity qui a appelé ce contrat. Pour le niveau racine ou pour les chaînes de délégués qui vont à la racine, c'est l'ID du compte payant pour la transaction.</td></tr><tr><td align="center"><code>msg.value</code></td><td align="center"></td><td>La valeur associée à la transaction associée à tinybar.</td></tr><tr><td align="center"><code>tx.origin</code></td><td align="center"></td><td>L'ID du compte payant pour la transaction, quelle que soit la profondeur.</td></tr><tr><td align="center"><code>tx.gasprice</code></td><td align="center"></td><td>Fixe (varie selon le calendrier des frais globaux et le taux de change).</td></tr><tr><td align="center"><p><code>selfdestruct</code></p><p><code>(adresse payable)</code></p></td><td align="center"><code>SELFDESTRUCT</code></td><td>L'adresse ne sera pas réutilisable en raison de la politique de numérotation du compte Hedera. Sur <code>SELFDESTRUCT</code> les contrats HBAR et jetons HTS sont transférés aux destinataires. Si le destinataire n'existe pas ou n'a pas de franchise pour l'un des jetons HTS, cet opcode échouera. </td></tr><tr><td align="center"><code><adresse>.code</code></td><td align="center"></td><td>Les adresses précompilées du contrat ne signaleront aucun code, y compris le contrat du système HTS.</td></tr><tr><td align="center"><code><adresse>.codehash</code></td><td align="center"></td><td>Les adresses de contrat de précompilation rapporteront le hash de code vide.</td></tr><tr><td align="center"></td><td align="center"><code>PRNGSEED</code></td><td>Cet opcode retourne un nombre aléatoire basé sur le hash de l'enregistrement n-3.</td></tr><tr><td align="center"><code>delegateCall</code></td><td align="center"></td><td>Les contrats ne peuvent plus utiliser <code>delegateCall()</code> pour invoquer des contrats système. Les contrats devraient plutôt utiliser la méthode <code>call()</code>.</td></tr><tr><td align="center"><code>blobVersionedHashesAtIndex</code></td><td align="center"><code>BLOBHASH</code></td><td>L'opération <code>BLOBHASH</code> retournera tous les zéros en tout temps.</td></tr><tr><td align="center"><code>blobBaseFee</code></td><td align="center"><code>BLOBBASEFEE</code></td><td><p>L'opération <code>BLOBBASEFEE</code> retournera</p><p><code>1</code> en tout temps.</p></td></tr></tbody></table>

Référence : [HIP-866](https://hips.hedera.com/hip/hip-866), [HIP-868](https://hips.hedera.com/hip/hip-868)

***

### Limitation des fonctions `fallback()` / `receive()` dans Hedera Smart Contracts <a href="#limitation-on-fallback-receive-functions-in-hedera-smart-contracts" id="limitation-on-fallback-receive-functions-in-hedera-smart-contracts"></a>

Lors du développement de contrats intelligents sur Hedera, Il est important de comprendre que les fonctions `fallback()` et `receive()` **ne sont pas** déclenchées lorsqu'un contrat reçoit HBAR via un transfert de cryptomonnaies.

Sur Ethereum, ces fonctions servent de mécanismes "catch-all" lorsqu'un contrat reçoit Ether. En Hedera, cependant, les soldes des contrats peuvent changer grâce à des opérations HAPI natives, indépendamment des appels de messages EVM, rendant impossible le maintien des invariants liés à l'équilibre avec seulement les méthodes `fallback()` ou `receive()`.

#### Variables impactées

- **`msg.sender`:** L'adresse qui lance l'appel du contrat.
- **`msg.value`:** Le montant du HBAR envoyé avec l'appel.

#### Points clés

- Les développeurs devraient implémenter des fonctions explicites pour gérer les transferts HBAR.
- Pour désactiver complètement les opérations natives, envisagez de soumettre une [proposition d'amélioration Hedera (HIP)](https://hips.hedera.com/).

Comprendre ces différences est crucial pour quiconque développe des contrats intelligents sur Hedera, en particulier ceux qui connaissent Ethereum.

***

## Déploiement de votre Contrat Intelligent

**SDK**

Vous pouvez utiliser un [SDK Hedera](../../../sdks-and-apis/sdks/) pour déployer votre bytecode de contrat intelligent sur le réseau. Cette approche ne nécessite pas l'utilisation d'outils EVM comme Hardhat ou une instance du relais JSON-RPC Hedera.

{% content-ref url="../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md" %}
[deploy-your-first-smart-contract.md](../../../tutorials/smart-contracts/deploy-your-first-smart-contract.md)
{% endcontent-ref %}

**Hardhat**

Hardhat peut être utilisé pour déployer votre contrat intelligent en pointant vers un [relais JSON-RPC](json-rpc-relay.md). Cependant, les outils EVM ne prennent pas en charge les fonctionnalités natives des contrats intelligents Hedera comme:

- Clé Admin
- Mémo du Contrat
- Associations automatiques de jetons
- Renouvellement automatique de l'ID du compte
- ID de nœud de prélèvement ou ID de compte
- Refuser les récompenses de prise

Si vous avez besoin de définir l'une des propriétés ci-dessus pour votre contrat, vous devrez appeler l'API `ContractCreateTransaction` en utilisant l'un des [SDK Hedera. (../../../sdks-and-apis/sdks/)

{% content-ref url="../../../tutorials/smart-contracts/deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md" %}
[deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md](../../../tutorials/smart-contracts/deploy-a-smart-using-hardhat-hedera-json-rpc-relay.md)
{% endcontent-ref %}

***

## Foire aux questions

<details>

<summary><strong>Puis-je utiliser des fonctions Solidity directement avec l’EVM Hedera ?</strong></summary>

Oui, vous pouvez utiliser les fonctions Solidity directement avec Hedera EVM. Cependant, reportez-vous à la table [Solidity Variables and Opcodes](./#solidity-variables-and-opcodes) pour comprendre toute modification des descriptions d'opcode qui reflètent mieux leur comportement sur le réseau Hedera.

</details>

<details>

<summary><strong>Que dois-je faire si mon contrat repose sur des codes de blobs ?</strong></summary>

Si votre contrat repose sur des codes opcodes associés au blob, introduits dans le fork dur de Cancun, vous pouvez tout de même le déployer sur Hedera. Les codes de blobs **ne vont pas** échouer. Ils retourneront les valeurs par défaut comme [spécifié par l'EVM](https://www.evm.codes/?fork=cancun).&#x20

</details>

<details>

<summary><strong>Y a-t-il des considérations spéciales pour utiliser des opcodes EVM mis à jour sur Hedera?</strong></summary>

Oui, alors que le EVM d'Hedera prend en charge les opcodes mis à jour du fork dur de Cancun, vous devriez connaître les coûts intrinsèques du gaz et les coûts des données d'entrée propres à Hedera. Reportez-vous au tableau des [horaires et frais de gaz](gas-and-fees.md) pour plus d'informations.

</details>
