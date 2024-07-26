---
description: Frais de réseau Hedera
---

# Frais

Les tableaux des frais de test de Hedera ci-dessous offrent une estimation des frais de transaction et de requête pour tous les services de réseau. Les tables ci-dessous contiennent des valeurs USD, HBAR et Tinybar (t<unk> ) pour chaque appel API. Tous les frais de fonctionnement sur le réseau de test Hedera sont payés dans le test HBAR, qui est librement disponible et n'est utile qu'à des fins de développement.

Les estimations de frais sont basées sur des hypothèses à propos des détails d'un appel API spécifique. Par exemple, les frais pour un transfert de cryptomonnaie HBAR (CryptoTransfer) suppose une signature unique sur la transaction et les frais de stockage d'un fichier supposent un fichier de 48 octets stocké pendant 30 jours. Les transactions qui dépassent ces hypothèses de base seront plus chères; nous vous recommandons d'augmenter vos frais maximaux admissibles pour tenir compte de la complexité supplémentaire.

### Frais du réseau principal

Les frais de transaction et de requête principaux peuvent être estimés à l'aide de [Hedera Fee Estimator](https://hedera.com/fees). L'Estimateur de Frais vous permet de déterminer les frais (en USD et en HBAR, en utilisant le taux de change actuel en vigueur sur le réseau principal) pour les transactions et requêtes individuelles en fonction de leurs caractéristiques, ainsi que les coûts prévus en fonction du volume prévu de ces opérations. Les estimations peuvent ne pas être exactes à 100 % et les prix sous-jacents peuvent être modifiés sans préavis.

## Dénominations et abréviations HBAR

| Dénominations  | Abréviations       | Montant de la cryptomonnaie HBAR |
| -------------- | ------------------ | -------------------------------- |
| gigabar        | 1 Għ               | = 1 000 000 000 <unk>            |
| megabar        | 1 Mo               | = 1 000 000 <unk>                |
| kilobar        | 1 Ko               | = 1 000 <unk>                    |
| barre latérale | 1 Go               | = 1 <unk>                        |
| millibar       | 1 000 m<unk>       | = 1 <unk>                        |
| microbar       | 1,000,000 μħ       | = 1 <unk>                        |
| tinybar        | 100 000 000 t<unk> | = 1 <unk>                        |

## Frais de transaction et de requête

Tous les frais sont sujets à changement. Les frais ci-dessous reflètent un prix de base pour la transaction ou la requête. Les caractéristiques de la transaction peuvent augmenter le prix à partir du prix de base indiqué ci-dessous. Les caractéristiques de la transaction comprennent plus d'une signature, un champ de mémo, etc. Veuillez consulter l'[estimateur de frais Heder](https://hedera.com/fees) pour estimer les frais de transaction ou de requête.

### Service de cryptomonnaies

<table><thead><tr><th width="482">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Crypter la création</td><td>$0.05</td></tr><tr><td>CryptoAccountAutoRenew</td><td>$0.00022</td></tr><tr><td>Supprimer l’autorisation de cryptage</td><td>$0.05</td></tr><tr><td>Autorisation de cryptage</td><td>$0.05</td></tr><tr><td>CryptoUpdate</td><td>$0.00022</td></tr><tr><td>Transfert de cryptomonnaies</td><td>$0.0001</td></tr><tr><td>CryptoTransfer (frais personnalisés)</td><td>$0.002</td></tr><tr><td>Supprimer les cryptomonnaies</td><td>$0.005</td></tr><tr><td>Enregistrements de comptes de cryptage</td><td>$0.0001</td></tr><tr><td>Obtenir un solde de compte Crypto</td><td>$0.00</td></tr><tr><td>Informations sur le cryptage</td><td>$0.0001</td></tr><tr><td>CryptoGetStakers</td><td>$0.0001</td></tr></tbody></table>

### Service de consensus

<table><thead><tr><th width="484">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Consensus lors de la création d'un sujet</td><td>$0.01</td></tr><tr><td>Sujet de mise à jour du contenu</td><td>$0.00022</td></tr><tr><td>Supprimer ce sujet</td><td>$0.005</td></tr><tr><td>Envoyer un message au Consensus</td><td>$0.0001</td></tr><tr><td>Information sur le sujet du Consensus</td><td>$0.0001</td></tr></tbody></table>

### Service de jeton

<table><thead><tr><th width="486">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>format@@0 TokenCreate</td><td>$1.00</td></tr><tr><td>Créer un jeton (frais personnalisés)</td><td>$2.00</td></tr><tr><td>TokenUpdate</td><td>$0.001</td></tr><tr><td>Mise à jour du calendrier des frais de jeton</td><td>$0.001</td></tr><tr><td>TokenDelete</td><td>$0.001</td></tr><tr><td>Associé de jetons</td><td>$0.05</td></tr><tr><td>TokenDissociate</td><td>$0.05</td></tr><tr><td>Monnaies TokenMonnaie (fongible)</td><td>$0.001</td></tr><tr><td>Monnaie jeton (non fongible)</td><td>$0.02</td></tr><tr><td>Monnaie de jeton (10 000 NFTs de menthe en vrac)</td><td>$200</td></tr><tr><td>Brûlure de jeton</td><td>$0.001</td></tr><tr><td>TokenGrantKyc</td><td>$0.001</td></tr><tr><td>Jetons révoqués Kyc</td><td>$0.001</td></tr><tr><td>TokenFreeze</td><td>$0.001</td></tr><tr><td>TokenUnfreeze</td><td>$0.001</td></tr><tr><td>format@@0 TokenPause</td><td>$0.001</td></tr><tr><td>Rétablir le jeton</td><td>$0.001</td></tr><tr><td>TokenWipe</td><td>$0.001</td></tr><tr><td>TokenGetInfo</td><td>$0.0001</td></tr><tr><td>format@@0 TokenGetNftInfo</td><td>$0.0001</td></tr><tr><td>Infos TokenGetNftInfo</td><td>$0.0001</td></tr><tr><td>format@@0 TokenGetAccountNftInfos</td><td>$0.0001</td></tr><tr><td>TokenUpdateNfts (mise à jour des métadonnées de 1 NFT)</td><td>$0.001</td></tr><tr><td>TokenUpdateNfts (mettre à jour plusieurs NFTs en un seul appel)</td><td>N * $0.001</td></tr></tbody></table>

### Planifier la transaction

<table><thead><tr><th width="491">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Créer un calendrier</td><td>$0.01</td></tr><tr><td>Panneau de planification</td><td>$0.001</td></tr><tr><td>ScheduleDelete</td><td>$0.001</td></tr><tr><td>Planifier des infos</td><td>$0.0001</td></tr></tbody></table>

### Service de fichiers

<table><thead><tr><th width="495">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Fichier créé</td><td>$0.05</td></tr><tr><td>Mise à jour du fichier</td><td>$0.05</td></tr><tr><td>Fichier supprimé</td><td>$0.007</td></tr><tr><td>Ajouter un fichier</td><td>$0.05</td></tr><tr><td>Le fichier contient du contenu</td><td>$0.0001</td></tr><tr><td>Récupération du fichier</td><td>$0.0001</td></tr></tbody></table>

### Service de Contrat Intelligent

<table><thead><tr><th width="501">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Créer un contrat</td><td>$1.0</td></tr><tr><td>Contrat mis à jour</td><td>$0.026</td></tr><tr><td>Supprimer le contrat</td><td>$0.007</td></tr><tr><td>Appel de contrat</td><td>$0.05</td></tr><tr><td>ContractCallLocal</td><td>$0.001</td></tr><tr><td>GetByteCode du contrat</td><td>$0.05</td></tr><tr><td>GetBySolidityID</td><td>$0.0001</td></tr><tr><td>ContractGetInfo</td><td>$0.0001</td></tr><tr><td>ContractGetRecords</td><td>$0.0001</td></tr><tr><td>Renouvellement automatique du contrat</td><td>$0.026</td></tr></tbody></table>

### Divers

<table><thead><tr><th width="508">Opérations</th><th>USD ($)</th></tr></thead><tbody><tr><td>Transaction Ethereum</td><td>$0.0001</td></tr><tr><td>PrngTransaction</td><td>$0.001</td></tr><tr><td>Obtenir la version</td><td>$0.001</td></tr><tr><td>Obtenir par clé</td><td>$0.0001</td></tr><tr><td>La transaction a été reçue</td><td>$0.0000</td></tr><tr><td>Enregistrement de la transaction</td><td>$0.0001</td></tr></tbody></table>
