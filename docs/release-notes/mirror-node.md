---
description: Hedera mirror node release notes
---

# Hedera Mirror Node

| Network | Current Version | Upcoming Version |
| :--- | :--- | :--- |
| **Mainnet** | 0.38.0 | 0.38.1 |
| **Testnet** | 0.38.0 | 0.38.1 |
| **Previewnet** | 0.38.0 | 0.38.1 |

## Upcoming Releases

## [v0.38.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.38.1)

This release is a small bug fix release that contains some important fixes for our mirror node monitoring component. We added a new cluster health check to the monitor that takes into account publishing status. The load balancer uses this health check to determine which cluster to route traffic to. The old health check endpoint didn't take into account whether transaction publishing was active or successful and so would not route traffic to the public mirror node during main node upgrades.

Besides the new health check, the monitor had fixes to its rate calculation at low TPS, not sampling when idle, node validation, and the alerts it generates. The mainnet network configuration of the monitor now points to the public mirror node and we've added the new previewnet node to the previewnet network configuration.

There were also a number of other fixes to clean up code and fix tests. We've made an effort to reduce our code smells as seen in [SonarCloud](https://sonarcloud.io/dashboard?id=hedera-mirror-node).

## Latest Releases

## [v0.38.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.38.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 16, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: AUGUST 13, 2021**
{% endhint %}

This release wraps up NFT and custom fee support by adding additional test coverage and fixing any remaining bugs. Specifically, NFT support was added to our monitor tool and our acceptance tests. Custom fees was also added to the acceptance tests and had some bug fixes.

Mainnet public saw some monitoring improvements including adding HTTPS support to our external monitor dashboard and the addition of a platform not active alert that inhibits all other alerts.

There were a number of bug fixes in this release. The stream file health check that was disabled in the last release due to a bug was fixed and re-enabled. The address book update flow saw a couple of important fixes as well.

#### Breaking Changes

The payer account ID in transaction assessed custom fee REST API response was removed. This is a change in services 0.16 whereby custom fees are now charged from the account who send the triggering tokens, not necessarily the payer of the transaction.

## [v0.37.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.37.2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 4, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: AUGUST 5, 2021**
{% endhint %}

A small bug fix release that addresses some issues with our [HIP-18](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-18.md) support.

## [v0.37.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.37.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JULY 29, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JULY 15, 2021**
{% endhint %}

This release broadens our support for [non-fungible tokens](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) \(NFTs\) with new NFT-specific REST APIs. A new API was added to return a list of NFTs for a particular token ID. We also added a new API to return a single NFT by its token ID and serial number. Finally, we added an API to see the transaction history for a particular NFT. In an effort to have more manageable REST API code, we now adopt a more object-oriented approach by utilizing models, view-models and services. Below is an example of the three new APIs:

`GET /api/v1/tokens/0.0.1500/nfts`

```text
{
  "nfts": [{
    "account_id": "0.0.1002",
    "created_timestamp": "1234567890.000000010",
    "deleted": false,
    "metadata": "ahf=",
    "modified_timestamp": "1234567890.000000010",
    "serial_number": 2,
    "token_id": "0.0.1500"
  },{
    "account_id": "0.0.1001",
    "created_timestamp": "1234567890.000000009",
    "deleted": false,
    "metadata": "bTM=",
    "modified_timestamp": "1234567890.000000008",
    "serial_number": 1,
    "token_id": "0.0.1500"
  }],
  "links": {
    "next": null
  }
}
```

`GET /api/v1/tokens/0.0.1500/nfts/1`

```text
{
  "account_id": "0.0.1001",
  "created_timestamp": "1234567890.000000008",
  "deleted": false,
  "metadata": "bTM=",
  "modified_timestamp": "1234567890.000000009",
  "serial_number": 1,
  "token_id": "0.0.1500"
}
```

`GET /api/v1/tokens/0.0.1500/nfts/1/transactions`

```text
{
  "transactions": [{
    "consensus_timestamp": "1234567890.000000009",
    "transaction_id": "0.0.8-1234567890-000000009",
    "receiver_account_id": "0.0.1001",
    "sender_account_id": "0.0.2001",
    "type": "CRYPTOTRANSFER"
  }, {
    "consensus_timestamp": "1234567890.000000008",
    "transaction_id": "0.0.8-1234567890-000000008",
    "receiver_account_id": "0.0.2001",
    "sender_account_id": null,
    "type": "TOKENMINT"
  }],
  "links": {
    "next": null
  }
}
```

## [v0.36.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.36.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JULY 19, 2021**
{% endhint %}

We are happy to [announce](https://hedera.com/blog/now-available-public-mainnet-mirror-node-managed-by-hedera) the availability of a publicly accessible, free-to-use, mainnet Mirror Node operated by the Hedera team. As part of this, we put a large amount of effort into fine-tuning our Kubernetes deployment. We migrated to [Flux 2](https://fluxcd.io/), a GitOps-based deployment tool that allows us to declaratively specify the expected state of the Mirror Node in git and manage our rollouts. You can browse our [deploy branch](https://github.com/hashgraph/hedera-mirror-node/tree/deploy) and see the exact config and versions rolled out to various clusters and environments. The Helm chart was updated to add `PodDisruptionBudgets`, adjust alert rules and other improvements to make it easier to automate the deployment.

This release is the first version of the Mirror Node with preliminary support for non-fungible tokens \(NFTs\). NFT support is being added to the Hedera nodes as outlined in [HIP 17](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md). We spent time [designing](https://github.com/hashgraph/hedera-mirror-node/blob/v0.36.0/docs/design/nft.md) how that NFT support will look like for the Mirror Node. Modifications to the schema were made to add new tables and fields and the Importer was updated to ingest NFT transactions. The existing REST APIs were updated to add NFT related fields to the response. This includes adding a `type` field to the token related APIs to indicate fungibility and a`nft_transfers` to `/api/v1/transactions/{id}`:

```text
{
  "transactions": [{
      "consensus_timestamp": "1234567890.000000001",
      "name": "CRYPTOTRANSFER",
      "nft_transfers": [
        {
          "receiver_account_id": "0.0.121",
          "sender_account_id": "0.0.122",
          "serial_number": 104,
          "token_id": "0.0.14873"
        }
      ]
  }]
}
```

One thing to note is that we did not add NFT transfers to the list transactions endpoint in an effort to reduce the size and improve the performance of that endpoint. In the next release, we will add new NFT specific REST APIs.

Continuing upon the theme of the last release, we made additional changes to the Rosetta API to bring it up to par with the rest of the components. Rosetta now includes support for HTS via both is data and construction APIs.

The Importer saw a large focus on improving performance and resiliency. It is now highly available \(HA\) when run inside Kubernetes. This allows more than one instance to run at a time and to failover to the secondary instance when the primary becomes unhealthy. A special Kubernetes ConfigMap named `leaders` is used to atomically elect the leader.

We’re improving our ingestion time dramatically for entity creation. Previously those were database finds followed by updates. Since inserts are always faster than find and updates, we’ve optimized this to insert the updates into a temporary table and at the end upsert those to the final table. A record file with 6,000 new entities went from 21 seconds to 600 ms, making it 35x improvement. Balance file processing was optimized to greatly reduce memory by only keeping one file in memory at a time.

### Breaking Changes

In honor of [Juneteenth](https://en.wikipedia.org/wiki/Juneteenth) and as part of the general industry-wide movement, we renamed our `master` branch to `main`. If you have a clone or fork of the Mirror Node Git repository, you will need to take the below steps to update it to use `main`:

```text
git branch -m master main
git fetch origin
git branch -u origin/main main
git remote set-head origin -a
```

As part of our optimization to reduce memory usage, we now process some things earlier in the lifecycle. Due to this we had to rename some properties to reflect this change. We also changed the disk structure if you are using the `keepFiles` \(now renamed to `writeFiles`\) properties to write the stream files to disk after download. It is no longer archived into folders by day. Instead, the folder structure will exactly match the structure in the bucket. This opens the possibility for a mirror node to download and mirror the bucket itself using a S3 compatible API like [MinIO](https://min.io/). Below is a summary of the renamed properties:

* Renamed `hedera.mirror.importer.downloader.balance.keepSignatures` to `hedera.mirror.importer.downloader.balance.writeSignatures`
* Renamed `hedera.mirror.importer.parser.balance.keepFiles` to `hedera.mirror.importer.downloader.balance.writeFiles`
* Renamed `hedera.mirror.importer.parser.balance.persistBytes` to `hedera.mirror.importer.downloader.balance.persistBytes`
* Renamed `hedera.mirror.importer.downloader.event.keepSignatures` to `hedera.mirror.importer.downloader.event.writeSignatures`
* Renamed `hedera.mirror.importer.parser.event.keepFiles` to `hedera.mirror.importer.downloader.event.writeFiles`
* Renamed `hedera.mirror.importer.parser.event.persistBytes` to `hedera.mirror.importer.downloader.event.persistBytes`
* Renamed `hedera.mirror.importer.downloader.record.keepSignatures` to `hedera.mirror.importer.downloader.record.writeSignatures`
* Renamed `hedera.mirror.importer.parser.record.keepFiles` to `hedera.mirror.importer.downloader.record.writeFiles`
* Renamed `hedera.mirror.importer.parser.record.persistBytes` to `hedera.mirror.importer.downloader.record.persistBytes`

## [v0.35.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.35.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JULY 8, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 21, 2021**
{% endhint %}

Most of the changes in this release were operational improvements around our Kubernetes deployment. These changes were necessary as we begin to convert more environments from virtual machines to Kubernetes-based. We added our acceptance tests to the Helm chart so that it can trigger automatically during upgrades and verify the deployment was successful. On the importer, we added a new health check to the probes that verifies that stream files are successfully being parsed. And we fixed the importer so that the probes are started before long-running database migrations, allowing us to finally enable its liveness probe. There were a lot of smaller fixes to the charts, so please see the linked PRs for further details.

The monitor saw a brand new REST API that lists active subscriptions. This is used in our cluster to determine overall cluster health and route traffic via our load balancers. We added an OpenAPI spec and Swagger UI for this API as well.

Special thanks to [@si618](https://github.com/si618) for fixing the build on Windows and adding a GitHub workflow to make sure it stays fixed.

And also thanks to [@safinbot](https://github.com/safinbot) for addressing some errors in our REST API OpenAPI specification.

### Breaking changes

The REST API maximum and default limit was lowered from 1000 to 500. If you explicitly send a number of more than 500, your request will fail. Please update your client code appropriately.

## [v0.34.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.34.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 16, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 11, 2021**
{% endhint %}

In Hedera Mirror Node v0.34.0, we started work on [designing](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/design/nft.md) support for [NFTs](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) that will come in a future Hedera Services release.

By default, the mirror node will validate that at least one-third of all nodes in the address book have signed a stream file before importing it into its database. This ensures that the main nodes have reached two-thirds consensus on the transactions in the file. For performance or verification reasons, you may want to decrease or increase this default percentage. To support this use case, we added a `hedera.mirror.importer.downloader.consensusRatio` property that controls the ratio of verified nodes \(nodes used to come to consensus on the signature file hash\) to the total number of nodes available.

We took the time to undertake some major dependency upgrades for the Rosetta API. This included major updates to the Hedera and Rosetta SDKs that both required a large amount of refactoring. A number of bugs in Rosetta were addressed as well as improvements to Rosetta's CI workflow. These changes lay the groundwork for additional Rosetta improvements in a future release.

To avoid duplication, we wanted to unify our JMeter and Monitor performance tests. To do so, we needed the newer monitor tool to have feature parity with our JMeter tests. To accomplish this, we've split the publish to HAPI and subscribe to mirror node flows in the monitor to allow for subscribe only. In this iteration, only the gRPC API supports subscribe only. With this change, we were able to remove our JMeter code and optimize the `hedera-mirror-test` image from 1.5G to 0.5G.

We made some operational improvements to our helm chart including alert dependencies. Alert dependencies help avoid a flood of alerts that are all related to the same root cause. We also made some bug fixes to the chart that could occur when enabling or disabling some components in favor of external databases or message buses.

## [v0.33.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.33.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 10, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 21, 2021**
{% endhint %}

This release adds support for HAPI 0.13.2. This brings with it a new address book file format that is more compact and doesn't duplicate IP address and port information. We took the time to adjust our database to reflect the newer format while maintaining compatibility with the older format.

A big focus of this release was on improving the Helm charts for use in production deployments. We now auto-generate passwords for components that require one and ensure they remain the same on upgrades by using Helm's [lookup](https://helm.sh/docs/chart_template_guide/functions_and_pipelines/#using-the-lookup-function) feature. We added `env`, `envFrom`, `volumes`, `volumeMounts` properties to all charts for more flexible configuration. We added a `global.image.tag` chart property to make it easier to test out custom versions. And we made it easier to use dependencies that can be outside the cluster like Redis and PostgreSQL.

Some internal improvements saw us automating our release process so that version bumps and release note generation can be kicked off via GitHub. This now also includes generating a CHANGELOG and keeping it up to date with the release notes. And finally we updated our acceptance tests to automatically pull and use the latest address book along with validating all nodes to ensure only the latest, valid nodes are used for validation.

## [v0.32.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.32.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 19, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 11, 2021**
{% endhint %}

In this release we took the time to do some performance optimizations of both the importer and the monitor. If you're using a containerized mirror node, the Java applications now uses more of the available memory that's already been allocated to it. We optimized the size of some internal queues to reduce the likelihood of out of memory errors. And we now use a more efficient streaming method to write entities to the database and avoid large memory allocations. All these combine to greatly reducing overall memory usage and improve overall performance for the importer. The monitor also saw performance improvements to allow it to publish transactions at a rate of 10,000 TPS.

This release updates more of our system to handle the revised scheduled transaction design that will be available soon on mainnet. Both the acceptance tests and monitor were updated to be able to publish the new transactions.

We now expose the raw transaction bytes encoded in Base64 format in the REST API. Persisting the bytes of the `Transaction` protobuf in the database is an option that's been available for a while but until now has not been available via the API. Persisting the data is off by default as does increase the size of the database quite a bit. The Hedera managed mirror nodes will not have that functionality turned on to reduce storage.

## [v0.31.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.31.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 30, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: APRIL 26, 2021**
{% endhint %}

After scheduled transactions were made available in previewnet, we listened to user feedback and further iterated on the design to make it easier to use. This release adds support for this [revised scheduled transactions](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md) design planned to be released in HAPI v0.13. There was no impact to our REST API format, only the importer needed to be updated to parse and ingest the new proto format. Our monitor API and acceptance tests will be adjusted in the next release once the SDKs add support for the new design.

This release also adds support for the newly announced account balance file format that was released in HAPI v0.12. The new [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/account_balance_file.proto)based format will eventually replace the CSV format in July 2021. Until then, both formats will exist simultaneously in the bucket so users can transition at their leisure. Besides being more efficient to parse, the new files are also compressed using Gzip for reduced storage and download costs. We also took the time to improve the balance file parsing performance regardless of format. Average parse times should decrease by about 27%.

For our REST API, we now expose an `entity_id` field on our transactions related APIs. This field represents the main entity associated with that transaction type. For example, if it was a HCS transaction it would be the topic ID created, updated, or deleted.

`GET /api/v1/transactions/0.0.1009-1234567890-999999998`

```text
{
  "transactions": [{
    "consensus_timestamp": "1234567890.999999999",
    "entity_id": "0.0.108763",
    "valid_start_timestamp": "1234567890.999999998",
    "charged_tx_fee": 0,
    "memo_base64": null,
    "result": "SUCCESS",
    "scheduled": false,
    "transaction_hash": "aGFzaA==",
    "name": "CRYPTOUPDATEACCOUNT",
    "node": "0.0.3",
    "transaction_id": "0.0.1009-1234567890-999999998",
    "valid_duration_seconds": "11",
    "max_fee": "33",
    "transfers": []
  }]
}
```

We continue to make progress towards our goal of switching to TimescaleDB. We fixed the user and database initialization issues and tested a migration from PostgreSQL. We switched out the TimescaleDB Helm chart to a more stable one and explored our hosting options for production. Finally, we switched to SCRAM-SHA-256 to improve the security of our database user authentication.

### Breaking changes:

There were a number of breaking changes this release to be aware of. If you're using our Helm chart, we have switched the importer from a `StatefulSet` to a `Deployment` since it no longer has the need for a persistent volume. We also switched the Traefik dependency from a `Deployment` to a `DaemonSet`. Both of these will require manual intervention to delete the old workload before upgrading. Support for Helm 2 was dropped since it is no longer [supported](https://helm.sh/blog/helm-v2-deprecation-timeline/) by the community after November 13, 2020. If you're directly reading from our database, a rename of the `t_entities` table and its columns may impact you as well.

## [v0.30.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.30.0)

Mirror node v0.30 brings operational improvements with changes to our continuous integration and monitoring components.

With this release, we've completed the migration from CircleCI to GitHub Actions. CircleCI had some limitations with our use of [Testcontainers](https://www.testcontainers.org/) for unit testing against 3rd party dependencies. We previously had a mixture of GitHub Actions and CircleCI with the latter using slightly different commands than local testing. Consolidating to GitHub Actions allowed us to reduce this difference and further parallelize our checks.

To improve our runtime observability and testing coverage, we've continued to invest in our monitor tool this cycle. Scheduled transaction support was recently added supporting both `ScheduleCreate` and `ScheduleSign` operations. We've added the three new mainnet nodes the monitor's default configuration. A bug with the monitor unable to reach expected TPS with multiple scenarios was fixed.

The REST API also saw some bug fixes including a fix to queries with a credit/debit parameter now able to retrieve token only transfers. The transaction API now populates the token transfers list for all transaction types instead of being limited to just crypto transfers.

## [v0.29.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.29.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 5, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 26, 2021**
{% endhint %}

This release brings an assortment of under the hood improvements across modules and refinements of multiple REST API's.

Historical entity information prior to OA is now available. In this release we've added a repeatable Java migration that will import entity information from a mainnet network snapshot. This runs during upgrade, is configureable \(`hedera.mirror.importer.importHistoricalAccountInfo`\) and works in combination with the `hedera.mirror.importer.startDate`setting.

The REST API now expands its filtering options support specifically around transfers and in relation to tokens. Previously the `account.id`and `credit/debit` filtering options supported HBAR transfers only, this release expands both filters to include tokens also.

The stateproof REST API and `check-state-proof` package have also been improved. The API now supports filtering for scheduled transactions via `/api/v1/transactions/:transactionId/stateproof?scheduled=true` as-well as a more compact response format. For record streams that utilize the newer improved HAPI v5 version the stateproof API response send back metadata hashes instead of the full raw bytes. With this, the response is more light weight.

```text
 {
     "address_books": [
       "address book content"
     ],
     "record_file": {
       "head": "content of the head",
       "start_running_hash_object": "content of the start running hash object",
       "hashes_before": [
         "hash of the 1st record stream object",
         "hash of the 2nd record stream object",
         "hash of the (m-1)th record stream object"
       ],
       "record_stream_object": "content of the mth record stream object",
       "hashes_after": [
         "hash of the (m+1)th record stream object",
         "hash of the (m+2)th record stream object",
         "hash of the nth record stream object"
       ],
       "end_running_hash_object": "content of the end running hash object",
     },
     "signature_files": {
       "0.0.3": "signature file content of node 0.0.3",
       "0.0.4": "signature file content of node 0.0.4",
       "0.0.n": "signature file content of node 0.0.n"
     },
     "version": 5
 }
```

The REST API now also supports repeatable `account.id` query parameters when filtering, with a configureable setting for the maximum number of repeated query parameters  
`/api/v1/(accounts|balances|transactions)?account.id=:id&account.id=:id2...`

`GET /api/v1/accounts?account.id=0.0.7&account.id=0.0.9`

```text
{
     "accounts": [
       {
         "balance": {
           "timestamp": "0.000002345",
           "balance": 70,
           "tokens": [
             {
               "token_id": "0.0.100001",
               "balance": 7
             },
             {
               "token_id": "0.0.100002",
               "balance": 77
             }
           ]
         },
         "account": "0.0.7",
         "expiry_timestamp": null,
         "auto_renew_period": null,
         "key": null,
         "deleted": false
       },
       {
         "balance": {
           "timestamp": "0.000002345",
           "balance": 90,
           "tokens": []
         },
         "account": "0.0.9",
         "expiry_timestamp": null,
         "auto_renew_period": null,
         "key": null,
         "deleted": false
       }
     ],
     "links": {
       "next": null
     }
   }
```

Multiple modules have also seen security and standardization improvements by the addition of more robust automated analysis tools such as `gosec` as-well as the implementation of suggestions from a 3rd party code audit.

This release also saw a step to support the new and improved v2 offerings of the \(Java SDK\)\[[https://github.com/hashgraph/hedera-sdk-java\](https://github.com/hashgraph/hedera-sdk-java\)\]. Both the monitor module and acceptance tests were updated to use the new SDK and utilize features such as in-built retry and support for scheduled transactions.

## [v0.28.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.28.2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MARCH 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 10, 2021**
{% endhint %}

This releases finalizes support for scheduled transactions and HAPI protobuf v0.12. Two new schedule specific REST APIs were added including `/api/v1/schedules` and `/api/v1/schedules/:id`. The former lists all schedules with various filtering options available and the latter returns a specific schedule by its schedule ID.

`GET /api/v1/schedules?account.id=0.0.1024&schedule.id=gte:4000&order=desc&limit=10`

```text
{
    "schedules": [
      {
        "admin_key": {
          "_type": "ProtobufEncoded",
          "key": "7b2233222c2233222c2233227d"
        },
        "consensus_timestamp": "1234567890.000030003",
        "creator_account_id": "0.0.1024",
        "executed_timestamp": null,
        "memo": "Created per governing council decision dated 02/03/21",
        "payer_account_id": "0.0.1024",
        "schedule_id": "0.0.4000",
        "signatures": [
          {
            "consensus_timestamp": "1234567890.000030001",
            "public_key_prefix": "CQkJ",
            "signature": "CQkJ"
          }
        ],
        "transaction_body": "AQECAgMD"
      }
    ],
    "links": {
      "next": null
    }
}
```

In HAPI v0.12, new memo fields were added to all entity types bringing parity across all services. Mirror node now supports the new fields including for update operations where the memo field can be set to `null`, empty string or a non-empty string to keep, clear or replace the existing memo, respectively.

Historically, the importer application has always downloaded stream files and saved to the filesystem in one thread then read those files and ingested them into the database in another thread. This has sometimes caused the database and filesystem to get out of sync and require manual intervention to fix. It also makes the importer stateful and as a result could not support running multiple instances for high availability.

With this release, we've removed the need for importer to read and write to the filesystem. Instead, the downloader and parser threads now communicate via an in-memory queue. To accomplish this, we also had to remove the `t_application_status` table in favor of calculating the last successful file directly from the stream file tables. In addition to fixing the aforementioned issues, the removal of the filesystem has resulted in a 5% latency improvement.

Other changes include adding an `index` field to `record_file` table to simulate a blockchain index and updating our [Google Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) to v0.27. Also, we added support for the v5 stream files to the `check-state-proof` client app.

## [0.27.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.27.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: FEBRUARY 11, 2021**
{% endhint %}

This release adds a new REST API component that implements the [Rosetta API](https://www.rosetta-api.org/). The Rosetta API is an open standard for integrating with blockchain-oriented systems. Implementing the Rosetta AP provides a number of advantages. It reduces the time and effort it takes for wallets, exchanges, etc. to integrate with the Hedera network if they have integrated with Rosetta in the past. Even if the systems integrator has not used Rosetta previously, using the Rosetta API in lieu of our separate [REST API](https://docs.hedera.com/guides/docs/mirror-node-api/cryptocurrency-api) might be useful to reduce the friction with using a non-blockchain DLT like Hedera.

[Scheduled transactions](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) is an new feature being added to the main nodes in a future release. Scheduled transactions allows transactions to be submitted without all the necessary signatures and will execute once all the required parties sign. The mirror node has been updated to understand and store these new types of transactions. Additionally, we've updated our existing REST APIs to expose this information. The next release will add additional schedule specific REST APIs.

We made a concerted effort this release to improve our tests. Most of our flaky tests were fixed so that our continuous integration runs smoother. We also improved the stability of our acceptance tests. The REST API monitor also had some logging and useability fixes to aid in production observability.

## [v0.26.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.26.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 1, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JANUARY 22, 2021**
{% endhint %}

This release is mainly focused on adding support for the upcoming features in the main nodes. We added support for the `newTotalSupply` field to the transaction record in HAPI `0.10.0`. We also documented our [design](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/design/scheduled-transactions.md) for the upcoming [scheduled transactions](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) services that's coming in a future release of HAPI. Our next minor version will have preliminary support for that.

But by far the biggest change is support for the new record file V5 and signature file V5 format. These files are uploaded to cloud storage and pulled by the mirror nodes to populate its database. Since it's the core communication format between the main nodes and mirror nodes, it took a bit of refactoring and new code to support the new format while retaining compatibility with previous stream files.

#### Warning! If you don't upgrade your Mirror Node to v0.26.0 or later before HAPI v0.11.0 is released in a few weeks, your mirror node will be unable to process new transactions.

We continued our progress on switching to TimescaleDB. We integrated a TimescaleDB helm chart into our Kubernetes deployment and added migration scripts to convert from PostgreSQL to TimescaleDB. We're still in the testing phase so it's still recommended to stick with the v1 schema \(the default\) for now.

## [v0.25.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.25.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 12, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JANUARY 8, 2021**
{% endhint %}

This release saw a slew of enhancements to our new monitoring module. The [monitor](https://github.com/hashgraph/hedera-mirror-node/blob/v0.25.0/docs/monitor.md) is a standalone component that can publish and subscribe to transactions from various Hedera APIs to gauge the health of the system. New in this release is the ability to automatically create entities on startup using a new expression syntax. This is useful to avoid boilerplate configuration and manual entity creation steps that vary per environment.

A sample percentage property was added to the monitor to control how often the REST API should be verified. We took the time to properly document the monitor tool detailing its configuration and operational steps. Finally, we added a number of new metrics and a Grafana [dashboard](https://github.com/hashgraph/hedera-mirror-node/blob/v0.25.0/docs/monitor.md#dashboard--metrics) to view them.

We made progress towards our goal of replacing PostgreSQL with [TimescaleDB](https://www.timescale.com/). This release contains the initial database migrations to setup the mirror node from scratch using TimescaleDB. These migrations are hidden behind a feature flag. In an upcoming release we'll add further functionality including data migration scripts and Helm support.

## [v0.24.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.24.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 28, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: DECEMBER 10, 2020**
{% endhint %}

This release adds [OpenAPI 3.0](https://swagger.io/specification) specification support to our REST API. The OpenAPI specification is available at `/api/v1/openapi.yml`and serves as a formal specification of our API. Clients can use the specification to shorten the amount of time it takes to integrate with our API by generating code or tests harnesses. It also provides us with a new auto-generated API documentation site viewable at `/api/v1/docs`.

We now have support for the [AWS Default Credential Provider Chain](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html). Now instead of only being able to provide static access and secret keys in the configuration, you can rely on the default provider chain to retrieve your credentials automatically from the environment \(environment variables, `~/.aws/credentials`, etc\). See our [documentation](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/configuration.md#connect-to-s3-with-the-default-credentials-provider) for more information.

We've enhanced our monitoring tools to provide greater observability into the mirror node's operation. In addition to publishing, our monitor tool now supports subscribing to the gRPC and REST APIs to verify end to end functionality of Hedera. It will also generate metrics off this information. We take advantage of Loki's new log alerting capability and now can alert off of any errors we see in logs that might be cause for concern.

## [v0.23.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.23.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 20, 2020**
{% endhint %}

This release focuses on finalizing our support for the new Hedera Token Service \(HTS\) provided by the Hedera API [v0.9.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0). There are no new HTS features, just various fixes to make it compatible with the latest protobuf. HTS is currently enabled in previewnet and should be enabled in testnet very soon, so please try it out and let us know if you have any feedback.

A new Helm [chart](https://github.com/hashgraph/hedera-mirror-node/tree/master/charts/hedera-mirror-monitor) was added to run the monitor application. The monitor is still under heavy development so stay tuned.

Most of the other changes were bug fixes. We now use SonarCloud to scan for vulnerabilities and bugs and have addressed all the major items. You can view our SonarCloud [dashboard](https://sonarcloud.io/dashboard?id=hedera-mirror-node) to track our progress. Entities are now only inserted for successful transactions and we fixed the wrong address book being updated. There were multiple issues with the state proof alpha API that were resolved. For the gRPC API, we improved its performance and lowered its CPU usage. Also related to gRPC, we now enable server sent keep alive messages and permit a lower client sent keep alive messages of 90 seconds, which should hopefully address timeout issues that some users have reported.

## [v0.22.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.22.0)

This release continues our improvements to our Kubernetes support as well as monitoring and performance improvements across the modules.

We improved our custom PrometheusRule alerts for the Importer, GRPC and REST API modules, as well as added dashboards for our gRPC and REST API modules. Additionally, we increased our pod resources limits to optimize Importer ingestion and gRPC streaming performance in a Kubernetes cluster. Our existing js based monitor and REST performance tests were both updated to include HTS support.

We also improved our data generator module with support for the majority of HAPI transactions the mirror node importer ingests. Additionally, we also added a java based monitor module that supports generation and publishing of transactions.

This release also includes an improvements to avoid the stale account info bug that stems from balance stream files being received at a slower frequency than record stream files. Now account creations and account info changes will be reflected in REST API call even though the updated balance may not have been received.  
We also extended our REST API support to include case insensitive support query parameters. `/api/v1/transactions?transactionType=tokentransfers` and `/api/v1/transactions?transactiontype=tokentransfers` are now both acceptable.

## [v0.21.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.21.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 24, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 13, 2020**
{% endhint %}

This release continues our focus on the Hedera Token Service \(HTS\) by adding three new token REST APIs. A token discovery REST API `/api/v1/tokens` shows available tokens on the network. A token REST API `/api/v1/tokens/${tokenId}` shows details for a token on the network. A token supply distribution REST API `/api/v1/tokens/${tokenId}/balances` shows token distribution across accounts. These APIs have already made their way to previewnet so check them out!

Continuing our HTS theme, we enhanced our testing frameworks with token support. Our acceptance tests can send HTS transactions to HAPI and wait for those transactions to show up via the mirror node REST API. Additionally, our performance tests can simulate a HTS load to test how the system responds to HTS transactions.

We improved our existing REST APIs by adding a way to filter by transaction type. When searching for transactions or showing the transactions for a particular account you can now filter via an optional `transactionType` query parameter. This feature can be used with the transactions API in the format `/api/v1/transactions?transactionType=tokentransfers` while the format for the accounts API is `/api/v1/accounts/0.0.8?transactionType=TOKENTRANSFERS`.

We improved our Kubernetes support with AlertManager integration. There are now custom `PrometheusRule` alerts for each component that trigger notifications based upon Prometheus metrics. A custom Grafana dashboard was created that shows currently firing alerts.

## [v0.20.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.20.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 11, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 3, 2020**
{% endhint %}

This is a big release that contains support for a new HAPI service and whole new runtime component to dramatically improve performance. Due to the magnitude of the changes, it did take us a little longer to mark it as generally available as we wanted to ensure it was tested as much as possible beforehand.

First up is support for the Hedera Token Service \(HTS\) that was recently [announced](https://hedera.com/blog/previewnet-hedera-token-service-hts-early-access) and rolled out to previewnet. A lot of work was put into supporting the new transaction types on the parser side including enhancing the schema with new tables and ingesting them via the record stream. HTS also required a new balance file version that adds token information to the CSV. Token information is now returned for our existing REST APIs while the next release will contain token specific REST APIs for further granularity. Check it out in previewnet and let us know if you have any feedback!

We made a lot of strides in improving the ingestion performance in previous releases, but since we also wanted to ensure low end to end HCS latency via our gRPC API we had to sacrifice some of that speed. As a result, we could only ingest at about 3,000 transactions per second \(TPS\) before latency spiked above 10 seconds. This was entirely due to our use of PostgreSQL notify/listen to notify the gRPC API of new data.

In this release, we add a new notification mechanism without sacrificing speed or latency with our support for Redis pub/sub. With Redis, the mirror node can now ingest at least 10,000 TPS while still remaining under 10 seconds from submitting the topic message and receiving it back via the mirror node's streaming API. Redis is enabled by default, but it can be turned off if HCS latency is not a concern and you want to avoid another runtime dependency.

We also added support for the HAPI protobuf [changes](https://hedera.com/blog/changes-to-hedera-api-hapi-for-v0-8-0-and-v0-9-0) that are coming in v0.9.0. The protobuf is removing two deprecated fields while adding a new `signedTransactionBytes` field. Since the mirror node still needs to support historical transactions we retain support for parsing transactions that contain the old payload format.

## [v0.19.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.19.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: OCTOBER 6, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 29, 2020**
{% endhint %}

This release finishes the State Proof alpha REST API and makes it [generally available](http://www.hedera.com/blog/introducing-state-proof-alpha). As part of this, we made a lot of improvements to the check-state-proof command line tool that queries the API and validates the files locally. We also now store the node account used to verify record file, ensuring greater accuracy as to the provenance of the state proof.

There's been some changes to the public Hedera environments lately and we've updated the mirror node to reflect that. We added support for the new previewnet environment and we updated the configuration to point to the new testnet bucket after its recent reset. Please ensure your mirror node has all of the data in the previous bucket before updating to this release, assuming you're not specifying the bucket name manually.

We added proper liveness and readiness probe endpoints for all our components. If you're not familiar with the concept of liveness and readiness probes, check out the Kubernetes [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) on the subject. Our new liveness endpoint now does not fail if external dependencies are down like the database, ensuring the application doesn't restart unnecessarily. Even if you're not using Kubernetes it would be worthwhile to look into to ensure your mirror node is using the appropriate endpoint for health checks, based upon your needs.

## [v0.18.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: SEPTEMBER 22, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 15, 2020**
{% endhint %}

Fix two regressions in the 0.18 release train.

## [v0.18.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.1)

Contains a small change to the State Proof Alpha REST API to only return the current address book for now.

## [v0.18.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.18.0)

Building upon the availability of the [State Proof Alpha REST API](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/docs/design/stateproofalpha.md) in the last release, we've added [sample code](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/hedera-mirror-rest/state-proof-demo) in JavaScript to retrieve the state proof from a mirror node and locally verify it. This allows users to obtain cryptographic proof that a particular transaction took place on Hedera. The validity of the proof can be checked independently to ensure that the supermajority of Hedera mainnet stake had reached consensus on that transaction. Similar to the promise of the ultimate state proofs, the user can trust this state proof alpha served by the mirror nodes, even when the user does not trust the mirror nodes.

Importer can now be configured to connect to Amazon S3 using temporary security credentials via [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html). With this, a user that does not have permission to access an AWS resource can request a temporary role that will grant them that permission. See the [configuration documentation](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/docs/configuration.md#connect-to-s3-with-assumerole) for more information.

Importer also added two new properties to control the subset of data it should download and validate. The `hedera.mirror.importer.startDate` property can be used to exclude data from before this date and "fast-forward" to the point in time of interest. By default, the `startDate` will be set to the current time so mirror node operators can get up and running quicker with the latest data and reduce cloud storage retrieval costs. Note that this property only applies on the importer's first startup and can't be changed after that. The `hedera.mirror.importer.endDate` property can be used to exclude data after this date and halt the importer. By default it is set to a date far in the future so it will effectively never stop.

### Breaking Changes

The aforementioned `startDate` property does change how the mirror node operators on initial start from previous releases. By defaulting to now, users standing up a new mirror node will no longer retrieve all historical data and will instead only retrieve the latest data. Current users upgrading to this release will not be affected even if their data ingest is not fully caught up since this property only applies if the database is empty like it is on first start. To revert to the previous behavior, a date in the past can be specified like the Unix epoch `1970-01-01T00:00:00Z`.

## [v0.17.3](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.3)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: SEPTEMBER 14. 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: SEPTEMBER 3, 2020**
{% endhint %}

This release contains the port of a bug fix to better manage the `VertxException: Thread blocked` issue seen in [\#945](https://github.com/hashgraph/hedera-mirror-node/issues/945)

## [v0.17.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.2)

A small bug fix to better support resetting the mirror node when a stream reset is performed on the network environment

## [v0.17.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.1)

A small fix to correct a performance regression with not properly caching a heavily used query.

## [v0.17.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.0)

This release adds support for the storage of the network address books from file `0.0.101` and `0.0.102`in the mirror node database.  
The mirror node will now retrieve file address book contents which include node identifiers and their public keys from the database instead of the file system at startup.

This sets the stage for an additional feature which is the State Proof alpha REST API at `/transactions/${transactionId}/stateproof`.  
With this release it is possible to request the address book, record file and signature files that contain the contents of a transaction and allow for cryptographic verification of the transaction. Mirror node users can now actively verify submitted transactions for themselves.

Other changes include support for continuous deployment \(CD\) using [Github Actions](https://github.com/features/actions) that use [FluxCD](https://fluxcd.io/) to deploy master versions to a Kubernetes cluster. Additionally, this release includes fixes to the database copy operation optimization and improved handling of buffer size used when copying large topic messages.

## [v0.16.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.16.0)

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: AUGUST 18, 2020**
{% endhint %}

This release includes the foundation for some larger features to come. Notably, cloud bucket names are now set based on network specifications and users no longer need to explicitly state bucket names for demo, test and main networks. The `record_file` table contents are also expanded to include the start and end consensus timestamps of their containing transactions. The `record_file` table also saw a clean up to remove the path to the file.

Additionally, this release streamlines the helm chart architecture with a common chart for shared resources. It also adds dependabot to facilitate dependency update management. The parser was also update to handle signature files across multiple time bucket groups for greater parsing robustness.

Memory improvements were also made in the parser to improve ingestion performance. Due to performance pg notify was also removed in favor of direct psql notify to support faster streaming of incoming topic messages.

## [v0.15.3](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.3)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

Works around an issue sending large JSON payloads via pg\_notify by ignoring them for now. This occurs when a consensus message is sent with a message that exceeds 5824 bytes, which is also very close to the protobuf limit.

## [v0.15.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.2)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JULY 20, 2020**
{% endhint %}

This release improves the topic message ingest rate that regressed in the previous release. This is just a stop gap and future releases will increase this further.

## [v0.15.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JULY 15, 2020**
{% endhint %}

A hot fix release to address two high priority parsing errors with the new consensus message chunk header.

## [v0.15.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.15.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JULY 15, 2020**
{% endhint %}

This release adds support for HCS topic fragmentation that will soon be rolled out to main nodes in the 0.6.0 release. For larger consensus messages that don't fit in the max transaction size of 6144 bytes, a standard chunk info header can be supplied to indicate how that message should be split into smaller messages. The Mirror Node now understands this chunk information and stores it in the database. Additionally, it will return this [data](https://github.com/hashgraph/hedera-mirror-node/blob/a2f69ee1243fbbbfbc133549f9162bfc3a08f464/hedera-mirror-protobuf/src/main/proto/com/hedera/mirror/api/proto/ConsensusService.proto#L58) when subscribing to the topic via the gRPC API. The Java SDK is being updated to automatically split and reconstruct this message as appropriate.

Other changes include optimizations around end to end latency of the gRPC API. This was accomplished mainly by adding a new `NotifyingTopicListener` that uses PostgreSQL's LISTEN/NOTIFY functionality.

## [v0.14.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.14.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JULY 15, 2020**
{% endhint %}

This release further optimizes the ingestion rate. Initial tests indicate a 2x to 3x improvement.

## [v0.14.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.14.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JULY 15, 2020**
{% endhint %}

This release is all about performance optimizations. We reworked some of the foreign keys to improve the ingestion performance by a few thousand transactions per second. We also fixed an out of memory issue with the gRPC API and did some optimizations in that area.

Besides performance, we made some other small improvements. We now set `topicRunningHashV2AddedTimestamp` with a default value for mainnet, making it not fail on startup if a value is not provided. Containerized acceptance and performance tests were added, making it easier to test at scale.

#### Breaking Changes

We removed `hedera.mirror.grpc.listener.bufferInitial` and `hedera.mirror.grpc.listener.bufferSize` properties since we removed the shared poller's buffer.

We also renamed some tables and columns which would affect you if you directly use the database structure. We renamed `t_transactions` to `transaction`, `t_cryptotransferlists` to `crypto_transfer` and `non_fee_transfers` to `non_fee_transfer`.

## [v0.13.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.2)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JUNE 23, 2020**
{% endhint %}

Bug fix release to fix an out of memory issue with the gRPC API.

## [v0.13.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.1)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JUNE 23, 2020**
{% endhint %}

Small bug fix release to address grpc NETTY issue blocking acceptance tests

## [v0.13.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.13.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JULY 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: JUNE 23, 2020**
{% endhint %}

This release is a smaller release mainly focused on bug fixes with some minor enhancements. We added a new property `hedera.mirror.importer.downloader.endpointOverride` for testing. We also added `hedera.mirror.importer.downloader.gcpProjectId` to support specifying requester pays credentials with a personal account. Finally, we improved our Marketplace support getting us one closer to making it available.

## [v0.12.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.12.0)

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: MAY 29, 2020**
{% endhint %}

This feature release contains a few nice additions while fixing a few critical bugs. We made good progress on adding our application to [Google Cloud Platform Marketplace](https://console.cloud.google.com/marketplace). This should be wrapping up soon and enable a "one click to deploy" of the mirror node to Google's Cloud. Additionally, some extra fields were added to our APIs. We added `runningHashVersion` to the REST and GRPC APIs. Finally, we added `transactionHash` to the transaction REST API.

We improved the importer ingestion rate from 3400 to 5600 transactions per second in our performance test environment. There's still room for improvement and we plan on making additional performance optimizations in an upcoming release.

### Breaking Changes

We added an option to keep signature files after verification. By default, we no longer store signatures on the filesystem. If you'd like to restore the old behavior and keep the signatures, you can set `hedera.mirror.importer.downloader.record.keepSignatures=true` and `hedera.mirror.importer.downloader.balance.keepSignatures=true`.

We changed the bypass hash mismatch behavior in this release. Bypassing hash mismatch could be used in combination with other parameters to fast forward mirror node to newer data or to overcome stream resets. Previously you had to specify this via a database value in `t_application_status`. Since this data is not application state but considered more a user supplied value, we added a new property `hedera.mirror.importer.verifyHashAfter=2020-06-05T17:16:00.384877454Z` for this purpose.

## [v0.11.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.11.0)

{% hint style="success" %}
**MAINNET UPGRADE COMPLETED: JUNE 10, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPGRADE COMPLETED: MAY 29, 2020**
{% endhint %}

This release was mainly focused on refactoring code and properties as a necessary step for future enhancements. We also continued making improvements to our Kubernetes support. To that end, we added Prometheus REST metrics, Helm tests and Mirror Node can now run in GKE.

We added a new parameter to all of the topic related REST APIs to return a topic message in plaintext instead of binary. Messages submitted to HAPI are submitted as binary and stored in the Mirror Node that way as well. If you know the messages are actually strings encoded in UTF-8, then you can set `encoding=utf-8`and the REST API will make a best effort conversion to string. By default or if you pass a query parameter of `encoding=base64`, it will return the message as base64 encoded binary.

**Breaking Changes**

Please note when upgrading that we made major breaking changes to the naming of our configuration properties. We've renamed all `hedera.mirror.api` properties to `hedera.mirror.rest`. We also renamed the properties `apiUsername` to `restUsername` and `apiPassword` to `restPassword` to reflect that as well. Any properties that were used by the importer module were renamed to be nested under `hedera.mirror.importer`. We apologize for any inconvenience.

We've removed the `hedera.mirror.addressBookPath` property in favor of a `hedera.mirror.importer.initialAddressBook` property. The former was overloaded to be both the initial bootstrap address book and the live address book being updated by file transactions for `0.0.102`. The live address book is now hardcoded to `${hedera.mirror.importer.dataPath}/addressbook.bin` and cannot be changed.

The REST API to retrieve a topic message by its consensus timestamp now supports both a plural \(`/topics/messages/:consensusTimestamp`\) and singular \(`/topic/message/:consensusTimestamp`\) URI path. The singular format is deprecated and will be going away in the near future, so please update to the plural format soon.

We removed the singular form of a few alpha topic REST APIs. The `/topic/:id/message` API was removed in favor of the plural form `/topics/:id/messages`. Similarly, the `/topic/:id/message/:sequencenumber`API was removed in favor of its plural form `/topics/:id/messages/:sequencenumber`. Please update accordingly.

## [v0.10.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.10.1)

Small bug fix release to address a REST API packaging issue.

## [v0.10.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.10.0)

In preparation for Hedera Node release 0.5.0, we're releasing v0.10.0 to support the latest version of [HAPI](https://docs.hedera.com/guides/docs/hedera-api). The changes include renaming Claims to LiveHash and new response codes. One important HAPI change is the addition of a `topicRunningHashVersion` to the transaction record. This change was necessary as the way the topic running hash is changing with the release of 0.5.0. As a result, the Hedera Mirror Node added this new field to its database and a migration is ran to populate it with either the new or old version depending upon the release date of 0.5.0.

Unfortunately, this necessitated adding a required field `hedera.mirror.topicRunningHashV2AddedTimestamp` to control this behavior and will fail on startup if this is not populated. This is just a temporary measure. Once Hedera Node 0.5.0 is released to testnet and mainnet we will update this so it's automatically populated with the known date.

Other changes include adding Google PubSub support to publish JSON representing the `Transaction` and `TransactionRecord` protobuf to a message queue for external consumption. We've also added REST API metrics and added Traefik as an API gateway for our helm chart.

#### Breaking changes

We've had to remove our event stream support. This area of the code was never enabled and was untested and was incurring technical debt without providing any benefit. If it becomes necessary in the future, we can re-add it within our newly refactored framework.

The new `/api/v1/topics/:id` alpha REST API that was added in 0.9 has been changed to `/api/v1/topics/:id/messages`. This change was made to align the API with the other topic message APIs as it refers to the messages entity and not the topic entity.

## [v0.9.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.9.1)

Small bug fix release to address not being able to handle address book updates that span multiple transactions.

## [v0.9.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.9.0)

This release contains another new REST API for our [consensus service](https://www.hedera.com/consensus-service/). You can now retrieve all topic messages in a particular topic, with additional filtering by sequence number and consensus timestamp. Here's an example:

`GET /api/v1/topic/7?sequencenumber=gt:2&timestamp=lte:1234567890.000000006&limit=2`

```text
{
  "messages": [
    {
      "consensus_timestamp": "1234567890.000000003",
      "topic_id": "0.0.7",
      "message": "bWVzc2FnZQ==",
      "running_hash": "cnVubmluZ19oYXNo",
      "sequence_number": 3
    },
    {
      "consensus_timestamp": "1234567890.000000004",
      "topic_id": "0.0.7",
      "message": "bWVzc2FnZQ==",
      "running_hash": "cnVubmluZ19oYXNo",
      "sequence_number": 4
    }
  ],
  "links": {
     "next": "/api/v1/topic/7?sequencenumber=gt:2&timestamp=lte:1234567890.000000006&timestamp=gt:1234567890.000000004&limit=2"
  }
}
```

The other big feature of this release is [Kubernetes](https://kubernetes.io/) support. We've create a [Helm](https://helm.sh/) chart that can be used to deploy a highly available Mirror Node with a single command. This feature is still under heavy development as we work towards converting our current deployments to this new approach.

## [v0.8.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.8.1)

Small bug fix release to fix a packaging issue.

## [v0.8.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.8.0)

Mirror node v0.8.0 is here! We're made great strides in making the mirror node easier to run and manage. In particular, we added support for [requester pays](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html) buckets. This will allow anyone to run a mirror node as long as they are willing to pay for the cost to retrieve the data. Currently only Hedera and a few partners have access to the bucket, so enabling this will open up that data to our community. We are still working on a migration of the buckets to this model, so stay tuned.

We also added two new experimental REST APIs to retrieve HCS data. Firstly, we added `/api/v1/topic/message/${consensusTimestamp}` to retrieve a topic message by its consensus timestamp. Secondly, we added `/api/v1/topic/${topic}/message/${seqNum}` to retrieve a particular topic message by its sequence number from a topic. These APIs are considered alpha and may changed or be removed in the future. We also dramatically increased test coverage for the REST APIs and squashed some bugs in the process.

For our GRPC API, we had to switch from R2DBC to Hibernate to reach the scale and stability that we needed. In doing so, we can now support a lot more concurrent subscribers at a higher throughput. It should also finally put to rest any stability concerns with the GRPC component.

There are a few breaking changes that we had to make. We now no longer write and store record or balance files to the filesystem after they are inserted into the database. If you still need these files, you can set `hedera.mirror.parser.balance.keepFiles` and `hedera.mirror.parser.record.keepFiles` to true.

Also, we moved the persist properties to be grouped under a new path. That is we moved options like `hedera.mirror.parser.record.persistTransactionBytes` to `hedera.mirror.parser.record.persist.transactionBytes`. Please update your local configuration accordingly.

## [v0.7.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.7.0)

0.7.0 focuses on refactoring the record file parsing to decouple the parsing from the persisting of data. This refactoring is laying the groundwork for additional performance improvements and allowing additional downstream components to register for notification of the transactions.

## [v0.6.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.6.0)

* Release focused on stability and performance improvements. 
* End to end test coverage. 

This release was mainly focused on enhancing the stability and performance of the mirror node. We improved the transaction ingestion speed from 600 to about 4000 transactions per second. At the same time, we greatly improved the resiliency and performance of the GRPC module. We also added acceptance tests to test out HCS end to end.

**Breaking Change**

Please note that one potentially breaking change in this release is to reject subscriptions to topics that don't exist. This avoids the server having to poll repeatedly until it is created and taking up resources for a topic that may never exist. It is expected that clients or the [SDK](https://github.com/hashgraph/hedera-sdk-java/issues/367) will poll periodically after creating a topic until that topic makes its way to the mirror node. This functionality is hidden behind a feature flag but will slowly be rolled out over the next month.

## v0.5.3

* Now supports all HCS functionality including a streaming gRPC API for message topic subscription. 
* Changed how the mirror node verifies mainnet consensus. Mirror node now waits for at least third of node signatures rather than greater than two thirds to verify consensus.
* Added new mainnet nodes to the mirror node address book.
* Access still restricted to a white listed set of IP addresses. Request access [here](https://learn.hedera.com/l/576593/2020-01-13/7z5jb).
* Please see the Mirror Node releases page for the full list of changes [here](https://github.com/hashgraph/hedera-mirror-node/releases).
* We occasionally may encounter a situation where an additional 15-20 second delay in message round trip time is experienced and subscriber connections are dropped. No messages are lost, and the consensus time is not affected. Clients are encouraged to reconnect. This issue will be fixed in a subsequent release of the Hedera mirror node. Some third-party mirror nodes should not be affected by this issue. We also don't expect it to impact the exchanges using the REST end point for the mirror node. 

