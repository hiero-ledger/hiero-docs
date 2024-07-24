# Création de contrats intelligents

Un [contrat intelligent](../../support-and-community/glossary.md#smart-contract) est un programme immuable composé d'un ensemble de logiques (variables d'état, fonctions, gestionnaires d'événements, etc.) ou des règles qui peuvent être déployées, stockées et accédées sur une [technologie distribuée de livre](../../support-and-community/glossary.md#distributed-ledger-technology-dlt) comme Hedera. Les fonctions contenues dans un contrat intelligent peuvent mettre à jour et gérer l'état du contrat et lire les données du contrat déployé. Ils peuvent également créer et appeler d'autres fonctions de contrats intelligents du réseau. Les contrats intelligents sont sécurisés, étanches et transparents, offrant un nouveau niveau de confiance et d'efficacité.

Hedera prend en charge tous les langages qui se compilent sur le réseau principal Ethereum. Ceci inclut [Solidity](../../support-and-community/glossary.md#solidity) et [Vyper](../../support-and-community/glossary.md#vyper). Ces langages de programmation compilent du code et produisent [bytecode](../../support-and-community/glossary.md#bytecode) que la [machine virtuelle Ethereum (EVM)](../../support-and-community/glossary.md#ethereum-virtual-machine-evm) peut interpréter et comprendre.

- Pour en savoir plus sur le langage de programmation Solidity, consultez la documentation maintenue par l'équipe Solidity [here](https://docs.soliditylang.org/en/v0.8.19/).
- Pour en savoir plus sur Vyper, consultez la documentation maintenue par l'équipe Vyper [here](https://docs.vyperlang.org/en/stable/).

De plus, de nombreux outils sont disponibles pour écrire et compiler des contrats intelligents, y compris le populaire [Remix IDE](../../support-and-community/glossary.md#remix-ide) et [Hardhat](../../support-and-community/glossary.md#hardhat). L'IDE Remix est une plateforme conviviale qui vous permet d'écrire et de compiler facilement vos contrats intelligents et d'effectuer d'autres tâches telles que le débogage et le test. En utilisant ces outils, vous pouvez créer des contrats intelligents puissants et sécurisés qui peuvent être utilisés à diverses fins, de simples transferts de jetons à des instruments financiers complexes.

**Exemple**

Ce qui suit est un exemple très simple de contrat intelligent écrit dans le langage de programmation Solidity. Le smart contract définit les variables d'état `owner` et `message`, avec des fonctions comme `set_message` (qui modifie les détails d'état en écrivant) et `get_message`(qui lit les détails d'état).

```solidity
solidité pragma >=0.7.0 <0.8. ;

Contrat HelloHedera {
    // le propriétaire du contrat, défini dans le constructeur
    propriétaire d'adresse;

    // le message que nous stockons dans la chaîne
    ;

    constructor(string memory message_) {
        // définit le propriétaire du contrat pour `kill()`
        owner = msg. ender;
        message = message_;
    }

    fonction set_message(string memory message_) public {
        // ne permet au propriétaire de mettre à jour le message
        si (msg. finale! owner) retourne;
        message = message_;
    }

    // retourne une chaîne
    function get_message() public view (string memory) {
        return message;
    }
}
```

***

## Choses que vous devriez considérer lors de la création d'un contrat

**Associations automatiques de jetons**

Un créneau d'auto-association est un ou plusieurs créneaux que vous approuvez qui permettent d'envoyer des jetons à votre contrat sans autorisation explicite pour chaque type de jeton. Si cette propriété n'est pas définie, vous devez approuver chaque jeton avant qu'il ne soit transféré au contrat pour que le transfert soit réussi via la `TokenAssociateTransaction` dans les SDKs. En savoir plus sur les associations de jetons automatiques [here](../accounts/account-properties.md#automatic-token-associations).

Cette fonctionnalité est exclusivement accessible lors de la configuration d'une API `ContractCreateTransaction` via les SDK Hedera. Si vous déployez un contrat sur Hedera en utilisant des outils EVM tels que Hardhat et le relais RPC JSON Hedera, Veuillez noter que cette propriété ne peut pas être configurée, car les outils EVM ne sont pas compatibles avec les fonctionnalités uniques de Hedera.

**Clé d'administration**

Les contrats ont la possibilité d'avoir une [clé d'admin](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_create.proto#L117). Ce concept est natif des contrats Hedera et permet de mettre à jour les propriétés du compte contractuel. Notez que cela n'affecte pas le contrat [bytecode](../../support-and-community/glossary.md#bytecode) et ne concerne pas la mise à niveau. Si la clé d'administration n'est pas définie, vous ne serez pas en mesure de mettre à jour les propriétés natives Hedera suivantes (notées dans [ContractUpdateTransactionBody](https://github. om/hashgraph/hedera-protobufs/blob/main/services/contrat\_update.proto) protobuf) pour votre contrat une fois déployé :

- [`autoRenewPeriod`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L78)
- [`memoField`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L88)
- [`max_automatic_token_associations`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L105)
- [`auto_renew_account_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L111)
- [`staked_id`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L116)
- [`decline_reward`](https://github.com/hashgraph/hedera-protobufs/blob/main/services/contract\_update.proto#L134)

Vous ne pouvez pas définir le champ clé admin si vous déployez un contrat via des outils tels que Hardhat. Ce champ peut être défini si désiré en déployant un contrat en utilisant l'un des Hedera [SDKs](../../sdks-and-apis/sdks/).&#x20

**Taille maximale du contrat de stockage**

Les contrats sur Hedera ont une limite de stockage de 16 384 000 paires clés (\~100MB).&#x20

**Louer**

Bien que le loyer ne soit pas activé pour les contrats déployés aujourd'hui sur Hedera, vous voudrez être familier avec le concept de loyer, car cela peut avoir un impact potentiel sur les coûts de la maintenance de votre contrat sur le réseau. Veuillez vous référer à la documentation de Smart Contract Rent Rent [here](smart-contract-rent.md).

**Frais de transaction et de gaz**

Il y a des frais de transaction Hedera et des frais EVM associés au déploiement d'un contrat. Pour consulter la liste des frais de base, consultez la page de frais [here](../../networks/mainnet/fees/) et la calculatrice de frais [here](https://hedera.com/fees).

***

## FAQ sur les Contrats Intelligents

<details>

<summary>What is a smart contract?</summary>

Un contrat intelligent est un programme écrit dans une langue qui peut être interprétée par l'EVM. Veuillez vous référer à [glossary](../../support-and-community/glossary.md) pour plus de mots-clés et de définitions.

</details>

<details>

<summary>What programming language does Hedera support for smart contracts?</summary>

Hedera supporte Solidity et Vyper.

</details>

<details>

<summary>Can I write and compile my smart contracts using Remix IDE or other Ethereum ecosystem tools? </summary>

Vous pouvez utiliser Remix IDE ou d'autres outils écosystèmes Ethereum pour écrire, compiler et déployer votre contrat intelligent sur Hedera. Consultez nos outils compatibles EVM [here](../../#evm-compatible-tools).&#x20

</details>

<details>

<summary>Where can I find the smart contracts that are deployed to each Hedera network (previewnet, testnet, mainnet)?</summary>

Sur votre explorateur de blocs de confiance préféré (également appelé Explorateur de nœuds miroir en Hedera). Pour voir les explorateurs hébergés par la communauté, consultez la page [here](../../networks/community-mirror-nodes.md).&#x20

</details>

<details>

<summary>Which ERC token standards are supported on Hedera?</summary>

Hedera supporte les normes de jetons ERC-20 et ERC-721 et peut trouver la liste complète des normes supportées [here](tokens-managed-by-smart-contracts/).

</details>
