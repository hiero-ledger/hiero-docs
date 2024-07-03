---
description: Hedera mirror node release notes
---

# Hedera Mirror Node

For the latest versions supported on each network please visit the Hedera status [page](https://status.hedera.com/).

## Latest Releases

## [v0.105.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.105.0)

A design document was added for the implementation of [HIP-904](https://hips.hedera.com/HIP/hip-904.html) Friction-less Airdrops on mirror node. Please watch the [epic](https://github.com/hashgraph/hedera-mirror-node/issues/8081) to monitor the progress of airdrop development.

Citus, our sharded database, continues to make progress with this release making its way to one of our two testnet clusters. We'll monitor its deployment for a period and make any necessary fixes. The ZFS CSI driver we use for Citus saw an upgrade. Finally, multiples issues were fixed with the PostgreSQl to Citus migration script.

For `/api/v1/contracts/call`, the maximum data size was increased to 128 KiB to provide better Ethereum compatibility. Also, additional logic and validation was added to more closely align with consensus nodes error scenarios.

### Upgrading

If you're using the ZFS CSI driver, please ensure its CRDs are updated before upgrading:

```
for crd in zfsbackups zfsnodes zfsrestores zfssnapshots zfsvolumes; do kubectl patch crd $crd.zfs.openebs.io --type merge -p '{"metadata": {"annotations": {"helm.sh/resource-policy": "keep", "meta.helm.sh/release-name": "mirror", "meta.helm.sh/release-namespace": "common"}, "labels": {"app.kubernetes.io/managed-by": "Helm"}}}'; done
```

## [v0.104.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.104.0)

This release adds a Redis cache to the REST API to improve the performance of `/api/v1/transactions`. This functionality is currently disabled by default as we fine tune it.

[HIP-857](https://hips.hedera.com/hip/hip-857) NFT Allowances API made a lot of progress this release. It's taking a bit longer than a usual API since it is our first Java-based REST API that requires some extra groundwork. This release enables the rest-java Helm chart by default allowing users to test out the NFT allowances API in all environments. While most functionality is present, please be aware that some parts are still under development. A new index was added to the NFT allowance table to speed up look-ups by spender account. The existence check for numeric entity ID was removed to improve its performance and to better support partial mirror nodes. Finally, we added initial acceptance tests to verify the API end to end.

Our Citus deployment is nearing the finish line. Citus is now deployed to previewnet and it now runs both PostgreSQL and Citus deployments in parallel. Internally, we've deployed it to a mainnet staging environment with a full size database for further testing. This deployment was possible due to the dramatic increase in migration time we implemented this release. Mainnet previously took more than a month to migrate but with this release it should complete within a week or so. Expect testnet to be migrated to Citus very soon as well.

## [v0.103.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.103.0)

This release adds support for making metadata information from [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-657](https://hips.hedera.com/hip/hip-657), and [HIP-765](https://hips.hedera.com/hip/hip-765) available in the REST API. In particular, this adds a base64 encoded `metadata` field to the `/api/v1/tokens` endpoint. It also adds `metadata` and `metadata_key` fields to the `/api/v1/tokens/{id}` endpoint.

The contract call API saw some noticeable performance improvements with the implementation of lazy loading for nested items. Previously it was eagerly loading all the account information even for simpler calls that didn't need the data. With the switch to make these additional queries lazy, we see an improvement of 50-90% in request throughput. That change plus an improvement in the performance of the NFT count query should result in additional performance and stability of the API.

Work is still underway on [HIP-857](https://hips.hedera.com/hip/hip-857) NFT allowance REST API. This release adds EVM address and alias support to the new endpoint and fixes the error response format.

## [v0.102.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.102.0)

This is a smaller bug fix release with incremental improvements to some in-flight projects.

For [HIP-857](https://hips.hedera.com/hip/hip-857), an alpha version of the NFT allowance REST API is now in place. It can be used to experiment with while we work towards implementing the remaining query parameters and squashing any bugs. The Jooq library was integrated into the rest-java module to allow for dynamic SQL querying based upon user input. The next release should leverage this functionality to fully implement the remaining parts of the API.

Our Citus implementation was successfully deployed to the performance environment and it is passing initial benchmarks. Preliminary results show that Citus improves ingest performance by 600ms while sharding the data across multiple nodes. Promtail was enabled on Citus nodes to capture database logs and a new ZFS dashboard was added to Grafana.

## [v0.101.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.101.0)

This release adds support for storing the new mutable metadata information available in [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-657](https://hips.hedera.com/hip/hip-657), and [HIP-765](https://hips.hedera.com/hip/hip-765). For now, it just persists the data and in future releases we'll expose it via the REST APIs.

The `/api/v1/tokens` REST API now supports multiple `token.id` parameters. This allows users to efficiently query for multiple tokens in a single call.

The `/api/v1/contracts/call` REST API saw some major performance improvements this release. The first change was to switch the Kubernetes node pools to a different machine class that provides dedicated resource allocation. The endpoint was also switched from a reactive MVC stack to a synchronous MVC stack. Simultaneously, the module enabled the new virtual thread technology that replaces platform threads. These changes combined to improve the request throughput by 1-2x.

Another important change was to enable batching between the download and parser threads in the importer. For now, this functionality is configured to behave as before with no batching. When configured manually, this can reduce sync times for historical data by at least 12x. In the future, we'll look at ways to automatically enable this functionality when the importer is attempting to catch up.

## [v0.100.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.100.0)

This release implements [HIP-859](https://hips.hedera.com/hip/hip-859), adding support for returning gas consumed in the contract result REST APIs. The current `gasUsed` field in the API holds the amount of gas charged, while the new `gasConsumed` field holds the amount of gas actually used during EVM execution. Providing this extra data will allow users to provide a more accurate gas when invoking a contract and reduce the fees they are charged.

`/api/v1/contracts/call` now supports a configurable max gas limit property `hedera.mirror.web3.evm.maxGas`. The default value remains at 15 million but operators can now choose to increase it to suite their needs. The EVM version and features have been upgraded to `v0.46`. This brings feature parity with the latest consensus node software for EVM execution.

There was a large amount of work to improve our integration with Citus. Three repeatable migrations were enhanced to work optimally with Citus: account balance migration, token balance migration, and synthetic transfer approval migration. Token account insertion was optimized to improve its performance by removing the join on the token table. Range partitioning was removed for entity related tables since it caused degraded performance due to having sparse partitions. Finally, the deployment now supports different sized disks for individual workers to optimize for unbalanced data.

## [v0.99.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.99.0)

This release contains the implementation of [HIP-873](https://hips.hedera.com/hip/hip-873) adding token decimals to the REST API. Previously users had to make `N + 1` queries to determine accurate token balance information by querying `/api/v1/accounts/{id}/tokens` once and `/api/v1/tokens/{id}` `N` times to get the relevant decimal information. This HIP adds `decimals` to both `/api/v1/tokens/{tokenId}/balances` and `/api/v1/accounts/{id}/tokens` so that decimal information is directly returned alongside the token relationships and the additional `N` queries are unnecessary. It also adds `name` and `decimals` fields to the `/api/v1/tokens` response to expose more of the existing token information on that API.

The `/api/v1/contracts/call` REST API now supports a configurable `hedera.mirror.web3.evm.maxDataSize` property so that mirror node operators can adjust how large of a payload they wish to support. The default value for the max data size was increased from `24 KiB` to `25 KiB` for creates and from `6 KiB` to `25 KiB` for calls. This change makes it possible for view functions with large inputs like [oracles](https://hedera.com/blog/supras-dora-price-feeds-now-live-on-hedera) to now work on the network.

There were a few items to improve the performance and security of the mirror node. A new `hedera.mirror.importer.downloader.maxSize=50MiB` property controls the maximum stream file size it will attempt to download. This protects the mirror node against large files uploaded accidentally or via malicious actors. The importer was refactored to support batch stream file ingestion so that it is possible to process multiple stream files in one transaction. This will help pave the way for future enhancements like improving historical synchronization times.

The database saw a number of improvements including new [setup documentation](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database/README.md#setup) with recommendations for how to configure the database. Our Citus deployment had some notable additions including a huge improvement in performance by adjusting its resource configuration. The Stackgres version was upgraded to 1.8 and ZFS to 2.4.1. The entity stake calculation was optimized for Citus so it runs efficiently in a sharded database. Finally, database metrics were fixed so that partitioned tables are appropriately aggregated under the parent table name.

## [v0.98.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.98.0)

This release saw the implementation of [HIP-844](https://hips.hedera.com/hip/hip-844) Handling and externalization improvements for account nonce updates. This HIP resolve issues where the consensus nodes and the mirror nodes are account nonces are out of sync. The consensus nodes now sends the mirror node the up-to-date account nonce instead of the mirror node attempting to increment the nonce based upon its prior state.

There were two important changes to the database that helped to reduce its size substantially. The `topic_message` table primary key index was dropped in favor of relying upon a similar index on the `transaction` table. This simple change shaved 800 GiB off the mainnet database. The staking reward calculation performance was improved to only write accounts that elected to receive rewards. This reduces the staking reward calculation runtime from 47 minutes down to less than 2 minutes. A migration also removes the existing staking rows that did not have a staking reward election, shrinking those tables by 155 GiB. Note that to realize these disks savings mirror node operators will need to manually perform a full vacuum on the `entity_stake` and `entity_stake_history` tables. So in total the size of the mirror node database was reduced by almost 1 TB this release!

There was quite a bit of technical debt paid down in this release. We've removed support for the event file format from the importer. This format was never fully implemented in the mirror node, didn't support the latest version, and no user interest in this data was expressed during its 4 years of existence. The acceptance tests were refactored to use the OpenAPI generated models, ensuring we dogfood our own API specification. The brittle `MockPool` tests were removed in favor of additional coverage in other, easier to maintain tests. The REST API tests now uses the correct read only user and common database setup that the other modules use. Finally, the unused `RestoreClientIntegrationTest` and associated test images were removed.

Our Citus deployment saw a number of improvements. Performance was optimized for hash insertions by reducing the shard count for hash tables. Entity upserts saw improvement by increasing the number of CPU resources to the database. Finally, the transactions list and accounts by ID endpoints saw their read performance improved for Citus.

\
[v0.97.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.97.0)
---------------------------------------------------------------------------------------------------------------

This release sees some incremental changes to the REST API. The REST API now supports a [RFC5988](https://www.rfc-editor.org/rfc/rfc5988) compliant `Link` header in its response as an alternative to the `links.next`in the response body. Either link can now be used for pagination, but the `Link` header provides a standard approach that's supported by more tools. The `/api/v1/accounts/{id}?timestamp` endpoint now shows historically accurate staking information in its response so that users can view their past pending rewards. The timestamp in the response of the `/api/v1/tokens/{id}/balances` endpoint is now more accurate by reflecting the max balance timestamp of the tokens in its response.

The helm chart was verified to be compatible with Kubernetes 1.28 and saw its dependencies all bumped to the latest release. The new rest-java module was converted from WebFlux to servlets with virtual threads. This should increase its scalability once we implement HIP-857 NFT allowance REST API in a future relase. Some internal refactoring of `BatchEntityListener` to `BatchPublisher` will enable future improvements to historical syncing and batch processing.

The `/api/v1/contracts/call` endpoint saw a lot of important bug fixes this release. Support for historical blocks should be complete with some remaining bugs ironed out. The supported operations [documentation](https://github.com/hashgraph/hedera-mirror-node/tree/main/docs/web3#supportedunsupported-operations) was updated to reflect this increased level of compatibility.

## [v0.96.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.96.0)

With a lot of the developers taking some time off for the holiday season, this release is a bit smaller than normal but still contains some important changes. The previewnet and testnet bootstrap address books were updated to reflect the current state of the network. The default volume size for Loki was increased from 100 GB to 250 GB to account for increasing amounts of log activity. The processing of `EthereumTransaction` was made more resilient so that the importer does not halt if encounters a badly encoded transaction. Finally, a memory leak in the REST API should greatly reduce out of memory errors and improve request throughput.

To improve ingest performance of entity tables when used with a distributed SQL database we introduced a new `temporary` database [schema](https://www.postgresql.org/docs/current/ddl-schemas.html). This schema is used to hold the temporary data inserted when processing entities from a record file. Previously this information was added to temporary tables created within the transaction scope, but these temporary tables could not be made distributed in Citus. With the temporary schema, this information can now be distributed appropriately to ensure optimal ingest performance. This change does require manual DDL statements be ran before the next upgrade (see next section).

### Breaking Changes

As previously mentioned, a new database schema was introduced to handle the processing of upsertable entities. This change\
doesn't require any manual steps for new operators that use one of our initialization scripts or helm charts to\
configure the database. However, existing operators upgrading to 0.96.0 or later are required to create the schema by\
configuring and executing a [script](https://github.com/hashgraph/hedera-mirror-node/blob/v0.96.0/hedera-mirror-importer/src/main/resources/db/scripts/init-temp-schema.sh) _**before**_ the upgrade.

```
PGHOST=127.0.0.1 ./init-temp-schema.sh
```

Another breaking change concerns operators using our `hedera-mirror-common` chart. The aforementioned Loki volume size increase was made to the embedded `PersistentVolumeClaim` on the Loki `StatefulSet`. Kubernetes does not allow changes to this immutable field so to workaround the `StatefulSet` will need to be manually deleted for the upgrade of the common chart.

## [v0.95.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.95.0)

This release saw the Java components upgraded to use [Java 21](https://www.oracle.com/news/announcement/ocw-oracle-releases-java-21-2023-09-19/). In a future release, we will explore the new language features in 21 like virtual threads to unlock additional scalability. Some technical debt items were tackled including removing redundant test configuration by creating a common test hierarchy. Explicit `@Autowired` annotations on test constructors were removed, reducing boilerplate. Finally, various classes were renamed to align to our naming standards including the removal of the `Mirror` prefix from classes that were not used across modules.

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM archive node for historical blocks saw some major additions including initial support for historical blocks. EVM Configuration is now loaded based upon block number instead of always utilizing the latest EVM. This ensures that `/api/v1/contracts/call` simulates the execution as it would've been on consensus nodes at that point in time. Database queries were adapted to work with timestamp filters to allow for returning historical block information.

Our distributed database effort saw some notable improvements including upgrading the version of Citus to 12.1. PostgreSQL 16 support was tested confirming compatibility with both regular PostgreSQL and Citus. Both `/api/v1/topics/{id}/messages/{sequenceNumber}` and `/api/v1/topics/{id}/messages` saw optimizations implemented when used with Citus.

### Upgrading

If you're compiling locally, ensure you have upgraded to Java 21 in your terminal and IDE. For MacOS, we recommend using [SDKMAN!](https://sdkman.io/) to manage Java versions so that upgrading is as simple as `sdk install java 21-tem`. If you're using a custom `Dockerfile` ensure it is also updated to Java 21. We recommend [Eclipse Temurin](https://hub.docker.com/\_/eclipse-temurin) as the base image for our Java components.

## [v0.94.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.94.1)

Provides an important fix to pending reward calculation that regressed due to the balance deduplication work. The database migration for this will take approximately 17 minutes on mainnet.

## [v0.94.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.94.0)

This release is mainly a bug fix release along with some minor technical debt items. A new Helm chart for the new REST Java module was added in anticipation of future work to support new APIs in Java only instead of the current JavaScript based approach. Support for Elasticsearch metrics export was removed in favor of relying solely upon Prometheus. The `/api/v1/contracts/call` API some some notable bug fixes and performance improvements. Finally, some technical debt was tackled by refactoring `SqlEntityListener` to use a new `ParserContext` which should reduce its maintenance burden.

## [v0.93.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.93.0)

This release deduplicates balance history resulting in a major reduction in database size with no loss in balance granularity. The mainnet database saw a 45% reduction going from 50 TB to 28TB! This deduplication process works by not updating balance history if the account did not experience a balance change since the last snapshot. A migration to deduplicate historical balances runs asynchronously in the background and against mainnet state took about 24 hours to complete. Because the index was changed to reverse the order from `(timestamp, account_id)` to `(account_id, timestamp)`, this required a large effort to rework queries in multiple REST APIs. Also, the balance tables are now partitioned and this meant changes in our database metrics to properly aggregate child tables on their parent name.

HIP-584 continues to chug along with multiple bug fixes and optimizations. Changing per request objects to be singletons resulted in a large decrease in memory and CPU usage, allowing more concurrent requests to be handled. Web3 k6 tests were hooked into our automated performance testing to ensure they run every release to ensure no regressions. Finally, support for historical blocks made progress with more of the plumbing put in place to process non-latest blocks.

A new cluster database health check was added to the monitor to provide proper failover in multi-cluster deployments. The local file stream provider now allows for input files to be grouped by date for faster processing when the directory contains millions of files. This is a step towards a faster historical syncing mode. Finally, REST API queries were optimized for Citus deployments so that they could reach parity with PostgreSQL.

## [v0.92.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.92.0)

[HIP-584](https://hips.hedera.com/hip/hip-584) Mirror EVM Archive node saw further refinements in this release. Memory usage was optimized by making most classes stateless and leveraging `ThreadLocal` where appropriate. Work continues on making `/api/v1/contracts/call` support historical blocks with lower level support for historical contract state, account, and contract information added. Additional test coverage for estimate gas was introduced to compare gas estimation is close to the actual gas used via HAPI. Finally, an issue with the `blockhash` operation returning `0x0` was resolved.

We added an option to deduplicate account and token balance data for Citus that can dramatically reduce the size of the balance tables. Balance tables currently consume 50% of the database. In the next release, we will bring this capability to regular PostgreSQL to realize those cost savings there as well. We now no longer update the token history for supply change operations like mint, burn, or wipe thus reducing the amount of data in this table.

## [v0.91.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.91.0)

This release adds support for ad-hoc transaction filters using the [Spring Expression Language](https://docs.spring.io/spring-framework/reference/core/expressions.html) (SpEL). SpEL filters can be used for both including or excluding transactions from being persisted to the database. Previous filtering [capability](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#transaction-and-entity-filtering) allowed mirror node operators to include or exclude certain entity IDs or transaction types, but if they needed something more custom they were out of luck. SpEL itself is pretty powerful and allows access to any bean or class on the classpath, so to reduce the attack surface we limit it to only allow filtering on the [TransactionBody](https://github.com/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_body.proto) and the [TransactionRecord](https://github.com/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_record.proto) contained within the record file. See the [docs](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#spring-expression-language-support) for additional details and examples. Below is an example that only includes transactions with certain memos:

```
hedera.mirror.importer.parser.include[0].expression=transactionBody.memo.contains("hedera")
```

Monitoring saw improvements this release with newly added metrics for the size of table and indexes on disk to help track the growing size of the database. Likewise, new cache metrics were implemented to monitor the cache hit rate and size. Dashboards were updated to visualize these new metrics.

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM archive node saw support for `block.prevrandao` implemented. Also, support for `pending`, `safe`, and `finalized` block types were added with all three being equivalent to `latest` since Hedera's blocks are always final. Work has started on historical support for `/api/v1/contracts/call` so that specific blocks in the past can be queried. This release saw the lower level queries for token balance implemented.

[HIP-794](https://hips.hedera.com/hip/hip-794) Sunset account balance saw a couple of remaining items completed. The balance timestamp that was added in v0.89.0 was put to use to provide more accurate timestamps for the accounts and balances REST API. Finally, the reconciliation job was disabled since it doesn't make sense to reconcile from balance data that mirror node generates itself.

## [v0.90.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.90.0)

This release was mainly focused on testing and bug fixes. Acceptance tests saw a number of improvements including a reduction in overall costs by caching contract creation used in multiple tests. Acceptance tests were added to verify support for HIP-786 staking properties in network stake REST API. An issue with exchange rates varying in different environments was solved by querying the exchange rate REST API and using that to calculate fees to HAPI. The importer had an option added to fail on recoverable errors to find record stream issues earlier in the lifecycle.

## [v0.89.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.89.0)

[HIP-786](https://hips.hedera.com/hip/hip-786) adds support for enriched staking metadata exports to the record stream for use by downstream systems. The mirror node now ingests the new `max_stake_rewarded`, `max_total_reward`, `reserved_staking_rewards`, `reward_balance_threshold`, and `unreserved_staking_reward_balance` fields and persists them to the database. The REST API has been updated to expose this data via `/api/v1/network/stake`.

[HIP-794](https://hips.hedera.com/hip/hip-794) Sunsetting Balance File saw further refinements in this release. The mirror node now captures the consensus timestamp at which the balance was updated for both accounts and token relationship. This information will be used in the future to provide more accurate balance timestamps in the API and to deduplicate the balance information. Entity balance tracking and migration was enabled in the Rosetta API. Finally, we now track the balance of all entity types and not just accounts and contracts.

[HIP-584](https://hips.hedera.com/hip/hip-584) Mirror EVM Archive node continues to improve with the addition of support for the PRNG system contract. Missing Besu internal precompiles for the Istanbul release are now properly registered. A lot of new tests were added in the form of integration, acceptance, and performance tests.

There were a number of technical debt items addressed in this release. The importer component saw noticeable improvements in CPU and memory usage at 10,000 transactions per second. It now uses about 50% less memory and 33% less CPU. The Log4j2 logging framework was replaced with Logback to provide a path to compiling to native code and to simplify configuration. The `EntityId` saw its final improvement with the addition of a cache to reduce allocating temporary immutable objects. Tests were standardized to use the simpler and logging framework agnostic `OutputCaptureExtension`. Finally, we researched approaches to parallel transaction insertion and saw a path forward for additional ingest scalability.

## [v0.88.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.88.0)

This release contains support for [HIP-794](https://hips.hedera.com/hip/hip-794) Sunsetting Account Balance File. Consensus nodes will soon stop generating account balance files every 15 minutes due to the growing number of accounts making this operation unsustainable. To fill in the gaps, mirror nodes will now generate balance snapshot information from the record stream. This change will be transparent to end users and operators alike since the same data will be returned by the various APIs. For now, we're generating synthetic `account_balance_file` rows (not files) until we can remove the reliance on this table everywhere. In this release, we updated the accounts by ID, balance, and network supply REST APIs to not depend upon this table. Entity stake calculation and a fungible token migration were updated similarly. The next release will see further work in this area.

[HIP-584](https://hips.hedera.com/hip/hip-584) saw the exchange rate precompiles `tinycentsToTinybars` and `tinybarsToTinycents` implemented. Also added was support for the HTS `redirectForToken` precompile. But the main focus was on testing and bug fixes with a large number of them squashed in this release.

There was good amount of technical debt addressed in `0.88`. For starters, we have a new `hedera-mirror-rest-java` module that is intended to contain new or existing REST APIs converted from JavaScript. By creating any new REST APIs in Java and slowly converting existing APIs to Java we can improve the quality of this area of the codebase and promoting code reuse with the other modules. A community member helped us to remove the deprecated Spring Cloud Kubernetes property `spring.cloud.kubernetes.enabled` since it was no longer used anyway. We also took the time to remove unused Flyway placeholders properties and eliminate code redundancy in web3 acceptance tests. Finally, we removed the `type` field from the widely used `EntityId` in the codebase and eliminated the unnecessary `AssessedCustomFeeWrapper`.

## [v0.87](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.87.0)

This release wraps up the initiative to ensure we capture all changes to Hedera entities. One of the oldest tickets in the repository going back to 2019 was completed, finally persisting the [FreezeTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/freeze.proto) details to the database. There's a new option to store the raw [TransactionRecord](https://github.com/hashgraph/hedera-protobufs/blob/main/services/transaction\_record.proto) protobuf bytes that is set to off by default. The custom fee table was split into separate main and history tables for consistency with other data and improved querying efficiency.

An asynchronous database migration was added to efficiently update every account's crypto allowance amount after support for live allowance tracking was implemented in the last release. Furthermore, new crypto and fungible acceptance tests verify the live allowance tracking works correctly. Finally, we now rerun conditional migrations that would historically run only on initial startup. For migrations like balance initialization this means we automatically correct account and token relationship balances after ingesting the first balance file. For other migrations, it means they are triggered automatically based upon a specific record file version being ingested.

The REST API had a couple of noticeable changes. We now show only active allowances in the `/api/v1/accounts/{id}/allowances/crypto` and `/api/v1/accounts/{id}/allowances/tokens` REST APIs, providing consistency with how consensus nodes return this data. The `/api/v1/network/stake` API saw a change in how its stake value is calculated by changing it to stake rewarded plus not rewarded.

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM Archive Node saw a number of `HederaTokenService` precompiles implemented including `allowance`, `getApproved`, `isApprovedForAll` `updateTokenExpiryInfo`, `updateTokenInfo`, and `updateTokenKeys`. A large focus on testing resulted in increased integration and acceptance test coverage. The extra coverage resulted in a number of bugs being found and squashed, improving the reliability of `/api/v1/contracts/call`.

A lot of work went into the operations side of things as well. A good number of metrics and Grafana dashboards saw cleanup and improvements to aid in production monitoring. All chart dependencies saw version bumps and configuration adjustments to bring them up to date. Kubernetes 1.27 compatibility was confirmed as a deployment target while still ensuring backwards compatibility with prior Kubernetes versions. Compressed ZFS volume support now handles Kubernetes upgrades properly. Our Citus deployment saw an upgrade to Citus 12 with PostgreSQL 15. This release brings in the improvements we contributed upstream to Citus' `create_time_partitions` stored procedure so that it can support the `bigint` type that we use to store consensus timestamps. This allowed us to remove the `pg_partman` extension in favor of the native `create_time_partitions`. The `pg_cron` extension was also removed in favor of a Java-based scheduled service running on the importer.

## [v0.86](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.86.0)

There was a ton of progress towards our goal capturing all [entity changes](https://github.com/hashgraph/hedera-mirror-node/issues/6110). This release adds the highly requested feature of tracking the remaining hbar and fungible token allowances and showing it via the `amount` field on the REST API. Importantly, this tracking only applies to new or updated allowances. Existing allowances will see their remaining allowance adjusted appropriately in the next release.

Traditionally, the mirror node has only stored the aggregated transfers from the transaction record. Now in addition we store the itemized transfers from the transaction body by default and embed it within the `transaction` table. For partial mirror nodes, we now create entities during balance initialization. This means even if a mirror node starts up now this migration can be rerun to create every account and contract with accurate balance information. In the next release, we'll extend this to automatically rerun this migration when processing the first account balance file.

Finally, we added an `entity_transaction` join table to start tracking all entities associated with a transaction. This will enable us provide a better transaction search experience and find all transactions associated with an entity. This functionality is disabled in this release as we iterate on it to make it performant.

Support for Ethereum transaction type 1 as specified in [EIP-2930](https://eips.ethereum.org/EIPS/eip-2930) is now available. Previously only legacy and type 2 Ethereum transactions were supported. The new EIP-2930 transactions can be sent either directly to HAPI via an [EthereumTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/ethereum\_transaction.proto) or via the [JSON-RPC Relay](https://swirldslabs.com/hashio/).

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM Archive node saw a large number of precompiles implemented. This includes all of the following:

- `approve`
- `approveNFT`
- `associateToken`
- `associateTokens`
- `burnToken`
- `createFungibleToken`
- `createFungibleTokenWithCustomFees`
- `createNonFungibleToken`
- `createNonFungibleTokenWithCustomFees`
- `deleteToken`
- `freezeToken`
- `pauseToken`
- `setApprovalForAll`
- `transferFrom`
- `transferFromNFT`
- `transferNFT`
- `transferNFTs`
- `transferToken`
- `transferTokens`
- `unfreezeToken`
- `unpauseToken`

## [v0.85](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.85.0)

With the recent [testnet reset](https://docs.hedera.com/hedera/networks/testnet#test-network-resets) that occurred on 2023-07-27, a new cloud storage bucket was created to store the stream files generated by the consensus nodes. This release will now use that updated bucket name by default when the network is set to testnet.

There were a few optimizations done specifically for the JSON-RPC Relay in this release that other users might also find useful. The `/api/v1/accounts/idOrAddress` REST API now supports an optional `transactions` query parameter that when set to `false` will omit the list of nested transactions. It defaults to `true` to match the previous behavior. If you're not using the transactions from this API please consider setting it to `false` to reduce the amount of data returned and provide an improved response time for your queries. Additionally, the `/api/v1/contracts/results` REST API was updated to include more fields to match the detailed results returned from `/api/v1/contracts/results/{id}`.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) EVM Archive Node is adding functionality at a steady pace. This release adds support for the Ethereum Shanghai EVM version that adds a new `PUSH0` opcode. Estimate gas calls that were failing for contract creates over 6 KB were fixed. Much of the focus was on implementing various precompiles including: approve allowance, burn, create, delete allowance, dissociate, grant KYC, revoke KYC, and wipe.

Making progress on capturing all state changes, we now keep a history of an account's daily entity stake. In the future, this information will be used to provide an accurate staked amount when looking up an account's historical information using `/api/v1/accounts/{id}?timestamp=`.

There were a lot of other bug fixes and small improvements. Please see the release notes below for the full list.

## [v0.84](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.84.0)

This release contains support for [HIP-729](https://hips.hedera.com/hip/hip-729) contract nonce externalization. Consensus nodes now track and externalized contract nonce information in the record stream. A contract's nonce increases whenever it creates another contract. Mirror nodes persist this data and expose a contract's nonce on the `/api/v1/contracts` and `/api/v1/contracts/{id}` REST APIs.

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM archive node continues to make progress. This release contains full support for contract state reads for both precompiled and non-precompile functions. Support was added for contract state modifications for non-precompile functions with the exception of lazy account creation.

Our [goal](https://github.com/hashgraph/hedera-mirror-node/issues/6110) of capturing all entity changes saw further refinements. Token information saw a history table be added to keep track of any changes to the token metadata over time. Also, we now use the real-time token balance in the token balances REST API.

## [v0.83](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.83.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JULY 7, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 29, 2023**
{% endhint %}

In this release we made the highly requested change to show the list of NFT transfers on the transactions list REST API. Originally, only the `/api/v1/transactions/{id}` showed the list of `nft_transfers` due to performance concerns with joining on another large table for such a heavily used and heavyweight API. To show this information while staying performant, we had to denormalize the NFT transfer information to nest it under the `transaction` table as a JSONB column. This avoids an extra join and allows us to return the given information with the existing query.

The mirror node is focused on tracking all possible changes to Hedera entities over time. To that end a NFT history table was created to capture all possible changes to a NFT over time. In addition to persisting this data, we're also exposing more of this historical information via the API. Now when the `timestamp` parameter is supplied on the `/api/v1/accounts/{id}` endpoint it will show the historical view of that account. Previously, the parameter would only be used for displaying the list of `transactions` at the given time. Expect additional improvements around historical entity information in the next release.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) continues to make strides every release. This release focused on improving precompile support for `/api/v1/contracts/call`. There is now support for the CREATE2 opcode along with non-static contract state reads for precompile and non-precompile functions. Non-static contract modifications for non-precompile functions (excluding lazy account creation) was also worked on. Finally, acceptance test coverage was greatly increased and a number of bugs were addressed.

This release adds integration with the [Stackgres Operator](https://stackgres.io/) to provide a highly available [Citus](https://www.citusdata.com/) deployment. Stackgres is an established Kubernetes operator for PostgreSQL and their support for the Citus extension has made it easy to provide a production ready deployment without depending upon expensive, cloud-specific managed database services. This along with the ZFS volume compression we added in a previous release should greatly reduce the total cost of running a mirror node while providing horizontal scalability.

### Upgrading

Expect upgrading to take about an hour due to the large NFT transfer migration in this release. As always, it's recommended upgrades be completed in a staging environment first (e.g. a red/black deployment) to allow for deployment verification and reduce downtime before being opened up to customer traffic.

## [v0.82](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.82.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 22, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 8, 2023**
{% endhint %}

[HIP-679](https://hips.hedera.com/HIP/hip-679.html) saw its initial work completed in this release to support a restructured bucket. The importer now supports the existing account ID-based bucket path along with a future node ID-based bucket path. It also adds a path type property that can automatically switch between the two so the transition between the two formats is transparent to mirror node operators. For now, the default path type will stay as account ID until node ID becomes a reality to reduce the S3 costs.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) Mirror EVM Archive Node saw a large number of improvements to bring it closer to parity with consensus nodes. Stacked state and database accessors were integrated to allow for smarts contracts to change state temporarily. An operation tracer was added to make it easier to debug smart contracts in an environment.

Finally, a topic message lookup table was added to optimize finding topic messages on distributed databases.

## [v0.81](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.81.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 1, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 24, 2023**
{% endhint %}

This release comes with a number of improvements to support the JSON-RPC Relay. We now support alias and EVM address lookups for the `account.id` parameter on the balances endpoint. We optimized the transaction nonce filtering in `/api/v1/contracts/{id}/results` by denormalizing the data. Finally, an issue with empty `function_parameters` in `/api/v1/contracts/results/{id}` response was addressed.

The other big item we worked on was [support](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database/zfs.md) for volume level compression with Citus. We know that the type of time series data the mirror node stores would be highly compressible and we wanted to use that to our advantage to both reduce increasing storage costs and improve read/write performance. PostgreSQL supports a basic form of compression at the column level called [TOAST](https://www.postgresql.org/docs/current/storage-toast.html), but it only takes effect for very large columns. Citus has compression when using their columnar storage access method, but we found it to be too slow for our needs. Since with Citus we knew we wouldn't be using a SaaS service we had more control over the database deployment, so we decided to experiment with Kubernetes volume compression. By creating custom Kubernetes node pools exclusively for Citus, we could install [ZFS](https://en.wikipedia.org/wiki/ZFS) via the [zfs-localpv](https://github.com/openebs/zfs-localpv) and enable [Zstandard](http://facebook.github.io/zstd/) compression on the database's underlying volume. The results were a **3.6x compression ratio with zero loss in performance**. To put into perspective, that means the current mainnet database size of 17TB could be reduced to 4.7TB.

Other areas of improvement include improving documentation around disaster recovery efforts. This includes a [runbook](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/runbook/restore.md) on restoring a mirror node from backup. There's also a [runbook](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/runbook/stream-verification.md) on how to perform local stream file verification. Acceptance tests have been previously integrated into the automated deployment process but suffered from a long execution time mainly due to using Gradle to download dependencies at runtime. We containerized the acceptance tests so the dependencies are downloaded at build time reducing runtime by 3-4x.

## [v0.80](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.80.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 17, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 11, 2023**
{% endhint %}

Work continues on [HIP-584](https://hips.hedera.com/HIP/hip-584.html) with this release the first to support non-static contract state reads for non-precompile functions. Please see the Swagger UI [table](https://github.com/hashgraph/hedera-mirror-node/pull/5949/files) for `/api/v1/contracts/call` for a breakdown of which functionality is supported in what release. More estimate gas functionality was copied from services code to make progress on estimation. A new stacked state frame functionality was added to be used in the future to support contract writes and cached reads.

The [Spotless](https://github.com/diffplug/spotless/tree/main) code formatting tool was used to format the entire codebase to be consistent. A git commit hook was added to ensure any new changes stays consistent and developers can focus on what matters.

Finally, there were a large number of bug fixes and performance improvements. See below for the full details.

## [v0.79](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.79.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 9, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 2, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) EVM archive node saw further progress this release with a focus on testing and establishing the foundation for estimate gas functionality in the next release. While consensus nodes undergo a modularization effort that will pay dividends down the road, the archive node needs functionality for estimate gas before that process could be completed. To make progress on HIP-584, the necessary EVM logic was temporarily copied from consensus nodes into the mirror node web3 module. A large focus was placed on increasing acceptance test coverage for contract call with precompiles.

Users writing dApps want to be able to monitor for token approval and transfer events. HAPI transactions like `CryptoTransfer`, `CryptoApproveAllowance`, `CryptoDeleteAllowance`, `TokenMint`, `TokenWipe`, and `TokenBurn` do not emit events that could be captured by monitoring tools like [The Graph](https://thegraph.com/en/) since they’re executed outside the EVM. To address, the mirror node now generates synthetic contract log events for these non-EVM HAPI transactions.

A new subscription API was [designed](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/contract-log-subscription.md) for [HIP-668 GraphQL API](https://github.com/hashgraph/hedera-improvement-proposal/pull/668). Once it's implemented in a future release, the new contract log subscription will stream contract events to clients via a WebSocket connection.

For our Citus database transition, PostgreSQL 15 compatibility was verified and made the default for this v2 schema. The lookup of historical balance information via `/api/v1/balances?timestamp=` was optimized for sharded databases so it stays performant. Performance testing showed a decrease in shard count could greatly improve performance so we lowered the number of shards from 32 to 16. This testing also allowed us to provide an initial [recommended](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#database-migration-from-v1-to-v2) resource configuration for the Citus deployment.

There was a large focus on test improvements in this release. In addition to the aforementioned HIP-584 test coverage, we also optimized the acceptance tests to reduce the overall test duration in Kubernetes by half without reducing coverage. The acceptance test logs were cleaned up to reduce unnecessary log statements and standardize its output. The hbar balance used by the tests now is logged at the end of test execution. Acceptance tests for hollow account creation were added. We now generate multi-platform snapshot images from the `main` branch for testing with local node. [Testkube](https://testkube.io/) configuration was enhanced to make it more configurable. Finally, all Java test compiler warnings were fixed and will now fail the build if any future warnings occur.

### Known Issues

There is a [bug](https://github.com/hashgraph/hedera-mirror-node/issues/5944) introduced by [#5776](https://github.com/hashgraph/hedera-mirror-node/pull/5776) that causes the importer to fail on startup. It's recommended to hold off on upgrading to v0.79.0 until we can address this in a v0.79.1. Alternatively, it can be worked around by disabling the faulty migration by setting `hedera.mirror.importer.migration.syntheticTokenAllowanceOwnerMigration.enabled=false`.

## [v0.78](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.78.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 21, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: APRIL 13, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) Mirror Evm Archive Node now has token precompile support. This was the last major piece of functionality needed for the `/api/v1/contracts/call` to be considered `eth_call` equivalent. The new API was added to the REST API's OpenAPI documentation so that it appears on our [Swagger UI](https://mainnet-public.mirrornode.hedera.com/api/v1/docs). A number of performance optimizations were worked on to make it scalable as well as various test improvements to verify its correctness. Various bugs were addressed including the proper handling of reverts. In the next few releases, we plan to fine tune contract call and implement contract gas estimation.

A large focus was put on performance and resiliency this release. On the performance front, we've optimized the list schedules REST API to be scalable on Citus. Performance tests can now trigger automatically via TestKube once the helm tests complete. Those same k6 performance tests were enhanced to automatically pick appropriate configuration values specific to the environment. The transaction hash table was partitioned and the ingest process made to insert hashes in parallel. This change dramatically speeds up the time to insert the optional transaction hashes. Similarly, an option was added to control which transaction types should cause a hash insertion.

On the resiliency front, the importer component was analyzed for any code paths that may cause record file processing to halt due to bad input from consensus nodes. Any such code was made to handle the error, log/notify, and move on to the next transaction. This change makes the mirror node ingestion more resilient and moves toward preferring availability over correctness. Partial mirror nodes that might become stuck due to having an incomplete address book can now continue to ingest with a new `consensusMode` property and logic. Partial mirror nodes will now also be able to have a corrected account and token balance even if the entity was missing a deleted flag. Finally, we were able to complete a longstanding refactoring effort to move all transaction specific logic to individual transaction handlers and fixed a number of bugs in the process.

There were a few important bugs fixed in this release that are worth noting. A fix was put in place to correct inaccurate fungible token total supply. Additionally, NFTs for deleted tokens no longer appear as active in the REST API.

## [v0.77](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.77.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 4, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 29, 2023**
{% endhint %}

This release fixes the tracking of NFT balances. Historically, these came from the balance file sent by the consensus nodes every 15 minutes. When we started tracking the live fungible token balances and moved away from using this balance file we unfortunately broke the NFT balance calculation. We not only fixed the issue but went ahead and took the time to track the up-to-date NFT balance as well.

The `/api/v1/contracts/{id}/state` REST API shows the current state of a contract's slot values. Users requested the ability to query for the key/value pairs for their contract at an arbitrary point in the past. To address, we now expose a `timestamp` query parameter that will get the historical contract state. This allows the JSON-RPC relay to offer a proper `eth_getStorageAt` with support for historical blocks.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) continues to make progress. Quite a few bugs were squashed including handling reverts and populating the revert reason and raw data. Performance tests were added to k6 to load test contract calls with token precompiles.

## [v0.76](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.76.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MARCH 23, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 13, 2023**
{% endhint %}

The new `/api/v1/contracts/call` REST API as specified in [HIP-584](https://hips.hedera.com/HIP/hip-584.html) is finally ready for initial production use. This release adds support for rate limiting the API with an initial value of 100 requests per second per instance. Tags were added to the gas per second metric to indicate if the request was a call, an estimate, or resulted in an error for increased observability. Various bug fixes were also addressed.

[HIP-668](https://github.com/hashgraph/hedera-improvement-proposal/pull/668) GraphQL API was added to our deployment with the addition of a new helm chart for this API. This will allow for initial use of the API in all environments with the understanding that it's still very much alpha and subject to change.

We made a lot of headway on our Citus integration. Citus was upgraded to 11.2 which showed a nice 15-20% performance boost for a number of query patterns. We optimized the contract results APIs performance by distributing based upon contract ID instead of payer account. Search for a transaction by its hash on Citus was improved by adding the distribution column to the query and limiting the results to a timestamp range. The search for an account by its ID also saw improvements on Citus.

### Breaking Changes

The Helm charts contain some breaking changes to be aware of. The `hedera-mirror` wrapper chart enables the new `hedera-mirror-graphql` sub-chart by default. The GraphQL deployment requires a new `mirror_graphql` database user to exist for it to successfully start up. You can create the user by logging into the database as owner and executing the following SQL query:

```
create user mirror_graphql with login password 'password' in role readonly;
```

If you're using the embedded PostgreSQL sub-chart (which we don't recommend for production use), then you'll have to delete its StatefulSet before upgrading due to a major bump in its chart version.

The `hedera-mirror-common` chart had all of its components upgraded to new major versions that include breaking changes. If you're using this chart, run the following commands before upgrading:

```
kubectl delete daemonset mirror-prometheus-node-exporter
kubectl delete daemonset mirror-traefik
kubectl delete statefulset mirror-loki
kubectl delete ingressroute mirror-traefik-dashboard
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagers.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_podmonitors.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_probes.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_prometheuses.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_prometheusrules.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_thanosrulers.yaml
```

## [v0.75](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.75.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MARCH 2, 2023**
{% endhint %}

Work continues on [HIP-584](https://hips.hedera.com/HIP/hip-584.html) to get it closer to production ready for simple contract calls. Caching logic was added to the repository layer to optimize its capability along with performance tests to verify those improvements. A metric was added to track the gas per second being used along with various other bug fixes.

The monitor API and dashboard used internally for observing our production systems was containerized. Additionally, it was integrated into the Helm chart and invoked as part of the Helm tests to ensure the deployment is verified.

Finally, there were a number of query optimizations as part of our Citus effort.

## [v0.74](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.74.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 18, 2023**
{% endhint %}

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 14, 2023**
{% endhint %}

This release switches the testnet bucket to the new one created for the [testnet reset](https://docs.hedera.com/hedera/networks/testnet#test-network-resets) that occurred on January 26, 2023. It also updates the address book to reflect the additional nodes added to testnet since the last reset. If you're running a testnet mirror node, please see the [reset instructions](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) for help getting your node updated.

In [HIP-668](https://github.com/hashgraph/hedera-improvement-proposal/pull/668), we propose adding a new mirror node GraphQL API and would greatly appreciate your feedback. In this release, a new GraphQL module with a simple account lookup query was added to provide the basis for future work on this HIP. In the next release, we will add the automated deployment of this module to all environments. It is considered an alpha API subject to breaking changes at any time, so it's not recommended to depend upon for production use. This has been made explicit by using `/graphql/alpha` in its URL.

Finally, a number of query optimizations were implemented for Citus while ensuring it doesn't cause regressions with the existing database. This will continue to be the focus of the next few releases.

## [**v0.73**](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.73.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 10, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: FEBRUARY 3, 2022**
{% endhint %}

This release is the first one with support for [HIP-584](https://hips.hedera.com/HIP/hip-584.html) EVM Archive Node. HIP-584 allows mirror nodes to act as a read only EVM for free execution of smart contracts. This new feature is considered alpha with much of the work still to be implemented like support for precompiled contracts, gas estimation, etc. This functionality requires the mirror node to be configured to ingest the optional traceability sidecar files and it requires a network where those files are generated. Currently only previewnet has contract traceability enabled.

The testnet bucket name has been updated to the new bucket name after its recent quarterly [reset](https://docs.hedera.com/hedera/testnet#test-network-resets). Likewise the bootstrap testnet address book was updated to reflect the additional testnet nodes that have been added since the previous reset. Mirror node operators running a testnet node should either manually populate the new bucket name or update to this release.

The remaining work targeted significant testing improvements and bug fixes. Our performance tests were expanded to all endpoints to catch issues earlier in the lifecycle. Additional acceptance test coverage was added along with a number of fixes. CI stability has greatly improved with a focus on fixing flaky tests. Code smells as reported by [Sonar](https://sonarcloud.io/project/overview?id=hedera-mirror-node) were reduced to only a handful and in the next release reduced all the way down to zero. Finally, we merged work that enables nightly performance testing in our integration and mainnet staging environments via [TestKube](https://testkube.io/).

## [**v0.72**](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.72.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JANUARY 18, 2022**
{% endhint %}

This release is a smaller release as most of the team took time off for the holidays. Still, we managed to implement [HIP-583](https://hips.hedera.com/HIP/hip-583.html) Expand alias support in CryptoCreate & CryptoTransfer Transactions. We now allow hollow accounts to be later finalized into a contract when it is fully created.

We also worked on adding support for [Testkube](https://testkube.io/). Testkube allows us to automate our testing in Kubernetes environments by triggering tests based upon various conditions. Specifically, it will be used to run nightly performance regression tests against a mainnet staging environment to ensure our API performance doesn't regress. We'll continue to expand on this automated testing in future releases.

There were also a number of bug fixes in this release, mainly focused on fixing our release process after the switch from Maven to Gradle in the last release.

## [v0.71](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.71.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 19, 2023**
{% endhint %}

As of this release, all account and token balances in the REST API will reflect their real time balance information. Historically, the mirror node has relied upon the balance file uploaded by consensus nodes every 15 minutes for its balance information. We've been working towards this milestone for many releases gradually rolling out real-time balance tracking to more entities and more APIs. This release completes this migration with the addition of real time token balances to both the accounts and the balances REST APIs.

The mirror node now implements support for [HIP-583](https://hips.hedera.com/HIP/hip-583.html) alias on `CryptoCreate` transactions. With this, clients can directly set an alias during account creation instead of relying upon the implicit auto-account creation during transfers. The mirror node respects this explicit alias along with the new explicit EVM address in both `CryptoCreate` or the `TransactionRecord`. This avoids the brittle EVM address calculation on the mirror node that has caused us some trouble in the past.

This release completes the migration from Maven to Gradle for our build process. A lot of work has been put into the new build to improve its performance and stability both locally and in continuous integration (CI). GitHub Actions workflows have been consolidated from one workflow per module to a single Gradle build workflow with a matrix strategy running them in parallel for each module and database schema. This greatly simplifies the workflow configuration making it easier to maintain and debug.

We continue to make progress on our Citus exploration. The v2 schema for Citus now does timestamp based partitioning of data and automates this process via pg\_cron. A Citus specific environment was created and we're currently conducting performance tests against it at scale to verify it meets our requirements.

This release adds automation to keep our GCP Marketplace application up to date with each release. While not fully automatic due to the manual nature of Marketplace version submission, now any new production tag will trigger the generation and verification of the marketplace images.

This was a big release and there were a lot of other various improvements and fixes. See the full release note below.

## [v0.70](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.70.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: DECEMBER 14, 2022**
{% endhint %}

As part of [HIP-406](https://hips.hedera.com/HIP/hip-406.html), the mirror node is adding a new account staking rewards REST API. This API will show the staking rewards paid to an account over time. The mirror node now also shows staking reward transfers in the transaction REST APIs (e.g. `/api/v1/transactions`, `/api/v1/transactions/{id}`, and the list of transactions in `/api/v1/accounts/{id}`). This can be useful to show which transaction involving your account after the staking period ended triggered the lazy reward payout.

`GET /api/v1/accounts/{id}/rewards`

```
{
  "rewards": [{
    "account_id": "0.0.1000",
    "amount": 10,
    "timestamp": "123456789.000000001"
  }],
  "links": {
    "next": null
  }
}
```

The REST API saw further improvements outside of staking. The accounts REST APIs now show a calculated expiration timestamp to mirror the HAPI `CryptoGetInfo` query. Previously expiration timestamp only shows up if explicitly sent via a transaction that supports it (mainly update transactions). Now if it's `null` we calculate it as `created_timestamp.seconds + auto_renew_period`. Every contract results endpoint was updated to include an address field for the EVM address of the created contract.

This release makes progress on being able to execute contract calls on the mirror node as outlined in [HIP-584](https://hips.hedera.com/HIP/hip-584.html). A lot of the groundwork is being laid that will be further refined in upcoming releases.\\

## [v0.69](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.69.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 5, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 29, 2022**
{% endhint %}

As noted in previous releases, [HIP-367](https://hips.hedera.com/hip/hip-367) is deprecating the token relationship information returned from HAPI queries. In this release, its mirror node replacement is now feature complete. We now track and show the current fungible token balance in the token relationships API instead of relying upon the 15 minute balance export from consensus nodes. In a future release, the accounts and balances REST APIs will be updated to show the current fungible token balance.

The importer component now supports a local file stream provider. This allows it to read stream files from a local directory instead of just the S3-compatible providers it supported previously. This mode is useful for debugging stream files received out of band or for reducing complexity and latency in a local node setup. To try it out, set `hedera.mirror.importer.downloader.cloudProvider=LOCAL` and populate the `hedera.mirror.importer.dataPath`/`streams` folder with the same file structure as the cloud buckets.

We now show a contract's `CREATE2` EVM address in the contract logs REST APIs. Previously, we would convert the Hedera `shard.realm.num` to a 20-byte EVM address but this did not always reflect the true EVM address of the contract. Using the `CREATE2` form of the EVM address provides increased Ethereum compatibility.

We continue to make progress on converting our build process to Gradle. This release adds a Golang Gradle plugin to download the Go SDK and use it to build and test the Rosetta module.

## [v0.68](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.68.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 18, 2022**
{% endhint %}

Besides the usual round of bug fixes, this release focuses on some internal enhancements to lay the groundwork for some upcoming features. We now track and persist the current fungible token balance in the database. This information is not yet exposed on any API but will be rolled out to the token relationships, accounts and balances REST APIs in the near future.

We're continuing our work towards [CitusDB](https://www.citusdata.com/) as a possible database replacement in this release by adding distribution columns and fixing our v2 schema tests.

Finally, we implemented initial Gradle support to improve build times and provide a better developer experience. Initial testing shows build and test times reduced from 8 minutes overall down to 2 minutes. The Gradle and Maven build scripts will be maintained concurrently for a few releases until we can ensure the Gradle build reaches feature parity with Maven.

## [v0.67](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.67.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 10, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 10, 2022**
{% endhint %}

[HIP-367](https://hips.hedera.com/hip/hip-367) deprecated the list of token balances for an account returned via HAPI. The mirror node has been working on its replacement for a few releases by [storing](https://github.com/hashgraph/hedera-mirror-node/issues/4030) the current account balance, [combining](https://github.com/hashgraph/hedera-mirror-node/issues/4150) contract and entity tables, and [adding a history table](https://github.com/hashgraph/hedera-mirror-node/issues/3251) for `token_account`. This work has paved the way for a new token relationships REST API that will list all fungible and non-fungible tokens associated with a particular account. This API also returns some metadata like balance, KYC status, freeze status, and whether it's an automatic association or not. Currently the fungible token balance being returned is from the 15 minute account balance file. We are [actively](https://github.com/hashgraph/hedera-mirror-node/issues/4402) working towards tracking the real-time fungible token balance and it will be updated to reflect that in a future release.

`GET /api/v1/accounts/{id}/tokens`

```
{
  "tokens": [{
    "automatic_association": true,
    "balance": 15,
    "created_timestamp": "1234567890.000000002",
    "freeze_status": "UNFROZEN",
    "kyc_status": "GRANTED",
    "token_id": "0.0.1135"
  }],
  "links": {
    "next": null
  }
}
```

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) details how smart contract traceability information from consensus nodes is made available via sidecar files. The contract state changes within the sidecar are persisted by mirror nodes and made available on the contract results APIs. However, these state changes do not always reflect the full list of smart contract storage values since not all slots are modified during any particular contract invocation. We now persist a rolled up view of state changes to track the latest slot key/value pairs. This contract state information is now exposed via a new `/api/v1/contracts/{id}/state` REST API where `id` is either the `shard.realm.num`, `realm.num`, `num`, or a hex encoded EVM address of the smart contract.

`GET /api/v1/contracts/{id}/state`

```
{
  "state": [{
    "address": "0x0000000000000000000000000000000000001f41",
    "contract_id": "0.0.100",
    "slot": "0x0000000000000000000000000000000000000000000000000000000000000001",
    "timestamp": "1676540001.234390005",
    "value": "0x0000000000000000000000000000000000000000000000000000000000000010"
  }],
  "links": {
    "next": null
  }
}
```

In a push for further decentralization, we now randomize node used to download data files after reaching consensus. Previously, the data structure we used generally caused us to use the first node returned in the verified list, which was usually `0.0.3`. We now randomly pick a node until we can successfully download the stream file. We also internally changed all tables that used node account ID to use its node ID instead.

On the testing front, we enhanced various test and monitoring tools to add support for new APIs. We also added an acceptance test startup probe to delay the start of the tests until the network as a whole was healthy. This avoids the mirror node acceptance tests reporting a false positive when a long migration or startup process on the consensus or mirror nodes causes a delay.

## [v0.66](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.66.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: OCTOBER 24, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: OCTOBER 17, 2022**
{% endhint %}

Continuing our goal of having up to date balances everywhere, this release now shows the current hbar balance on the balances REST API. If you provide a `timestamp` parameter, it will fallback to the previous behavior and use the 15 minute balance file. This allows us to continue to provide a historical view of your balance over time, while also showing the latest balance if no specific time range is requested. Live fungible token balance is actively being worked on for an upcoming release.

Another big feature this release is support for cloud storage failover. The importer can now be configured with multiple S3 download sources and will iterate over each until one is successful. This makes the mirror node more decentralized and provides more resiliency in the face of cloud failure. The existing `hedera.mirror.importer.downloader` properties used to configure the `cloudProvider`, `accessKey`, `secretKey`, etc. will continue to be supported and inserted as the first entry in the sources list, but it's recommended to migrate your configuration to the newer format. Also in the downloader, we increased the downloader batch size to 100 to improve historical synchronization speed. A `hedera.mirror.importer.downloader.sources.connectionTimeout` property was added to avoid occasional connection errors.

```
hedera:
   mirror:
    importer:
      downloader:
        sources:
        - backoff: 30s
          connectionTimeout: 10s
          credentials:
            accessKey: <redacted>
            secretKey: <redacted>
          maxConcurrency: 50
          projectId: myapp
          region: us-east-2
          type: GCP
          uri: https://storage.googleapis.com
        - credentials:
            accessKey: <redacted>
            secretKey: <redacted>
          type: S3
```

[HIP-573](https://hips.hedera.com/hip/hip-573) gives token creators the option to exempt all of their token’s fee collectors from a custom fee. This mirror node release adds support to persist this new `all_collectors_are_exempt` HAPI field and expose it via the `/api/v1/tokens/{id}` REST API.

An important characteristic of a mirror node is that anyone can run one and store only the data they care about. The mirror node has supported such capability for a few years now but the configuration syntax was a bit tricky to get correct. To address this shortcoming, we add some [examples](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#transaction-and-entity-filtering) to the configuration documentation to clarify things. This entity filtering was historically limited to just create, update and delete operations on entities. We've now expanded this filtering to include payer account IDs and accounts or tokens involved in transfers.

### Breaking Changes

As part of the S3 failover work, we made a number of changes to existing properties to streamline things and only support one property for all stream types. The `hedera.mirror.importer.downloader.(balance|event|record).batchSize` properties were removed in favor of a single, generic `hedera.mirror.importer.downloader.batchSize`. Likewise, the `hedera.mirror.importer.downloader.(balance|event|record).threads` properties were removed in favor of `hedera.mirror.importer.downloader.threads`. The `hedera.mirror.importer.downloader.(balance|event|record).prefix` properties were removed in favor of hardcoded configuration since there's never been a need to adjust these. If you're using any of these properties, please adjust your config accordingly.

If you're writing stream files to disk after downloading by enabling the `writeFiles` or `writeSignatures` properties, there is one other breaking change to be aware of. As part of our migration away from node account IDs, we changed the paths on disk to use the node ID as well. If you'd like to avoid having two directories for the same node please rename your local directories manually. For example, change `${dataDir}/recordstreams/record0.0.3` to `${dataDir}/recordstreams/record0`.

## [v0.65](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.65.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: OCTOBER 11, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: OCTOBER 6, 2022**
{% endhint %}

The mirror node now calculates consensus using the staking weight of all the nodes as outlined in [HIP-406](https://hips.hedera.com/HIP/hip-406.html). If staking is not yet activated, it falls back to the previous behavior of counting each node as `1/N` weight where `N` is the number of consensus nodes running on the network. The `/api/v1/accounts` and `/api/v1/accounts/{id}`REST APIs now expose a `pending_reward` field that provides an estimate of the staking reward payout in tinybars as of the last staking period. The `/api/v1/network/supply` REST API updated its configured list of unreleased supply accounts to accurately reflect the separation of accounts done for staking purposes by Hedera.

This release implements the contract actions REST API detailed in [HIP-513](https://hips.hedera.com/hip/hip-513). An example of an actions payload is shown below. We added `transaction_hash`, `transaction_index`, `block_hash` and `block_number` fields to the contract logs REST APIs. This work was done to optimize the performance for the `eth_getLogs` JSON-RPC method used by the relay. Also for the relay, we now support lookup of non-Ethereum contract results by its 32 byte transaction hash.

`GET /api/v1/contracts/results/0.0.5001-1676540001-234390005/actions`

```
{
  "actions": [{
    "call_depth": 1,
    "call_type": "CALL",
    "call_operation_type": "CALL",
    "caller": "0.0.5001",
    "caller_type": "CONTRACT",
    "from": "0x0000000000000000000000000000000000001389",
    "timestamp": "1676540001.234390005",
    "gas": 1000,
    "gas_used": 900,
    "index": 1,
    "input": "0xabcd",
    "to": "0x70f2b2914a2a4b783faefb75f459a580616fcb5e",
    "recipient": "0.0.8001",
    "recipient_type": "CONTRACT",
    "result_data": "0x1234",
    "result_data_type": "OUTPUT",
    "value": 100
  }],
  "links": {
    "next": null
  }
}
```

To support mirror node explorers being able to display the transaction that created an entity, we added a `created_timestamp` field to the accounts REST API.

The Rosetta module saw a large number of improvements this release. Rosetta now supports a transaction memo be returned in its metadata response. To improve performance of large blocks, Rosetta now limits the number of transactions in its block response. The importer balance reconciliation job was disabled in the Rosetta Docker image since Rosetta performs its own reconciliation process. There were other various fixes to the reconciliation process balance offset, charts testing failing for PRs from forked repos, and fixing a slow search block by hash query in rosetta.

## [v0.64](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.64.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: SEPTEMBER 26 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 12, 2022**
{% endhint %}

In the last release, we began keeping track of the current balance of every account and contract instead of solely relying upon the balance file written every 15 minutes by consensus nodes. In this release, we now show this up-to-date balance on the `/api/v1/accounts` and `/api/v1/accounts/{id}` REST APIs in the existing `balance` field. Token balances and the balances REST API still shows the balance information from the 15 minute balance file. In a future release, we'll look at changing those to track the current balance.

As part of [HIP-406](https://hips.hedera.com/hip/hip-406), it details a pending reward calculation that can be used to estimate the reward payout between your last payout event and the staking period that just ended. The mirror node now does a similar calculation daily and will in a future release show this pending reward amount on the REST API.

The [reconciliation](https://github.com/hashgraph/hedera-mirror-node/tree/main/docs/importer#reconciliation-job) job periodically runs and reconciles the balance files with the crypto transfers that occurred in the record files. This job allowed us to catch an [issue](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#record-missing-for-fail\_invalid-nft-transfers) with missing transactions for `FAIL_INVALID` crypto transfers that was fixed in hedera-services `v0.27.7`. This release contains the errata for the missing transactions that allows reconciliation to proceed successfully once again. It also saw performance improvements including a `delay` property to throttle its speed and added job status persistence so it doesn't restart from the beginning every time. A new `remediationStrategy` property provides a mechanism to continue after failure to aid in debugging multiple reconciliation errors.

## [v0.63](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.63.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: SEPTEMBER 7 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 2, 2022**
{% endhint %}

This release adds a highly requested feature: the mirror node now tracks the current account balance. Previously, the mirror node would store balance information whose source was a balance file that consensus nodes generate and upload every 15 minutes. As a result, balance information was always behind by up to 15 minutes for active accounts. We were able to figure out a way to track this information at scale with SQL in this release. The next release will actually expose this up to date account balance information on both `/api/v1/accounts` or `/api/v1/accounts/{id}`. In future releases, will look at adding live balances to `/api/v1/balances` when no timestamp parameter is provided and track up to date token balances.

Work continues on [HIP-513 Contract Traceability](https://hips.hedera.com/HIP/hip-513.html), with this release adding a few important items. Consensus nodes will, when first activating the sidecar mechanism, send migration records that includes all smart contract runtime bytecode and current storage values. The mirror node now supports receiving these special migration sidecars and updating its database with the migrated data. This paves the way for the mirror node to have the necessary information to execute smart contracts without modifying state in a future release. Also in this release we now show the contract initcode that was used to unsuccessfully create a smart contract in a new `failed_initcode` field in the contract result REST API.

The network supply REST API saw an update to adjust the unreleased supply accounts used to calculate the unreleased supply. This change was necessary as Hedera adjusts the treasury accounts for use with staking.

## [v0.62](https://github.com/hashgraph/hedera-mirror-node/releases?page=2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: AUGUST 22, 2022**
{% endhint %}

Mirror Node 0.62 saw [HIP-406](https://hips.hedera.com/HIP/hip-406.html) staking related improvements to its REST API and partial support for HIP-513 contract traceability.

The `/api/v1/network/nodes` will now use the address book stake as a fallback when it has not seen any `NodeStakeUpdate` transactions on the network. This release also contains a new network stake REST API `/api/v1/network/stake` to show aggregate stake information common to all nodes:

```
{
  "max_staking_reward_rate_per_hbar": 17808,
  "node_reward_fee_fraction": 0.0,
  "stake_total": 35000000000000000,
  "staking_period": {
    "from": "1658774045.000000000",
    "to": "1658860445.000000000"
  },
  "staking_period_duration": 1440,
  "staking_periods_stored": 365,
  "staking_reward_fee_fraction": 1.0,
  "staking_reward_rate": 100000000000,
  "staking_start_threshold": 25000000000000000
}
```

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) Smart Contract Traceability adds support for an optional sidecar to contain contract traceability information. In this release, the mirror node supports downloading and persisting contract state changes, contract initcode, contract runtime bytecode, and contract actions (AKA traces). The `/api/v1/contracts/{id}` REST API now shows the runtime bytecode for newly created contracts. The next release will support a sidecar migration that will populate contract state changes and bytecode for all existing contracts.

[HIP-435](https://hips.hedera.com/HIP/hip-435.html) Record Stream V6 required changes to the state proof REST API in order to not break when V6 was enabled. With this release, the API was updated to support record files in the new v6 format.

The Rosetta API saw a few minor fixes and improvements. It now uses the Hedera network alias everywhere in the Rosetta server . It also fixes the issue that Rosetta did not support alias as the from address for crypto transfers. Additionally, the Rosetta `sub_network_identifier` was disabled since it was not needed.

There were a surprising number of technical debt improvements this release. The REST API and monitor API were both converted from CommonJS to ES6 modules, allowing us to finally upgrade some of our dependencies to the latest version. The REST API spec tests were organization into folders by endpoint and changed to use a single database container for the entire suite. On the importer, mutable contract information was merged into the `entity` table. The `RecordItem` constructor was removed everywhere in favor its builder method. Finally, we added parser performance tests to be able to generate large record files and stress test record file ingestion.

### Breaking Changes

In a recent release, we added the `stake_total` field to the `/api/v1/network/nodes` API to show the aggregate stake of the network. With the addition of the new `/api/v1/network/stake` API, we now have a separate API to return aggregate staking information associated with the network. As such, we made the decision in this release to remove the `stake_total` field from the response of the `/api/v1/network/nodes` API to stay consistent. If you're using this field, please update your code to use the `stake_total` field in the `/api/v1/network/stake` API.

## [v0.61](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.61.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 2, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JULY 27, 2022**
{% endhint %}

This release adds initial support for [HIP-513](https://hips.hedera.com/hip/hip-513) Smart Contract Traceability Extension. Contract traceability information is now available inside an optional sidecar file uploaded separately to cloud storage. Mirror node operators can choose whether to download this extra information by configuring the `hedera.mirror.importer.parser.record.sidecar` properties on the importer. By default, sidecar files will not be downloaded. Enabling it will permit contract state, actions, and bytecode data to be persisted by the mirror node. HIP-513 support is incomplete in this release and the next release will enable full persistence of all sidecar types.

The transactions REST API now supports multiple `transactiontype` query filters to simplify searches across types.

The version of the mirror node in [GCP Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node?project=mirrornode-non-prod-314918) was updated to v0.60.0. This required migration to the new GCP Producer Portal which should help streamline future version updates.

The monitor components saw an option added to retrieve the address book on startup. This avoids having to configure the list of nodes to monitor manually in pre-production environments and ensure the list of nodes is up to date. The monitor now uses OpenAPI generated models to dog food our OpenAPI schema. We also added an option to the monitor to set the max memo length property for published transactions.

## [v0.60](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.60.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JULY 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JULY 14, 2022**
{% endhint %}

The two big features of this release are support for a data retention period and HIP-351 pseudorandom number generation.

On public networks, mirror nodes can generate tens of gigabytes worth of data every day and this rate is only projected to increase. Mirror nodes now support an optional data [retention period](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#retention) that is disabled by default. When enabled, the retention job purges historical data beyond a configured time period. By reducing the overall amount of data in the database it will reduce operational costs and improve read/write performance. Only insert-only data associated with balance or transaction data is deleted. Cumulative entity information like accounts, contracts, etc. are not deleted.

[HIP-351](https://hips.hedera.com/hip/hip-351) adds a pseudorandom number generator transaction. The mirror node now persists this `PrngTransaction` type including the pseudorandom number or the bytes it generates. A future release will expose this information on the REST API.

There were various other improvements in this release. Block numbers are now migrated to be consistent with other mirror nodes regardless of their configured start date when it receives the first v6 record file with the canonical block number from consensus nodes. We added the reward rate at the start of the staking period to the nodes REST API. Rosetta now shows fee crypto transfers operation type as `FEE`. Rosetta also shows account aliases as account addresses in Rosetta DATA API response.

## [v0.59](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.59.0)

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 29, 2022**
{% endhint %}

The previous release saw support for the persistence of [HIP-406](https://hips.hedera.com/HIP/hip-406.html) staking-related data. Staking persistence saw further fine-tuning in this release to adapt to changes in the `NodeStakeUpdateTransaction` protobuf. The `decline_reward`, `staked_account_id`, `staked_node_id` fields were added to `/api/v1/accounts` and `/api/v1/accounts/{id}` to show account-level staking properties. We also added staking related fields to the existing `/api/v1/network/nodes` REST API (see example below).

`GET /api/v1/network/nodes`

```
{
  "nodes": [
    {
      "description": "address book 1",
      "file_id": "0.0.102",
      "max_stake": 50000,
      "memo": "0.0.4",
      "min_stake": 1000,
      "node_account_id": "0.0.4",
      "node_cert_hash": "0x01d...",
      "node_id": 1,
      "public_key": "0x4a...",
      "service_endpoints": [],
      "stake": 20000,
      "stake_not_rewarded": 19900,
      "stake_rewarded": 100,
      "stake_total": 100000,
      "staking_period": {
        "from": "1655164800.000000000",
        "to": "1655251200.000000000"
      },
      "timestamp": {
        "from": "16552512001.000000000",
        "to": null
      }
    }
  ],
  "links": {
    "next": null
  }
}
```

Support for the new record file v6 format as defined in [HIP-435](https://hips.hedera.com/HIP/hip-435.html) was added in this release. Record file v6 adds block number as well as support for the new sidecar record files that carry detailed contract traceability information that mirror nodes can optionally choose to download. The record and signature files are now in a more maintainable protobuf format that should make them easier to enhance with new fields in the future without requiring breaking changes. Also, the v6 record files will now be compressed which should translate into reduced network and storage costs while potentially improving performance. Once v6 is enabled in a future hedera-service's release, mirror node operators will be required to update to a version that supports the new v6 format to avoid downtime.

Rosetta saw a number of improvements this release to better align it with the Rosetta specification. A configurable valid duration seconds option was added to the transaction construction API to support customization of this value. Support for a consistent block number regardless of `startDate` was added in Rosetta now that Hedera has a consistent block as defined in [HIP-415](https://hips.hedera.com/HIP/hip-415.html). A `0x` prefix was added to alias addresses returned via the API to denote that the data is hex-encoded.

## [v0.58](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.58.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 22, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JUNE 16, 2022**
{% endhint %}

This release contains support for HIP-406 Staking, HIP-410 Wrapped Ethereum Transaction, and HIP-482 JSON-RPC Relay as well as a long overdue upgrade to Java 17.

[HIP-406 Staking](https://hips.hedera.com/HIP/hip-406.html) is coming and the mirror node is getting ready for it. This release we added persistence support to store staking information. In the next release, we'll expose this information via our APIs.

[HIP-410](https://hips.hedera.com/HIP/hip-410.html) and [HIP-482](https://hips.hedera.com/HIP/hip-482.html) are both intended to improve the onramp for existing Ethereum developers. Towards that end, we added pagination support to both of the contract logs REST APIs. You can now page through logs via a combination of a consensus timestamp and log index parameters. The new blocks REST APIs also saw new `gas_used` and `logs_bloom` fields added that show the aggregated values for all transactions within the block. Finally, we added a new network fee schedule REST API. Currently, it only exposes the gas price for `ContractCall`, `ContractCreate`, and `EthereumTransaction` types in tinybars.

`GET /api/v1/network/fees`

```
{
  "fees": [
    {
      "gas": 35561,
      "transaction_type": "ContractCall"
    },
    {
      "gas": 481934,
      "transaction_type": "ContractCreate"
    },
    {
      "gas": 35561,
      "transaction_type": "EthereumTransaction"
    }
  ],
  "timestamp": "1633392000.387357562"
}
```

Since mirror node's inception in 2019, it has used Java 11 to build and run due to it being the most recent LTS release. After Java 17 LTS was released in September 2021 we knew we wanted to upgrade. With this release we upgraded to 17 after validating that the mirror node was still functional and performant. If you're using our official container images, they are also on Java 17 so there will be no migration necessary besides updating the image. If you're running outside of a container, you'll need to either upgrade your JRE to 17 or rebuild the jar from source with `-Djava.version=11`.

## [v0.57](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.57.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 25, 2022**
{% endhint %}

This release is focused on adding the necessary data and APIs needed for the JSON-RPC Relay defined in [HIP-482](https://hips.hedera.com/hip/hip-482). The JSON-RPC Relay implements the Ethereum JSON-RPC [standard](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/ethereum/eth1.0-apis/assembled-spec/openrpc.json) and relays [HIP-410 Ethereum transactions](https://hips.hedera.com/HIP/hip-410) to consensus nodes. Since the concept of a block is crucial for JSON-RPC APIs, this release also contains the implementation of [HIP-415 Introduction of Blocks](https://hips.hedera.com/hip/hip-415).

The mirror node now exposes the concept of blocks as introduced in [HIP-415](https://hips.hedera.com/hip/hip-415#mirror-nodes). We now calculate and store the cumulative gas used and the contract log bloom filter for the block as a whole. This HIP defines three new REST APIs and this release includes all three: a list blocks REST API, a get blocks REST API, and a list contract results REST API. The new `/api/v1/blocks` API supports the usual `limit` and `order` query parameters along with `timestamp` and `block.number` to support equality and range operators for consensus timestamps and block numbers, respectively. The `/api/v1/blocks/{hashOrNumber}` is identical to the list blocks but only returns a single block by either its block hash or its block number. Finally, a `/api/v1/contracts/results` REST API was added that is identical to the existing `/api/v1/contracts/{id}/results` but able to search across contracts.

`GET /api/v1/blocks`

```
{
  "blocks": [{
    "count": 4,
    "gas_limit": 150000000,
    "gas_used": 50000000,
    "hapi_version": "0.24.0",
    "hash": "0xa4ef824cd63a325586bfe1a66396424cd33499f895db2ce2292996e2fc5667a69d83a48f3883f2acab0edfb6bfeb23c4",
    "logs_bloom": "0x549358c4c2e573e02410ef7b5a5ffa5f36dd7398",
    "name": "2022-04-07T16_59_23.159846673Z.rcd",
    "number": 19533336,
    "previous_hash": "0x4fbcefec4d07c60364ac42286d5dd989bc09c57acc7370b46fa8860de4b8721e63a5ed46addf1564e4f8cd7b956a5afa",
    "size": 8489,
    "timestamp": {
      "from": "1649350763.159846673",
      "to": "1649350763.382130000"
    }
  }],
  "links": {
    "next": null
  }
}
```

`GET /api/v1/blocks/{hashOrNumber}`

```
{
  "count": 4,
  "gas_limit": 150000000,
  "gas_used": 50000000,
  "hapi_version": "0.24.0",
  "hash": "0xa4ef824cd63a325586bfe1a66396424cd33499f895db2ce2292996e2fc5667a69d83a48f3883f2acab0edfb6bfeb23c4",
  "logs_bloom": "0x549358c4c2e573e02410ef7b5a5ffa5f36dd7398",
  "name": "2022-04-07T16_59_23.159846673Z.rcd",
  "number": 19533336,
  "previous_hash": "0x4fbcefec4d07c60364ac42286d5dd989bc09c57acc7370b46fa8860de4b8721e63a5ed46addf1564e4f8cd7b956a5afa",
  "size": 8489,
  "timestamp": {
    "from": "1649350763.159846673"
    "to": "1649350763.382130000"
  }
}
```

A number of changes were made in support of [HIP-410 Ethereum Transactions](https://hips.hedera.com/hip/hip-410#mirror-node). The `/api/v1/accounts/{idOrAlias}` REST API was updated to accept an EVM address as a path parameter in lieu of an ID or alias. An `ethereum_nonce` and `evm_address` was added to the response of `/api/v1/accounts/{idOrAliasOrAddress}` and `/api/v1/accounts`. The existing `/api/v1/contracts/results/{transactionId}` was updated to accept the 32 byte Ethereum transaction hash as a path parameter in addition to the transaction ID that it supports now. Its response, as well as the similar `/api/v1/contracts/{idOrAddress}/results/{timestamp}`, was updated to add the following new Ethereum transaction fields:

```
{
  "access_list": "0xabcd...",
  "block_gas_used": 564684,
  "chain_id": "0x0127",
  "gas_price": "0xabcd...",
  "max_fee_per_gas": "0xabcd...",
  "max_priority_fee_per_gas": "0xabcd...",
  "nonce": 1,
  "r": "0x84f0...",
  "s": "0x5e03...",
  "transaction_index": 1,
  "type": 2,
  "v": 0
}
```

_Note: Existing fields omitted for brevity._

A new exchange rate REST API `/api/v1/network/exchangerate` was added that returns the [exchange rate](https://github.com/hashgraph/hedera-protobufs/blob/main/services/exchange\_rate.proto) network file stored in `0.0.112`. It supports a `timestamp` parameter to retrieve the exchange rate at a certain time in the past.

```
{
  "current_rate": {
    "cent_equivalent": 596987
    "expiration_time": 1649689200
    "hbar_equivalent": 30000
  },
  "next_rate": {
    "cent_equivalent": 594920
    "expiration_time": 1649692800
    "hbar_equivalent": 30000
  },
  "timestamp": "1649689200.123456789"
}
```

A new `/api/v1/contracts/results/logs` API was added with the same query parameters and response as `/api/v1/contracts/{address}/results/logs` but with the ability to search across contracts. It does not support address as a query parameter as it’s expected users use the existing API if they need logs for a specific address. The same rules around not exceeding `maxTimestampRange` still applies and allows it to stay performant. Pagination is possible using a combination of the timestamp and index query parameters.

Finally, this releases completes our implementation of [HIP-423 Long Term Scheduled Transactions](https://hips.hedera.com/hip/hip-423). Two new fields `wait_for_expiry` and `expiration_time` were added to `/api/v1/schedules` and `/api/v1/schedules/{id}`

## [v0.56](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.56.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MAY 17, 2022**
{% endhint %}

This is a big release with support for six different Hedera Improvement Proposals. Most of these changes are on the ingest side of things and future releases will work on adding them to our APIs.

[HIP-16](https://hips.hedera.com/hip/hip-16) will enable contract expiry and brings a new auto renew account field that contains the account responsible for any renewal fees associated with the contract. The contract REST APIs now show this `auto_renew_account` field along with a new `permanent_removal` flag that is set to true when the system expires a contract.

We missed a requirement in our implementation of [HIP-329](https://hips.hedera.com/hip/hip-329) CREATE2 opcode to allow a HAPI client to do a native transfer of assets to a contract known only by its CREATE2 address. We fixed this logic gap and now support EVM addresses in a `CryptoTransferTransaction`.

[HIP-336](https://hips.hedera.com/hip/hip-336) saw further polish to our allowance support. Support for an optional spender ID parameter on our `/api/v1/accounts/{id}/nfts?spender.id={id}` REST API is now included.

[HIP-410](https://hips.hedera.com/hip/hip-410) brings with it the ability to wrap an Ethereum native transaction and submit it to Hedera. The mirror node can now parse this new `EthereumTransaction` along with the results from its execution. Any contracts created or results and logs generated by its execution will automatically show up on the relevant contract REST APIs. Support for specifying the contract initcode directly on a contract create was also added. To support Externally Owned Accounts (EOA) being able to submit Ethereum transactions, we now calculate and store the EVM address of the account's ECDSA secp256k1 alias. Finally, we added support for repeated topics in contract logs REST API to bring it more inline with the `eth_getLogs` JSON-RPC method. If you supply multiple different topic parameters (e.g. `topic0` and `topic1`) it is considered an `AND` operation as before, but if pass repeated parameters like `topic0=0x01&topic0=0x02&topic1=0x03` it means “(topic 0 is 0x01 or 0x02) and (topic 1 is 0x03)”.

[HIP-415](https://hips.hedera.com/hip/hip-415) is defining a block as the number of records files since stream start (AKA genesis). Since only mirror nodes have a full history, they will be used to provide consensus nodes the current block number to update their state. Since partial mirror nodes with an effective start date after stream start won't have all record files, they may contain an inconsistent value for their block number in contrast to other mirror nodes with all data. This release attempts to correct that with a migration to bring them inline with full mirror nodes so everyone has a consistent block number value.

[HIP-423](https://hips.hedera.com/hip/hip-423) Long term scheduled transactions enhances the existing scheduled transactions to allow time-based scheduling of transactions. This release adds ingest support for the new schedule-related fields. Next release will expose these fields via our existing schedule REST APIs.

### Upgrading

As part of this release, we have finished upgrading our PostgreSQL databases to version 14 and have updated all tests to use that version as well. We recommend mirror node operators plan their migration to PostgreSQL 14 at their earliest convenience. More details on the upgrade process can be found in our [database guide](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#upgrade).

The migrations in this release are estimated to take up to 2 hours against a mainnet database. However, the migration that takes up the bulk of this time will only run if it needs to correct block numbers. This is only necessary if your mirror node is a partial mirror node and has an effective start date that occurs after the stream start.

## [v0.55](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.55.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MAY 4, 2022**
{% endhint %}

This release is mainly focused on finishing out our support for [HIP-336](https://hips.hedera.com/hip/hip-336) Approval and Allowance API for Tokens. We added support for the new `CryptoDeleteAllowance` transaction and removed support for the `CryptoAdjustAllowance` transaction that didn't make it into the final design. NFT allowances are tracked at the NFT transfer granularity allowing for up to date allowance information on the mirror node. Current spender information will show up in both `/api/v1/accounts/{id}/nfts` and `/api/v1/tokens/{id}/nfts` REST APIs. We also added the `is_approval` flag to APIs that show transfers.

With more developers using computers using Apple's M-series CPUs, it become clear the mirror node needed to support ARM-based architectures to accommodate them. In this release we added multi-architecture Docker images using [docker buildx](https://docs.docker.com/buildx/working-with-buildx/). We now push `linux/amd64` and `linux/arm64` variants to our [Google Container Registry](https://gcr.io/mirrornode). If there's a need for additional operating systems or architectures in the future it can easily be expanded upon.

We also updated our [GCP Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) application to the latest version.

## [v0.54](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.54.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: APRIL 19, 2022**
{% endhint %}

This release adds support for three new REST APIs and four HIPs.

[HIP-21](https://hips.hedera.com/hip/hip-21) describes the need for a free network info query to enable SDKs and other clients to be able to retrieve the current list of nodes. In v0.49.1, we added a new `NetworkService.getNodes()` gRPC API. In this release, we're adding an equivalent address book API to our REST API. In addition to the standard `order` and `limit` parameters, it supports a `file.id` query parameter to filter by the two address books `0.0.101` or `0.0.102` and a `node.id` query parameter to filter nodes and provide pagination.

`GET /api/v1/network/nodes`

```
{
  "nodes": [
    {
      "description": "",
      "file_id": "0.0.102",
      "memo": "0.0.3",
      "node_account_id": "0.0.3",
      "node_cert_hash": "0x3334...",
      "node_id": 0,
      "public_key": "0x308201...",
      "service_endpoints": [
        {
          "ip_address_v4": "13.124.142.126",
          "port": 50211
        }
      ],
      "timestamp": {
        "from": "1636052707.740848001",
        "to": null
      }
    }
  ],
  "links": {
    "next": null
  }
}
```

[HIP-336](https://hips.hedera.com/hip/hip-336) describes new Hedera APIs to approve and exercise allowances to a delegate account. An allowance grants a spender the right to transfer a predetermined amount of the payer's hbars or tokens to another account of the spender's choice. In v0.50.0 we added database support to store the new allowance transactions. In this release, two new REST APIs were created to expose the hbar and fungible token allowances. Full allowance support won't be available until a future release when consensus nodes enable it on mainnet.

`GET /api/v1/accounts/{accountId}/allowances/crypto`

```
{
  "allowances": [
    {
      "amount_granted": 10,
      "owner": "0.0.1000",
      "spender": "0.0.8488",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": "1633466568.31556926"
      }
    },
    {
      "amount_granted": 5,
      "owner": "0.0.1000",
      "spender": "0.0.9857",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": null
      }
    }
  ],
  "links": {}
}
```

`GET /api/v1/accounts/{accountId}/allowances/tokens`

```
{
  "allowances": [
    {
      "amount_granted": 10,
      "owner": "0.0.1000",
      "spender": "0.0.8488",
      "token_id": "0.0.1032",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": "1633466568.31556926"
      }
    },
    {
      "amount_granted": 5,
      "owner": "0.0.1000",
      "spender": "0.0.9857",
      "token_id": "0.0.1032",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": null
      }
    }
  ],
  "links": {}
}
```

Also on the REST API, we added support for [HIP-329](https://hips.hedera.com/HIP/hip-329.html) CREATE2 addresses. Now any API that accepts a contract ID will also accept the 20-byte EVM address as a hex-encoded string. We improved the performance of the REST API by adding cache control headers to enable distributed caching via a CDN. The performance of the list transactions by type REST API saw a fix to improve its performance.

As part of [HIP-260](https://hips.hedera.com/hip/hip-260), contract precompile call data now populates new fields `amount`, `gas`, and `function_parameter` inside `ContractFunctionResult` within the `TransactionRecord`. Mirror node now stores these fields and exposes them via its existing contract results REST APIs.

There were a number of security improvements made to containerized mirror nodes. All Docker images now run as non-root regardless of running in Kubernetes or Docker Compose. The helm charts saw changes to conform to the Kubernetes [restricted](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) pod security standard. This ensures the mirror node runs with security best practices and reduces its overall attack surface. The Kubernetes Pod Security Standard replaces the deprecated PodSecurityPolicy and as such we've removed all configuration related to the latter.

### Upgrading

This release has a long migration that is expected to take around 75 minutes to complete, depending upon your database hardware and configuration. As always, we recommend a red/black deployment to eliminate downtime during migrations. If you're using the `hedera-mirror-common` chart, please check the [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack#upgrading-chart) upgrade notes to ensure Prometheus Operator can update successfully.

## [v0.53](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.53.1)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 7, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 29, 2022**
{% endhint %}

This release is mainly focused around the area of data integrity and ensuring the data in the mirror node is consistent with consensus nodes. To this end, we added an errata database migration that only runs for mainnet and corrects three [known issues](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#errata) that impacted the stream files. The state of the consensus nodes was never impacted, only the externalization of these changes to the stream files that the mirror node consumes.

To find the inconsistent data and ensure it stays consistent going forward, we added a new balance reconciliation job. This job runs nightly and compares the balance file information against the record file information to ensure they are in sync. It does three checks for each balance file: verifies the balance files add up to 50 billion hbars, verifies the aggregated hbar transfers match the balance file, and verifies the aggregated token transfers match the balance file. It can be disabled if not needed via `hedera.mirror.importer.reconciliation.enabled=false`.

We also fixed a bug that caused transfers with a zero amount to show up for crypto create transactions with a zero initial balance. This was due entirely to our code inserting the extra transfers, not because of any problem in the stream files. We also fixed an REST API bug that caused the contract byte code to show up as double encoded to hex.

For the Rosetta API, we added account alias support to various endpoints. And we now support parsing contract results for precompiled contract functions like HTS functions. This capability is disabled by a feature flag and will be enabled in a future release.

### Upgrading

This release contains a couple medium sized database migrations to correct the erroneous data in the database. It is expected to take about 45 minutes against a full mainnet database.

## [v0.52](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.52.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MARCH 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 15, 2022**
{% endhint %}

[HIP-331](https://hips.hedera.com/HIP/hip-331.html) is a community contributed improvement proposal requesting the addition of a new REST API to retrieve an account's list of owned non-fungible tokens (NFTs). The mirror node has an existing `/api/v1/tokens/{tokenId}/nfts` API to retrieve all NFTs for a given token, but it didn't satisfy the requirement to show NFTs across token classes. This release adds the new `/api/v1/accounts/{accountId}/nfts` API to satisfy this need. It is our first API with multiple query parameters required for paging and as such has a few restrictions around their use. Please see the OpenAPI [description](https://github.com/hashgraph/hedera-mirror-node/blob/21c6c464f77d555619af5955843e7d798fcd17b4/hedera-mirror-rest/api/v1/openapi.yml#L51) for this API for further details.

`GET /api/v1/accounts/0.0.1001/nfts?token.id=gte:1500&serialnumber=gte:2&order=asc&limit=2`

```
  {
    "nfts": [
      {
        "account_id": "0.0.1001",
        "created_timestamp": "1234567890.000000006",
        "deleted": false,
        "metadata": "bTI=",
        "modified_timestamp": "1234567890.000000006",
        "serial_number": 2,
        "token_id": "0.0.1500"
      },
      {
        "account_id": "0.0.1001",
        "created_timestamp": "1234567890.000000008",
        "deleted": false,
        "metadata": "bTM=",
        "modified_timestamp": "1234567890.000000008",
        "serial_number": 3,
        "token_id": "0.0.1500"
      }
    ],
    "links": {
      "next": "/api/v1/accounts/0.0.1001/nfts?order=asc&limit=2&token.id=gte:0.0.1500&serialnumber=gt:3"
    }
  }
```

The mirror node now has performance tests written using [k6](https://k6.io/) for all of our APIs. These tests can be used to verify the performance doesn't regress from release to release. In the future, we plan to integrate these into a [nightly regression test suite](https://github.com/hashgraph/hedera-mirror-node/issues/3099) to improve our current approach of testing each release.

A number of deployment issues were addressed in this release. We now disable leader election by default until we can fix the issues with its implementation. Likewise we changed the importer Kubernetes deployment strategy from a rolling update to recreate to avoid ever having multiple importer pods running concurrently. A migration readiness probe was added to the importer. This will mark importer pods as unready until it completes all database migrations. Doing this will ensure Helm doesn't finish its release and run its tests before the migrations are completed.

We continue to fine tune our Rosetta implementation with a number of performance improvements and bug fixes. The performance of the Rosetta get genesis balance script was improved to reduce initial startup time. The embedded PostgreSQL container was upgraded to PostgreSQL 14. The Rosetta unified Docker image was updated to comply with the Rosetta persistence requirements.

## [v0.51](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.51.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 28, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: FEBRUARY 25, 2022**
{% endhint %}

This is a smaller release focusing on observability improvements and Rosetta API fixes.

On the observability front, we've reduced the volume of log information the REST API produces in half. We also change the REST API to generate a consistent trace log for all responses that includes accurate client IPs, the elapsed time, and a status code. We reduced the number of time series by about 50% that the mirror node produces to reduce monitoring costs.

For the Rosetta API, we added a workaround for the missing disappearing token transfer issue that allows the check data reconciliation to pass. Overall reconciliation time was improved by tweaking configuration parameters and improving NFT balance tracking performance. We worked around slow genesis account balance file loading by Rosetta CLI by switching to a dynamic account balance loading approach. A number of other Rosetta issues were also addressed.

## [v0.50](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.50.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 23, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: FEBRUARY 15, 2022**
{% endhint %}

This release adds support for three new improvement proposals: HIP-260 Smart Contract Traceability, HIP-329 CREATE2 Opcode, and HIP-336 Allowance APIs. It also updates the REST API to reflect the latest phases of HIP-226 and HIP-227 and updates the Rosetta API for HIP-31.

[HIP-260](https://hips.hedera.com/hip/hip-260) describes a need for improving the traceability of smart contracts by providing a verifiable trail of contract state changes in the transaction record. The mirror node can now store these state changes and expose them via the contract results REST API to show the values read and written for each slot. This information was added to both `/api/v1/contracts/results/{transactionId}` and `/api/v1/contracts/{id}/results/{timestamp}`. Below is an example, with other fields omitted for brevity:

```
{
  "state_changes": [{
      "address": "0x0000000000000000000000000000000000001f41",
      "contract_id": "0.0.8001",
      "slot": "0x0000000000000000000000000000000000000000000000000000000000000002",
      "value_read": "0xaf846d22986843e3d25981b94ce181adc556b334ccfdd8225762d7f709841df0",
      "value_written": "0x000000000000000000000000000000000000000000c2a8c408d0e29d623347c5"
    }, {
      "address": "0x0000000000000000000000000000000000001f42",
      "contract_id": "0.0.8002",
      "slot": "0xe1b094dec1b7d360498fa8130bf1944104b7b5d8a48f9ca88c3fc0f96c2d7225",
      "value_read": "0x000000000000000000000000000000000000000000000001eafa3aaed1d27246",
      "value_written": null
   }]
}
```

[HIP-329](https://hips.hedera.com/hip/hip-329) adds support for [EIP-1014](https://eips.ethereum.org/EIPS/eip-1014) generated contract addresses via the CREATE2 opcode. As part of this, a new `evm_address` is added to the transaction record that will be present for contract create transactions. Additionally, this `evm_address` can be populated in any `ContractID` that appears in the transaction body. The mirror node was updated to be able to map this `evm_address` to its corresponding contract number and to expose this property on the contracts REST API. We also store full contract information for child contracts since they now appear as separate internal transactions in the record stream, filling a long-standing gap in missing smart contract data.

[HIP-336](https://hips.hedera.com/hip/hip-336) allowance functionality allows an account owner to delegate another account to spend hbars or tokens on his or her behalf. This feature provides an implementation of [ERC20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20), IERC20, and [ERC721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) on the Hedera network. The mirror node was updated to support these new transaction types and store the absolute or relative crypto, fungible or non-fungible allowances. In a later release we will expose this information via a REST API as detailed in the design [document](https://github.com/hashgraph/hedera-mirror-node/blob/v0.50.0/docs/design/allowances.md).

Multiple new fields were added to the contract REST APIs as outlined in [HIP-226](https://hips.hedera.com/hip/hip-227) and [HIP-227](https://hips.hedera.com/hip/hip-226). The fields `bloom`, `result`, and `status` were added to the contract results API response. `result` and `status` show similar information with the former being the HAPI response enum while the latter returning `0x1` or `0x0` to show if the transaction was successful or not, as is common in web3 APIs. We also added `bloom` to the contract logs API response. Finally, we now return a partial response for contract calls without a result.

The importer component added a new `hedera.mirror.importer.parser.record.entity.persist.topics` property to control the persistence of topic messages. This can be set to false for mirror node operators if topic message data is not being used. On mainnet alone, this data currently takes up to 2TB worth of storage.

The Monitor component gained support for parallel node validation to improve startup performance. Now all validation is done in a background thread, adding and removing nodes as necessary while the publisher thread continues publishing transactions without any interruptions. This re-work also fixed issues with subscription halting during node validation and taking too long to validate a down node.

Rosetta saw a few important improvements including adding support for [HIP-31](https://hips.hedera.com/hip/hip-31) expected token decimals. The Rosetta unified Docker image saw functionality added to automatically restore the database using a database snapshot on initial startup.

### Breaking Changes

As part of HIP-329 CREATE2, we renamed the existing `solidity_address` in the contract REST API to `evm_address`. This new name accurately reflects the naming in the HIP and protobuf and avoids tying the address to Solidity when Hedera supports more than just Solidity contracts.

## [v0.49](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.49.1)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 15, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: FEBRUARY 4, 2022**
{% endhint %}

This release implements three Hedera Improvement Proposals (HIPs) and upgrades the mirror node database to the latest version.

[HIP-21](https://hips.hedera.com/hip/hip-21) describes the need for a free network info query to enable SDKs and other clients to be able to retrieve the current list of nodes. To satisfy this need we added a new `NetworkService.getNodes()` streaming gRPC API to get the list of current nodes from the address book network file. By making it a streaming API we avoid the client having to handle paging themselves, while still allowing us to split the large address book into smaller chunks. Since there are two address book files, we provide an option to choose which `FileID` to return.

```
message AddressBookQuery {
    .proto.FileID file_id = 1; // The ID of the address book file on the network. Can be either 0.0.101 or 0.0.102.
    int32 limit = 2;           // The maximum number of node addresses to receive before stopping. If not set or set to zero it will return all node addresses in the database.
}

service NetworkService {
    rpc getNodes (AddressBookQuery) returns (stream .proto.NodeAddress);
}
```

[HIP-171](https://hips.hedera.com/hip/hip-171) describes the need for returning the payer account in the topic message REST API response. This release does just that while also adding in the topic message chunk information that was present in the gRPC API but missing from the REST API.

```
 {
+  "chunk_info": {
+    "initial_transaction_id": {
+      "account_id": "0.0.1000",
+      "nonce": 0,
+      "scheduled": false,
+      "transaction_valid_start": "1234567890-000000006"
+    },
+    "number": 2,
+    "total": 5
+  },
   "consensus_timestamp": "1234567890.000000001",
   "topic_id": "0.0.7",
   "message": "bWVzc2FnZQ==",
+  "payer_account_id": "0.0.1000",
   "running_hash": "cnVubmluZ19oYXNo",
   "running_hash_version": 2,
   "sequence_number": 1
 }
```

Continuing our support for [HIP-32](https://hips.hedera.com/hip/hip-32) auto account creation, we added alias support to our accounts REST API. Now when you query `/v1/api/accounts/:id` the `id` can be either a Hedera entity in the `0.0.x` form or a hex-encoded alias. An account's `alias` will now also show up in all of the accounts REST API output.

A lot of testing was done to ensure that PostgreSQL 14 functions correctly with the mirror node and provides as good as or better performance to older versions. We are now in the process of migrating our Hedera managed mirror nodes to PostgreSQL 14. We recommend other mirror node operators consider upgrading to the latest database version at their earliest convenience and have provided upgrade [instructions](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#upgrade) to aid in that process.

## [v0.48](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.48.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 26, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: JANUARY 18, 2022**
{% endhint %}

[HIP-206](https://hips.hedera.com/hip/hip-206) adds a parent consensus timestamp to the transaction record for internal transactions that occur like HTS precompiles invoked from a smart contract. To round out the `nonce` support in the last release we added `parent_consensus_timestamp` to `/api/v1/accounts/{id}` and `/api/v1/transactions`. This field helps define the parent/child relationships between transactions.

[HIP-226](https://hips.hedera.com/hip/hip-226) describes the recently added contract results REST API. Each release we've been iterating and adding more functionality to the API until it matches the description in the HIP. This release adds the list of logs generated by a smart contract execution. Here's a sample of the new JSON response:

```
{
    "amount": 30,
    ...
    "logs": [
      {
        "address": "0x0000000000000000000000000000000000001389",
        "contract_id": "0.0.5001",
        "data": "0x0123",
        "index": 0,
        "topics": [
          "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925",
          "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
          "0xe8d47b56e8cdfa95f871b19d4f50a857217c44a95502b0811a350fec1500dd67"
        ]
      },
      {
        "address": "0x000000000000000000000000000000000000138a",
        "contract_id": "0.0.5002",
        "data": "0x0123",
        "index": 1,
        "topics": [
          "0x97c1fc0a6ed5551bc831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
        ]
      }
    ]
  }
```

In the last release, we added a new Web3 JSON-RPC API. With this release, we've added a `hedera-mirror-web3` Helm chart that can be used to deploy it on Kubernetes. Additional metrics were added to the Java module and a new Grafana dashboard was created to visualize them. The Web3 API was also added to the docker-compose file.

### Upgrading

If you're upgrading an existing deployment of our Helm chart, there are a few breaking changes to keep in mind. First, we deploy the new Web3 API chart by default and it requires a `mirror_web3` database user exist with read permission. Please create the new database user before upgrading or you can disable the `hedera-mirror-web3` sub-chart.

We've upgraded the `PodDisruptionBudget` resources from `policy/v1beta1` to `policy/v1` and as a result now require Kubernetes 1.21 or greater. For the `hedera-mirror` chart, if you're using the optional `postgresql` sub-chart you must scale the PostgreSQL replicas down to one before initiating an upgrade in order to upgrade repmgr.

If you're using the `hedera-mirror-common` chart, there are a number of breaking changes in the community sub-charts it uses. Before upgrading, you will need to delete the `prometheus-adapter` and `kube-state-metrics` deployments. You'll also need to reinstall a few custom resource definitions. Run the below commands to do so:

```
kubectl delete deployment -l app.kubernetes.io/name=kube-state-metrics --cascade=orphan
kubectl delete deployment -l app=prometheus-adapter
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagers.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_podmonitors.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_probes.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheuses.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_prometheusrules.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml
kubectl apply --server-side -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.53.1/example/prometheus-operator-crd/monitoring.coreos.com_thanosrulers.yaml
```

## [v0.47](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.47.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 3, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: DECEMBER 30, 2021**
{% endhint %}

This release continues the focus on Smart Contracts 2.0. The mirror node is useful for debugging a smart contract execution and our focus has been on providing APIs to make developers' lives easier. To that end, we added support for transaction ID nonce, a new contract logs REST API, and a new web3 API component.

The new Web3 API module provides an implementation of existing JSON-RPC APIs for the Hedera network. JSON-RPC API is a widely used standard for interacting with distributed ledgers. The aim in providing a Hedera implementation of these APIs is to ease the migration of existing dApps to Hedera and simplify the developer on-ramp. Currently, the Web3 module only provides a partial implementation of the [Ethereum JSON-RPC API](https://eth.wiki/json-rpc/API). Specifically, only the `eth_blockNumber` method has been implemented in this release as we focused on putting the groundwork in place first.

As part of [HIP-32](https://hips.hedera.com/HIP/hip-32.html) and [HIP-206](https://hips.hedera.com/HIP/hip-206.html) a `nonce` field was added to the `TransactionID` protobuf to guarantee uniqueness for platform generated transactions. This `nonce` field was added to any REST API that returns transaction data. A `nonce` query parameter was added to `/api/v1/transactions/:transactionId`, `/api/v1/transactions/:transactionId/stateproof`, and `/api/v1/contracts/results/:transactionId` to be able to distinguish between a user-submitted transaction and an internal transaction generated as a result of that transaction. Note that `/api/v1/transactions/:transactionId` without a `nonce` parameter will default to returning all transactions regardless of nonce while the other APIs will default `nonce` to `0`.

The new `/api/v1/contracts/{id}/results/logs` REST API provides a search API to query for logs across contract executions for a particular contract. Searching by consensus timestamp and topics is supported. Note that for performance reasons it doesn't currently support pagination and requires a timestamp or timestamp range be provided to search by topic.

## [v0.46](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.46.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 20, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: DECEMBER 17, 2021**
{% endhint %}

0.46 is a feature-packed release and includes support for three new HIPs, three new REST APIs, a redesigned monitor dashboard, a new BDD test suite for the Rosetta API, and one new module. Let’s dive in!

[HIP-222](https://hips.hedera.com/hip/hip-222) adds support for ECDSA (secp256k1) keys to the Hedera network to help improve the developer experience of migrating a dApp to Hedera. The mirror node was updated to be able to parse and store the new key type along with its corresponding new signature. Since this key type is also considered a primitive key like ED25519 (e.g. not a complex key list or threshold key), you can now search for accounts and tokens by their 66-character ECDSA public key. As an added bonus, we now also allow searching by complex public keys that are a key list or threshold key with exactly one primitive key.

[HIP-32](https://hips.hedera.com/hip/hip-32) auto-account creation lets a new user receive ℏ via a CryptoTransfer _without_ having already created an account on the network. The mirror node was updated to store the user's alias and map transfers that contain an alias to the newly created account ID. In an upcoming release, we'll add the ability to query for an account by its alias.

[HIP-206](https://hips.hedera.com/hip/hip-206) integrates the Hedera Token Service (HTS) into the Hedera Smart Contract Service (HSCS), allowing contracts to transfer, mint, burn, associate, and dissociate tokens programmatically. While support for consensus nodes is still being worked on, the mirror node now has preliminary support for HTS precompiled transactions. This adds a new `nonce` field to the transaction ID as well as the concept of parent/child relationships between transactions. In an upcoming release, we'll add `nonce` support and expose parent/child relationships to the REST API.

The REST API saw three new REST APIs created to support the new Smart Contracts 2.0 feature. After executing a smart contract, developers can use the `/api/v1/contracts/{id}/results` API to search for a contract's execution results by timestamp range or payer account (e.g. `from`). Once the result is located, the new `/api/v1/contracts/{id}/results/{timestamp}` API can retrieve detailed information about the smart contract call. If you already know the transaction's ID, you can directly retrieve the smart contract results via the new `/api/v1/contracts/results/{transactionId}` API. In an upcoming release, we'll add support for logs, state changes, and more to this API. Below is an example response:

`/api/v1/contracts/results/{transactionId}`

```json
{
    "amount": 30,
    "block_hash": "0x6ceecd8bb224da491",
    "block_number": 17,
    "call_result": "0x0606",
    "contract_id": "0.0.5001",
    "created_contract_ids": ["0.0.7001"],
    "error_message": "",
    "from": "0x0000000000000000000000000000000000001f41",
    "function_parameters": "0x0707",
    "gas_limit": 987654,
    "gas_used": 123,
    "hash": "0x3531396130303866616264653464",
    "timestamp": "167654.000123456",
    "to": "0x0000000000000000000000000000000000001389"
}
```

Other miscellaneous changes include now validating the REST API tests against the OpenAPI specification. This should help keep the specification file better in sync with the code until we can fully integrate the specification. The Node.js based monitor dashboard saw a visual refresh in order to make it easier to use. The Rosetta API saw a new suite of crypto and token Behavior Driven Development (BDD) tests. As a result of these tests a number of bugs were found and addressed. Finally, we refactored some common classes into a `hedera-mirror-common` module to share code with a future API module.

#### Database Migration

Due to the aforementioned features, we had to make changes to the database schema that may take some time to complete. We've tested the migrations against a mainnet database with 1.9B+ transactions and it completed in around five hours. Mirror node operators may see the migration take more or less time depending upon the size of their data, database hardware, and database configuration flags.

## [v0.45.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.45.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 8, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: DECEMBER 6, 2021**
{% endhint %}

The mirror node now captures the full history of changes that occur to accounts and contracts over time. Prior to this, the mirror node would only maintain the current state of a Hedera entity. With this change, all create, update and delete transactions that occur for an entity will cause a snapshot to be created to represent how it appeared at each of those points in time. This information can be used to query the API for an entity at a particular consensus timestamp in the past.

Currently, this historical lookup option is only supported on the contracts REST API. For example, you can now search for `/api/v1/contracts/0.0.1000?timestamp=lte:1609480800` to see the state of the contract `0.0.1000` on January 1st, 2021. Related to contracts, we added new acceptance tests for contract related APIs. The [design document](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/smart-contracts.md) was updated to detail new APIs that we are proposing to implement. Those changes are also detailed in Hedera Improvement Proposals (HIPs) for [contract results](https://github.com/hashgraph/hedera-improvement-proposal/pull/226) and [contract execution logs](https://github.com/hashgraph/hedera-improvement-proposal/pull/227) REST APIs. Please take a look and let us know if you have any feedback.

On the CitusDB front, we continue to make progress. All of our reference tables are now removed in favor of database or application enums. This should help improve performance and streamline the database migration. We've updated our test harness to use the latest version of CitusDB that uses PostgreSQL 14. Finally, we now create distributed tables with entity IDs used as distribution columns for partitioning and co-locate them with other tables as appropriate.

The Rosetta API also saw some improvements including the ability to create accounts online in `/construction/submit`. An issue with token balance reconciliation was also addressed.

## [v0.44.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.44.0)

Making [progress](https://github.com/hashgraph/hedera-mirror-node/issues/2675) on transitioning our database to [CitusDB](https://citusdata.com/), this release adds a new `v2` schema with initial support for CitusDB. Automated testing against CitusDB was added to our CI pipeline so that it runs concurrently with the `v1` PostgreSQL-based schema. The transaction payer account ID was added to transfer related tables. This will be used as a distribution column for database partitioning across a dimension that is not time-based. This allows the mirror node to scale reads and writes as more transaction payers use the system.

The rest of the release is mainly focused around performance improvements. We no longer persist minimal entity information for every entity ID encountered in a transaction. This was a performance drag but also caused problems with our plans to track entity history in an upcoming release. A few of our reference tables were removed in favor of using an application enum instead to map protobuf values to descriptive strings.

On the REST API, retrieval of accounts by public key was optimized to improve its performance. If your application does not require balance information, you can see additional performance gains by setting the new `balance` parameter to `false` for account API calls. The code was optimized to replace `Array.concat` with `Array.push` and to cache entity ID construction. The biggest change is probably the potentially breaking change to the `limit` parameter.

### Breaking Changes

The maximum number of rows the REST API can return was changed from 500 to 100. Likewise the default number of rows the REST API returns if the `limit` parameter is unspecified was changed from 500 to 25. If a request is sent requesting more than 100 it won't fail. Instead, it will transparently use the smaller of the two values. As a result, this should not be a breaking change unless your application makes assumptions about the exact number of results being returned. We may tweak these values in the future for performance reasons so it's good practice to update your application to handle arbitrary limits and results.

## [v0.43.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.43.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 18, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 12, 2021**
{% endhint %}

### Smart Contracts

With Hedera's increased focus on [Smart Contracts](https://hedera.com/blog/hedera-evm-smart-contracts-now-bring-highest-speed-programmability-to-tokenization), we took the time to revamp the mirror node's smart contract support and lay the groundwork for future enhancements. As detailed in the [design document](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/smart-contracts.md), plans include new contract-specific REST APIs and Ethereum-compatible APIs in the future.

To prepare for that, the database schema and importer were updated to normalize and store all contract-related information, fixing long-standing bugs like not storing contract bytecode and child contracts. The contract table was split from the generic entity table and work was started on making all the entity tables maintain a history of all changes. The REST API now supports searching for and retrieving specific contracts. Below is an example of retrieving a contract:

`/api/v1/contracts/{id}`

```
{
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2233222c2233222c2233227d"
  },
  "auto_renew_period": 7776000,
  "bytecode": "0xc896c66db6d98784cc03807640f3dfd41ac3a48c",
  "contract_id": "0.0.10001",
  "created_timestamp": "1633466229.96874612",
  "deleted": false,
  "expiration_timestamp": "1633466229.96874612",
  "file_id": "0.0.1000",
  "memo": "First contract",
  "obtainer_id": "0.0.101",
  "proxy_account_id": "0.0.100",
  "solidity_address": "0x00000000000000000000000000000000000003E9",
  "timestamp": {
    "from": "1633466229.96874612",
    "to": "1633466568.31556926"
  }
}
```

### Data Architecture

Over the last few months, work has been underway to analyze possible `PostgreSQL` replacements as the need for handling an ever-increasing amount of data puts strain on the existing mirror node database. After agreeing upon the acceptance criteria, priority was placed on a PostgreSQL-compatible distributed database that can shard our time-series data across many nodes for scale-out reads and writes. That would ensure the quickest time to market and ease transition for Hedera and others using the open source mirror node software. The four distributed databases we chose for our proof of concept included [CitusDB](https://citusdata.com/), [CockroachDB](https://cockroachlabs.com/), [TimescaleDB](https://www.timescale.com/), and [YugabyteDB](https://www.yugabyte.com/).

After a detailed analysis of each, we chose CitusDB for our next database due to its excellent PostgreSQL compatibility (it's a PostgreSQL extension) and its mature support for sharding time-series data. Its distributed query engine routes and parallelizes DDL, DML, and other operations on distributed tables across the cluster. And its columnar storage can compress data up to 8x, speeds up table scans, and supports fast projections. This release contains some foundational work to get our schema ready for partitioning. You can track our [progress](https://github.com/hashgraph/hedera-mirror-node/issues/2675) as we work towards integrating CitusDB into our codebase over the next few months. We plan on maintaining support for both databases for a period of time after the work is complete.

### Performance Improvements

As is usually the case, we took the time to optimize various pieces of the system to work at scale. Our transactions REST API saw some performance improvements by rewriting them using Common Table Expressions (CTE). This will pay future dividends with CitusDB as it allows queries to be ran in parallel easier. An issue with `/api/v1/topics/{id}/messages` timing out for some topics was addressed and the realm and topic number columns were combined to reduce the table and index size. `/api/v1/tokens/{id}/balances` also saw some performance improvements that decreased its average response time. Configuration options for faster historical ingestion were documented so that mirror node operators can get historical data faster.

## [v0.42.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.42.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: OCTOBER 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: OCTOBER 18, 2021**
{% endhint %}

This release saw a lot of improvements to the mirror node's Hedera Token Service functionality. Support for [HIP-24](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-24.md) pause feature on Hedera Token Service was completed. The importer can ingest the new token pause and unpause transaction types and update the token appropriately. Likewise, the token REST API was updated to show the new pause key and pause status.

Along those lines, the token REST API was also updated to show the token memo and a flag to show if it's deleted. Now when an account is dissociated from a token its supply will be properly updated to show the negative transfer. And if the token in that dissociate is of type NFT, all of the NFTs owned by that account will be properly marked as deleted. We also fixed issues with some special negative transfer amounts showing up in the transactions REST API.

A new network supply REST API was added to show the released supply. Having the open source mirror node calculate and show the release supply avoids any single point of failure with the current system because a user could ask multiple mirror nodes and compare their answers (or run their own mirror node).

`GET /api/v1/network/supply`

```
{
	"timestamp": "123456870.854775807",
	"released_supply": 1800000000000000000,
	"total_supply": 5000000000000000000
}
```

Continuing our theme of improving the Rosetta API, NFT support was added to the data and construction APIs. We took the time to convert it to a standard configuration library and reorganize the package structure to be flatter and more consistent. And contexts were added to every layer to enable proper cancellation and timeout support.

## [v0.41.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.41.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: OCTOBER 6, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 30, 2021**
{% endhint %}

This release focuses our efforts on improving our [Rosetta API](https://www.rosetta-api.org/) and making it ready for production use. A new Rosetta Helm chart was added for production deployments to Kubernetes. Observability improvements include health probes, metrics, request logs, alerts, and a Grafana dashboard. Postman integration tests were added to verify post-deployment functionality. Finally, a few important bugs were fixed including missing peer IP addresses and a token balance reconciliation failure.

The importer component was optimized to ingest transactions at 15,000 TPS or higher. This change included improvements to reduce CPU and memory usage while simultaneously increasing the allocated memory available to the process.

Other enhancements include revalidating main nodes periodically in the monitor and adding TLS support for the REST API's database connection.

## [v0.40.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.40.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: SEPTEMBER 27, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: SEPTEMBER 16, 2021**
{% endhint %}

This release adds support for [HIP-23](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-23.md) Automatic Token Association. This feature allows users to opt-in to receiving fungible or non-fungible tokens automatically as part of a transfer without having to be previously associated with the token. The mirror node now stores these implicitly created associations and returns them via its REST API. Additionally, we show the `max_automatic_token_associations` in the accounts REST API.

Besides updating it for HIP-23, the REST API saw quite a few other fixes and improvements. The accounts API now displays its memo and the `receiverSigRequired` field. The REST API packages were renamed to use the `@hashgraph` NPM package scope. This shouldn't be a breaking change as we don't currently publish those packages to NPM. A number of APIs were fixed to ensure lists were returned in a deterministic sort order. Also, the OpenAPI specification was fixed up so that it accurately reflects the current API and can be used to generate client code. Finally, the schedules API had some performance improvements.

On the monitoring side, we enhanced our Grafana dashboards to make them compatible with Grafana Cloud by adding datasource and cluster drop-downs.

## [v0.39.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.39.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 31, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: AUGUST 30, 2021**
{% endhint %}

This release provides compatibility with Hedera Services 0.17 including support for Non-Fungible Tokens (NFTs) and its enhancement to custom fees. For the latter, an NFT creator can set a royalty fee to be charged when fungible value is exchanged for one of their creations and the mirror node has been updated to track this new type of custom fees. Support was also added for effective payer accounts in assessed custom fees and for storing net-of-transfers in fractional fees.

The mostly unused data generator module was removed, resulting in a large increase in code coverage. Coverage has increased from 84% to 92%.

A good amount of bugs were fixed including a crash on REST API startup if the database was down, monitor taking too long to startup, OpenAPI fixes, and more.

## [v0.38.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.38.1)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: AUGUST 17, 2021**
{% endhint %}

This release is a small bug fix release that contains some important fixes for our mirror node monitoring component. We added a new cluster health check to the monitor that takes into account publishing status. The load balancer uses this health check to determine which cluster to route traffic to. The old health check endpoint didn't take into account whether transaction publishing was active or successful and so would not route traffic to the public mirror node during main node upgrades.

Besides the new health check, the monitor had fixes to its rate calculation at low TPS, not sampling when idle, node validation, and the alerts it generates. The mainnet network configuration of the monitor now points to the public mirror node and we've added the new previewnet node to the previewnet network configuration.

There were also a number of other fixes to clean up code and fix tests. We've made an effort to reduce our code smells as seen in [SonarCloud](https://sonarcloud.io/dashboard?id=hedera-mirror-node).

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

This release broadens our support for [non-fungible tokens](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) (NFTs) with new NFT-specific REST APIs. A new API was added to return a list of NFTs for a particular token ID. We also added a new API to return a single NFT by its token ID and serial number. Finally, we added an API to see the transaction history for a particular NFT. In an effort to have more manageable REST API code, we now adopt a more object-oriented approach by utilizing models, view-models and services. Below is an example of the three new APIs:

`GET /api/v1/tokens/0.0.1500/nfts`

```
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

```
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

```
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

This release is the first version of the Mirror Node with preliminary support for non-fungible tokens (NFTs). NFT support is being added to the Hedera nodes as outlined in [HIP 17](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md). We spent time [designing](https://github.com/hashgraph/hedera-mirror-node/blob/v0.36.0/docs/design/nft.md) how that NFT support will look like for the Mirror Node. Modifications to the schema were made to add new tables and fields and the Importer was updated to ingest NFT transactions. The existing REST APIs were updated to add NFT related fields to the response. This includes adding a `type` field to the token related APIs to indicate fungibility and a`nft_transfers` to `/api/v1/transactions/{id}`:

```
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

The Importer saw a large focus on improving performance and resiliency. It is now highly available (HA) when run inside Kubernetes. This allows more than one instance to run at a time and to failover to the secondary instance when the primary becomes unhealthy. A special Kubernetes ConfigMap named `leaders` is used to atomically elect the leader.

We’re improving our ingestion time dramatically for entity creation. Previously those were database finds followed by updates. Since inserts are always faster than find and updates, we’ve optimized this to insert the updates into a temporary table and at the end upsert those to the final table. A record file with 6,000 new entities went from 21 seconds to 600 ms, making it 35x improvement. Balance file processing was optimized to greatly reduce memory by only keeping one file in memory at a time.

### Breaking Changes

In honor of [Juneteenth](https://en.wikipedia.org/wiki/Juneteenth) and as part of the general industry-wide movement, we renamed our `master` branch to `main`. If you have a clone or fork of the Mirror Node Git repository, you will need to take the below steps to update it to use `main`:

```
git branch -m master main
git fetch origin
git branch -u origin/main main
git remote set-head origin -a
```

As part of our optimization to reduce memory usage, we now process some things earlier in the lifecycle. Due to this we had to rename some properties to reflect this change. We also changed the disk structure if you are using the `keepFiles` (now renamed to `writeFiles`) properties to write the stream files to disk after download. It is no longer archived into folders by day. Instead, the folder structure will exactly match the structure in the bucket. This opens the possibility for a mirror node to download and mirror the bucket itself using a S3 compatible API like [MinIO](https://min.io/). Below is a summary of the renamed properties:

- Renamed `hedera.mirror.importer.downloader.balance.keepSignatures` to `hedera.mirror.importer.downloader.balance.writeSignatures`
- Renamed `hedera.mirror.importer.parser.balance.keepFiles` to `hedera.mirror.importer.downloader.balance.writeFiles`
- Renamed `hedera.mirror.importer.parser.balance.persistBytes` to `hedera.mirror.importer.downloader.balance.persistBytes`
- Renamed `hedera.mirror.importer.downloader.event.keepSignatures` to `hedera.mirror.importer.downloader.event.writeSignatures`
- Renamed `hedera.mirror.importer.parser.event.keepFiles` to `hedera.mirror.importer.downloader.event.writeFiles`
- Renamed `hedera.mirror.importer.parser.event.persistBytes` to `hedera.mirror.importer.downloader.event.persistBytes`
- Renamed `hedera.mirror.importer.downloader.record.keepSignatures` to `hedera.mirror.importer.downloader.record.writeSignatures`
- Renamed `hedera.mirror.importer.parser.record.keepFiles` to `hedera.mirror.importer.downloader.record.writeFiles`
- Renamed `hedera.mirror.importer.parser.record.persistBytes` to `hedera.mirror.importer.downloader.record.persistBytes`

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

By default, the mirror node will validate that at least one-third of all nodes in the address book have signed a stream file before importing it into its database. This ensures that the main nodes have reached two-thirds consensus on the transactions in the file. For performance or verification reasons, you may want to decrease or increase this default percentage. To support this use case, we added a `hedera.mirror.importer.downloader.consensusRatio` property that controls the ratio of verified nodes (nodes used to come to consensus on the signature file hash) to the total number of nodes available.

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

A big focus of this release was on improving the Helm charts for use in production deployments. We now auto-generate passwords for components that require one and ensure they remain the same on upgrades by using Helm's [lookup](https://helm.sh/docs/chart\_template\_guide/functions\_and\_pipelines/#using-the-lookup-function) feature. We added `env`, `envFrom`, `volumes`, `volumeMounts` properties to all charts for more flexible configuration. We added a `global.image.tag` chart property to make it easier to test out custom versions. And we made it easier to use dependencies that can be outside the cluster like Redis and PostgreSQL.

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

This release also adds support for the newly announced account balance file format that was released in HAPI v0.12. The new [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/account\_balance\_file.proto)based format will eventually replace the CSV format in July 2021. Until then, both formats will exist simultaneously in the bucket so users can transition at their leisure. Besides being more efficient to parse, the new files are also compressed using Gzip for reduced storage and download costs. We also took the time to improve the balance file parsing performance regardless of format. Average parse times should decrease by about 27%.

For our REST API, we now expose an `entity_id` field on our transactions related APIs. This field represents the main entity associated with that transaction type. For example, if it was a HCS transaction it would be the topic ID created, updated, or deleted.

`GET /api/v1/transactions/0.0.1009-1234567890-999999998`

```
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

Historical entity information prior to OA is now available. In this release we've added a repeatable Java migration that will import entity information from a mainnet network snapshot. This runs during upgrade, is configureable (`hedera.mirror.importer.importHistoricalAccountInfo`) and works in combination with the `hedera.mirror.importer.startDate`setting.

The REST API now expands its filtering options support specifically around transfers and in relation to tokens. Previously the `account.id`and `credit/debit` filtering options supported HBAR transfers only, this release expands both filters to include tokens also.

The stateproof REST API and `check-state-proof` package have also been improved. The API now supports filtering for scheduled transactions via `/api/v1/transactions/:transactionId/stateproof?scheduled=true` as-well as a more compact response format. For record streams that utilize the newer improved HAPI v5 version the stateproof API response send back metadata hashes instead of the full raw bytes. With this, the response is more light weight.

```
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

The REST API now also supports repeatable `account.id` query parameters when filtering, with a configureable setting for the maximum number of repeated query parameters\
`/api/v1/(accounts|balances|transactions)?account.id=:id&account.id=:id2...`

`GET /api/v1/accounts?account.id=0.0.7&account.id=0.0.9`

```
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

This release also saw a step to support the new and improved v2 offerings of the (Java SDK)\[[https://github.com/hashgraph/hedera-sdk-java\\](https://github.com/hashgraph/hedera-sdk-java/)]. Both the monitor module and acceptance tests were updated to use the new SDK and utilize features such as in-built retry and support for scheduled transactions.

## [v0.28.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.28.2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: MARCH 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: MARCH 10, 2021**
{% endhint %}

This releases finalizes support for scheduled transactions and HAPI protobuf v0.12. Two new schedule specific REST APIs were added including `/api/v1/schedules` and `/api/v1/schedules/:id`. The former lists all schedules with various filtering options available and the latter returns a specific schedule by its schedule ID.

`GET /api/v1/schedules?account.id=0.0.1024&schedule.id=gte:4000&order=desc&limit=10`

```
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

We continued our progress on switching to TimescaleDB. We integrated a TimescaleDB helm chart into our Kubernetes deployment and added migration scripts to convert from PostgreSQL to TimescaleDB. We're still in the testing phase so it's still recommended to stick with the v1 schema (the default) for now.

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

We now have support for the [AWS Default Credential Provider Chain](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html). Now instead of only being able to provide static access and secret keys in the configuration, you can rely on the default provider chain to retrieve your credentials automatically from the environment (environment variables, `~/.aws/credentials`, etc). See our [documentation](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/configuration.md#connect-to-s3-with-the-default-credentials-provider) for more information.

We've enhanced our monitoring tools to provide greater observability into the mirror node's operation. In addition to publishing, our monitor tool now supports subscribing to the gRPC and REST APIs to verify end to end functionality of Hedera. It will also generate metrics off this information. We take advantage of Loki's new log alerting capability and now can alert off of any errors we see in logs that might be cause for concern.

## [v0.23.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.23.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: DECEMBER 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 20, 2020**
{% endhint %}

This release focuses on finalizing our support for the new Hedera Token Service (HTS) provided by the Hedera API [v0.9.0](https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0). There are no new HTS features, just various fixes to make it compatible with the latest protobuf. HTS is currently enabled in previewnet and should be enabled in testnet very soon, so please try it out and let us know if you have any feedback.

A new Helm [chart](https://github.com/hashgraph/hedera-mirror-node/tree/master/charts/hedera-mirror-monitor) was added to run the monitor application. The monitor is still under heavy development so stay tuned.

Most of the other changes were bug fixes. We now use SonarCloud to scan for vulnerabilities and bugs and have addressed all the major items. You can view our SonarCloud [dashboard](https://sonarcloud.io/dashboard?id=hedera-mirror-node) to track our progress. Entities are now only inserted for successful transactions and we fixed the wrong address book being updated. There were multiple issues with the state proof alpha API that were resolved. For the gRPC API, we improved its performance and lowered its CPU usage. Also related to gRPC, we now enable server sent keep alive messages and permit a lower client sent keep alive messages of 90 seconds, which should hopefully address timeout issues that some users have reported.

## [v0.22.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.22.0)

This release continues our improvements to our Kubernetes support as well as monitoring and performance improvements across the modules.

We improved our custom PrometheusRule alerts for the Importer, GRPC and REST API modules, as well as added dashboards for our gRPC and REST API modules. Additionally, we increased our pod resources limits to optimize Importer ingestion and gRPC streaming performance in a Kubernetes cluster. Our existing js based monitor and REST performance tests were both updated to include HTS support.

We also improved our data generator module with support for the majority of HAPI transactions the mirror node importer ingests. Additionally, we also added a java based monitor module that supports generation and publishing of transactions.

This release also includes an improvements to avoid the stale account info bug that stems from balance stream files being received at a slower frequency than record stream files. Now account creations and account info changes will be reflected in REST API call even though the updated balance may not have been received.\
We also extended our REST API support to include case insensitive support query parameters. `/api/v1/transactions?transactionType=tokentransfers` and `/api/v1/transactions?transactiontype=tokentransfers` are now both acceptable.

## [v0.21.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.21.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: NOVEMBER 24, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET UPDATE COMPLETED: NOVEMBER 13, 2020**
{% endhint %}

This release continues our focus on the Hedera Token Service (HTS) by adding three new token REST APIs. A token discovery REST API `/api/v1/tokens` shows available tokens on the network. A token REST API `/api/v1/tokens/${tokenId}` shows details for a token on the network. A token supply distribution REST API `/api/v1/tokens/${tokenId}/balances` shows token distribution across accounts. These APIs have already made their way to previewnet so check them out!

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

First up is support for the Hedera Token Service (HTS) that was recently [announced](https://hedera.com/blog/previewnet-hedera-token-service-hts-early-access) and rolled out to previewnet. A lot of work was put into supporting the new transaction types on the parser side including enhancing the schema with new tables and ingesting them via the record stream. HTS also required a new balance file version that adds token information to the CSV. Token information is now returned for our existing REST APIs while the next release will contain token specific REST APIs for further granularity. Check it out in previewnet and let us know if you have any feedback!

We made a lot of strides in improving the ingestion performance in previous releases, but since we also wanted to ensure low end to end HCS latency via our gRPC API we had to sacrifice some of that speed. As a result, we could only ingest at about 3,000 transactions per second (TPS) before latency spiked above 10 seconds. This was entirely due to our use of PostgreSQL notify/listen to notify the gRPC API of new data.

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

Importer can now be configured to connect to Amazon S3 using temporary security credentials via [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API\_AssumeRole.html). With this, a user that does not have permission to access an AWS resource can request a temporary role that will grant them that permission. See the [configuration documentation](https://github.com/hashgraph/hedera-mirror-node/blob/v0.18.0/docs/configuration.md#connect-to-s3-with-assumerole) for more information.

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

This release contains the port of a bug fix to better manage the `VertxException: Thread blocked` issue seen in [#945](https://github.com/hashgraph/hedera-mirror-node/issues/945)

## [v0.17.2](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.2)

A small bug fix to better support resetting the mirror node when a stream reset is performed on the network environment

## [v0.17.1](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.1)

A small fix to correct a performance regression with not properly caching a heavily used query.

## [v0.17.0](https://github.com/hashgraph/hedera-mirror-node/releases/tag/v0.17.0)

This release adds support for the storage of the network address books from file `0.0.101` and `0.0.102`in the mirror node database.\
The mirror node will now retrieve file address book contents which include node identifiers and their public keys from the database instead of the file system at startup.

This sets the stage for an additional feature which is the State Proof alpha REST API at `/transactions/${transactionId}/stateproof`.\
With this release it is possible to request the address book, record file and signature files that contain the contents of a transaction and allow for cryptographic verification of the transaction. Mirror node users can now actively verify submitted transactions for themselves.

Other changes include support for continuous deployment (CD) using [Github Actions](https://github.com/features/actions) that use [FluxCD](https://fluxcd.io/) to deploy master versions to a Kubernetes cluster. Additionally, this release includes fixes to the database copy operation optimization and improved handling of buffer size used when copying large topic messages.

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

The REST API to retrieve a topic message by its consensus timestamp now supports both a plural (`/topics/messages/:consensusTimestamp`) and singular (`/topic/message/:consensusTimestamp`) URI path. The singular format is deprecated and will be going away in the near future, so please update to the plural format soon.

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

```
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

- Release focused on stability and performance improvements.
- End to end test coverage.

This release was mainly focused on enhancing the stability and performance of the mirror node. We improved the transaction ingestion speed from 600 to about 4000 transactions per second. At the same time, we greatly improved the resiliency and performance of the GRPC module. We also added acceptance tests to test out HCS end to end.

**Breaking Change**

Please note that one potentially breaking change in this release is to reject subscriptions to topics that don't exist. This avoids the server having to poll repeatedly until it is created and taking up resources for a topic that may never exist. It is expected that clients or the [SDK](https://github.com/hashgraph/hedera-sdk-java/issues/367) will poll periodically after creating a topic until that topic makes its way to the mirror node. This functionality is hidden behind a feature flag but will slowly be rolled out over the next month.

## v0.5.3

- Now supports all HCS functionality including a streaming gRPC API for message topic subscription.
- Changed how the mirror node verifies mainnet consensus. Mirror node now waits for at least third of node signatures rather than greater than two thirds to verify consensus.
- Added new mainnet nodes to the mirror node address book.
- Access still restricted to a white listed set of IP addresses. Request access [here](https://learn.hedera.com/l/576593/2020-01-13/7z5jb).
- Please see the Mirror Node releases page for the full list of changes [here](https://github.com/hashgraph/hedera-mirror-node/releases).
- We occasionally may encounter a situation where an additional 15-20 second delay in message round trip time is experienced and subscriber connections are dropped. No messages are lost, and the consensus time is not affected. Clients are encouraged to reconnect. This issue will be fixed in a subsequent release of the Hedera mirror node. Some third-party mirror nodes should not be affected by this issue. We also don't expect it to impact the exchanges using the REST end point for the mirror node.
