# Clés et signatures

## Types de clés : ECDSA vs Ed25519

Une clé peut être une [clé publique](../support-and-community/glossary.md#public-key) d'un système [Ed25519](../support-and-community/glossary.md#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k1), ou un [contrat intelligent](../support-and-community/glossary.md#smart-contract). L'algorithme correspondant génère des clés publiques et privées uniques les unes aux autres. La clé publique peut être partagée et visible aux autres utilisateurs du réseau dans un [Explorateur de réseau](../support-and-community/glossary.md#network-explorer) ou [API REST](../support-and-community/glossary.md#rest-api). La [clé privée](../support-and-community/glossary.md#private-key) est gardée secrète du propriétaire et donne accès au propriétaire pour modifier les entités (comptes, jetons, etc.).&#x20

Les clés privées _ne peuvent être récupérées qu'une fois perdues si elles sont créées avec une phrase de récupération associée à laquelle vous pouvez accéder. Les clés sont mutables et peuvent être mises à jour une fois définies pour une entité. Généralement, vous aurez besoin de la clé actuelle pour signer la transaction pour mettre à jour les clés.&#x20

### Choix entre les clés ECDSA et Ed25519

Le choix entre les clés ECDSA et ED25519 dépend principalement de votre cas d'utilisation spécifique :

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>ECDSA</strong></td><td><strong>Ed25519</strong></td></tr><tr><td><strong>Cas d'utilisation</strong></td><td>Idéal si vous voulez utiliser <a href="../support-and-community/glossary.md#metamask">MetaMask</a> ou si vous avez besoin de support pour plus d'outillage de développeurs EVM. Convient pour les applications interfacées avec des réseaux compatibles Ethereum ou EVM en raison de l'adresse EVM associée.</td><td>Préférée si la longueur des clés, la sécurité et la performance sont importantes. Les clés publiques ECDSA sont deux fois plus longues pour le même niveau de sécurité.</td></tr></tbody></table>

{% hint style="info" %}
**Note** : Les portefeuilles Hedera tels que [HashPack](https://www.hashpack.app/) prennent en charge les deux types de clés.
{% endhint %}

## Structures clés

Hedera prend en charge les types de structure clés suivants :

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th width="240"></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Description</strong></td><td><strong>Exemple</strong></td></tr><tr><td><strong>Clé simple</strong></td><td>Une seule clé sur un compte.</td><td><strong>Compte</strong> <strong>Clé</strong> <br>       { <br>           Clé 1 <br>        }<br>Une seule clé est requise pour signer le compte.</td></tr><tr><td><strong>Liste de clés</strong></td><td>Toutes les clés de la liste de clés sont requises pour signer les transactions impliquant le compte.</td><td><strong>Clé du cpte client</strong><br>     <strong>Liste de clés (3/3)</strong> <br>          { <br>               Clé 1 <br>               Clé 2 <br>               Clé 3 <br>          }<br>Les trois clés de la liste sont requises pour signer pour le compte.</td></tr><tr><td><strong>Seuil de clé</strong></td><td>Un sous-ensemble de clés défini comme le seuil est requis pour signer la transaction qui implique le compte hors du nombre total de clés.</td><td><strong>Clé du cpte client</strong><br>      <strong>ThresholdKey (1/3)</strong> <br>          { <br>              Clé 1 <br>              Clé 2 <br>              Clé 3 <br>          }<br>Une des trois clés de la liste des clés est requise pour signer pour le compte.</td></tr></tbody></table>

{% hint style="info" %}
🔔 Les structures clés peuvent être imbriquées. Cela signifie que vous pouvez avoir un système de clés plus complexe avec des listes de clés à l'intérieur des clés de seuil, des clés de seuil dans les listes de clés, etc. Un exemple de liste de clés imbriquées peut être vu [here](https://hashscan.io/mainnet/adminKey/0.0.2).
{% endhint %}

Tous les types de transaction supportent les structures clés ci-dessus qui spécifient un champ clé. Pour qu'une transaction soit réussie, les signatures fournies doivent correspondre aux exigences de la structure clé définie.

## Foire Aux Questions

<details>

<summary>What is a key in Hedera?</summary>

Une clé dans Hedera peut être une [clé publique](../support-and-community/glossary.md#clé publique) d'un système pris en charge comme [ED25519](../support-and-community/glossary. d#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k), ou un [contrat intelligent](../support-and-community/glossary.md#smart-contract). L'algorithme correspondant génère des clés publiques et privées uniques les unes aux autres. La clé publique peut être partagée et visible aux autres utilisateurs du réseau dans un [Explorateur de réseau](../support-and-community/glossary.md#network-explorer) ou des API REST. La [clé privée](../support-and-community/glossary.md#private-key) est gardée secrète et donne accès au propriétaire pour modifier les entités (comptes, jetons, etc.).

</details>

<details>

<summary>What happens if I lose my private key?</summary>

Les clés privées ne peuvent être récupérées qu'une fois perdues si elles sont créées avec une phrase de récupération associée à laquelle vous pouvez accéder. Il est crucial de sécuriser et sécuriser vos clés privées en leur donnant accès pour modifier vos entités Hedera, comme les comptes et les jetons.

</details>
