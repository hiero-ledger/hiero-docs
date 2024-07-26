# Création automatique de compte

La création de comptes automatiques est un flux unique dans lequel les applications, telles que les portefeuilles et les échanges, peuvent créer instantanément des comptes utilisateurs gratuits, même sans connexion Internet. Applications can make these by generating an **account alias.** The alias account ID format used to specify the account alias in Hedera transactions comprises the shard ID, realm ID, and account alias <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark>. Ceci est un identifiant de compte alternatif comparé au format de numéro de compte standard <mark style="color:purple;">`<shardId>.<realmId>`<accountNum>\`</mark><mark style="color:blue;">.</mark>

L'alias du compte peut être l'un des types pris en charge :

<details>

<summary>Clé publique</summary>

L'alias de la clé publique peut être un type de clé publique ED25519 ou ECDSA secp256k1. \
\
**Example**\
\
ECDSA secp256k1 Public Key:\
`02d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
DER Encoded ECDSA secp256k1 Public Key Alias:\
`302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`\
\
ECDSA secp256k1 Public Key Alias Account ID: \
`0.0.302d300706052b8104000a03220002d588ec1000770949ab77516c77ee729774de1c8fe058cab6d64f1b12ffc8ff07`
\
\
\
\
EDDSA ED25519 Public Key:\
`1a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
DER Encoded EDDSA ED25519 Public Key Alias:\
`302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`\
\
EDDSA ED25519 Public Key Alias Account ID: \
`0.0.302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f`

</details>

<details>

<summary>Adresse EVM</summary>

L'alias d'adresse EVM est créé en utilisant les 20 octets les plus à droite du hash `Keccak-256` de 32 octets d'une clé publique `ECDSA secp256k1`. Ce calcul se fait de la manière décrite par le [Livre Jaune Ethereum](https://ethereum.github.io/yellowpaper/paper.pdf). L'adresse EVM n'est pas équivalente à la clé publique ECDSA. \
\
Le format acceptable pour les transactions Hedera est l'ID du compte d'alias d'adresse EVM. Le format acceptable pour les adresses publiques Ethereum pour désigner une adresse de compte est l'adresse publique encodée en hexa. \
\
**Exemple**\
\
Adresse EVM : `b794f5ea0ba39494ce839613fffba74279579268`\
\
HEX Adresse EVM encodée EVM : `0xb794f5ea0ba39494ce839613fffba74279579268`\EVM Adresse Alias Account ID: `0. .b794f5ea0ba39494ce839613fffba74279579268`

</details>

Le format <mark style="color:purple;">`<shardNum>.<realmNum>.<alias>`</mark> n'est acceptable que s'il est spécifié dans les types de transaction `TransferTransaction`, `AccountInfoQuery`, et `AccountBalanceQuery`. Si ce format est utilisé pour spécifier un compte dans un autre type de transaction, la transaction ne réussira pas.&#x20

Référence de la proposition d'amélioration de Hedera : [HIP-583](https://hips.hedera.com/hip/hip-583)

## **Flux de création automatique de compte**

### **1. Créer un alias de compte**

Créez un alias de compte et convertissez-le au format ID du compte alias. Le format de l'identifiant du compte d'alias nécessite l'ajout du numéro de fragment et des numéros de Realm à l'alias du compte. Ce formulaire de compte est purement un compte local, c'est-à-dire qu'il n'est pas enregistré sur le réseau Hedera.&#x20

### **2. Déposez des jetons sur l'ID du compte Alias du compte**

Une fois que l'identifiant du compte alias est créé, les applications peuvent créer une transaction pour transférer des jetons vers l'ID du compte alias pour les utilisateurs. Les utilisateurs peuvent transférer des jetons HBAR, fungible ou non-fongibles sur l'ID du compte alias. Cela déclenchera la création du compte officiel Hedera. Lorsque vous utilisez le flux de création de compte automatique, le premier jeton transféré à l'ID du compte alias est automatiquement associé au compte.

Le transfert initial de jetons vers l'ID du compte alias fera quelques choses:

1. Le système créera d'abord une transaction initiée par le système pour créer un nouveau compte sur Hedera. Ceci est de créer un nouveau compte sur Hedera officiellement. Cette transaction se produit une nanoseconde avant la transaction de transfert de jeton subséquente.&#x20
2. Une fois le nouveau compte créé, le système attribuera un nouveau numéro de compte et l'alias du compte sera stocké avec le compte dans le champ alias de l'état. Le nouveau compte aura un "compte auto-créé" défini dans le champ mémo du compte.
   - Pour les comptes créés en utilisant l'alias de la clé publique, le compte sera assigné à la clé publique qui correspond à la clé publique de l'alias.&#x20
   - Pour un compte créé via un alias d'adresse EVM, le compte n'aura pas de clé publique de compte, créant un [compte creux](auto-account-creation.md#auto-account-creation-evm-addresss-alias).
3. Une fois que le nouveau compte est officiellement créé, la transaction de transfert de jetons instanciée par l'utilisateur transférera les jetons vers le nouveau compte. &#x20
4. Le compte spécifié pour payer les frais de transaction de transfert de jetons sera également facturé les frais de transaction de création de compte dans tinybar.&#x20

Les interactions ci-dessus introduisent le concept de [transactions parentales et enfants](../transactions-and-queries.md#nested-transactions). La transaction parent est la transaction qui représente le transfert de jetons du compte de l'expéditeur vers le compte de destination. La transaction enfant est la transaction que le système a initiée pour créer le compte. Ce concept est important car le relevé de transaction ou le reçu parent ne retournera pas le nouveau numéro de compte. Vous devez obtenir le relevé de transaction ou la réception de la transaction enfant. Les transactions parent et enfant partageront le même ID de transaction, sauf que la transaction enfant a une valeur nonce ajoutée. &#x20

{% hint style="info" %}
**transaction parente** : la transaction responsable du transfert des jetons vers le compte de destination de l'alias ID.

**transaction enfant** : la transaction qui a créé le nouveau compte
{% endhint %}

### **3. Obtenir le nouveau numéro de compte**

Vous pouvez obtenir le nouveau numéro de compte de l'une des façons suivantes :

- Demander l'enregistrement ou le reçu de la transaction parent et définir le marquage booléen de la transaction enfant égal à vrai.&#x20
- Demander la réception ou l'enregistrement de l'opération de créer une transaction en utilisant l'ID de l'opération de transfert parent et en incrémentant la valeur du nonce de 0 à 1.
- Spécifiez l'ID du compte alias du compte dans une demande de transaction `AccountInfoQuery`. La réponse retournera l'ID du compte du compte.
- Inspectez la liste des transferts de transactions parentes pour le compte avec un transfert égal à la valeur de transfert de jeton.

## Création automatique du compte : alias d'adresse EVM

Les comptes créés avec le flux de création automatique de compte en utilisant un [alias d'adresse EVM](account-properties.md#account-alias-evm-address) résultent en la création d'un **compte creux**. Les comptes creux sont des comptes incomplets avec un numéro de compte et un alias mais pas une clé de compte. Le compte creux peut accepter des transferts de jetons dans le compte dans cet état. Il ne peut pas transférer de jetons depuis le compte ou modifier des propriétés de compte tant que la clé du compte n'a pas été ajoutée et que le compte n'est pas terminé.

Pour mettre à jour le compte creux dans un compte complet, le compte creux doit être spécifié comme un compte payeur de frais de transaction pour une transaction Hedera. Elle doit également signer la transaction avec la clé privée ECDSA correspondant à l'alias d'adresse EVM. Quand le compte creux devient un compte complet, vous pouvez utiliser le compte pour payer des frais de transaction ou mettre à jour les propriétés du compte comme vous le feriez avec des comptes Hedera réguliers.

## Exemples

<details>

<summary>Auto-create an account using a public key alias</summary>

:black\_circle: [Java](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AccountAliasExample.java) \
:black\_circle: [JavaScript](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/account-alias.js) \
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/alias\_id\_example/main.go) &#x20

</details>

<details>

<summary>Créer automatiquement un compte en utilisant une adresse EVM (adresse publique) alias</summary>

:black\_circle: [Java ](https://github.com/hashgraph/hedera-sdk-java/blob/develop/examples/src/main/java/AutoCreateAccountTransferTransactionExample.java)\
:black\_circle: [JavaScript](https://github.com/hashgraph/hedera-sdk-js/blob/develop/examples/transfer-using-evm-address.js)\
:black\_circle: [Go](https://github.com/hashgraph/hedera-sdk-go/blob/develop/examples/account\_create\_token\_transfer/main.go)

</details>
