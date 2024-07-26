# Réseau local

## Introduction

Hedera Localnet fournit aux développeurs un framework complet pour tester et affiner localement les applications basées sur Hedera. En opérant en dehors des réseaux publics, Localnet est crucial dans le cycle de vie du développement de logiciels, en éliminant les goulets d’étranglement des E/S du réseau, en minimisant les conflits de ressources partagées et en offrant un contrôle complet sur les configurations du réseau. Ce réseau local comprend deux offres de produits principales, [Local Node](https://github.com/hashgraph/hedera-local-node) et [Solo](https://github.com/hashgraph/solo), chacune servant des objectifs distincts dans le processus de développement et de test. Pour les développeurs qui viennent de commencer avec Local Node, voici le chemin recommandé pour les tests :

<figure><img src="../../.gitbook/assets/localnet-dev-testing-path.png" alt="" width="375"><figcaption><p>Hedera Local Node Testing Workflow</p></figcaption></figure>

1. **Node local (mode simple et multi-nodes)**: Commencez par tester votre prototype initial sur un nœud local. Cette étape permet des itérations et débogage rapides dans un environnement contrôlé. Si votre application a besoin de gérer des scénarios plus complexes, exécutez la configuration _Multinoeud_.&#x20
2. **Solo**: Puis passez à _Solo_ pour des tests avancés dans des conditions de réseau réalistes.&#x20
3. **Previewnet** : Testez ensuite sur Hedera Previewnet pour la dernière fois ou la prochaine vérification du code.
4. **Testnet** : Enfin, testez sur le réseau de test pour la vérification de code stable avant de le déployer sur Hedera Mainnet.

***

## Noeud local

### Aperçu

Le noeud local réplique un réseau Hedera composé d'un seul nœud (ou peu si configuré) sur la machine locale du développeur, offrant un environnement contrôlé pour développer, tester et expérimenter des [applications décentralisées (dApps)](. /../support-and-community/glossary.md#decentralized-application-dapp). Cette configuration locale utilise [Docker Compose](https://docs.docker.com/compose/) pour créer plusieurs conteneurs, chacun avec un rôle spécifique au sein du réseau, y compris mais non limité à:

- **Consensus Node** : Simule le comportement du mécanisme de consensus Hedera, les transactions de traitement/commande et participe à l’algorithme de consensus du réseau.
- [**Noeud de miroir**](../../support-and-community/glossary.md#mirror-nodes)**:** Fournit un accès aux données historiques, aux enregistrements de transactions et à l'état actuel du réseau sans participer au consensus. Ceci est utile pour interroger et analyser.
- [**Relais JSON-RPC**](../../support-and-community/glossary.md#json-rpc-relay): Offre une implémentation locale JSON-RPC de l' [Ethereum](../../support-and-community/glossary.md#ethereum) des API JSON-RPC pour Hedera afin d'activer les interactions avec les contrats intelligents et les comptes. Ceci est particulièrement utile pour les développeurs familiers avec l'outillage Ethereum et l'écosystème.
- [**Explorateur de Node Miroir**](../../support-and-community/glossary.md#network-explorer): Une interface Web qui permet aux développeurs d'auditer visuellement les transactions, les comptes et les autres activités du réseau.

### Configuration et configuration

La configuration à un seul nœud simule les fonctions du réseau à plus petite échelle (sur un seul nœud), idéale pour le débogage, le test et le développement de prototypes. La configuration multi-nœuds distribue plusieurs instances des nœuds réseau Hedera sur une seule machine à l'aide de conteneurs Docker, destinés aux tests avancés et à l'émulation de réseau.

➡️ [**Configuration du nœud unique**](single-node-configuration.md)

➡️ [**Configuration des multi-nodes**](multinode-configuration.md)

### Modes Opérationnels

Le noeud local offre deux modes en fonction des besoins d'un développeur :

<details>

<summary><strong>Mode Plein</strong></summary>

Le mode complet est activé avec l'option `--full`, et le système est conçu pour capturer et stocker des données complètes. Voici comment ça marche :

- **Transfert de données**: chaque nœud du réseau génère des fichiers de flux d'enregistrements pendant l'opération. Les fichiers de flux d'enregistrements sont une séquence d'enregistrements regroupés sur un intervalle spécifique. Le réseau Hedera consolide périodiquement ces enregistrements de transaction en fichiers de flux, qui sont ensuite mis à la disposition des noeuds réseau et des noeuds de miroir. En mode complet, ces fichiers sont systématiquement téléchargés dans leur propre répertoire dans le segment `minio`. MinIo est une plate-forme de stockage d'objets qui fournit des outils dédiés pour le stockage, la récupération et la recherche de blobs. Ce processus est géré par des conteneurs de téléchargement spécifiques assignés à chaque nœud, à savoir:
  - `record-streams-uploader-N` (contient des flux d'enregistrement)
  - `account-balances-uploader-N` (contient les fichiers des soldes de compte)
  - `record-sidecar-uploader-N` (contient une liste de `TransactionSidecarRecords` qui ont tous été créés sur un intervalle spécifique et liés au même `RecordStreamFile`.

</details>

<details>

<summary><strong>mode Turbo</strong></summary>

Le mode Turbo est le paramètre par défaut lors de l'exécution du noeud local. Ce mode donne la priorité à l'efficacité et à la vitesse, avec les caractéristiques clés suivantes :

- **Accès aux données locales** : Au lieu de télécharger des données sur le cloud, les fichiers de flux d'enregistrements sont lus directement à partir de leurs répertoires locaux correspondants sur chaque nœud. Cette méthode réduit considérablement la latence et la consommation de ressources, en le rendant idéal pour les scénarios où l'accès immédiat aux données et les hautes performances sont prioritaires par rapport au stockage à long terme et à l'accessibilité externe.

</details>

Avec ces deux options, les utilisateurs peuvent adapter le fonctionnement du nœud local à leurs besoins, que ce soit pour assurer la saisie et la sauvegarde de données complètes ou pour optimiser les performances et la rapidité.

***

## Solo

Solo propose une solution de test de réseau privé avancée et adopte une stratégie Kubernetes-first pour créer un réseau qui imite complètement un environnement de production. Explorez le dépôt Solo [here](https://github.com/hashgraph/solo).

_**Plus d'infos à venir...**_

***

## Ressources supplémentaires

- [**Hedera Local Node Repo**](https://github.com/hashgraph/hedera-local-node)
- [**Docker Compose Documentation**](https://docs.docker.com/compose/intro/features-uses/)
- [**Exécuter un noeud local sur Gitpod**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/gitpod.md) **\[tutorial]**
- [**Exécuter un noeud local dans Codespaces**](../../tutorials/local-node/how-to-run-hedera-local-node-in-a-cloud-development-environment-cde/codespaces.md) **\[tutorial]**
