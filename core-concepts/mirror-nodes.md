---
description: Store history and cost-effectively query data
---

# Mirror Nodes

_Mirror nodes are newly available in beta as a developer preview as of August 29th._

Mirror nodes provide a way to store and cost-effectively query historical data from the public ledger while minimizing the use of Hedera network resources. Mirror nodes support the Hedera network services currently available and can be used to retrieve the following information:

* Transactions and records
* Event files
* Balance files

A beta version of the mirror node code is [available on GitHub](https://github.com/hashgraph/hedera-mirror-node).

{% hint style="info" %}
**Current Status**\
The beta mirror node is available to deploy and downloads transaction data via an Amazon or Google bucket. A full version is actively being developed and will connect directly to a Hedera network (testnet or mainnet).\
\
**UPDATE: LIMITED AVAILABILITY**\
Please check out the mirror node information [here](https://docs.hedera.com/guides/docs/mirror-node-api/hedera-consensus-service-api-1).
{% endhint %}

## Understanding Mirror Nodes

Hedera mirror nodes receive information from Hedera network consensus nodes, either mainnet or testnet, and provide a more effective means to perform:

* Queries
* Analytics
* Audit support
* Monitoring

While mirror nodes receive information from the consensus nodes, they do not contribute to consensus themselves. The trust of Hedera is derived based on the consensus reached by the consensus nodes. That trust is transferred to the mirror nodes using signatures, chain of hashes, and state proofs.

At version 1.0, mirror nodes will run the same code as consensus nodes.

To make the initial deployments easier, the beta version of the mirror nodes strive to take away the burden of running a full Hedera node through the creation of periodic files that contain processed information (such as account balances or transaction records) and have the full trust of the Hedera consensus nodes. The beta mirror node software reduces the processing burden by receiving pre-constructed files from the network, validating those, populating a database, and providing REST APIs.

![](../.gitbook/assets/betamirrornode-overview.jpg)

Beta mirror nodes work by validating the signature files associated with the record, balance, and event files from the consensus nodes that were uploaded to a cloud storage solution from the network.

As transactions reach consensus on the Hedera network, either mainnet or testnet, Hedera consensus nodes add the transaction and its associated records to a record file. A record file contains a series of ordered transactions and their associated records. After a given amount of time, a record file is closed and a new one is created. This process repeats as the network continues to receive transactions.

Once a record file is closed, the consensus nodes generate a signature file. The signature file contains a signature for the corresponding record file’s hash. Along with the signature file of the consensus node, the record file also contains the hash of the previous record file. The former record file can now be verified by matching the hash of the previous record file.

Hedera consensus nodes push new record files and signature files to the cloud storage provider – currently AWS S3 and Google File Storage are supported. Mirror nodes download these files, verify their signatures based on their hashes, and only then make them available to be processed.

## Using Mirror Nodes

To get access to a Hedera beta mirror node, please follow the link below.

{% content-ref url="../mirrornet/hedera-mirror-node.md" %}
[hedera-mirror-node.md](../mirrornet/hedera-mirror-node.md)
{% endcontent-ref %}

### REST API from Hedera

Hedera will provide a REST API to easily query a mirror node that is hosted by Hedera, removing the complexity of having to run your own. After you are whitelisted to a Hedera mirror node on mainnet, you can check out the mirror node REST API docs below.

{% content-ref url="../docs/mirror-node-api/" %}
[mirror-node-api](../docs/mirror-node-api/)
{% endcontent-ref %}

{% hint style="info" %}
Hedera will provide this as a service to bootstrap early use, but anticipates a full ecosystem and marketplace to be established by third parties.
{% endhint %}

### Run a Mirror Node

Anyone can run a Hedera beta mirror node by downloading and configuring the software on their computer. By running a beta mirror node, you are able to connect to the appropriate cloud storage and store account balance files, record files, and event files as described above. Please check out the below links on how to get started.

{% content-ref url="../mirrornet/run-your-own-beta-mirror-node/" %}
[run-your-own-beta-mirror-node](../mirrornet/run-your-own-beta-mirror-node/)
{% endcontent-ref %}

{% content-ref url="../mirrornet/one-click-mirror-node-deployment.md" %}
[one-click-mirror-node-deployment.md](../mirrornet/one-click-mirror-node-deployment.md)
{% endcontent-ref %}
