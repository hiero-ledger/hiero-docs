# Comptes

Les comptes sont le point de départ central lorsque vous interagissez avec le réseau Hedera et que vous utilisez les services Hedera. Un compte Hedera est une entité, un type d'objet distinct, stocké dans le livre, qui contient des jetons. Les comptes peuvent contenir le jeton fungible Hedera (HBAR), le champignon personnalisé et les jetons non-fongibles personnalisés (NFTs) créés sur le réseau Hedera.&#x20

Le jeton natif Hedera HBAR (<unk> ) est un jeton utilitaire principalement utilisé pour payer les transactions et les frais de requête lors de l'interaction avec le réseau. Le symbole HBAR est représenté comme "<unk> ".  Les applications peuvent faire référence à HBAR comme la dénomination de jeton; cependant, le réseau retourne des informations dans tinybars (t<unk> ), une dénomination de HBAR. 100 000 000 t<unk> sont équivalents à 1 <unk> . Cela inclut les frais de transaction ou les soldes de HBAR des comptes.&#x20

Vous interagissez avec le réseau en soumettant des transactions qui modifient l'état du livre ou en soumettant des requêtes qui lisent les données du livre. La plupart des transactions et des requêtes ont des [frais de transaction](../../networks/mainnet/fees/) qui sont facturés en HBAR. Contrairement aux jetons personnalisés que les utilisateurs créent sur le réseau Hedera, aucun identifiant de jeton ne représente le jeton HBAR natif.&#x20

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><a href="account-creation.md">         <strong>Création de compte</strong></a></td><td><a href="account-creation.md">account-creation.md</a></td></tr><tr><td>   <a href="auto-account-creation.md"><strong>Création automatique de compte</strong></a></td><td><a href="auto-account-creation.md">auto-account-creation.md</a></td></tr><tr><td><a href="account-properties.md">     <strong>Propriétés du compte client</strong></a></td><td><a href="account-properties.md">account-properties.md</a></td></tr></tbody></table>

## Foire Aux Questions

<details>

<summary>What is a Hedera account?</summary>

Un compte Hedera est une entité unique dans le réseau Hedera qui peut contenir des jetons. Celles-ci peuvent être le jeton fungible natif de Hedera (HBAR), le champignon personnalisé ou [jetons non-fongibles (NFTs)](../../support-and-community/glossary.md#non-fungible-token-nft).

</details>

<details>

<summary>How are new accounts created on Hedera?</summary>

Les nouveaux comptes sont créés en soumettant une transaction au réseau et en payant les frais de transaction. Vous aurez besoin d'accéder à un compte existant avec suffisamment de HBAR pour couvrir ces frais. Si vous n'avez pas accès à un compte existant, vous pouvez utiliser un portefeuille pris en charge, visitez le [portail des développeurs Heder](https://portal. edera.com/), ou utilisez la fonction "Création automatique de comptes" pour les applications.

</details>

<details>

<summary>What is the 'Auto Account Creation' feature?</summary>

[Création automatique de compte](auto-account-creation.md) permet aux applications de générer instantanément des comptes utilisateurs gratuits, même sans connexion Internet, en créant un alias de compte.&#x20

</details>

<details>

<summary>What is a "hollow" account?</summary>

Si un compte est créé avec une [adresse EVM](auto-account-creation.md#evm-address) alias via [Création automatique de compte](auto-account-creation.md), cela aboutit à un compte "hollow". Ce compte a un numéro de compte et un alias mais aucune clé de compte. Il peut accepter les transferts de jetons mais ne peut pas transférer de jetons ou modifier les propriétés du compte tant que la clé du compte n'a pas été ajoutée, complétant le compte.

</details>
