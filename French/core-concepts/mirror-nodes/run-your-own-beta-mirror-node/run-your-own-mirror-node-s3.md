# Run Your Mirror Node with Amazon Web Services S3 (AWS)

## Pré-requis

- An [Amazon Web Services](https://aws.amazon.com/free/?trk=ps\_a131L0000085DvcQAE\\&trkCampaign=acq\_paid\_search\_brand\\&sc\_channel=ps\\&sc\_campaign=acquisition\_US\\&sc\_publisher=google\\&sc\_category=core\\&sc\_country=US\\&sc\_geo=NAMER\\&sc\_outcome=acq\\&sc\_detail=aws%20account\\&sc\_content=Account\_e\\&sc\_segment=432339156165\\&sc\_medium=ACQ-P|PS-GO|Brand|Desktop|SU|AWS|Core|US|EN|Text\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&ef\_id=Cj0KCQjw8IaGBhCHARIsAGIRRYrLfWc3ykRf\_hAUeVvf4nNEYvacHwk\_w1jAuSj6hQZ8\_muh0T5p3acaAkZDEALw\_wcB:G:s\\&s\_kwcid=AL!4422!3!432339156165!e!!g!!aws%20account\\&all-free-tier.sort-by=item.additionalFields.SortRank\\&all-free-tier.sort-order=asc\\&awsf.Free%20Tier%20Types=*all\\&awsf.Free%20Tier%20Categories=*all) account.
- Compréhension de base des nœuds miroir Hedera.
- [Docker](https://www.docker.com/) (`>= v20.10.x)` installed and opened on your machine. Exécutez `docker -v` dans votre terminal pour vérifier la version que vous avez installée.
- [Java](https://www.java.com/en/) (openjdk@17: Java version 17), [Gradle](https://gradle.org/install/) (the latest version), and [PostgreSQL](https://www.postgresql.org/) (the latest version) are installed on your machine.

## 1. Create an IAM user

This step will teach you how to create a new IAM (_Identity and Access Management)_ user and generate new access keys in your AWS account. The **access key,** **secret** and **project ID** will be used to access S3 from the Hedera Mirror Node.

- Create an [IAM (Identity and Access Management) user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-set-up.html#create-an-admin) with either an administrator or custom policy. If you're unfamiliar with using AWS, go with the administrator policy:

{% tabs %}
{% tab title="Administrator Policy" %}

- Refer to AWS documentation to create an IAM user with an administrator policy [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
  - This sets up an IAM user with Administrator Access Policy
  - This user has full access and can delegate permissions to every service and resource in AWS.
- Once that is complete, select **Users** from the left IAM navigation bar
- Select the **Administrator** from the **User name** column
- Select the **Security credentials** tab
- Select **Create access key**
- Copy or download your **Access key ID** and **Secret access key**
  {% endtab %}

{% tab title="Custom Policy" %}

- Enable access to billing data
  - Suivre l'étape 2 [here](https://docs.aws.s3.amazonaws.com/.com/IAM/latest/UserGuide/getting-started\_create-admin-group.html)
- À partir de la barre de navigation à gauche de l'IAM, sélectionnez **Conditions**
- Cliquez sur **Créer la politique**
  - Service
    - Entrez **S3** en tant que service
  - Actions
    - Niveau d'accès
    - Sélectionnez **Liste** et **Lire**
- Ressource
  - Sélectionnez **Spécifier une ressource ARN pour GetBucketLocation**
  - Ajouter un ARN
    - hedera-mainnet-streams
  - Ajouter un ARN
    - hedera-mainnet-streams/\*
- Cliquez sur **Next:Tags**
- Cliquez sur **Suivant: Révision**
  - Entrez un nom pour la politique
- Cliquez sur **Créer la politique**
- Dans la barre de navigation gauche de la console IAM, sélectionnez **Utilisateur** **Groupes**
- Cliquez sur **Créer un groupe**
- Entrez un nom de groupe d'utilisateur
- Sélectionnez la politique qui a été créée à l'étape précédente
- Cliquez sur **Créer un groupe**
- Cliquez sur **Utilisateurs** à partir de la barre de navigation de la console IAM à gauche
- Cliquez sur **Ajouter un utilisateur**
- Entrez le nom d'utilisateur
- Sélectionnez **Accès Programmatique pour le type d'accès**
- Cliquez sur **Suivant : Autorisations**
- Sélectionnez le groupe qui a été créé à l'étape précédente
- Cliquez sur **Prochain: Tags**
- Cliquez sur **Suivant: Révision**
- Cliquez sur **Créer un utilisateur**
- Copiez ou téléchargez votre **ID de clé d'accès** et **Clé d'accès secret**
  {% endtab %}
  {% endtabs %}

## 2. Cloner le dépôt du noeud miroir

- Ouvrez votre terminal et exécutez les commandes suivantes pour cloner le nœud miroir [repository](https://github.com/hashgraph/hedera-mirror-node), puis `cd` dans le dossier `hedera-mirror-node` :

<pre class="language-bash"><code class="lang-bash"><strong>git clone https://github.com/hashgraph/hedera-mirror-node.git
</strong>cd hedera-mirror-node
</code></pre>

## 3. Configurer le noeud miroir

Le fichier `application.yml` est le fichier de configuration principal du nœud miroir Hedera. Dans cette étape, nous mettrons à jour le fichier de configuration avec vos paramètres spécifiques, comme votre clé d'accès AWS, votre secret et le réseau Hedera que vous voulez mettre en miroir.

- Ouvrez le fichier `application.yml` à la racine avec un éditeur de texte de votre choix.
- `cd` dans le dossier `hedera-mirror-node` de votre terminal ou IDE.
- Trouvez les champs suivants et remplacez les espaces réservés par votre clé d'accès AWS, clé secrète, ID du projet et le réseau que vous souhaitez mettre en miroir :

| Élément           | Libellé                                             |
| ----------------- | --------------------------------------------------- |
| **accessKey**     | Clé d'accès AWS                                     |
| **cloudProvider** | s3                                                  |
| **Clé secrète**   | Clé secrète AWS                                     |
| **réseau**        | Entrez un réseau pour exécuter un noeud miroir pour |

{% code title="application.yml" %}

```yaml
hedera:
  mirror:
    importer: 
      downloader:
        accessKey: ENTREZ ACCESS KEY ICI
        cloudProvider: "s3"
        secretKey: ENTRÉE SECRET KEY ICI
      network: PREVIEWNET/TESTNET/MAINNET #Pick one network
```

{% endcode %}

## 4. Lancer votre nœud miroir

Démarrez et exécutez le nœud miroir Hedera en utilisant Docker. Les outils de développement de paquets Docker dans un environnement autonome appelé _container_ qui peut inclure des bibliothèques, du code, de l'exécution, et plus encore.

- À partir du répertoire des noeuds miroirs, exécutez la commande suivante :

```bash
docker compose -d db && docker logs hedera-mirror-node-db-1 --follow
```

## 5. Accéder aux données de votre noeud miroir

Une fois que le noeud miroir est exécuté avec succès, il télécharge les données depuis le réseau Hedera et les stocke dans une base de données PostgreSQL. Pour accéder aux données des noeuds de miroir, entrez le conteneur de la base de données et connectez-y à l'aide de Docker et de l'outil de ligne de commande `psql`.

- Ouvrez un nouveau terminal et exécutez la commande suivante pour afficher la liste des conteneurs :

```bash
docker ps
```

<figure><img src="../../../.gitbook/assets/docker ps (1).png" alt=""><figcaption></figcaption></figure>

- Exécutez la commande suivante pour entrer dans le conteneur `hedera-mirror-node-db-1` :

```bash
docker exec -it hedera-mirror-node-db-1 bash
```

- Entrez la commande suivante pour accéder et interroger la base de données:

```bash
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_pass port=5432"
```

- Entrez la commande suivante pour afficher la liste complète de toutes les tables de la base de données :

```bash
\dt
```

<figure><img src="../../../.gitbook/assets/list of relations s3 mirror.png" alt=""><figcaption></figcaption></figure>

- Pour quitter la console de base de données `psql`, exécutez la commande quit :

```bash
\q
```

- Enfin, exécutez la commande suivante pour arrêter Docker et supprimer les conteneurs créés :

```bash
docker compose vers le bas
```

#### Félicitations ! Vous avez exécuté et déployé avec succès un nœud miroir Hedera avec Amazon Web Services S3 (AWS) 🚀
