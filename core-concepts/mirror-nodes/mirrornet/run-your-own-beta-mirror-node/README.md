# Run Your Own Beta Mirror Node

## Overview

A Beta Mirror Node is a node that receives pre-constructed files from the Hedera Mainnet. These pre-constructed files include **transaction records** and **account balance files**. Transaction records include information about a transaction like the transaction ID, transaction hash, account, etc. The account balance files give you a snapshot of the balances for all accounts at a given timestamp.

In this tutorial, you will run your own Beta Mirror Node. You will need to create either a Google Cloud Platform (GCP) or Amazon Web Services (AWS) account if you do not have one already. The Beta Mirror Node object storage bucket, where you will pull the account balance and transaction data from, is stored in GCP or AWS bucket and are configured for [requester pays](https://cloud.google.com/storage/docs/requester-pays). This means the Beta Mirror Node operator is responsible for the operational costs of reading and retrieving data from the GCP/AWS. A GCP/AWS account will provide the necessary billing information for requesting the data.

{% content-ref url="run-your-own-mirror-node-gcs.md" %}
[run-your-own-mirror-node-gcs.md](run-your-own-mirror-node-gcs.md)
{% endcontent-ref %}

{% content-ref url="run-your-own-mirror-node-s3.md" %}
[run-your-own-mirror-node-s3.md](run-your-own-mirror-node-s3.md)
{% endcontent-ref %}
