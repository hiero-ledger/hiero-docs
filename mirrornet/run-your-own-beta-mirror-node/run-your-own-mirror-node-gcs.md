# Run your mirror node using GCS

## Requirements

* [Google Cloud Platform Account](https://cloud.google.com/)
  * Create a [project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) and link your [billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account)
  * Get your [accessKey](https://cloud.google.com/storage/docs/authentication/managing-hmackeys), [secretKey](https://cloud.google.com/storage/docs/authentication/managing-hmackeys), and [project ID](https://cloud.google.com/resource-manager/docs/creating-managing-projects)

{% hint style="info" %}
**Note:** Buckets are also available in Amazon Web Services (AWS)
{% endhint %}

* [Docker](https://www.docker.com/get-docker)
  * Check to see if you have it installed from your terminal: `docker --version && docker-compose --version`
  * For mirror node versions 0.35.0 and higher you will need docker v3.3.3+
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Hedera Mirror Node Repository](https://github.com/hashgraph/hedera-mirror-node)
  * You will be prompted to download the repo in the following steps.
* [PostgreSQL](https://www.postgresql.org/download/)
  * Download PostgreSQL to be able to access the database.

## 1. Obtain Google Cloud Platform Requester Pay Information

You will need to grab the **secret key, access key**, and **project ID** from your Google Cloud Platform account

* From the left navigation bar select **STORAGE > SETTINGS**
* Click on the **Interoperability** tab and scroll down to the **User account HMAC** section
* Set your default project
* Click **create keys** to generate access keys for your account

![](../../.gitbook/assets/hmac\_keygen.gif)

* You should see the **access key** and **secret** columns populate on the table
* You will use these keys to populate the **application.yml** configuration file in a later step

## 2. Clone Hedera Mirror Node Repository

* Open your terminal
* Clone the `hedera-mirror-node` repository

```
git clone https://github.com/hashgraph/hedera-mirror-node
```

* CD to the `hedera-mirror-node` folder

```
cd hedera-mirror-node
```

## 3. Configure your application.yml file

* Open the **application.yml** file found in the root directory with a text editor of your choice
* Input the following information and uncomment each line
* Buckets are preconfigured for each network

| Item              | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| **accessKey**     | Your project access key from your Google Cloud Platform account |
| **cloudProvider** | GCP                                                             |
| **secretKey**     | Your secret key from your Google Cloud Platform account         |
| **gcpProjectId**  | Your Google Cloud Platform project ID                           |
| **network**       | Enter the network you would to run your mirror node for         |

{% tabs %}
{% tab title="Sample application.yml file" %}
```
hedera:
  mirror:
    importer: 
      downloader:
        accessKey: GOOG....
        cloudProvider: "GCP"
        secretKey: h+....
        gcpProjectId: ENTER GCP PROJECT ID HERE
      network: PREVIEWNET/TESTNET/MAINNET #Pick one network
```
{% endtab %}
{% endtabs %}

## 4. Start Your Beta Mirror Node

* From the `hedera-mirror-node` directory, run the following command in your terminal:

```
docker-compose up
```

## 5. Access Your Beta Mirror Node Data

To access the mirror node data now available to you, access the `hedera-mirror-node_db_1` container.

* Open a new terminal
* To view the `hedera-mirror-node_db_1` container ID, enter the following command:

```
docker ps
```

* Enter the following command to access the docker container

```
docker exec -it <containerId> bash
```

* Enter the following command to access the database

```
psql "dbname=mirror_node host=localhost user=mirror_node password=mirror_node_pass port=5432"
```

* Enter the following command to view the complete list of tables

```
\dt
```

![](<../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png>)

You have successfully deployed a Hedera mirror node ​ 🥳 !
