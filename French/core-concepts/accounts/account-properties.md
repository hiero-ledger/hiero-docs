# Propriétés du compte

## ID du compte client

L'ID du compte est l'ID de l'entité du compte sur le réseau Hedera. L'ID du compte comprend le **numéro d'éclat**, le **numéro de royaume** et un **compte** <mark style="color:purple;">`<shardNum>.<realmNum><account>`</mark>**.** L'ID du compte est utilisé pour spécifier le compte dans toutes les transactions et requêtes Hedera. Il peut y avoir plus d'un ID de compte qui peut représenter un compte.\

<details>

<summary>Nombre de fragments</summary>

Format: **`shardNum`**.realmNum.account\`

Le numéro de fragment est le numéro du fragment dans lequel le compte existe. Un fragment est une partition des données reçues par les nœuds participant à un fragment donné.  Aujourd'hui, Hedera ne fonctionne qu'en un seul fragment. Cette valeur restera nulle jusqu'à ce que Hedera opère dans plus d'un fragment. Cette valeur est non négative et fait 8 octets.\
\
Par défaut: `0`

</details>

<details>

<summary>Numéro de domaine</summary>

Format: `shardNum.`**`realmNum`**`.account`\
\
Le numéro du royaume est le numéro du royaume que le compte existe dans un fragment donné. Aujourd'hui, Hedera opère dans un seul domaine. Cette valeur restera nulle jusqu'à ce que Hedera opère dans plus d'un fragment. Cette valeur est non négative et fait 8 octets. Le compte ne peut appartenir qu'à un seul royaume. L'ID du royaume peut être réutilisé dans d'autres fragments. \
\
Par défaut: `0`

</details>

<details>

<summary><strong>Account</strong></summary>

Format: `shardNum.realmNum.`**`account`**

Le `account` peut être l'un des suivants :\
\
:black\_circle: [Numéro de compte](account-properties.md#account-number) \
:black\_circle: [Compte Alias](account-properties.md#account-alias)

</details>

### **Numéro de compte**

Chaque compte Hedera a un **numéro de compte** fourni par le système lorsque le compte est créé.  Un numéro de compte est un nombre non négatif de 8 octets. Vous pouvez utiliser le numéro de compte pour spécifier le compte dans toutes les transactions Hedera et requêtes de requête. Les numéros de compte sont uniques et immuables. Le numéro de compte pour un compte nouvellement créé est retourné dans le reçu de transaction ou l'enregistrement de l'opération pour l'ID de l'opération qui a créé le compte. L'ID du numéro de compte a le format suivant  <mark style="color:purple;">`<shardNum>.<realmNum>.<accountNum>`</mark><mark style="color:blue;">.</mark>

<table><thead><tr><th width="199">ID du numéro de compte</th><th width="523.3333333333333">Libellé</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>Le numéro de compte 10 au format d'identification du numéro de compte.</td></tr></tbody></table>

#### Alias de numéro de compte

Tous les comptes peuvent avoir un \*\*alias de numéro de compte. \* Un alias de numéro de compte est une forme hexadécidée du numéro de compte préfixé avec 20 octets de zéros. C'est une adresse compatible EVM qui fait référence au compte Hedera. L'alias du numéro de compte ne contient pas l'ID du fragment et l'ID du royaume.&#x20

Cette propriété du compte n'est pas stockée dans l'état du nœud de consensus. Vous ne verrez pas cette valeur retournée lorsque vous interrogerez les nœuds de consensus pour l'objet du compte et que vous inspecterez le champ alias du compte.
\
Le noeud miroir va calculer le numéro de compte alias à partir du numéro de compte. L'alias du numéro de compte est calculé et retourné dans les API REST du compte uniquement si le compte n'a pas d'alias de compte existant. Par exemple, si le compte a été créé grâce au flux [création automatique de compte](auto-account-creation.md) en utilisant un alias de compte, l'alias du numéro de compte ne sera pas rempli. Si le compte a été créé normalement, alors le champ alias du compte conservera l'alias du numéro de compte.

<table><thead><tr><th width="175">ID du compte client</th><th>Exemple d'alias de numéro de compte</th></tr></thead><tbody><tr><td><code>0.0.10</code></td><td>La valeur d'encodage hexadécimal pour 10 est "0a."<br><br><code>0000000000000000000000000000000000000000000000000000000a</code></td></tr></tbody></table>

### Alias du compte

Certains comptes Hedera auront un **alias de compte**. Les alias de compte sont un pointeur vers l'objet du compte en plus d'être identifié par le numéro de compte. Les alias de compte sont assignés au compte lorsque le compte est créé via le flux [création automatique du compte](./#auto-account-creation). Le réseau ne génère pas l'alias du compte ; à la place, l'utilisateur spécifie l'alias du compte lors de la création du compte. Cette propriété sera nulle si un compte est créé via le flux de création de compte normal. Les alias de compte sont uniques et immuables. L'identifiant de l'alias du compte a le format suivant  <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark><mark style="color:blue;">.</mark>

Ce format n'est acceptable que si spécifié dans le `TransferTransaction`, `AccountInfoQuery` et `AccountBalanceQuery`. Si ce format est utilisé pour référencer un compte dans un autre type de transaction, la transaction ne réussira pas.&#x20

Le `alias` peut être l'un des types d'alias suivants :

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>               Clé publique</td><td><a href="account-properties.md#public-key-account-alias">#clé publique-pseudonyme</a></td></tr><tr><td>             Adresse EVM</td><td><a href="account-properties.md#public-key-account-alias">#clé publique-pseudonyme</a></td></tr></tbody></table>

#### Alias de cpte client public

La clé publique de l'alias du compte est la clé publique d'un type de clé ECDSA secp256k1 ou ED25519. Le format d'identification du compte de la clé publique est <mark style="color:blue;">`<shardNum>.<realmNum>.<alias>`</mark> où <mark style="color:blue;">`alias`</mark> est la clé publique. Ce format est créé en utilisant les octets d'une simple clé publique cryptographique supportée par Hedera. L'alias de compte de la clé publique n'est pas requis pour correspondre à la [clé publique](account-properties. d#keys) utilisés pour déterminer la signature cryptographique nécessaire pour signer les transactions du compte.

<details>

<summary>Exemple d'alias de clé publique ID de compte</summary>

`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`

</details>

#### **Alias du compte d'adresse EVM**

Un alias de compte d'adresse EVM est le plus à droite 20 octets du hash `Keccak-256` de 32 octets de la clé publique `ECDSA` du compte. Ce calcul se fait de la manière décrite par le [Livre Jaune Ethereum](https://ethereum.github.io/yellowpaper/paper.pdf). Notez que l'identifiant de récupération ne fait pas officiellement partie de la clé publique et n'est pas inclus dans le hash. Ceci est calculé sur les nœuds consensuels en utilisant la clé `ECDSA` fournie dans le flux [création automatique du compte](auto-account-creation.md).  L’adresse EVM est également connue sous le nom d’adresse publique. Le format d'ID du compte d'adresse EVM est <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> où <mark style="color:purple;">`alias`</mark> est l'adresse EVM.

Le compte d'adresse EVM et l'[alias du numéro de compte](account-properties.md#account-number-alias) sont des valeurs de 20 octets. Ils peuvent être différenciés car l'alias du numéro de compte est toujours préfixé par 12 octets. L'alias du compte d'adresse EVM est couramment utilisé dans les portefeuilles et les outils pour représenter les adresses du compte.&#x20

<details>

<summary>EVM Adresse Compte Compte Alias ID Compte Exemple</summary>

Le numéro du fragment et le numéro du royaume sont à 0 suivi de l'adresse EVM.&#x20

\
**Exemple**\
\
Adresse EVM : `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Adresse EVM encodée EVM : `0xb794f5ea0ba39494ce839613fffba74279579268`\
\
EVM Adresse Compte Alias ID de compte client : \
\
`0. .b794f5ea0ba39494ce839613fffba74279579268`

</details>

Référence de la proposition d'amélioration de Hedera : [HIP-583](https://hips.hedera.com/hip/hip-583)

## Mémo du compte

Un mémo est comme une courte note qui vit avec l'objet du compte dans l'état du registre et peut être vu sur un explorateur de réseau lors de la recherche du compte. Ce mémo de compte est limité à 100 caractères. Le mémo du compte est mutable et peut être mis à jour ou supprimé du compte à tout moment. La clé du compte est requise pour signer la transaction afin de faciliter tout changement à cette propriété.

{% hint style="warning" %}
Ne publiez aucune information privée dans le champ Mémo du compte. Ce champ est visible par tous les participants du réseau.&#x20;
{% endhint %}

## Nonce du compte client

Les comptes sur Hedera peuvent soumettre les types `EthereumTransaction` traités par la machine virtuelle Ethereum (EVM) sur un nœud consensuel. Le nonce sur le compte représente un nombre séquentiellement incrémenté des transactions soumises par un compte par le type `EthereumTransaction`. La valeur par défaut du compte nonce est définie à zéro.

Référence de la proposition d'amélioration de Hedera : [HIP-410](https://hips.hedera.com/hip/hip-410)

## Associations automatiques de jetons

Les comptes Hedera doivent généralement approuver les jetons personnalisés avant de les transférer sur le compte de réception. Le compte récepteur doit signer la transaction qui va associer les jetons, permettant aux jetons spécifiés d'être déposés dans leur compte. La fonctionnalité d'association automatique de jetons permet au compte de contourner l'association manuelle du jeton personnalisé avant de le transférer vers le compte. &#x20

Les comptes peuvent automatiquement approuver jusqu'à 5 000 jetons sans autoriser manuellement chaque jeton personnalisé. Supposons qu'un compte ait besoin de conserver un solde pour les jetons personnalisés supérieurs à 5 000. Dans ce cas, le compte doit approuver manuellement chaque jeton supplémentaire à l'aide de la transaction pour associer les jetons. Il n'y a pas de limite sur le nombre total de jetons qu'un compte peut conserver. Cette propriété est mutable et peut être modifiée après qu'elle soit définie.

Référence de la proposition d'amélioration de Hedera : [HIP-23](https://hips.hedera.com/hip/hip-23)

## Soldes

Lorsqu'un nouveau compte est créé, vous pouvez spécifier un solde initial de HBAR pour le compte. Le solde initial du HBAR pour le jeton est déduit du compte qui paie pour créer le nouveau compte. La création d'un compte avec un solde initial est optionnelle.&#x20

Un compte Hedera peut contenir un solde de HBAR et de jetons fongibles et non fongibles (FTP). Les soldes de comptes peuvent être consultés sur un [Explorateur de réseau](../../networks/community-mirror-nodes.md) et interrogés à partir des API REST du nœud miroir ou des nœuds de consensus.

| Type de jeton                                    | Libellé                                                                                                            | Exemple d'ID de jeton                                                                                              |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| **HBAR**                                         | Le jeton fungible natif Hedera utilisé pour payer les frais de transaction et sécuriser le réseau. | Aucun                                                                                                              |
| **Jeton Fungible Token**                         | Jetons fongibles personnalisés créés sur Hedera.                                                   | <p>L'identifiant de jeton fongible est représenté comme <code>0.tokenNum</code> <br><br>ex: <code>0.100</code></p> |
| **Jeton non-Fungible (NFTs)** | Jetons non-fongibles personnalisés (NFTs) créés sur Hedera.                     | <p>L'ID NFT est représenté comme <code>0.0.tokenNum-serialNum</code>.<br></p><p>ex: <code>0.101-1</code></p>       |

## Clés

Chaque compte doit avoir au moins une clé lors de la création. Si une clé n'est pas fournie au moment de la création du compte, le réseau rejettera la transaction. Les personnes qui ont accès à la ou aux clés privées du compte ont accès pour autoriser le transfert de jetons dans ou hors du compte et sont tenues de signer les transactions qui modifient le compte. La modification du compte comprend le changement de propriété, comme le solde, les clés, le mémo, etc.

Les comptes peuvent optionnellement avoir plus d'une clé associée. Ces types de comptes sont des comptes multi-signatures, ce qui signifie que vous aurez besoin de plus d'une clé pour signer la transaction pour changer de propriété sur un compte ou des HBAR débiteurs. Les conditions de signature pour un compte multi-signature dépendent de la structure de clé choisie par le compte. Pour prendre en charge les structures clés et les types de clés, suivez le lien ci-dessous.

{% content-ref url="../keys-and-signatures.md" %}
[keys-and-signatures.md](../keys-and-signatures.md)
{% endcontent-ref %}

{% hint style="danger" %}
Attention : La ou les clés privées associées au compte ne doivent pas être partagées avec qui que ce soit, car cela permettra à d'autres personnes d'autoriser des transactions depuis votre compte en votre nom. Partager votre clé privée est comme partager le mot de passe de votre compte bancaire. Veuillez vous assurer que vos clés privées sont stockées dans un portefeuille sécurisé.
{% endhint %}

## Signature du destinataire requise

Les comptes peuvent optionnellement demander au compte de signer les transactions qui déposent des jetons dans le compte. Cette fonction est définie à false par défaut. Si cette fonction est activée, le compte sera requis pour signer toutes les transactions qui déposent des jetons sur le compte. Cette propriété est mutable et peut être mise à jour après la création du compte.

## Staking

Staking in Hedera prend en compte et associe le solde HBAR à un noeud du réseau. Les soldes de jetons fongibles ou non fongibles personnalisés qu'un compte détient ne contribuent pas au positionnement sur le réseau. Le but du staking des comptes vers un noeud sur le réseau est de renforcer la sécurité du réseau. Pour contribuer à la sécurité du réseau, les comptes misés peuvent gagner des récompenses dans HBAR. Veuillez consulter cette [guide](https://docs.hedera.com/hedera/core-concepts/staking) pour plus d'informations sur le programme de récompenses de staking. Les contrats peuvent aussi miser leurs comptes pour gagner des récompenses.

{% hint style="info" %}
Un compte ne peut être mis en jeu que sur un seul noeud ou un seul compte à la fois.
{% endhint %}

<details>

<summary>Nœud pris ID</summary>

Un compte peut optionnellement choisir de mettre son HBAR à un noeud du réseau Hedera. L'ID du noeud misé est le noeud auquel un compte peut être attaché. Le solde complet du compte est misé sur le noeud. Ne confondez pas l'ID du noeud avec l'ID du compte. Si vous misez sur l'ID du compte du nœud, votre compte ne gagnera pas de récompenses de mise en jeu. \
\
Le solde du compte misé est liquide en tout temps. Cela signifie que vous pouvez transférer des jetons HBAR dans et hors du compte, et votre compte continuera d'être misé sur le noeud sans perturbation. \
\
Il n'y a pas de période de verrouillage. Cela signifie que les jetons HBAR de votre compte ne sont pas conservés pendant une période de temps avant que vous ne puissiez les utiliser. \
\
L'ID du noeud d'un noeud peut être trouvé [here](https://docs.hedera.com/hedera/networks/mainnet/mainnet-nodes) ou peut être interrogé depuis [nodes REST API](https://testnet. irrornode.hedera.com/api/v1/docs/#/network/getNetworkNodes).\Exemple :\
\
ID de nœud : `1`\

</details>

<details>

<summary>Staked Account ID</summary>

Un compte peut optionnellement choisir de mettre son HBAR sur un autre compte dans le réseau Hedera. Ceci est connu sous le nom de **mise indirecte**. L'ID du compte misé est l'ID du compte auquel il faut miser. Le solde complet du compte est misé sur le compte spécifié. \
\
Il n'y a pas de période de verrouillage et le solde est toujours liquide, tout comme le piquage sur un nœud. \
\
Comptes que la mise sur un autre compte ne gagne pas les récompenses de staking. Par exemple, si le compte A est misé sur le compte B, le compte B devra être misé sur un noeud afin de contribuer à la sécurité du réseau et de gagner des récompenses de mise en jeu. Le compte B gagnera les récompenses pour le pari sur un noeud pour les deux soldes HBAR dans le compte A + Compte B. Le compte A ne gagnera pas de récompense en misant. \
\
Exemple :\
\
ID du cpte client : `0.0.10`

</details>

<details>

<summary>Decline to Earn Staking Rewards</summary>

Les comptes peuvent refuser de gagner des récompenses de staking lorsqu'ils misent sur un nœud ou un compte. Le compte misé contribue encore au poids du noeud, mais ne gagne pas de récompenses ou est calculé dans le cadre du paiement des récompenses aux autres comptes qui ont choisi pour gagner des récompenses. Par défaut, tous les comptes misés gagneront des récompenses à moins que ce drapeau booléen soit défini sur true. Cette élection peut être modifiée en mettant à jour les propriétés du compte. Les comptes de trésorerie de Hedera permettent à ce drapeau de refuser de gagner des récompenses de mise en jeu.
\
Par défaut: `true` (tous les comptes acceptent de gagner des récompenses de staking si le compte est misé)

</details>

### Information sur la prise en charge

Le réseau stocke les métadonnées de staking pour un compte et des comptes contractuels. Ces informations sont retournées dans les demandes de requête d'information du compte (`AccountInfoQuery` ou`ContractInfoQuery`). Les métadonnées de staking pour un compte incluent les informations suivantes :

- **refuser\_reward:** si le compte a refusé de gagner des récompenses de mise en jeu
- **stake\_period\_start:** La période de staking pendant laquelle soit les paramètres de staking pour ce compte soit le contrat ont changé (comme le début du staking ou le changement de staked\_node\_id) ou la récompense la plus récente a été gagnée, quelle que soit la date postérieure. Si ce compte ou ce contrat n'est pas mis sur un nœud, alors ce champ n'est pas défini. La période de mise en jeu est de 24 heures, à partir de minuit UTC.
- **En attente\_reward:** Le montant en tiroirs qui sera reçu lors du prochain paiement de récompense de staking
- **staked\_to\_me:** Le solde total de la barre minuscule de tous les comptes misés sur ce compte
- **staked\_id:** ID du compte ou du noeud auquel ce compte ou ce contrat est en cours
  - **mis en jeu\_account\_id:** Le compte auquel ce compte ou ce contrat est lié
  - **staked\_node\_id:** L'ID du noeud auquel ce compte ou ce contrat est misé

Référence de la proposition d'amélioration de Hedera : [HIP-406](https://hips.hedera.com/hip/hip-406)

## Renouvellement automatique & **Expiration**

{% hint style="warning" %}
Les renouvellements automatiques et l'expiration (loyer) ne sont pas activés.
{% endhint %}

Comme les autres entités de Hedera, les comptes prennent du stockage sur le réseau. Pour couvrir les frais de stockage d'un compte, des frais de renouvellement seront facturés pour le stockage utilisé sur le réseau. Cette fonctionnalité n'est pas activée sur le réseau aujourd'hui; Toutefois, à l'avenir, lorsqu'il est activé, le compte doit disposer de fonds suffisants pour payer les frais de renouvellement. #x20

Le montant facturé pour le renouvellement sera facturé chaque période prédéterminée en secondes. L'intervalle de temps où le compte sera débité est la période de renouvellement automatique. Le système facturera automatiquement les frais de renouvellement du compte. Si le compte n'a pas de solde HBAR, il sera suspendu pendant une semaine avant qu'il ne soit supprimé du livre. Vous pouvez renouveler un compte pendant la période de suspension.

<details>

<summary> Temps d'expiration</summary>

L'horodatage du consensus effectif à (et après) auquel l'entité est configurée pour expirer.

</details>

<details>

<summary><strong>Renouvellement automatique du compte</strong></summary>

Le compte de renouvellement automatique est le compte qui sera utilisé pour payer les frais de renouvellement automatique. S'il n'y a pas de compte de renouvellement automatique spécifié, les frais de renouvellement de l'auto seront facturés au compte.

</details>

<details>

<summary><strong>Renouvellement automatique de la période</strong></summary>

L'intervalle auquel ce compte sera facturé les frais de renouvellement automatique. La période maximale de renouvellement automatique pour un compte est limitée à 3 mois (8000001 secondes). La période minimale de renouvellement automatique est de 30 jours. La période de renouvellement automatique est mutable et peut être mise à jour à tout moment. Si les fonds sont insuffisants, ils sont aussi longs que possible. S'il est vide lorsqu'il expire, il sera supprimé.

</details>

Référence de la proposition d'amélioration de Hedera : [HIP-16](https://hips.hedera.com/hip/hip-16)
