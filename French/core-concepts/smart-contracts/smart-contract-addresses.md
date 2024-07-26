# Adresses des contrats intelligents

Après qu'un contrat intelligent est déployé sur Hedera, il est associé à une adresse unique de contrat intelligent. Il y a deux types d'adresses par lesquelles un contrat intelligent peut être référencé dans le système :

**➡️** [**Adresse EVM Smart Contract**](smart-contract-addresses.md#evm-address)

**➡️** [**Smart Contract ID**](smart-contract-addresses.md#contract-id)

***

### Adresse EVM

L'adresse EVM standard du contrat intelligent est l'adresse qui est compatible avec EVM. L’adresse du contrat EVM est retournée par le système une fois le contrat déployé. Il s'agit du format d'adresse couramment utilisé dans l'écosystème Ethereum. Vous pouvez utiliser l'adresse EVM du contrat intelligent pour référencer les contrats intelligents dans les outils Ethereum Ecosystem tels que [Hardhat](../../support-and-community/glossary.md#hardhat) et [MetaMask](../../support-and-community/glossary.md#metamask).

Exemple de contrat EVM Adresse ID de contrat encodé hex : `0x000000000000000000000000000000000000002cd37f`

{% hint style="info" %}
_**Note:** Les contrats déployés en utilisant les transactions de l'API `ContractCreate` Hedera auront ce formulaire (par exemple, en utilisant ContractCreateTransaction dans les SDKs). Tous les autres cas de déploiement seront dans l'adresse EVM standard, post_ [_HIP-729_](https://hips.hedera.com/hip/hip-729)_._
{% endhint %}

Exemple d'adresse EVM du contrat : [`0x86ecca95fecdb515d068975eac4357contractd6e86c5`](https://hashscan.io/mainnet/contract/0.0.2958097?p=1\\&k=1685819177.474035003)

***

### ID du contrat

Dans le réseau Hedera, les contrats intelligents peuvent également être identifiés par un ID de contrat intelligent. Un identifiant de contrat intelligent est un identifiant de contrat natif du réseau Hedera. L’adresse EVM du contrat intelligent et l’ID du contrat intelligent sont tous deux des identifiants acceptés pour un contrat intelligent lorsqu’ils interagissent avec le contrat sur Hedera en utilisant les transactions Hedera.

Example Contract ID: `0.0.123`

Dans certains cas, l'adresse EVM est le format hexadécidé de l'ID du contrat.

L'ID du contrat intelligent n'est **pas compatible** format d'adresse accepté ou connu dans l'écosystème Ethereum. Par exemple, si vous utilisez MetaMask, vous ne spécifierez pas le contrat par son ID de contrat et utiliserez à la place son adresse EVM.

Lorsque vous consultez les informations du contrat, vous pouvez voir les deux types d'adresses notées dans Hedera Network Explorers comme [HashScan](https://hashscan.io/).

<figure><img src="../../.gitbook/assets/contract ID.png" alt=""><figcaption><p>Adresse EVM & exemple ID de contrat sur HashScan</p></figcaption></figure>

***

### Comptes Smart Contract

Similaire à [Ethereum](../../support-and-community/glossary.md#ethereum), les entités de contrats intelligents sont également un type de compte. Un contrat intelligent déployé sur Hedera peut contenir [HBAR](../../support-and-community/glossary.md#hbar), [fungible](../../support-and-community/glossary.md#fungible-token) et [jetons non-fungible ](../../support-and-community/glossary.md#non-fungible-token-nft).

<table><thead><tr><th width="289">Propriété de Smart Contract</th><th>Exemple</th></tr></thead><tbody><tr><td><strong>Smart Contract ID</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0.0.2940467</a></td></tr><tr><td><strong>Adresse EVM Smart Contract</strong></td><td><a href="https://hashscan.io/mainnet/contract/0.0.2940467?p1=1">0xde2b7414e2918a393b59fc130bceb75c3ee52493</a></td></tr><tr><td><strong>Smart Contract Hex Encoded Contract ID</strong></td><td>0x0000000000000000000000000000000000000000002cff73<br>*<em>Ceci n'est présent que si le contrat n'a pas été déployé via un outil EVM et à la place les SDK Hedera.</em></td></tr><tr><td><strong>Smart Contract Account ID</strong></td><td><a href="https://hashscan.io/mainnet/account/0.0.2940467?app=false&#x26;ph=1&#x26;pt=1&#x26;p2=1&#x26;p1=1">0.0.2940467</a></td></tr></tbody></table>
