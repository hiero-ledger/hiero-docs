# Exécutez votre propre nœud miroir avec Google Cloud Storage (GCS)

## Pré-requis

- Un compte [Google Cloud Platform ](https://cloud.google.com/).
- Compréhension de base des nœuds miroir Hedera.
- [Docker](https://www.docker.com/) (`>= v20.10.x)` installé et ouvert sur votre machine. Exécutez `docker -v` dans votre terminal pour vérifier la version que vous avez installée.
- [Java](https://www.java.com/en/) (openjdk@17: Java version 17), [Gradle](https://gradle.org/install/) (la dernière version) et [PostgreSQL](https://www.postgresql.org/) (la dernière version) sont installées sur votre machine.

## 1. Obtenir les informations de paiement du demandeur de la plateforme Google Cloud

Dans cette étape, vous générerez vos clés d'accès HMAC de la plate-forme Google Cloud. Ces clés sont nécessaires pour authentifier les requêtes entre votre machine et Google Cloud Storage. Ils sont similaires à un nom d'utilisateur et à un mot de passe. Suivez ces étapes pour récupérer votre **clé d'accès, secret** et **ID du projet** :

- Créez un nouveau [project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) et liez votre [compte de facturation](https://cloud.google.com/billing/docs/how-to/manage-billing-account).
- Dans la barre de navigation de gauche, sélectionnez **Stockage Cloud > Paramètres.**
- Cliquez sur l'onglet **Interopérabilité** et descendez jusqu'à la section **HMAC du compte utilisateur**.
- Si vous n'avez pas encore de jeu de projet par défaut, définissez-le maintenant.
- Cliquez sur **créer des clés** pour générer des clés d'accès pour votre compte.

<figure><img src="../../../.gitbook/assets/gcs mirror2.png" alt=""><figcaption></figcaption></figure>

- Vous devriez voir les colonnes **clé d'accès** et **secret** remplir sur la table des clefs d'accès.
- Vous utiliserez ces clés pour configurer le fichier **`application.yml`** dans une étape ultérieure.

## 2. Cloner le dépôt de noeud miroir Hedera

- Ouvrez votre terminal et exécutez les commandes suivantes pour cloner le `hedera-mirror-node` [repository](https://github.com/hashgraph/hedera-mirror-node) puis `cd` dans le dossier `hedera-mirror-node` :

```bash
git clone https://github.com/hashgraph/hedera-mirror-node
cd hedera-mirror-node
```

## 3. Configurer le noeud miroir

Le fichier **`application.yml`** est le fichier de configuration principal du nœud miroir Hedera. Nous allons mettre à jour ce fichier avec vos clés GCP et le réseau Hedera que vous voulez miroir.

- Ouvrez le fichier `application.yml` à la racine avec un éditeur de texte de votre choix.
- Trouvez la section suivante et remplacez les espaces réservés par votre **clé d'accès** GCP, **clé secrète**, **ID du projet**, et le réseau que vous souhaitez mettre en miroir:

| Élément           | Libellé                                                                |
| ----------------- | ---------------------------------------------------------------------- |
| **accessKey**     | Votre clé d'accès depuis votre compte GCP                              |
| **cloudProvider** | GCP                                                                    |
| **Clé secrète**   | Votre clé secrète de votre compte GCP                                  |
| **gcpProjectId**  | Votre ID de projet GCP                                                 |
| **réseau**        | Entrez le réseau sur lequel vous souhaitez exécuter votre noeud miroir |

{% code title="application.yml" %}

```yaml
hedera:
  mirror:
    importer:
      downloader:
        accessKey: ENTRER ACCESS KEY ICI
        cloudProvider: "GCP"
        secretKey: ENTRER SECRET KEY ICI
        gcpProjectId: ENTER GCP PROJECT ID ICI
      network: PREVIEWNET/TESTNET/MAINNET #Pick one network
```

{% endcode %}

- Enregistrez les modifications et fermez le fichier.

## 4. Démarrez votre nœud miroir Hedera

Maintenant, commençons le nœud miroir Hedera en utilisant Docker. Docker vous permet d'exécuter facilement des applications dans un environnement autonome appelé _container_.

- À partir du répertoire `hedera-mirror-node`, exécutez la commande suivante:

```bash
docker compose -d db && docker logs hedera-mirror-node-db-1 --follow
```

## 5. Accédez aux données de votre noeud miroir Hedera

Cette étape vous montre comment accéder aux données que votre nœud miroir Hedera est en train de collecter. Le noeud miroir stocke ses données dans une base de données PostgreSQL, et vous utilisez Docker pour vous connecter à cette base de données. Pour accéder aux données des noeuds miroirs, nous devons entrer le conteneur **`hedera-mirror-node-db-1`** .

- Ouvrez un nouveau terminal et exécutez la commande suivante pour afficher la liste des conteneurs :

```bash
docker ps
```

- Entrez la commande suivante pour accéder au conteneur Docker :

```bash
docker exec -it hedera-mirror-node-db-1 bash
```

- Entrez la commande suivante pour accéder à la base de données :

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_pass port=5432"
```

- Entrez la commande suivante pour afficher la liste complète des tables de la base de données :

```bash
\dt
```

![](<../../../.gitbook/assets/image (4).png>)

- Pour quitter la console `psql`, exécutez la commande quit :

```bash
\q
```

- Enfin, exécutez la commande suivante pour arrêter et supprimer les conteneurs créés :

```bash
docker compose vers le bas
```

#### Félicitations ! Vous avez exécuté et déployé avec succès un nœud miroir Hedera avec Google Cloud Storage (GCS) 🚀
