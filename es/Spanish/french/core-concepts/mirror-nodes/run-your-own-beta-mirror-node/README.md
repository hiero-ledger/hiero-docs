# Run Your Own Mirror Node

## Overview

A Hedera Mirror Node is a node that receives pre-constructed files from the Hedera Mainnet. These pre-constructed files include **transaction records** and **account balance files**. Transaction records include information about a transaction, like the transaction ID, transaction hash, account, etc. The account balance files give you a snapshot of the balances for all accounts at a given timestamp.

In this tutorial, you will run your own Hedera Mirror Node. You will need to create either a Google Cloud Platform (GCP) or an Amazon Web Services (AWS) account if you do not have one already. The Hedera Mirror Node object storage bucket, where you will pull the account balance and transaction data from, is stored in GCP or AWS bucket and is configured for [requester pays](https://cloud.google.com/storage/docs/requester-pays). This means the mirror node operator is responsible for the operational costs of reading and retrieving data from the GCP/AWS. A GCP/AWS account will provide the necessary billing information for requesting the data.

{% content-ref url="run-your-own-mirror-node-gcs.md" %}
[run-your-own-mirror-node-gcs.md](run-your-own-mirror-node-gcs.md)
{% endcontent-ref %}

{% content-ref url="run-your-own-mirror-node-s3.md" %}
[run-your-own-mirror-node-s3.md](run-your-own-mirror-node-s3.md)
{% endcontent-ref %}

## Minimum Hardware Requirements & Associated Costs

To run a Hedera Mirror Node, you'll need specific hardware and resources. The recommended minimum requirements for running a Mirror Node, along with the associated costs, are outlined below.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><p><mark style="background-color:yellow;"><strong>Compute Engine</strong></mark></p><ul><li><strong>Region:</strong> Iowa</li><li><strong>Provisioning Model:</strong> Regular</li><li><strong>Instance Type:</strong> n1-standard-4</li><li><strong>Operating System / Software:</strong> Free</li><li><strong>Total hours per month:</strong> 1,460</li><li><strong>Sustained Use Discount:</strong> Applied (30%)</li></ul></td><td>The estimated cost for the compute engine per month is <strong>USD 194.18</strong>.</td><td></td></tr><tr><td><p><mark style="background-color:yellow;"><strong>Cloud SQL for PostgreSQL</strong></mark></p><ul><li><strong>Instance Type:</strong> db-highmem-4</li><li><strong>Location:</strong> Iowa</li><li><strong>Number of Instances:</strong> 1</li><li><strong>Total hours per month:</strong> 730.0</li><li><strong>SSD Storage:</strong> 250.0 GiB</li><li><strong>Backup:</strong> 250.0 GiB</li></ul></td><td><p>The estimated cost for Cloud SQL for PostgreSQL per month is <strong>USD 303.46</strong>.</p><p><em><strong>*</strong>The Mainnet full node needs a much bigger database (50TB) for the complete transaction history.</em></p></td><td></td></tr><tr><td><p><mark style="background-color:yellow;"><strong>Persistent Disk (Accompanying)</strong></mark></p><ul><li><strong>Product accompanying:</strong> GKE Standard</li><li><strong>Zonal SSD PD:</strong> 50 GiB (2 x boot disk)</li></ul></td><td><p>The estimated cost for the persistent disk per month is <strong>USD 17.00</strong>.</p><p>Based on these specifications, the total estimated monthly cost to run a Hedera Mirror Node is <strong>USD 514.64</strong>.</p></td><td></td></tr></tbody></table>

{% hint style="warning" %}
**PLEASE NOTE:** These are estimated costs, and actual costs will vary depending on usage and any changes to the pricing of the resources used. Always refer to the most recent price lists from the respective services for accurate costs.
{% endhint %}
