---
description: Hedera 镜像节点发布笔记
---

# Hedera Mirror Node

对于每个网络支持的最新版本，请访问Hedera 状态 [page](https://status.hedera.com/)。

## 最新发布

## [v0.105.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.105.0)

为实现 [HIP-904](https://hips.hedera.com/HIP/hip-904.html)镜像节点上的 Friction-less Airdrops 添加了一个设计文件。 请观看 [epic](https://github.com/hashgraph/hedera-miror-node/issues/8081) 以监测空投开发的进展。

Citus——我们的狭窄数据库——继续取得进展，这个版本进入了我们的两个测试网集群之一。 我们将在一段时间内监测其部署情况，并作出任何必要的补救。 我们使用 Citus 的 ZFS CSI 驱动程序看到了升级。 最后，PostgreSQL已修复到Citus 迁移脚本的多重问题。

对于`/api/v1/contracts/call`，最大数据尺寸已增加到128 KiB，以提供更好的Ethereum 兼容性。 此外，还增加了更多的逻辑和验证，以便与协商一致的节点错误情景更加一致。

### 升级

如果您正在使用 ZFS CSI 驱动程序，请确保其CRD 更新后才能升级：

```
for crd in zfsbackups zfsnodes zfsrestores zfssnapshots zfsvolumes; do kubectl patch crd $crd.zfs.openebs.io --type merge -p '{"metadata": {"annotations": {"helm.sh/resource-policy": "keep", "meta.helm.sh/release-name": "mirror", "meta.helm.sh/release-namespace": "common"}, "labels": {"app.kubernetes.io/managed-by": "Helm"}}}'; done
```

## [v0.104.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.104.0)

这个版本在 REST API 中添加了一个 Redis 缓存，以提高`/api/v1/transactions` 的性能。 此功能目前被默认禁用，因为我们正在对其进行微调。

[HIP-857](https://hips.hedera.com/hip/hip-857) NFT 津贴 API 取得了很多进展。 比通常的 API 花费更长的时间，因为这是我们第一个基于 JavaScript 的REST API，需要一些额外的地面工作。 该版本默认允许用户在所有环境中测试NFT津贴 虽然大多数功能都存在，但是请注意有些部分仍在开发中。 在NFT津贴表中添加了一个新的指数，以加速Spender帐户的外观看。 为了提高其性能和更好地支持部分镜像节点，已删除数值实体ID的存在检查。 最后，我们添加了初始验收测试以验证 API 终点。

我们的 Citus 部署正接近终点线。 Citus 现在被部署到预览网，它现在同时运行PostgreSQL 和 Citus 的部署。 在内部，我们已经将它部署到一个具有全尺寸数据库的主机暂存环境，以供进一步测试。 之所以能够进行这种部署，是因为我们执行这次部署的移徙时间急剧增加。 Mainnet以前需要一个多月的时间进行迁移，但在这次释放后，它应该在大约一个星期内完成。 期望测试网也很快迁移到Citus。

## [v0.103.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.103.0)

这个版本会增加支持在 REST API中提供来自 [HIP-646](https://hips.hedera.com/hip/hip-646)、 [HIP-657](https://hips.hedera.com/hip/hip/hip-657)和 [HIP-765](https://hips.hedera.com/hip/hip-765) 的元数据信息。 尤其是，这将在 `/api/v1/tokens` 端添加一个 base64 编码的 `metadata` 字段。 It also adds `metadata` and `metadata_key` fields to the `/api/v1/tokens/{id}` endpoint.

合同调用 API 看到了一些明显的性能改进，安装了嵌套物品的延迟加载。 以前，它正在热切地加载所有帐户信息，即使是不需要数据的简单呼叫。 在切换到将这些额外查询放松之后，我们看到请求的输送量提高了50%至90%。 这种变化加上改进NFT计数查询的性能应导致API的更大性能和稳定性。

[HIP-857](https://hips.hedera.com/hip/hip-857) NFT 津贴 REST API。 这个版本将EVM地址和别名添加到新的终点并修复错误响应格式。

## [v0.102.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.102.0)

这是一个较小的 bug 修复版本，对一些飞行中的项目进行了递增改进。

For [HIP-857](https://hips.hedera.com/hip/hip-857), an alpha version of the NFT allowance REST API is now in place. 当我们努力实现剩余的查询参数并擦除任何错误时，它可以用于试验。 Jooq 库已并入rest-java模块，允许根据用户输入进行动态 SQL 查询。 下一个版本应该利用这个功能来充分实现API的其余部分。

我们的Citus的执行成功地部署到了业绩环境中，并且正在通过最初的基准。 初步结果表明，Citus在跨越多个节点隐藏数据的同时，提高了600毫秒的性能。 在 Citus 节点上启用了Promtail 以捕获数据库日志，并且在Grafana 上添加了一个新的 ZFS 仪表板。

## [v0.101.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.101.0)

This release adds support for storing the new mutable metadata information available in [HIP-646](https://hips.hedera.com/hip/hip-646), [HIP-657](https://hips.hedera.com/hip/hip-657), and [HIP-765](https://hips.hedera.com/hip/hip-765). 现在，它只是保存数据，并且在将来发布的版本中，我们会透过REST API泄露它。

"/api/v1/tokens" REST API 现在支持多个`token.id`参数。 这允许用户在单次通话中高效查询多个令牌。

"/api/v1/contracts/call" REST API 看到了这次发布的一些重大性能改进。 第一个变化是将Kubernetes节点池切换到提供专用资源分配的不同机器类。 端点也已从被动的MVC堆栈转换为同步的MVC堆栈。 同时，该模块启用了替换平台线程的新虚拟线程技术。 这些变化加在一起将请求吞吐量提高1-2x。

另一个重要的变化是允许在导入器中的下载线程和解析器线程之间进行分批处理。 现在，此功能已配置为像以前一样没有批量操作。 手动配置时，可以减少历史数据的同步时间至少12x。 今后，当导入器试图赶上时，我们会研究自动启用此功能的方式。

## [v0.100.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.100.0)

This release implements [HIP-859](https://hips.hedera.com/hip/hip-859), adding support for returning gas consumed in the contract result REST APIs. API中当前的`gasUsed`字段持有充电量， 新的 `gasConsumed` 字段持有EVM 执行期间实际使用的气体数量。 提供这种额外数据将使用户能够在援引合同时提供更准确的气体，并减少其收取的费用。

`/api/v1/contracts/call`现在支持可配置最大气体限制属性`hedera.mirror.web3.evm.maxGas`。 默认值仍然是1,500万，但运营商现在可以增加它来满足他们的需要。 EVM 版本和功能已升级到\`v0.46'。 这就为EVM执行提供了最新的协商一致节点软件的功能等值。

在改进我们与Citus的整合方面有大量工作要做。 加强了三种可重复的迁移以便最好地使用Citus：帐户平衡迁移、象征性平衡迁移和合成转移批准迁移。 通过移除代币表上的加入，优化了代币账户插入，以提高其性能。 清除了与实体相关的表格的范围分区，因为这种分区稀少导致性能下降。 最后，现在的部署支持为每个工人提供不同大小的磁盘，以便优化不平衡的数据。

## [v0.99.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.99.0)

This release contains the implementation of [HIP-873](https://hips.hedera.com/hip/hip-873) adding token decimals to the REST API. Previously users had to make `N + 1` queries to determine accurate token balance information by querying `/api/v1/accounts/{id}/tokens` once and `/api/v1/tokens/{id}` `N` times to get the relevant decimal information. This HIP adds `decimals` to both `/api/v1/tokens/{tokenId}/balances` and `/api/v1/accounts/{id}/tokens` so that decimal information is directly returned alongside the token relationships and the additional `N` queries are unnecessary. 它还在 `/api/v1/tokens` 响应中添加了 `name` 和 \`十进制' 字段，以暴露更多关于此 API 的现有令牌信息。

`/api/v1/contracts/call` REST API 现在支持一个可配置的 `hedera.mirror.web3.evm。 axDataSize`属性让镜像节点操作员能够调整他们想要支持的载荷的大小。 最大数据大小的默认值已从24KiB`增加到25Kib`以创建，并从6Kib`增加到25Kib`以进行调用。 这个更改可以让大型输入的视图功能，如 [oracles](https://hedera.com/blog/suthas-dora-price-feeds-now live-on-hedera) 现在可以在网络上工作。

有几个项目可以改进镜像节点的性能和安全。 一个新的 `hedera.mirror.importter.downloader.maxSize=50Mib` 属性控制了它将尝试下载的最大流文件大小。 这将保护镜像节点免受意外或恶意演员上传的大型文件的伤害。 导入器被重置以支持批量流文件摄取，从而可以在一次交易中处理多个流文件。 这将有助于为未来的强化铺平道路，如改善历史同步时间。

数据库中出现了一些改进，包括新的[设置文档](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/database/README.md#setup)，以及如何配置数据库的建议。 我们的Citus部署有一些显著的增添，包括通过调整其资源配置大大改进了业绩。 Stackgres版本升级到1.8版，ZFS升级到2.4.1。 该实体的股权计算是优化的 Citus ，因此它可以在一个破碎的数据库中有效运行。 最后，确定了数据库指标，以便按上级表名称适当汇总分区表。

## [v0.98.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.98.0)

这个版本实现了 [HIP-844](https://hips.hedera.com/hip/hip-844) 帐户更新后的处理和外部化改进。 这个HIP解决了协商一致的节点和镜像节点为非同步帐户的问题。 协商一致的节点现在向镜像节点发送最新帐户而不是镜像节点尝试根据其先前状态增加non。

对数据库作了两项重要的改动，大大减少了数据库的规模。 `topic_message`表的主要密钥索引被删除，以求依靠一个类似的索引到 `transaction`表。 这个简单的更改将800 GiB从主网数据库中分离出来。 隐藏奖励计算性能已经提高到只能写入选中领取奖励的账户。 这将分期奖励计算运行时间从47分钟减至2分钟以下。 移徙还消除了没有举行危险奖励选举的现有危险行数，使得这些表减少了155GiB。 请注意，如果要实现这些磁盘存储镜像节点操作员将需要手动在 `entity_stock` 和 `entity_stock_history` 表格上执行一个完整的真空。 所以镜像节点数据库的总大小减少了将近1个TB，这个版本！

在这次释放中支付了相当多的技术债务。 我们已经从导入器中删除了事件文件格式的支持。 此格式从未在镜像节点中完全实现，不支持最新版本， 而且用户在其存在的4年期间没有对这一数据表示兴趣。 接受测试被重新计入以使用OpenAPI生成的模型，确保我们对我们自己的 API 规格进行评价。 Brittle `MockPool`测试被移除，更容易保持测试覆盖。 REST API 测试现在使用了正确的只读用户和其他模块使用的普通数据库设置。 最后，未使用的 "RestoreClientIntegrationTest" 和相关的测试图像已被删除。

我们部署的Citus看到了一些改进。 通过减少散列表的碎片计数优化了哈希插入的性能。 实体上级因增加数据库的 CPU 资源数量而有所改善。 最后，账号终点的交易清单和账户显示，Citus的读取性能有所改善。

\
[v0.97.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.97.0)
--------------------------------------------------------------------------------------------------------------

本版本看到了对REST API的一些渐进改变。 REST API 现在支持一个 [RFC5988](https://www.rfc-editor.org/rfc/rfc5988) 响应标题中的兼容的`Link`，作为响应正文中的 `links.next`的替代。 这两种链接现在都可以用于分页，但是`Link`标题提供了一种标准方法，得到了更多工具的支持。 The `/api/v1/accounts/{id}?timestamp` endpoint now shows historically accurate staking information in its response so that users can view their past pending rewards. The timestamp in the response of the `/api/v1/tokens/{id}/balances` endpoint is now more accurate by reflecting the max balance timestamp of the tokens in its response.

头盔图表已核实与Kubernetes 1.28兼容，并且看到其依赖关系都已经过最后一次释放。 新的 rest-java 模块已从 WebFlux 转换为带虚拟线程的servlet。 一旦我们在今后的相对关系中执行857个净值救济金救济金救济金API，这将提高其可调整性。 一些内部将`BatchEntityListener`重整为`BatchPublisher`，将使历史同步和批量处理能够在未来得到改进。

`/api/v1/contracts/call`端点发现了许多重要的错误修复此次发布。 对历史区块的支持应该是完整的，一些剩余的 bug 应该被清除。 支持的操作 [documentation](https://github.com/hashgraph/hedera-miror-node/tree/main/docs/web3#supportedunsupported-operations) 已更新，以反映这种提高的兼容性。

## [v0.96.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.96.0)

有很多开发人员在节假日休息一段时间， 这个版本比正常版本稍小，但仍然包含一些重要的更改。 预览网和测试网引导地址簿已更新，以反映网络的当前状态。 Loki的默认体积从100GB增加到250GB，以致日志活动量增加。 处理`EtherumTransaction`的能力更强，所以如果遇到编码不当的交易，进口商就不会停止。 最后，REST API 中的内存泄漏应能大大减少内存错误并提高请求输送量。

To improve ingest performance of entity tables when used with a distributed SQL database we introduced a new `temporary` database [schema](https://www.postgresql.org/docs/current/ddl-schemas.html). 此schema用于存档文件处理实体时插入的临时数据。 以前，这一信息是在交易范围内制作的临时表格中添加的，但这些临时表格不能用Citus分发。 通过临时模式，这些信息现在可以适当地分配，以确保最佳性能。 这种更改确实需要手动DDL语句在下次升级之前进行(见下一节)。

### 打破更改

如前所述，采用了一个新的数据库方案，以处理可调取实体的处理工作。 This change\
doesn't require any manual steps for new operators that use one of our initialization scripts or helm charts to\
configure the database. 然而，升级到 0.96.0 或更高版本的现有操作者需要创建一个
配置并执行一个 [script](https://github.com/hashgraph/hedera-mirror-node/blob/v0.96.0/hedera-mirror-importter/src/main/resources/db/scripts/init-tem-schema.sh) _**之前**_ 升级之前。

```
PGHOST=127.0.0.1 ./init-temp-schema.h
```

另一个重大变化涉及使用我们的`hedera-miror-common`图表的操作者。 上面提到的 Loki 音量增加是在 Loki `StatefulfulSet` 上嵌入的 `PersistentVolumeClaim` 增加的。 Kubernetes不允许更改这个不可变的字段，因此需要手动删除 `StatefulSet` 周围的工作才能升级通用的图表。

## [v0.95.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.95.0)

这次发布使Java 组件升级为使用 [Java 21](https://www.oracle.com/news/announcement/ocw-oracle-releases-java-21-2023-09-19/)。 在未来的发布中，我们将探索21种新的语言功能，如虚拟线程，解锁额外的可扩展性。 处理了一些技术债务项目，包括通过创建一个共同的测试等级来删除多余的测试配置。 测试构造器上的明确`@Autowered`注释已被删除，降低锅炉配置。 末尾。 不同类被重新命名以符合我们的命名标准，包括从不同模块使用的类中移除`Mirror`前缀。

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM 归档节点历史区块有一些重要的添加，包括对历史区块的初始支持。 EVM 配置现在是基于方块编号加载的，而不是总是使用最新的EVM。 这可以确保`/api/v1/contracts/call`模拟执行，因为它当时将会出现在协商一致的节点上。 数据库查询已被修改以使用时间戳过滤器，以便返回历史块信息。

我们分配的数据库工作有了一些显著的改进，包括将Citus版本升级到12.1。 PostgreSQL 16 支持已测试确认与 PostgreSQL 和 Citus 的兼容性。 Both `/api/v1/topics/{id}/messages/{sequenceNumber}` and `/api/v1/topics/{id}/messages` saw optimizations implemented when used with Citus.

### 升级

如果您正在本地编译，请确保您已经升级到 Java 21，您的终端和 IDE。 对于MacOS，我们建议使用 [SDKMAN!](https://sdkman.io/) 来管理Java 版本，这样升级就像`sdk install java 21-tem`一样简单。 如果你正在使用自定义 `Dockerfile` ，它也会更新到 Java 21。 我们建议 [Eclipse Temurin](https://hub.docker.com/\_/eclipse-temurin) 作为我们的 Java 组件的基础图像。

## [v0.94.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.94.1)

提供一个重要的解决方法来解决因平衡重复工作而倒退的待定奖励计算问题。 数据库迁移大约需要17分钟的主机。

## [v0.94.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.94.0)

这个版本主要是一个错误修复版本以及一些次要的技术债务项目。 为新的 REST Java 模块添加了一个新的头盔图表，预计未来的工作将只支持在 Java 中的新API，而不是当前基于 JavaScript 的方法。 取消了对Elasticsearch methrics 导出的支持，以利于完全依靠Prometheus。 "/api/v1/contracts/call" API 有一些值得注意的错误修复和性能改进。 最后，有些技术债务是通过重新计算`SqlEntityListener'来解决的，使用一个新的`ParserContext\`，可以减轻它的维护负担。

## [v0.93.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.93.0)

这种发行会避免平衡历史的重复，导致数据库大小大量缩减，平衡颗粒没有损失。 主网数据库减少45%，从50TB减少到28TB！ 如果帐户自上次快照以来没有发生余额变化，这个重复过程可以不更新余额历史。 转移到重复的历史余额在后台异步，与主网相比，大约花了24个小时才能完成。 因为索引被更改，从`(timestamp, account_id)` 到 `(conction_id, 时间戳)`, 这需要付出大量努力在多个REST API中重新处理查询。 此外，余额表现已分成若干部分，这意味着改变我们的数据库指标，以便在其母姓名上适当汇总儿童表。

HIP-584继续与多个错误修复和优化。 将每个请求对象更改为单一字母，导致内存和 CPU 使用率大幅降低，从而可以处理更多的并行请求。 Web3k6测试被绑定到我们的自动性能测试中，以确保每次测试都能运行，以确保不出现倒退。 最后，由于为处理非最新的区块而安装了更多的管道，因此对历史区块的支持取得了进展。

在监视器中添加了一个新的集群数据库健康检查，以提供多集群部署中的适当故障。 本地文件流提供商现在允许按日期分组输入文件，以便在目录包含数百万文件时更快地处理。 这是朝着更快的历史同步模式迈出的一步。 最后，REST API 查询优化了 Citus 的部署，以便他们能够与 PostgreSQL 达成对等。

## [v0.92.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.92.0)

[HIP-584](https://hips.hedera.com/hip/hip-584) Mirror EVM 档案节点在这个版本中看到了进一步的改进。 通过使大部分类无国籍并酌情利用`ThreadLocal`来优化内存使用。 仍在继续努力使`/api/v1/contracts/call`支持历史区块，但对历史合同状态、帐户的支持程度较低，并且增加了合同信息。 采用了估计气体的额外试验覆盖率，以比较气体估计值接近通过HAPI使用的实际气体。 最后，一个 `blockhash` 操作返回 `0x0` 的问题已经解决。

我们为Citus添加了重复账户和代币余额数据的选项，这将大大减少平衡表的规模。 余额表目前消耗50%的数据库。 在下一次发布中，我们将把这种能力带到定期的“PostgreSQL”，以实现这些节约。 我们现在不再更新像Mint这样的供应更改操作的令牌历史记录， 从而减少本表中的数据量。

## [v0.91.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.91.0)

此版本为使用 [Spring Expression Language] (https://docs.spring.io/spring-frameworke/reference/core/expressions.html) 的广告临时交易过滤器添加支持。 SpEL过滤器可以同时用于包括或排除交易被存入数据库。 上一个过滤 [capability](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/configuration. d#交易和实体过滤允许镜像节点操作者包含或排除某些实体 ID或交易类型， 但如果他们需要更多的风俗，他们就不运气。 SpEL本身很强大，允许访问课堂上的任何蜜蜂或班级， 这样可以减少攻击表面，我们将其限制为只允许在 [TransactionBody](https://github) 过滤。 om/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_body.proto)和 [TransactionRecord](https://github.com/hashgraph/hedera-sdk-java/blob/develop/sdk/src/main/proto/transaction\_record.proto) 包含在记录文件中。 更多细节和示例请参阅 [docs](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/configuration.md#spring-expression-language-support)。 下面是一个仅包含有某些备忘录的交易的例子：

```
hedera.mirror.importter.parser.include[0].expression=transactionBody.memo.contains("hedera")
```

通过新增的表尺寸和磁盘上索引的尺寸，监测改进了这种版本，以帮助跟踪数据库日益扩大的规模。 同样，实施了新的缓存计量，以监测缓存撞击速度和规模。 更新了仪表板以可视化这些新的计数。

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM 归档节点支持`block.prebandao` 实现。 此外，由于Hedera的区块总是最终的，所以增加了对`待定`、`安全`和`最终完成`区块类型'的支持，所有这三个区块都等同于`最新'。 已经开始对`/api/v1/contracts/call\`的历史支持，以便能够查询过去的特定区块。 这次发布实现了较低级别的 token 余额查询。

[HIP-794](https://hips.hedera.com/hip/hip-794) Sunset account balance 看到一些剩余的项目已完成。 V0.89.0中添加的余额时间戳被用来为帐户提供更准确的时间戳和余额REST API。 最后，调节任务被禁用，因为从平衡数据进行调节而镜像节点生成自己是没有意义的。

## [v0.90.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.90.0)

这个版本主要侧重于测试和错误修复。 接受测试有了一些改进，包括通过在多次测试中缓存合同创立，从而降低了总成本。 添加了接受测试以验证HIP-786隐藏属性在网络股份REST API中的支持。 一个汇率在不同环境中不同的问题是通过查询汇率REST API并用它来计算HAPI的费用来解决的。 导入器有一个选项可以在可回收的错误上失败，无法在生命周期早期发现记录流问题。

## [v0.89.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.89.0)

[HIP-786](https://hips.hedera.com/hip/hip-786)增加了支持丰富的元数据导出到记录流，供下游系统使用。 镜像节点现在正在生成新的 `max_stak_rewared`, `max_total_rewarded`, `reserved_staking_rewarded`, `rewide-balanc_theghold`, 和 `unreserved_staking_reward_balanc` 字段，并将它们保存在数据库中。 REST API 已被更新，以透过`/api/v1/network/stak`显示此数据。

[HIP-794](https://hips.hedera.com/hip/hip-794) 太阳平衡文件在这个版本中看到了进一步的改进。 镜像节点现在抓取了协商一致的时间戳，在这个时间戳中，余额被更新为帐户和代币关系。 这种信息今后将用于提供更准确的 API 平衡时间戳，并重复平衡信息。 Rosetta API启用了实体平衡跟踪和迁移。 最后，我们现在追踪所有实体类型的余额，而不仅仅是账户和合同。

[HIP-584](https://hips.hedera.com/hip/hip-584) 镜像EVM 归档节点继续改进，增加了对 PRNG 系统合同的支持。 伊斯坦布尔释放的缺失的别斯内部预编资料现在已经得到适当登记。 以集成、接受和性能测试的形式增加了许多新的测试。

这次发行中涉及一些技术性债务项目。 导入器组件的CPU和内存使用率明显提高，达到每秒10 000笔交易。 现在它使用的内存约减少了50%，CPU减少了33%。 Log4j2日志框架被替换为返回，以提供一个编译本地代码和简化配置的路径。 随着一个缓存的增加，`EntityId`最终得到改善，以减少临时不可变对象的分配。 测试已经标准化，以使用简单的记录框架“OutputCaptureExtension”。 最后，我们研究了并行纳入交易的办法，并看到了一条向前迈进的道路，可以进一步扩大交易范围。

## [v0.88.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.88.0)

此版本包含支持 [HIP-794](https://hips.hedera.com/hip/hip-794) 太阳设置账户平衡文件。 共识节点很快将停止每15分钟生成账户余额文件，因为越来越多的账户使这一业务无法持续。 为了填补空白，镜像节点现在将从记录流生成平衡快照信息。 此更改对终端用户和操作者都是透明的，因为各种API将返回相同的数据。 现在，我们正在生成合成的`account_balanc_file`行(不是文件)，直到我们能够在任何地方消除对此表的依赖。 在这个版本中，我们更新了帐户的 ID、余额和网络供应REST API以免依赖于此表。 实体股权计算和可互换代币迁移也作了类似更新。 下一次发布将在这一领域开展进一步工作。

[HIP-584](https://hips.hedera.com/hip/hip-584) 已实现了汇率预编译`tinycentsToTinybars`和`tinybars`。 还添加了对HTS `redirectForToken`预编译的支持。 但主要的焦点是测试和错误修复，在这个版本中大量它们被冲洗。

`0.88'中涉及的技术债务数额很大。 对于启动器，我们有一个新的 `hedera-miror-rest-java` 模块，它旨在包含从 JavaScript 转换成的新的 REST API。 通过在 Java 中创建任何新的 REST API，并慢慢地将现有的 API转换为 Java ，我们可以提高代码库的这个区域的质量，并促进代码与其他模块的重新使用。 一个社区成员帮助我们移除已废弃的Spring Cloud Kubernetes属性`spring.cloud.kubernetes.enabled`，因为它已不再使用。 我们还花了时间去除未使用的 Flyway占位符属性，并取消web3 验收测试中的代码冗余。 最后，我们从代码库中广泛使用的`EntityId`中删除了`type`字段，并删除了不必要的`AssessedCustomFeeWrapper\`。

## [v0.87](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.87..0)

这个版本总结了倡议，以确保我们抓住Hedera实体的所有变化。 存储库中最老的工单之一可追溯到2019年已完成，最后将 [FreezeTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/freeze.proto) 详细信息持续到数据库。 现在有一个新的选项来存储原始的 [TransactionRecord](https://github.com/hashgraph/hedera-protobufs/blob/main/services/transaction\_record.proto) 默认关闭原始字节。 自定义收费表被分成单独的主要表格和历史表格，以便与其他数据保持一致并提高查询效率。

在支持实时津贴跟踪后，添加了异步数据库迁移，以有效更新每个账户的加密允许金额。 此外，新的加密和可替代的验收测试核实实时津贴跟踪工作是否正常。 最后，我们现在重新运行有条件的迁移，这种迁移在历史上只能在初步启动时进行。 对于像平衡初始化这样的迁移，这意味着我们在摄入第一个余额文件后自动纠正帐户和代币关系余额。 对于其他迁移，它意味着它们会自动根据特定的记录文件版本而触发。

REST API 有一些显著变化。 We now show only active allowances in the `/api/v1/accounts/{id}/allowances/crypto` and `/api/v1/accounts/{id}/allowances/tokens` REST APIs, providing consistency with how consensus nodes return this data. `/api/v1/network/stak` API 看到了通过将其变成奖励外加没有奖励来计算其利益值的方式发生了变化。

[HIP-584](https://hips.hedera.com/hip/hip/hip-584) EVM 档案节点看到一些`HederaTokenService`的预编实现，包括`allowance`、`getApproved`、`isApprovedForAll` `updateTokenExpiryInfo`、`updateTokenInfo`和`updateTokenKeys`。 由于将重点放在测试上，综合性和接受性测试的范围得到了扩大。 额外的覆盖率导致发现和挤压了一些bug，提高了`/api/v1/contracts/call`的可靠性。

许多工作也涉及事情的行动方面。 许多计量仪表板和Grafana仪表板都进行了清理，并改进了生产监测方面的援助。 所有图表依赖项都看到了版本错误和配置调整以使其更新。 经确认，Kubernetes 1.27兼容性是一个部署目标，同时仍然确保与以前的Kubernetes 版本兼容。 压缩的 ZFS 音量支持现在正确处理 Kubernetes 升级。 我们的 Citus 部署使用了 PostgreSQL 15 升级到 Citus 12。 这个版本带来了我们上游对Citus`create_time_partitions`的改进，以便它能够支持我们用来存储协商一致的时间戳的`bigint`类型。 这使我们能够移除`pg_partman`扩展，以利于本机`create_time_partitions`。 ”pg_cron”扩展也被移除，以利于在导入器上运行基于 JavaScript 的预定服务。

## [v0.86](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.86.0)

我们在抓取所有 [实体更改](https://github.com/hashgraph/hedera-miror-node/issues/610)目标方面取得了一定进展。 这个版本添加了请求很高的功能，用于跟踪剩余的哈巴和可替换的代币允许，并通过REST API上的`amount`字段显示它。 重要的是，这种追踪只适用于新的或更新的津贴。 现有津贴将在下次发放时适当调整剩余津贴。

传统上，镜像节点只存储了交易记录中的总汇传输。 此外，我们还默认从交易主体的项目传输，并将其嵌入`transaction`表中。 对于部分镜像节点，我们现在在平衡初始化期间创建实体。 这意味着，即使镜像节点现在启动，这个迁移可以重新运行以创建每个帐户并以准确的平衡信息签约。 在下次发布中，我们会在处理第一个账户余额文件时自动重新运行此迁移。

最后，我们添加了一个 `entity_transaction` 合并表格，开始跟踪与交易相关的所有实体。 这将使我们能够提供更好的交易搜索体验，并找到与一个实体相关的所有交易。 这个功能在这个版本中被禁用，因为我们在它上进行反转以使它能够正常运行。

[EIP-2930](https://eips.etherum.org/EIPS/eip-2930)中指定的以太坊交易类型1的支持现在是可用的。 以前只支持遗留的和类型2Etherum的交易。 新的 EIP-2930 交易可以通过一个 [EthereumTransaction](https://github.com/hashgraph/hedera-protobufs/blob/main/services/eyseum\_transaction.proto)或通过 [JSON RPC Relay](https://swirldlllabs.com/hashhashio/) 直接发送至 HAPI 。

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM 档案节点实现了大量预编译。 2. 包括下列各项：

- `核准`
- `approviveNFT`
- `associateToken`
- `associate-Tokens`
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

## [v0.85](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.85.0)

使用最近的 [testnet reset](https://docs.hedera)。 2023-07-27年发生的 om/hedera/networks/testnet#test-network-resets)，创建了一个新的云存储桶，用于存储由共识节点生成的流文件。 此版本将在网络设置为测试网时默认使用更新的bucket名称。

在这个版本中专门为JSON-RPC 继电器进行了几次优化，其他用户也可能认为是有用的。 `/api/v1/accounts/idOrAddress` REST API 现在支持一个可选的 `transactions` 查询参数，当设置为 `false` 时，它将省略嵌套交易列表。 它默认为`true`以匹配以前的行为。 如果您没有使用此 API 的交易，请考虑将其设置为 `false` 以减少返回的数据数量，并为您的查询提供更好的响应时间。 Additionally, the `/api/v1/contracts/results` REST API was updated to include more fields to match the detailed results returned from `/api/v1/contracts/results/{id}`.

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) EVM 归档节点正在以稳定的速度添加功能。 这个版本为上海EVM添加了一个新的 `PUSH0` opcode 的 Ethereum 上海EVM 版本提供支持。 估计未能签订合同的天然气通话超过6KB。 重点主要放在实施各种预编，其中包括：批准补贴、烧伤、创建、删除补贴、脱离关系、授予知识中心、 撤销知识中心和擦除。

在抓取所有状态变化方面取得进展，我们现在保留一个账户的日常实体利益攸关的历史。 In the future, this information will be used to provide an accurate staked amount when looking up an account's historical information using `/api/v1/accounts/{id}?timestamp=`.

还有许多其他错误修复和小的改进。 完整列表请参阅下面的发行注释。

## [v0.84](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.84.0)

This release contains support for [HIP-729](https://hips.hedera.com/hip/hip-729) contract nonce externalization. 共识节点现在正在跟踪记录流中的信息和外部化的合同。 合同一旦订立另一项合同，则无须增加。 Mirror nodes persist this data and expose a contract's nonce on the `/api/v1/contracts` and `/api/v1/contracts/{id}` REST APIs.

[HIP-584](https://hips.hedera.com/hip/hip-584) EVM 归档节点继续取得进展。 这个版本包含了对预编译和非预编译功能的合同状态读取的完全支持。 增加了对非预编译功能的合同状态修改支持，但创建延迟账户除外。

Our [goal](https://github.com/hashgraph/hedera-mirror-node/issues/6110) of capturing all entity changes saw further refinements. 令牌信息看到了一个历史表，以跟踪随着时间的推移对令牌元数据的任何变化。 此外，我们现在使用代币余额中的实时代币余额REST API。

## [v0.83](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.83.0)

{% hint style="success" %}
**MAINNET更新：7月7日，2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 29, 2023**
{% endhint %}

在这个版本中，我们做了强烈请求的更改，以显示交易列表REST API中的 NFT 传输列表。 Originally, only the `/api/v1/transactions/{id}` showed the list of `nft_transfers` due to performance concerns with joining on another large table for such a heavily used and heavyweight API. 在保持执行状态时显示此信息 我们不得不将NFT传输信息归类为JSONB列，以避免它在"交易" 表下被嵌入。 这避免了额外的加入，允许我们用现有的查询返回给定的信息。

镜像节点的重点是随着时间的推移跟踪Hedera实体的所有可能变化。 为此目的，创建了一个 NFT 历史表，以捕捉一段时间内对NFT 的所有可能更改。 除了坚持此数据外，我们还会透过API暴露更多的历史信息。 Now when the `timestamp` parameter is supplied on the `/api/v1/accounts/{id}` endpoint it will show the historical view of that account. 之前, 参数将仅用于显示给定时间的 `transactions` 列表。 预计在下次发布时将会在历史实体信息方面得到进一步改进。

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) 继续在每次发行中取得长足进展。 该版本的重点是改进对`/api/v1/contracts/call`的预编译支持。 现在支持CREATE2 opcode 以及非静态合同状态为预编和非预编译功能。 对非预编译函数(不包括修建迷幻账户)的非静态合同的修改也在进行之中。 最后，验收测试覆盖率大大提高，并解决了一些漏洞。

此版本添加了与 [Stackgres Operator] (https://stackgres.io/) 的集成以提供一个非常可用的 [Citus](https://www.citusdata.com/) 部署。 Stackgres是PostgreSQL的一个固定的Kubernetes运营商，他们对Citus扩展的支持使得能够在不依赖昂贵的情况下随时部署生产变得很容易。 云端管理数据库服务。 加上我们在上次发布中添加的 ZFS 音量压缩，将大大降低运行镜像节点的总成本，同时提供水平缩放。

### 升级

由于这个版本中的 NFT 大量迁移，预期升级到大约一小时的时间。 一如既往，建议先在预览环境中完成升级(例如) a 红色/黑色部署，以便在开放顾客交通之前进行部署核查和减少停机。

## [v0.82](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.82.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 22, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 8, 2023**
{% endhint %}

[HIP-679](https://hips.hedera.com/HIP/hip-679.html) 看到它在这个版本中完成了初始工作，以支持一个经过改组的桶。 导入器现在支持基于现有帐户的 ID 的 bucket 路径，以及未来节点基于 ID 的bucket 路径。 它还添加了路径类型属性，可以在两种格式之间自动切换，因此两种格式之间的转换对于镜像节点操作员是透明的。 现在，默认路径类型将作为帐户 ID，直到节点ID成为现实以降低S3成本。

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) Mirror EVM 档案节点看到了大量改进，以使其更接近于与共识节点的对等。 已将堆积状态和数据库存取器集成，以便智能合约暂时改变状态。 添加了一个操作追踪器，以便在环境中调试智能合约。

最后，增加了一个主题信息查询表，以优化在分布式数据库中找到主题信息。

## [v0.81](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.81.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 1, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：24, 2023**
{% endhint %}

这次发布伴随着一些改进以支持JSON-RPC 继电器。 我们现在支持别名和 EVM 地址查找余额端的 `account.id` 参数。 We optimized the transaction nonce filtering in `/api/v1/contracts/{id}/results` by denormalizing the data. Finally, an issue with empty `function_parameters` in `/api/v1/contracts/results/{id}` response was addressed.

The other big item we worked on was [support](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database/zfs.md) for volume level compression with Citus. 我们知道，镜像节点存储的时间序列数据类型将很容易压缩，我们想利用这种数据来降低增加的存储成本和提高读/写性能。 PostgreSQL 支持列一级的基本压缩形式，名为 [TOAST](https://www.postgresql.org/docs/current/storage-toast.html)，但它只对非常大的列生效。 Citus 在使用他们的 columnar 存储访问方法时有压缩，但我们发现它对我们的需要太慢。 既然我们知道我们不能使用 SaaS 服务，我们可以更多地控制数据库的部署。 因此，我们决定试用Kubernetes体积压缩。 通过创建自定义 Kubernetes 节点集合专用于 Citus ，我们可以通过 [zfs-localpv](https://en.wikipedia.org/wiki/ZFS) 安装 [ZFS](https://github)。 om/openebs/zfs-localpv，启用 [Zstandard](http://facebook.github.io/zstd/) 压缩数据库的内置卷。 结果是**3.6x压缩比，性能损失为零**。 从长远来看，这意味着目前17TB的主网数据库规模可减至4.7TB。

其他改进领域包括改进灾后恢复工作的文件。 这包括备份还原镜像节点的 [runbook](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/runbook/restore.md)。 还有一个 format@@0 (https://github.com/hashgraph/hedera-miror-node/blob/main/docs/runbook/stream-verification.md) 关于如何进行本地流文件验证的 [runbook](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/runbook/stream-verification.md)。 验收试验以前已经纳入自动部署过程，但执行时间很长，主要是因为使用Gradle在运行时下载依赖物。 我们已经装入验收测试，因此依赖关系会在构建时下载，运行时间减少3-4x。

## [v0.80](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.80.0)

{% hint style="success" %}
**MAINNET更新：17, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：至少11, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html)继续工作，此版本第一次支持非静态合同状态读取非预编译函数。 请查看 Swagger UI [table](https://github.com/hashgraph/hedera-miror-node/pull/5949/files) 来详细说明在什么版本中支持哪些功能。 从服务代码中复制了更多的估计气体功能，以便在估计方面取得进展。 添加了一个新的堆叠状态帧功能来支持合同写入和缓存的读。

[Spotless](https://github.com/diffplug/spotless/tree/main) 代码格式化工具以实现一致。 增加了一个 git commit 钩子，以确保任何新的更改保持一致，开发者可以集中注意什么重要。

最后，还有大量错误修复和性能改进。 详情见下文。

## [v0.79](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.79..0)

{% hint style="success" %}
**MAINNET更新：至少9, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：可能2，2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584. EVM 归档节点看到了进一步的进展，重点是在下一次发布时测试和建立估计气体功能的基础。 虽然协商一致的节点正在进行调整工作，从而能够在道路上带来红利。 归档节点需要在该进程完成之前估计气体的功能。 为了在HIP-584上取得进展，已暂时从共识节点复制必要的EVM逻辑到镜像节点web3模块。 在很大程度上，重点放在增加预编合同的接受性测试范围上。

写入 dApps 的用户想要能够监视令牌审批和传输事件。 HAPI 交易，如`CryptoTransfer`, `CryptoApproveveAllance`, `CryptoDeleteAllance`, `TokenMint`, `TokenWipe`, 和`TokenBurn`，都不会产生可以通过监视工具捕获的事件，例如[Graph](https://thegraph)。 因为他们是在EVM之外执行的。 为了解决这个问题，镜像节点现在生成这些非 EVM HAPI 交易的合成合同日志事件。

A new subscription API was [designed](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/design/contract-log-subscription.md) for [HIP-668 GraphQL API](https://github.com/hashgraph/hedera-improvement-proposal/pull/668). 一旦在将来发布，新的合同日志订阅将会通过 WebSocket 连接将合同事件流到客户端。

对于我们的 Citus 数据库转换，PostgreSQL 15 兼容性已被验证并成为此 v2 架构的默认值。 通过 `/api/v1/balances ?timestamp=` 查找历史平衡信息被优化为粉碎数据库，以便保持业绩。 业绩测试显示，碎片计数的减少能够大大提高业绩，因此我们将碎片从32个减少到16个。 这次测试还使我们能够为Citus 部署提供初始的 [recommended](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#database-migration-from-v1-to v2) 资源配置。

这次释放的重点是改进测试。 除了上述HIP-584测试覆盖面外，还包括以下方面： 我们还优化了验收测试，将Kubernetes的总试验时间减少一半，但不降低覆盖率。 验收测试日志已清理，以减少不必要的记录报表并使其输出标准化。 测试使用的hbar 余额在测试执行结束时记录。 增加了对空账户创建的接受测试。 我们现在从 "main" 分支生成多平台快照图像来测试本地节点。 [Testkube](https://testkube.io/) 配置得到增强，使其更易配置。 最后，所有 Java 测试编译器的警告都已修复，如果将来出现任何警告，将会使构建失败。

### 已知问题

有一个 [#5776](https://github.com/hashgraph/hedera-miror-node/issues/5944) 介绍的 [bug](https://github.com/hashgraph/hedera-miror-node/pull/5776) 导致导入者在启动时失败。 建议暂停升级到 v.79.0 直到我们可以在 v.79.1 中解决这个问题。 或者，它可以通过设置 `hedera.mirror.importter.migration.syntheticTokenallowanceOwnerMigration.enabled=false` 来禁用错误的迁移来解决。

## [v0.78](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.78..0)

{% hint style="success" %}
**MAINNET更新：APRIL 21, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 13, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) Mirror Evm 档案节点现在有代码预编译支持。 这是`/api/v1/contracts/call`被视为`eth_call`等效的最后一项主要功能。 新的 API 已被添加到REST API的OpenAPI 文档中，这样它就会出现在我们的 [Swagger UI](https://mainnet-public.mirrornode.hedera.com/api/v1/docs)。 目前正在进行一些性能优化，以使其可以调整，并改进各种测试，以核实其正确性。 解决了各种错误，包括正确处理反弹。 在今后几个版本中，我们计划调整合同呼叫并实施合同气体估算。

这次释放的主要重点是性能和复原力。 在性能方面，我们已经优化了列表的schedules REST API 以便可以在 Citus 上缩放。 头盔测试完成后，性能测试现在可以通过TestKube自动触发。 这些k6性能测试得到加强，以自动选择适合环境的配置值。 交易哈希表已被分割，并且进行了最聪明的流程以同时插入哈希。 这种变化大大加快了插入可选交易哈希的时间。 同样，在控制哪些交易类型应导致哈希插入时增加了一个备选方案。

在复原力方面。 导入器组件被分析到可能导致记录文件处理停止的任何代码路径，因为来自共识节点的错误输入。 任何这样的代码都是为了处理错误、记录/通知并移动到下一个交易。 这种更改使镜像节点的摄取更有弹性，并转向偏好可获得性而不是正确性。 部分镜像节点可能因地址簿不完整而被卡住，现在可以继续使用新的 "协商一致模式" 属性和逻辑。 部分镜像节点现在也能够有更正帐户和代币余额，即使该实体缺少删除的标记。 末尾。 我们已经完成了一项长期的调整工作，将所有交易的特定逻辑移到单独的交易处理程序，并且在过程中修复了一些漏洞。

在这个版本中修复了一些值得注意的重要bug。 为了纠正不准确的可互换令牌的总供应量，已经建立了一个修复办法。 此外，已删除代币的 NFTs 不再在REST API中显示。

## [v0.77](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.7.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: APRIL 4, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 29, 2023**
{% endhint %}

这个版本修复了NFT余额的跟踪。 从历史上看，它们来自协商一致节点每15分钟发送的平衡文件。 当我们开始跟踪可互换的实时代币余额并离开使用此平衡文件时，我们遗憾地打破了NFT余额的计算。 我们不仅解决了这个问题，而且继续努力，并花时间追踪最新的净捐助国平衡。

The `/api/v1/contracts/{id}/state` REST API shows the current state of a contract's slot values. 用户要求能够在过去的任意时刻查询其合同的钥匙/价值对。 要解决这个问题，我们现在会暴露一个 `timestamp` 查询参数，它将获得历史合同状态。 这使JSON-RPC 继电器能够提供一个适当的 `eth_getStorageAt` 并支持历史区块。

[HIP-584](https://hips.hedera.com/HIP/hip-584.html)继续取得进展。 相当多的bug已被冲压，包括处理还原和移植恢复原因和原始数据。 性能测试被添加到 k6 来加载带有令牌预编译的测试合同通话。

## [v0.76](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.76.0)

{% hint style="success" %}
**MAINNET更新：MARCH 23, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 13, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html)中规定的新的 `/api/v1/contracts/call` REST API已经准备就绪，可以开始生产。 这个版本为限制API率添加了支持，初始值为每次100个请求。 在气体中每秒计增加标记，以表明请求是否为调用， a 估计或导致提高可观察性的错误。 还处理了各种错误修复。

[HIP-668](https://github.com/hashgraph/hedera-reform-proposal/pull/668) GraphQL API 已添加到我们的部署中，并为此API添加了一个新的头盔图。 这将允许在所有环境中初始使用API，但有一项理解，即它仍然是非常透明的并且可能有变化。

我们在我们的Citus一体化方面取得了许多进展。 Citus 升级到11.2, 显示出一些查询模式的性能提升15-20%。 我们优化了合同结果 API 性能，根据合同ID 而不是付款人账户进行分配。 通过在 Citus 上的散列来搜索交易，通过在查询中添加分布列并将结果限制在时间戳范围内来改进。 通过其身份证件寻找账户也改进了Citus。

### 打破更改

头盔图中有一些令人了解的突然变化。 `hedera-mirror` 包装器图表默认允许新的 `hedera-miror-graphql` 子图表。 GraphQL 部署需要一个新的 `mirror_graphql` 数据库用户，才能成功启动。 您可以通过登录数据库创建用户作为所有者并执行以下SQL查询：

```
使用登录密码“密码”创建用户镜像；
```

如果您正在使用嵌入的 PostgreSQL 子图表(我们不建议生产) 然后你必须在升级之前删除它的StatefulSet, 因为它的图表版本有一个大的跳板。

\`hedera-miror-common'图将其所有组件升级到新的主要版本，包括断裂更改。 如果您正在使用此图表，在升级前运行以下命令：

```
kubectl delete mirror-promeus-node-exporter
kubectl delete daemsteen miror-traefik
kubectl delete statefulset miror-loki
kubectl delete ingresroute mirror-traefik-dashboard
kubectl apply --server-side --force-conflicts=true -f https://raw. ithubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagerconfigs.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw. ithubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_alertmanagers.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubuscontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_podmonitors.yaml
kubectl apply --serverside --force-conflicts=true -f https://raw.githubusercontent. om/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_probes.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_promeuses.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/ /example/prometheus-operator-crd/monitoring.coreos.com_promettheusrules.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator-crd/monitoring.coreos.com_servicemonitors.yaml
kubectl apply --server-side --force-conflicts=true -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/v0.63.0/example/prometheus-operator
```

## [v0.75](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.7.0)

{% hint style="success" %}
**MAINNET更新：MARCH 2, 2023**
{% endhint %}

[HIP-584](https://hips.hedera.com/HIP/hip-584.html) 继续工作，使它更接近生产，可以进行简单的合同调用。 将缓存逻辑添加到仓库层以优化其能力，同时进行性能测试以核实这些改进。 增加了一个度量来跟踪每秒使用的气体以及其他各种错误修复。

用于观测我们生产系统的内部监视器API和仪表板已经封装。 此外，它已被纳入头盔图，并被用作头盔测试的一部分，以确保对部署情况进行核查。

最后，作为我们Citus努力的一部分，进行了一些查询优化。

## [v0.74](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.74.0

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 18, 2023**
{% endhint %}

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 14, 2023**
{% endhint %}

这个版本将测试网桶切换到于2023年1月26日为[testnet reset](https://docs.hedera.com/hedera/networks/testnet#test-network-reset)创建的新的。 它还更新地址簿，以反映自上次重置以来添加到测试网上的其他节点。 如果您正在运行测试网镜像节点，请查看[reset instructions](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset)来帮助您更新节点。

在 [HIP-668](https://github.com/hashgraph/hedera-reform-proposal/pull/668)，我们提议添加一个新的镜像节点GraphQL API，并非常感谢您的反馈。 在这份版本中添加了一个新的GraphQL模块，它包含一个简单的账户查询查询查询查询，以便为今后关于这个HIP的工作提供基础。 在下次发布中，我们将添加此模块的自动部署到所有环境。 它被认为是一个 alpha API，在任何时候都会发生破坏性变化，所以它不会被推荐依赖于生产用途。 在其URL中使用 `/graphql/alpha`来表明这一点。

最后，为Citus 执行了一些查询优化，同时确保它不会导致现有数据库出现回归。 这将继续是今后几次释放的重点。

## [**v0.73**](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.73.0)

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 10, 2023**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: FEBRUARY 3, 2022**
{% endhint %}

这个版本是第一个支持 [HIP-584](https://hips.hedera.com/Hip-584.html) EVM 归档节点。 HIP-584允许镜像节点作为免费执行智能合约只读EVM。 这一新特征被认为是透明的，仍有许多工作有待执行，如支持预编订合同、气体估计等。 此功能需要将镜像节点配置为最佳可选的可追踪侧边ecar文件并需要生成这些文件的网络。 目前只有预览网已启用合同可追踪性。

testnet bucket 名称已更新到新的 bucket 名称，在其最近的季度 [reset](https://docs.hedera.com/hedera/testnet#test-network-resets)。 同样，bootstrap 测试网地址簿已更新，以反映自上次重置以来添加的额外测试网节点。 正在运行测试网节点的镜像节点操作员应该手动填充新的 bucket 名或更新到这个发布。

其余工作的目标是显著的测试改进和错误修复。 我们的性能测试已扩大到所有终点，以便在生命周期较早时捕获问题。 增加了验收试验的范围以及若干修正办法。 CI 的稳定性有了很大改善，重点放在修复易燃性测试。 [Sonar](https://sonarcloud.io/project/overview.id=hedera-miror-node)报告的代码冶炼量仅减少到几个，在下次发行中将整个过程减少到零。 最后，我们的合并工作使我们能够通过 [TestKube](https://testkube.io/)在整合中进行夜间性能测试和主网中的存档环境。

## [**v0.72**](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.72.0)

{% hint style="success" %}
**MAINNET更新：JANUARY 25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: JANUARY 18, 2022**
{% endhint %}

这次释放是一次较小的释放，因为大部分视察队在节假日休假。 尽管如此，我们还是设法实现 [HIP-583](https://hips.hedera.com/Hip-583.html) 在CryptoCreate & CryptoTransfer Transactions中扩展别名支持。 我们现在允许空洞的帐户在其全部创建后最后确定为一项合同。

我们还努力添加对 [Testkube](https://testkube.io/)的支持。 Testkube允许我们在Kubernetes环境中通过根据各种条件触发测试实现测试自动化。 具体而言，它将用于在一个主网中运行每晚性能回归测试，以确保我们的 API 性能不会出现倒退。 我们将在将来的版本中继续扩展这个自动化测试。

在这次发布中还有一些错误修复。 主要侧重于在最后一次释放中从Maven切换到Gradle后修复我们的释放过程。

## [v0.71](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.71.0)

{% hint style="success" %}
**MAINNET更新：JANUARY 19, 2023**
{% endhint %}

在发布时，REST API 中的所有帐户和代币余额将反映他们的实时平衡信息。 历史上，镜像节点每15分钟依靠协商一致节点上传的余额文件来获取平衡信息。 我们一直在努力实现这一里程碑，因为许多版本逐渐向更多的实体和更多的API进行实时平衡跟踪。 这个版本完成了这次迁移，添加了实时代币余额到帐户和余额REST API。

The mirror node now implements support for [HIP-583](https://hips.hedera.com/HIP/hip-583.html) alias on `CryptoCreate` transactions. 这样，客户可以在账户创建过程中直接设置别名，而不是在转账过程中依赖隐含的自动账户创建。 镜像节点尊重这个明确的别名以及`CryptoCreate`或`TransactionRecord`中的新的显式EVM地址。 这避免了镜像节点上的 brittle EVM 地址计算，这在过去给我们造成了一些麻烦。

这个版本完成了从Maven迁移到Gradle的建造过程。 新建筑已经投入大量工作，以改善其在当地和持续一体化方面的业绩和稳定性。 GitHub 行动工作流已从每个模块的一个工作流合并到单个Gradle 构建工作流，并有一个矩阵策略，同时运行每个模块和数据库方案。 这大大简化了工作流配置，使其更容易维护和调试.

我们继续在我们的Citus探索方面取得进展。 Citus 的 v2 schema 现在执行基于时间戳的数据分区，并通过pg\_cron 实现此过程的自动化。 Citus 特定环境已经创建，我们目前正在根据其规模进行性能测试，以核实它是否符合我们的要求。

这个版本增加了自动化以使我们的 GCP 市场应用程序跟上每次发布的更新。 虽然由于Marketplace版本提交的手工性质，并非完全自动， 现在任何新的生产标签都会触发市场图像的生成和验证。

这是一个很大的版本，还有许多其他的改进和修复。 见下面的完整版本说明。

## [v0.70](https://github.com/hashgraph/hedera-miror-node/releases/tag/v70.0.0)

{% hint style="success" %}
**MAINNET更新：十二月 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 14, 2022**
{% endhint %}

作为 [HIP-406]的一部分(https://hips.hedera.com/HiP/hip-406.html)，镜像节点正在添加一个新的帐户标记奖励REST API。 此 API 将显示一段时间内支付给帐户的分期奖励。 The mirror node now also shows staking reward transfers in the transaction REST APIs (e.g. `/api/v1/transactions`, `/api/v1/transactions/{id}`, and the list of transactions in `/api/v1/accounts/{id}`). 这可能有助于显示在分期结束后涉及您账户的交易触发了延迟奖励付款。

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

REST API 在评分之外有了进一步改进。 帐户REST API现在显示一个计算过期时间戳来镜像HAPI `CryptoGetInfo`查询。 之前到期的时间戳仅在通过支持它的交易明确发送时显示(主要是更新交易)。 现在，如果它是 `null` ，我们将它计算为 `created_timestamp.seconds + auto_recondition_period` 。 每个合同结果终点都已更新，以包括创建的合同的EVM地址的地址栏目。

此版本使得能够执行在 [HIP-584](https://hips.hedera.com/Hip-584.html)中概述的镜像节点上的合同调用。 许多基础正在布置，将在即将发布的版本中进一步完善。\\

## [v0.69](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.69.0)

{% hint style="success" %}
**MAINNET更新：DEEMBER 5, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 29, 2022**
{% endhint %}

正如在前几次版本中指出的那样， [HIP-367](https://hips.hedera.com/hip/hip-367)正在废弃从 HAPI 查询返回的令牌关系信息。 在这个版本中，其镜像节点替换现已完成。 我们现在跟踪并显示当前代币关系 API 中的可互换令牌平衡，而不是依赖从共识节点导出的15分钟平衡。 在未来的版本中，帐户和余额REST API将被更新，以显示当前可替换的代币余额。

导入组件现在支持本地文件流提供商。 这允许它从本地目录读取流文件，而不仅仅是它以前支持的 S3 兼容的提供者。 这种模式有助于调试从频带收到的流文件，或有助于降低本地节点设置的复杂性和延迟性。 若要尝试，请设置 `hedera.mirror.importter.downloader.cloudProvider=LOCAL` 并将`hedera.mirror.importter.dataPath`/`streams`文件夹与云桶相同的文件结构。

我们现在在合同日志REST API中显示了合同的`CREATE2` EVM地址。 以前，我们将把Hedera `shard.realm.num`转换为20字节EVM地址，但这并不总是反映合同的真实EVM地址。 使用EVM地址的 `CREATE2` 形式可以提高Etherum兼容性。

我们继续在将我们的建设进程转化为Gradle方面取得进展。 这个版本添加了一个 Golang Gradle 插件来下载 Go SDK 并用它来构建和测试Rosetta 模块。

## [v0.68](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.68.0)

{% hint style="success" %}
**MAINNET Upeted: NOVEMBER 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: NOVEMBER 18, 2022**
{% endhint %}

除了通常的错误修复外，这次发布侧重于一些内部增强功能，以便为一些即将到来的功能奠定基础。 我们现在追踪和保持数据库中当前可互换的代币平衡。 此信息尚未透露在任何 API 上，但将会呈现到令牌关系。 近期内的帐户和余额REST API。

我们正在继续努力实现 [CitusDB](https://www.citusdata.com/) 作为可能在这个版本中通过添加发行列和修复我们的 v2 schema 测试来替换的数据库。

最后，我们提供了最初的 Gradle 支持，以改善构建时间并提供更好的开发者体验。 初始测试显示构建和测试时间从总体的8分钟减少到2分钟。 Gradle和Maven的建筑脚本将同时维持几次发布，直到我们能够确保Gradle建筑实现与Maven的地物平等。

## [v0.67](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.67.0)

{% hint style="success" %}
**MAINNET更新：NOVEMBER 10, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 10, 2022**
{% endhint %}

[HIP-367](https://hips.hedera.com/hip/hip-367) 废弃了通过 HAPI 返回的帐户的代币余额列表。 镜像节点一直致力于替换它的几个版本由 [storing](https://github.com/hashgraph/hedera-mirror-node/issues/4030) 的经常账户余额、 [combining](https://github)。 om/hashgraph/hedera-miror-node/issues/4150) 合同和实体表，并[添加历史表](https://github.com/hashgraph/hedera-mirror-node/issues/3251) for `token_account`. 这项工作为新的代币关系 REST API 铺平了道路，该关系将列出与特定帐户相关联的所有可替换和不可替代的代币。 此 API 也返回一些元数据，例如余额、KYC 状态、冻结状态以及它是否是自动关联。 目前退回的可替代代币余额来自15分钟账户余额文件。 我们是 [actively](https://github.com/hashgraph/hedera-miror-node/issues/4402) 致力于跟踪实时可互换的代币平衡，并将更新它以在未来版本中反映这一点。

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

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) 详细说明如何通过sidecar文件提供来自协商一致节点的智能合同可追踪信息。 合同状态的变化通过镜像节点持续，并在合同结果 API上提供。 然而，仍然存在着这种情况。 这些状态变化并不总是反映智能合同存储值的完整清单，因为在任何特定的合同使用过程中，并不是所有槽位都被修改。 我们现在坚持对状态变化的滚动视图，以跟踪最新的时段键/值对。 This contract state information is now exposed via a new `/api/v1/contracts/{id}/state` REST API where `id` is either the `shard.realm.num`, `realm.num`, `num`, or a hex encoded EVM address of the smart contract.

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

为了推动进一步分散，我们现在在达成共识后随机化节点，用于下载数据文件。 以前，我们使用的数据结构一般导致我们使用经核实的清单中返回的第一个节点，通常是\`0.0.3'。 我们现在随机选择一个节点，直到我们能够成功下载流文件。 我们还内部更改了所有使用节点帐户 ID 来代替其节点ID 的表格。

在测试方面，我们加强了各种测试和监测工具，以增加对新API的支持。 我们还添加了一个接受测试启动探测器，以推迟测试的启动，直到整个网络健全为止。 这就避免了镜像节点接受测试，当共识上的长时间迁移或启动过程或镜像节点造成延误时报告错误的正数。

## [v0.66](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.66.0)

{% hint style="success" %}
**MAINNET更新：OCTOBER 24, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：OCTOBER 17, 2022**
{% endhint %}

继续我们的目标是在各地保持最新的平衡，这个版本现在显示了REST API余额中当前的hbar 平衡。 如果您提供了一个“时间戳”参数，它将回退到之前的行为并使用15分钟的平衡文件。 这使我们能够继续提供您一段时间内平衡的历史视图。 同时显示最新的平衡，如果没有要求具体的时间范围。 正在为即将发布的版本积极进行可互换的代币平衡。

此版本的另一个重要功能是支持云存储失败。 现在可以使用多个S3下载源配置导入器，并且每个导入器都会重复，直到成功。 这使镜像节点更加分散，能够在云端失灵时提供更大的复原力。 现有的 `hedera.mirror.importter.downloader` 属性用于配置 `cloudProvider`，`accessKey`，`secretKey`，等等。 将继续支持并作为源列表中的第一个条目插入 但建议将您的配置迁移到更新的格式。 同样在下载器中，我们将下载器批量增加到100，以提高历史同步速度。 已添加一个 `hedera.mirror.importter.downloader.sources.connectionTimeout` 属性，以避免偶尔的连接错误。

```
hedera:
   镜像:
    导入器:
      downloader:
        sources:
        - backoff: 30s
          connectionTimeout: 10s
          凭据:
            accessKey: <redacted>
            secretKey: <redacted>
          maxConforesty: 50
          projectId: myapp
          region: us-east-2
          type: GCP
          uri: https://storage。 oogleapis. om
        - 凭据：
            accessKey： <redacted>
            secretKey： <redacted>
          类型：S3
```

[HIP-573](https://hips.hedera.com/hip/hip-573) 给予令牌创建者免除他们所有的令牌收藏者的自定义手续费。 This mirror node release adds support to persist this new `all_collectors_are_exempt` HAPI field and expose it via the `/api/v1/tokens/{id}` REST API.

镜像节点的一个重要特点是，任何人都可以运行一个并只存储他们所关心的数据。 镜像节点支持这种功能已有几年了，但配置语法有点棘手，才能准确。 为了解决这个缺陷，我们在配置文件中添加了一些 [examples](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/configuration.md#transaction-entity-filtering) 以澄清事项。 这个实体过滤器历来仅限于创建、更新和删除实体上的操作。 我们现在已经扩大了此过滤范围，将付款人帐户ID和转账中涉及的帐户或代币包括在内。

### 打破更改

作为S3工作失败的一部分。 我们对现有的属性作了一些更改，以简化事项，并且只支持所有类型流的一个属性。 `hedera.mirror.importter.downloader.(balance|event|record).batchSize`属性被移除，为单个通用`hedera.mirror.importter.downloader.batchSize`。 同样，`hedera.mirror.importter.downloader.(balance|event|record).threads` 属性已经被移除为`hedera.mirror.importter.downloader.threads`。 `hedera.mirror.importter.downloader.(balance|event|record).prefix` 属性被移除为硬编码配置，因为从来没有必要调整这些属性。 如果您正在使用任何这些属性，请相应调整您的配置。

如果您通过启用 `writeFiles` 或 `writeSignatures` 属性在下载后将流文件写入磁盘， 另外还有一个令人注意到的重大变化。 作为我们从节点帐户ID移除的一部分，我们改变了磁盘上的路径以使用节点ID。 如果您想避免为同一个节点设置两个目录，请手动重命名您的本地目录。 For example, change `${dataDir}/recordstreams/record0.0.3` to `${dataDir}/recordstreams/record0`.

## [v0.65](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.65.0)

{% hint style="success" %}
**MAINNET更新：OCTOBER 11, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：OCTOBER 6, 2022**
{% endhint %}

镜像节点现在使用 [HIP-406](https://hips.hedera.com/HIP/hip-406.html)概述的所有节点的分量计算共识。 如果尚未激活staking， 它回溯到以前的行为，即将每个节点计为`1/N`重量，即`N`是网络上运行的协商一致节点的数量。 The `/api/v1/accounts` and `/api/v1/accounts/{id}`REST APIs now expose a `pending_reward` field that provides an estimate of the staking reward payout in tinybars as of the last staking period. "/api/v1/network/supply" REST API更新了其配置的未发布供应账户列表，以准确反映Hedera为预订目的而完成的账户分离。

这个版本实现了详细在 [HIP-513](https://hips.hedera.com/hip/hip-513)中包含的合同操作 REST API。 一个动作有效载荷的示例如下。 我们将`transaction_hash`, `transaction_index`, `block_hash` 和 `block_number` 字段添加到合同日志 REST API。 完成这项工作是为了优化继电器使用的 eth_getLogs\` JSON RPC 方法的性能。 同样为了中继，我们现在支持通过其32字节交易哈希查找非Ethereum 合同结果。

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

为了支持镜像节点探索器能够显示创建实体的交易，我们添加了一个 "created_timestamp" 字段到账户 REST API。

Rosetta模块看到了这次发布的大量改进。 Rosetta现在支持在其元数据响应中返回交易备忘录。 为了提高大区块的性能，Rosetta现在限制其区块响应中的交易次数。 由于Rosetta Docker进行了自己的调节过程，进口商的平衡调节工作在Rosetta Docker图像中被禁用。 和解进程平衡还有其他各种解决办法。 图表测试Forced reposs 失败，并在旋转中以散列查询来修复一个缓慢的搜索块。

## [v0.64](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.64.0)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 26 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 12, 2022**
{% endhint %}

最后一次发布， 我们开始跟踪每个帐户和合同的当前余额，而不是仅仅依靠每15分钟通过协商一致节点编写的余额文件。 In this release, we now show this up-to-date balance on the `/api/v1/accounts` and `/api/v1/accounts/{id}` REST APIs in the existing `balance` field. 代币余额和余额REST API 仍然显示15分钟余额文件中的余额信息。 在将来的一次发布中，我们会看到改变它们来跟踪当前的平衡。

[HIP-406](https://hips.hedera) om/hip/hip-406， 它详细说明了一个待处理的奖励计算方法，可以用来估计您上次的付款活动和刚结束的分期结束之间的奖励付款。 镜像节点现在每天进行类似的计算，并将在将来发布时在REST API上显示这个待处理的奖励金额。

[reconciliation](https://github.com/hashgraph/hedera-miror-node/tree/main/docs/importter#conceration-job) 任务定期运行并调节平衡文件与记录文件中发生的加密传输。 这个作业使我们能够找到一个 [issue](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#recordsing-for-fail\_invalid-nft-transfers) 缺少的交易交易，这些交易是在 hedera service `v0.27.7` 中固定的。 此版本包含缺失交易的错误，允许重新成功进行对账。 它还看到了性能的改善，包括一个“延迟”属性到减缓它的速度并增加了工作状态的持续性，所以它不会从每次开始就重新启动。 新的`remedationStrategy`属性'提供了一种机制，在多个调试错误时可以继续操作。

## [v0.63](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.63.0)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 7 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 2, 2022**
{% endhint %}

这个版本添加了一个请求很高的功能：镜像节点现在追踪当前帐户的余额。 以前，镜像节点将存储平衡信息，这些信息源是协商一致节点每15分钟生成和上传的平衡文件。 因此，进行中账户的余额信息总是滞后15分钟。 我们能够找到一种方法在这个版本中用SQL来跟踪这个信息。 The next release will actually expose this up to date account balance information on both `/api/v1/accounts` or `/api/v1/accounts/{id}`. 在将来的版本中，当没有提供时间戳参数时，将查看将实时余额添加到“/api/v1/balances”并跟踪最新的代币余额。

关于[HIP-513合同追踪](https://hips.hedera.com/HIP/hip-513.html)的工作仍在继续，本版本增加了一些重要项目。 共识节点将在首次激活侧面机制时发送迁移记录，包括所有智能合约运行时字节代码和当前存储值。 镜像节点现在支持接收这些特殊迁移侧面并使用迁移数据更新其数据库。 这将为镜像节点提供执行智能合约所需的信息而无需修改未来版本状态铺平道路。 同样在这个版本中，我们现在显示了用于在合同中新的 "failed_initcode" 字段创建智能合同的合同内代码，结果是REST API。

网络供货REST API 已更新，用于调整未释放供货的未释放供货账户。 这种更改是必要的，因为Hedera调整了财务帐户以便与标记一起使用。

## [v0.62](https://github.com/hashgraph/hedera-miror-node/releases?page=2)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 29, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 22, 2022**
{% endhint %}

Mirror Node 0.62 saw [HIP-406](https://hips.hedera.com/HIP/hip-406.html) staking related improvements to its REST API and partial support for HIP-513 contract traceability.

`/api/v1/network/nodes` 现在将使用地址簿中的存档作为回退，因为它没有在网络上看到任何`NodeStakeUpdate`的交易。 这个版本还包含一个新的网络密钥REST API `/api/v1/network/stak`，用以显示所有节点共有的总密钥信息：

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

[HIP-513](https://hips.hedera.com/HIP/hip-513.html) Smart Contract Traceability 增加了对可选侧边栏的支持，以包含合同可追踪性信息。 在这个版本中，镜像节点支持下载和坚持合同状态更改、合同initcode、合同运行时字节代码和合同行动(AKA轨迹)。 The `/api/v1/contracts/{id}` REST API now shows the runtime bytecode for newly created contracts. 下一个版本将支持双面迁移，它将包含合同状态更改和所有现有合同的字节代码。

[HIP-435](https://hips.hedera.com/HIP/hip-435.html) 记录流V6需要更改状态验证REST API，以便在启用V6时不要中断。 这次发布更新了 API 以支持新的 v6 格式的记录文件。

Rosetta API 看到了一些小修补和改进。 它现在在Rosetta服务器的任何地方都使用Hedera网络别名。 它还解决了Rosetta不支持别名作为加密转账地址的问题。 此外，Rosetta `sub_network_identifier` 因不需要而被禁用。

这次减免的技术债务的改善令人吃惊。 REST API 和监视器 API 都从CommonJS 转换为 ES6 模块， 允许我们最终将我们的一些依赖关系升级到最新版本。 REST API spec测试是按端点进入文件夹的组织，并且更改为整个套装使用单个数据库容器。 关于进口商，可变合同信息已并入`entity`表。 `RecordItem`的构造函数在任何地方都被移除，以利于它的生成器方法。 最后，我们添加了解析器性能测试，以便能够生成大型记录文件和压力测试记录文件。

### 打破更改

在最近的一次发布中，我们将 `stak_total` 字段添加到 `/api/v1/network/nodes` API中，以显示网络的总值。 由于添加了新的 `/api/v1/network/stak` API，我们现在有一个单独的 API 来返回与网络相关联的总收藏信息。 因此，我们在这个版本中决定从 `/api/v1/network/nodes` API响应中移除`stak_total`字段以保持一致性。 如果你正在使用这个字段，请更新你的代码来使用 `/api/v1/network/stak` API中的 `stak_total` 字段。

## [v0.61](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.61.0)

{% hint style="success" %}
**MAINNET更新：AUGUST 2, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：27, 2022**
{% endhint %}

此版本添加了 [HIP-513]的初始支持 (https://hips.hedera.com/hip/hip-513) 智能合同追踪扩展。 合同可追踪信息现在可在单独上载到云存储的可选侧边栏文件中获得。 镜像节点操作员可以选择是否通过设置导入器的`hedera.mirror.importter.parser.record.sidecar`属性来下载此额外信息。 默认情况下，侧边栏文件不会被下载。 启用它将允许镜像节点保存合同状态、动作和字节码数据。 HIP-513支持在这个版本中是不完整的，下一个版本将使所有类型的支持都能够持续存在。

交易REST API 现在支持多个“交易类型”查询过滤器，以简化跨类型搜索。

[GCP Marketplace](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node?project=mirrornode-non-prod-314918)中的镜像节点版本已更新到 v0.60.0。 这需要迁移到新的 GCP 生产者门户网站，这将有助于简化未来版本更新。

监视器组件在启动时添加了一个选项来检索地址簿。 这就避免了必须配置节点列表以便在生产前的环境中手动监测，并确保节点列表是最新的。 监视器现在使用 OpenAPI 生成模型来捕捉我们的 OpenAPI 方案。 我们还为监视器添加了一个选项来设置发布交易的最大备忘录长度属性。

## [v0.60](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.60.0)

{% hint style="success" %}
**MAINNET更新：18，2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：14, 2022**
{% endhint %}

这次发布的两个主要特点是支持数据保留期和HIP-351假定数字生成工作。

在公共网络上，镜像节点每天可以生成数十千兆字节的数据，这个速度预计只会增加。 镜像节点现在支持默认禁用的可选数据[保留期](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#保留) 如果启用，保留任务清除历史数据超过配置的时间。 通过减少数据库中的数据总量，它将减少业务费用并改善阅读/书写性能。 只删除与余额或交易数据关联的只插入数据。 不删除账户、合同等累积实体信息。

[HIP-351](https://hips.hedera.com/hip/hip-351) 添加了一个 pseudorandom 数字生成器交易。 镜像节点现在仍然保持此 "PrngTransaction" 类型，包括伪随机编号或它生成的字节。 未来发布将会在REST API上暴露此信息。

在这次释放中还有其他各种改进。 区块编号现在被迁移到与其他镜像节点一致，不管它们配置的起始日期，当它从协商一致的节点收到具有标准区块号的第一个v6记录文件时。 我们在触发期开始时将奖励率添加到节点REST API。 Rosetta 现在显示费用加密转账操作类型为 `FEEE` 。 Rosetta 还在Rosetta DATAAPI响应中显示账户别名作为账户地址。

## [v0.59](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.59.0)

{% hint style="success" %}
**TESTNET更新：JUNE 29, 2022**
{% endhint %}

上次版本支持 [HIP-406](https://hips.hedera.com/HIP/hip-406.html) staking数据。 StockeUpdateTransaction`protobuf 中的变化使得该版本的持久性进一步微调，以适应“NodeStakeUpdateTransaction” 的变化。 The`decline_reward`, `staked_account_id`, `staked_node_id`fields were added to`/api/v1/accounts`and`/api/v1/accounts/{id}`to show account-level staking properties. 我们还为现有的`/api/v1/network/nodes\` REST API 添加了相关字段(见下面的例子)。

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

在这个版本中添加了对 [HIP-435](https://hips.hedera.com/HIP/hip-435.html) 定义的新记录文件v6 格式的支持。 录制文件v6增加了块编号以及支持新的侧边区记录文件，这些文件载有详细的合同可追踪信息，镜像节点可选择下载。 记录和签名文件现在已经采用了更易维护的原始格式，这将使它们更易于在今后使用新的字段而无需改动即可改进。 而且，v6记录文件现在将被压缩，这将转化为减少网络和储存费用，同时有可能提高性能。 一旦v6在将来的套期保值服务释放时启用 镜像节点操作员需要更新到支持新的 v6 格式的版本，以避免故障。

Rosetta看到了这种释放的一些改进，以便使其更好地与Rosetta的规定保持一致。 一个可配置的有效持续时间秒选项被添加到交易生成API以支持自定义此值。 支持一个连贯一致的块编号，不管`startDate`都被添加到Rosetta中，Hedera 现在有一个定义为 [HIP-415]的一致块(https://hips.hedera.com/HIP/hip-415.html)。 通过 API 返回别名地址时添加了一个 `0x` 前缀，表示数据是十六进制编码。

## [v0.58](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.58.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JUNE 22, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 16, 2022**
{% endhint %}

这个版本包含了对HIP-406邮票、 HIP-410包装以太空交易、 HIP-482 JSON-RPC 继电器以及早该升级到 Java 17的支持。

[HIP-406 Staking](https://hips.hedera.com/HIP/hip-406.html)即将来临，镜像节点准备就绪。 这个版本我们添加了持久性支持以存储数据信息。 在下一次发布中，我们将通过我们的 API 来披露这个信息。

[HIP-410](https://hips.hedera.com/HIP/hip-410.html) 和 [HIP-482](https://hips.hedera.com/HIP/hip-482.html) 都是为了改进现有Etherum 开发者的开启。 为此，我们增加了对两个合同日志REST API的分页支持。 您现在可以通过日志通过协商一致的时间戳和日志索引参数的组合。 新的块REST API还看到添加了新的 `gas_used` 和 `logs_bloom` 字段，这些字段显示了块内所有交易的总值。 最后，我们添加了一个新的网络收费计划REST API。 目前，它只暴露了`ContractCall`, `ContractCreate`和`EtherumTransaction`类型的微量元素'的气体价格。

`GET /api/v1/network/fees`

```
Power
  "fees": [
    Power
      "gas": 35561,
      "transaction_type": "ContractCall"
    },
    len
      "gas": 481934,
      "transaction_type": "ContractCreate"
    },
    Power
      "gas": 35561,
      "transaction_type": "EtherumTransaction"
    }
  ],
  "timestamp": "1633392000. 873575662"
}
```

自从镜像节点于2019年开始以来，它使用Java 11来构建和运行，因为这是最近一次发布的LTS。 在Java 17 LTS于2021年9月获释之后，我们知道我们想升级了。 通过这个版本，我们在验证镜像节点仍然可以运行和运行后升级到17。 如果你正在使用我们的官方容器图像，他们也在 Java 17上，因此除了更新图像外，没有必要的迁移。 如果你在容器外运行，你需要将你的 JRE 升级到17，或者从源头重建"-Djava.version=11"。

## [v0.57](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.57..0)

{% hint style="success" %}
**MAINNET更新：25, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：25, 2022**
{% endhint %}

此版本侧重于添加在 [HIP-482]定义的 JSON RPC 继电器 (https://hips.hedera.com/hip/hip-482) 所需的数据和API。 JSON-RPC 继电器实现Etherum JSON-RPC [standard](https://playground.open-rpc.org/?schemaUrl=https://raw.githubusercontent.com/eferum/eth1.0-apis/assembed-spec/openrpc.json)，并将[HIP-410 Etherum交易](https://hips.hedera.com/HIP/hip-410)传递给协商一致的节点。 由于区块的概念对于JSON-RPC API至关重要，这个版本还包含了[HIP-415 Introduction of Blocks](https://hips.hedera.com/hip/hip-415)的实现。

镜像节点现在暴露了在 [HIP-415](https://hips.hedera.com/hip/hip-415#miror-nodes)中引入的方块概念。 我们现在计算并存储所使用的累积气体和合同日志博客过滤器作为一个整体块。 这个HIP定义了三个新的REST API，这个版本包括所有三个：一个列表块REST API。 一个获得方块REST API和列表合约结果 REST API。 新的 `/api/v1/blocks` API 支持通常的 `limit` 和 `order` 查询参数，以及`timestamp` 和 `block'。 umber` 分别支持平等和范围操作者获得协商一致的时间戳和阻止数字。 The `/api/v1/blocks/{hashOrNumber}` is identical to the list blocks but only returns a single block by either its block hash or its block number. Finally, a `/api/v1/contracts/results` REST API was added that is identical to the existing `/api/v1/contracts/{id}/results` but able to search across contracts.

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

为了支持[HIP-410 Etherum Transactions](https://hips.hedera.com/hip/hip-410#mirror-node)，做了一些修改。 The `/api/v1/accounts/{idOrAlias}` REST API was updated to accept an EVM address as a path parameter in lieu of an ID or alias. An `ethereum_nonce` and `evm_address` was added to the response of `/api/v1/accounts/{idOrAliasOrAddress}` and `/api/v1/accounts`. The existing `/api/v1/contracts/results/{transactionId}` was updated to accept the 32 byte Ethereum transaction hash as a path parameter in addition to the transaction ID that it supports now. Its response, as well as the similar `/api/v1/contracts/{idOrAddress}/results/{timestamp}`, was updated to add the following new Ethereum transaction fields:

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

_Note: existed field for brevity._

添加了一个新汇率REST API `/api/v1/network/exchangerate`，返回存储在`0.0.112`中的[汇率](https://github.com/hashgraph/hedera-protobufs/blob/main/services/exchange\_rate.proto)网络文件。 它支持一个 `timestamp` 参数，以便在过去的某个时候检索汇率。

```
Mr.
  "current_rate": Power
    "cent_equivalent": 596987
    "expiration_time": 1649689200
    "hbar_equivalent": 30000
  },
  "next _rate": 许诺
    "cent_equivalent": 594920
    "expiration_time": 1649692800
    "hbar_equivalent": 30000
  },
  "timestamp": "1649689200. 23456789"
}
```

A new `/api/v1/contracts/results/logs` API was added with the same query parameters and response as `/api/v1/contracts/{address}/results/logs` but with the ability to search across contracts. 它不支持作为查询参数的地址，因为它期望用户使用现有的API，如果他们需要登录特定地址。 同样的规则不超过`maxTimestampRange`，仍然适用，允许它保持性能。 分页可以使用时间戳和索引查询参数的组合。

最后，这次发布完成了我们对[HIP-423 长期计划交易](https://hips.hedera.com/hip/hip-423)的执行。 Two new fields `wait_for_expiry` and `expiration_time` were added to `/api/v1/schedules` and `/api/v1/schedules/{id}`

## [v0.56](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.56.0)

{% hint style="success" %}
**MAINNET更新：至少18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：至少17, 2022**
{% endhint %}

这是一个很大的版本，支持六项不同的黑德拉改进提议。 这些变化大多处于事物的最佳一边，未来发布的版本将有助于将它们添加到我们的API中。

[HIP-16](https://hips.hedera. om/hip/hip-16将使合同到期并带来一个新的自动续订账户字段，其中包含负责与合同有关的任何续订费的账户。 合同REST API现在显示这个`auto_rerecon_account`字段，以及一个新的`永久_removal`标记设置为真，当系统合同到期时。

我们错过了实现 [HIP-329](https://hips.hedera)的要求。 om/hip/hip-329) CREATE2 opcode 允许HAPI 客户端将资产本地传输到仅通过其CREATE2 地址知道的合同。 我们弥补了这个逻辑空白，现在我们支持在 `CryptoTransferTransaction` 中的 EVM 地址。

[HIP-336](https://hips.hedera.com/hip/hip-336)看到我们的津贴支持进一步增加。 Support for an optional spender ID parameter on our `/api/v1/accounts/{id}/nfts?spender.id={id}` REST API is now included.

[HIP-410](https://hips.hedera.com/hip/hip-410) 可以将以太空的原生交易包裹并提交给Hedera。 镜像节点现在可以解析这个新的 `EtherumTransaction` 以及它的执行结果。 任何创建的合约或通过其执行生成的结果和日志将自动显示在相关的合约REST API。 还增加了对在合同创建上直接指明合同内编码的支持。 支持外部拥有的账户能够提交以太坊交易， 我们现在计算和存储账户的 ECDSA secp256k1 别名的EVM 地址。 最后，我们还支持在合同日志 REST API 中重复出现的主题，使其更符合`eth_getLogs`JSON-RPC 方法。 如果您提供了多个不同的主题参数 (例如) `topic0` 和 `topic1`) 它与以前一样被认为是一个 `AND` 操作。 但如果传递重复的参数，如`topic0=0x01&topic0=0x02&topic1=0x03`，则意味着“(topic 0 is 0x01或 0x02) and (topic 1 is 0x03)”。

[HIP-415](https://hips.hedera.com/hip/hip-415) 正在定义一个块作为数据流启动以来的记录文件数量(AKA genesis)。 由于只有镜像节点有完整的历史记录，它们将被用来提供协商一致的节点，以更新它们的状态。 因为部分镜像节点在流开始后有效开始日期不会有所有记录文件， 它们可能包含一个与所有数据不同的方块编号不一致的值。 这次发布试图纠正这种情况，通过迁移使它们与完整的镜像节点保持一致，所以每个人都有一个一致的块数值。

[HIP-423](https://hips.hedera.com/hip/hip-423) 长期排定的交易增强了现有的排定交易，允许时间安排交易。 这个版本为新的调度相关字段添加了最宝贵的支持。 下次发布将通过我们现有的schedule REST API曝光这些字段。

### 升级

作为这次发布的一部分， 我们已经完成升级我们的 PostgreSQL 数据库到14版本，并更新了所有测试以使用该版本。 我们建议镜像节点操作员在方便时尽早将他们迁移到 PostgreSQL 14。 关于升级过程的更多详情可在我们的[数据库指南](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#upgrade)中找到。

这个版本中的迁移估计需要2个小时才能对主网数据库进行迁移。 然而，占这次大部分时间的迁移只有在需要纠正区号时才会进行。 只有当您的镜像节点是一个部分镜像节点，并且有一个有效的起始日期在流开始后才是必要的。

## [v0.55](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.55.0)

{% hint style="success" %}
**MAINNET更新：至少4, 2022**
{% endhint %}

这个版本主要侧重于完成我们对 [HIP-336]的支持 (https://hips.hedera.com/hip/hip-336) 令牌的审批和津贴API。 我们增加了对新的 `CryptoDeleteAllivance` 交易的支持，并且删除了对没有将其纳入最终设计的`CryptoAdjustAllance` 交易的支持。 NFT津贴在NFT传输颗粒性上进行跟踪，以便能够在镜像节点上获得最新的津贴信息。 Current spender information will show up in both `/api/v1/accounts/{id}/nfts` and `/api/v1/tokens/{id}/nfts` REST APIs. 我们还将 `is_approval` 标志添加到显示传输的API中。

随着更多的开发者使用计算机使用 Apple M系列的 CPU，它已经变得很清楚了镜像节点，以支持基于ARM 的建筑来容纳它们。 在这个版本中，我们使用[docker buildx](https://docs.docker.com/buildx/working-with-buildx/)添加了多架构Docker图像。 我们现在推送`linux/amd64`和`linux/arm64`版本到我们的[Google Container Registry](https://gcr.io/mirrornode)。 如果今后需要更多的操作系统或架构，就很容易加以扩充。

我们还将我们的[GCP Marketplace](https://console.cloud.google.com/marketplace/details/miror-node-public/hedera-mirror-node)应用程序更新到最新版本。

## [v0.54](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.54.0)

{% hint style="success" %}
**MAINNET更新：APRIL 25，2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 19，2022**
{% endhint %}

这个版本增加了对三个新的REST API和4个HIP的支持。

[HIP-21](https://hips.hedera. om/hip/hip-21描述了免费网络信息查询以启用 SDK 和其他客户端能够检索当前节点列表的必要性。 在 v0.49.1中，我们添加了一个新的 `NetworkService.getNodes()` gRPC API。 在这个版本中，我们正在向我们的REST API添加一个对应的地址簿API。 除了标准的 `order` 和 `limit` 参数外，它还支持一个 `file.id` 查询参数，用两个地址簿`0进行过滤。 第101`或`0.0.102`和一个`node.id`查询参数，以过滤节点并提供分页。

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

[HIP-336](https://hips.hedera.com/hip/hip-336)描述了新的 Hedera API，用于批准和执行对代表帐户的津贴。 一种津贴赋予发售人将预定数量的支付者头巾或代币转到另一个支出者选择的帐户的权利。 在 v0.50.0 中，我们添加了数据库支持来存储新的允许交易。 在这次发布中，创建了两个新的REST API以暴露hbar和可替代的象征性津贴。 全额备用津贴将在协商一致的节点在主机上启用后才能使用。

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

同样在REST API上，我们添加了对 [HIP-329](https://hips.hedera.com/HIP/hip-329.html) CREATE2 地址的支持。 现在任何接受合同ID的API也会接受20字节EVM地址作为十六进制编码字符串。 我们通过添加缓存控制头来启用通过 CDN 分布式缓存来提高REST API 的性能。 按类型REST API 开列的列表交易的性能看到了一个改进性能的修复方法。

作为 [HIP-260](https://hips.hedera.com/hip/hip-260)的一部分，合同预编译通话数据现在会在 `TransactionRecord` 中装入`ContractFunctionResult` 中的新字段`amount`、`gas`和`function_parameter` 。 镜像节点现在存储这些字段，并通过其现有的合同结果 REST API暴露它们。

对集装箱镜头节点作了一些安全改进。 所有Docker 图像现在都不能以 Kubernetes 或 Docker Compose 运行. 头盔图看到的变化符合Kubernetes [restricted](https://kubernetes.io/docs/concepts/security/pod-security standards/#restricted) pod 安全标准。 这确保镜像节点能够使用安全方面的最佳做法，并减少其整体攻击表面。 Kubernetes Pod Security Standard 取代了已废弃的 PodSecurityPolicy ，因此我们已经删除了与后者相关的所有配置。

### 升级

此版本的迁移时间很长，预计要完成大约75分钟，取决于您的数据库硬件和配置。 我们一如既往地建议进行红色/黑色部署，以消除移徙期间的停顿时间。 如果您正在使用 "hedera-mirror-common" 图，请检查 [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack#upgrading-chart) 升级笔记以确保Prometheus Operator 能够成功更新。

## [v0.53](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.53.1)

{% hint style="success" %}
**MAINNET更新：APRIL 7, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 29, 2022**
{% endhint %}

这种发布主要集中在数据完整性领域，确保镜像节点中的数据与共识节点一致。 为此目的，我们添加了一个错误的数据库迁移，它只运行主网并更正了影响流文件的三个[已知问题](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/database.md#errata)。 协商一致节点的状态从未受到影响，只有镜像节点消耗的流文件的这些更改的外部化才会受到影响。

为了找到前后不一致的数据并确保数据保持向前发展，我们增加了一项新的平衡调节工作。 这个任务每晚运行一次，并将平衡文件信息与记录文件信息进行比较，以确保它们处于同步状态。 它对每个平衡文件进行了三次检查：验证平衡文件加起来多达500亿hbar。 验证聚合的hbar传输与平衡文件匹配，并验证合计令牌传输与平衡文件匹配。 如果不需要通过 `hedera.mirror.importter.reacheration.enabled=false`来禁用它。

我们还修复了一个导致转账金额为零的错误，用于创建初始余额为零的加密交易。 这完全是由于我们的代码插入了额外的传输，而不是由于流文件中的任何问题。 我们还修复了一个REST API bug，它导致合同字节代码显示为双编码到十六进制。

对于Rosetta API，我们为各个端点添加了账户别名支持。 我们现在支持解析预编合同功能的合同结果，例如高密度疗法功能。 此功能被特征标记禁用，并将在未来的发布中启用。

### 升级

这个版本包含几个中等大小的数据库迁移，以纠正数据库中的错误数据。 预计一个完整的主网数据库需要约45分钟。

## [v0.52](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.52.0)

{% hint style="success" %}
**MAINNET更新：MARCH 18, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 15, 2022**
{% endhint %}

[HIP-331](https://hips.hedera.com/HIP/hip-331)。 tml是一个社区贡献的改进提议，要求添加一个新的 REST API 来检索帐户拥有的不可替代代币的列表 (NFTs)。 The mirror node has an existing `/api/v1/tokens/{tokenId}/nfts` API to retrieve all NFTs for a given token, but it didn't satisfy the requirement to show NFTs across token classes. This release adds the new `/api/v1/accounts/{accountId}/nfts` API to satisfy this need. 这是我们第一个拥有分页所需多个查询参数的API，因此有一些限制使用这些参数。 Please see the OpenAPI [description](https://github.com/hashgraph/hedera-mirror-node/blob/21c6c464f77d555619af5955843e7d798fcd17b4/hedera-mirror-rest/api/v1/openapi.yml#L51) for this API for further details.

`GET /api/v1/account/0.0.1001/nfts?token.id=gte:1500&serialnumber=gte:2&order=asc&limit=2`

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

镜像节点现在有使用 [k6](https://k6.io/) 编写的我们所有API性能测试。 这些测试可以用来验证性能不会从释放到释放。 今后，我们计划将这些内容纳入[每晚回归测试套件](https://github.com/hashgraph/hedera-miror-node/issues/3099)，以改进我们目前测试每个版本的方法。

在这次发布中讨论了一些部署问题。 我们现在禁用默认的领导人选项，直到我们能够解决其执行问题。 同样，我们改变了进口商Kubernetes的部署战略，从滚动更新来重新创建，以避免多个进口商同时运行。 向进口商增加了一项移民准备程度调查。 这将标记导入器点数为尚未就绪，直到它完成所有数据库迁移。 这样做将确保Helm 不会完成其释放并在迁移完成之前运行其测试。

我们继续通过一些性能改进和错误修复来完善我们的Rosetta执行工作。 Rosetta的性能得到了改进，以减少初始启动时间。 嵌入的 PostgreSQL 容器已升级到 PostgreSQL 14。 Rosetta统一Docker图像已经更新，以符合Rosetta持久性要求。

## [v0.51](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.51.0)

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 28, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: FEBRUARY 25, 2022**
{% endhint %}

这是一个较小的版本，侧重于观测能力改进和Rosetta API 修复。

在观察前端，我们已经减少了REST API 生成的日志信息量一半。 我们还更改REST API，以便为所有响应生成一个一致的跟踪日志，其中包括准确的客户端IP， 经过的时间和状态代码。 我们将镜像节点为减少监测费用而生产的时间序列数减少了大约50％。

对于Rosetta API，我们添加了一个解决失踪者消失的令牌传输问题的工作，它允许检查数据调节到通行证。 通过调节配置参数和改进NFT平衡跟踪性能，总体调节时间得到了改善。 我们通过切换到动态账户平衡加载方式来处理Rosetta CLI的慢速基因账户余额文件加载。 还讨论了其他一些Rosetta问题。

## [v0.50](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.50.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 23, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: FEBRUARY 15, 2022**
{% endhint %}

该版本为三项新的改进建议提供了支持：HIP-260智能合同可追踪性、HIP-3329 CREATE2 Opcode和HIP-336 津贴配额。 它还更新REST API，以反映HIP-226和HIP-227的最新阶段，并更新Rosetta API以供HIP-31使用。

[HIP-260](https://hips.hedera.com/hip/hip-260) 描述了需要通过提供可核查的交易记录中的合同状态变更跟踪来提高智能合同的可追踪性。 镜像节点现在可以存储这些状态更改，并通过合约结果 REST API 将其曝光，以显示每个槽的读写值。 This information was added to both `/api/v1/contracts/results/{transactionId}` and `/api/v1/contracts/{id}/results/{timestamp}`. 下面是一个示例，其它字段被省略为简洁：

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

[HIP-329](https://hips.hedera.com/hip/hip-329) 通过CREATE2 opcode为 [EIP-1014](https://eips.efum.org/EIPS/eip-1014)生成了合同地址。 作为这一部分，在交易记录中添加了一个新的`evm_address`，用于合同创建交易记录。 此外，此 `evm_address` 可以填充在交易主体中出现的 `ContractID` 中。 镜像节点已更新，以便能够将此 `evm_address` 映射到其对应的合同编号，并在合同中暴露这个属性。 我们还储存儿童合同的全部合同信息，因为它们现在已作为单独的内部交易出现在记录流中。 填补智能合同数据缺失方面的长期缺口。

[HIP-336](https://hips.hedera.com/hip/hip-336) 允许帐户所有者委托另一个帐户代表他或她花费hbars或令牌。 This feature provides an implementation of [ERC20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20), IERC20, and [ERC721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) on the Hedera network. 镜像节点已更新，以支持这些新的交易类型，并存储绝对或相对加密、可互换或不可互换的允许。 在稍后的版本中，我们将会透露这个信息，详情见设计 [document](https://github.com/hashgraph/hedera-mirror-node/blob/v0.50.0/docs/design/allowanc.md)。

如 [HIP-226](https://hips.hedera.com/hip/hip-227)和 [HIP-227](https://hips.hedera.com/hip/hip-226)所述，多个新字段被添加到合同的 REST API。 "bloom`、`result`、`status`"字段被添加到合同结果 API 响应中。 `result`和`status`显示了类似的信息，前者是 HAPI 响应号，而后者返回`0x1`或`0x0` 以显示交易是否成功。 在 web 3 APIs 中是常见的。 我们还将`bloom\`添加到合同日志 API 响应中。 最后，我们现在对没有结果的合同通话作出部分答复。

导入组件添加了一个新的 `hedera.mirror.importter.parser.record.entity.durden.topics` 属性来控制主题消息的持久性。 如果主题信息数据未被使用，这可以设置为 false，镜像节点操作员。 仅在主网上，这种数据目前就占用了多达2TB的存储价值。

监视器组件获得了对并行节点验证的支持，以提高启动性能。 现在，所有验证都是在后台线程中完成的，必要时添加和删除节点，同时发行商线程继续不间断地发布交易。 这种重新工作还可以解决订阅在节点验证过程中停止的问题，并且需要太长时间来验证一个下面的节点。

Rosetta看到了一些重要的改进，包括添加对 [HIP-31]的支持 (https://hips.hedera.com/hip/hip-31) 预期的小数. Rosetta 一致的 Docker 图像看到了新增的功能，以便在初始启动时使用数据库快照自动还原数据库。

### 打破更改

作为HIP-3329 CREATE2 的一部分，我们将合同中现有的`solidity_address`重命名为`evm_address`。 这个新名称准确地反映了HIP和原生的命名，避免了在Hedera不仅仅支持团结合约时将地址与团结联系在一起。

## [v0.49](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.49.1)

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 15, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：FEBRUARY 4, 2022**
{% endhint %}

本版本实现了三个Hedera Improvement Proposals (HIPs)，并将镜像节点数据库升级到最新版本。

[HIP-21](https://hips.hedera. om/hip/hip-21描述了免费网络信息查询以启用 SDK 和其他客户端能够检索当前节点列表的必要性。 为了满足这种需要，我们添加了一个新的 `NetworkService.getNodes()` 串流gRPC API 以从地址簿文件中获取当前节点列表。 通过将它变成流媒体API，我们可以避免客户必须处理自己的分页。 仍然允许我们将大型地址簿拆分成小块。 由于有两个地址簿文档，我们提供了一个选择返回哪个`FileID`的选项。

```
Message AddressBookQuery Power
    .proto. ileID file_id = 1; // 网络上地址簿文件的 ID。可以是 0。 页: 1
    int32 限制 = 2; // 停止前接收节点地址的最大数目。 如果没有设置或设置为零，它将返回数据库中的所有节点地址。


Service NetworkService Windows
    rpc getNodes (AddressBookQuery) 返回 (strew .proto.NodeAddress);
}
```

[HIP-171](https://hips.hedera.com/hip/hip-171)描述了在主题消息REST API 响应中返回付款人帐户的必要性。 这次发布只是在主题消息区块信息中添加了在 gRPC API 中存在但从REST API 中缺失的信息。

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

我们继续支持 [HIP-32](https://hips.hedera.com/hip/hip-32) 自动创建帐户，我们添加了别名支持我们的帐户REST API。 现在当你查询 `/v1/api/accounts/:id` 时，`id` 可以是 `0.0.x` 中的 Hedera 实体，也可以是 hex-encoded 别名。 一个帐户的 "别名" 现在也会显示在所有帐户的 REST API 输出中。

已经做了许多测试，以确保PostgreSQL 14能够正确地与镜像节点一起运行，并为旧版本提供好或更好的性能。 我们现在正在将Hedera管理的镜像节点迁移到 PostgreSQL 14。 我们推荐其他镜像节点操作员考虑尽早升级到最新的数据库版本，并提供了升级 [instructions](https://github)。 om/hashgraph/hedera-miror-node/blob/main/docs/database.md#upgrade)协助这一进程。

## [v0.48](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.48.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 26, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: JANUARY 18, 2022**
{% endhint %}

[HIP-206](https://hips.hedera.com/hip/hip-206)在交易记录中添加了母公司协商一致的时间戳，用于内部交易，如在智能合同中被引用的 HTS 预编译。 To round out the `nonce` support in the last release we added `parent_consensus_timestamp` to `/api/v1/accounts/{id}` and `/api/v1/transactions`. 此字段帮助定义交易之间的父/子关系。

[HIP-226](https://hips.hedera.com/hip/hip-226)描述了最近添加的合同结果 REST API。 我们一直在迭代并在API中添加更多功能直到匹配HIP中的描述。 此版本添加了智能合同执行生成的日志列表。 下面是新的 JSON 响应的样本：

```
请注意，
    "amount": 30,

    "logs": [
      Power
        "address": "0x000000000000000000000000000000000000000000000000001389",
        "contract_id": "0.0". 001",
        "data": "0x0123",
        "index": 0,
        "topics": [
          "0x97c1fc0a6ed5551bc8831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925",
          "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
          "0xe8d47b56e8cdfa95f871b19d4f50a857217c44a95502b0811a350fec1500dd67"
        ]
      },
      voir
        "address": "0x00000000000000000000000000138a",
        "contract_id": "0. 5002",
        "data": "0x0123",
        "index": 1,
        "topics": [
          "0x97c1fc0a6ed5551bc8831571325e9bdb365d06803100dc20648640ba24ce69750",
          "0x8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925"
        ]
      }
    ]
}
```

在上一次发布中，我们添加了一个新的 Web3 JSON API 通过这次发布，我们添加了一个 "hedera-miror-web3" 头盔图，可以用于在 Kubernetes 上部署它。 Java 模块中增加了额外的尺度，创建了一个新的Grafana 仪表板以使其直观化。 Web3 API 也被添加到docker-compose文件。

### 升级

如果你升级了我们的头盔图表的现有部署，就会有几处突破性的变化以便牢记。 首先，我们默认情况下部署了新的 Web3 API 图，它需要一个 `mirror_web3` 数据库用户存在并具有读取权限。 请在升级之前创建新的数据库用户，或者您可以禁用 `hedera-mirror-web3` 子图。

我们已经将 `政策/v1beta1` 中的 `PodDisruptionBudget` 资源升级到 `policy/v1` ，因此现在需要更多的 Kubernetes 1.21。 对于`hedera-mirror`图表， 如果你使用了可选的 `postgresql` 子图，你必须先将PostgreSQL 复制缩放到一个，然后才能升级repmgr。

如果你正在使用 `hedera-miror-common` 图表，它所使用的社区子图表中会出现一些突破性的变化。 升级前，您需要删除 "promeus-adapter" 和 "kube-state-metrics" 部署。 您还需要重新安装一些自定义资源定义。 运行下面的命令这样做：

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

## [v0.47](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.47.0)

{% hint style="success" %}
**MAINNET更新：JANUARY 3, 2022**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 30、2021**
{% endhint %}

此版本继续关注Smart Contracts 2.0。 镜像节点有助于调试智能合同的执行，我们的重点一直是提供 API ，使开发者的生活更加容易。 为此目的，我们增加了对交易ID停止的支持，一个新的合同日志REST API，以及一个新的 web3 API组件。

新的 Web3 API 模块为Hedera 网络提供了现有的 JSON RPC API。 JSON-RPC API 是一个广泛用于与分布式分类账互动的标准。 提供“Hedera ”实施这些API的目的是为了方便现有的 daps 迁移到Hedera 并简化开发者的斜坡。 目前，Web3模块仅提供部分实现 [Etherum JSON API ](https://eth.wiki/json-rpc/API)。 具体而言，在这个版本中只有`eth_blockNumber` 方法已经执行，因为我们的工作重点是首先奠定基础。

作为 [HIP-32](https://hips.hedera.com/HIP/hip-32.html)和 [HIP-206](https://hips.hedera.com/HIP/hip-206.html)的一部分，在 `TransactionID` 原始文件中添加了一个“noce”字段，以保证平台生成的交易的唯一性。 此 `nonce` 字段已添加到任何返回交易数据的 REST API。 `/api/v1/transactions/:transactionId`、`/api/v1/transactions/:transactionId/statesproof`添加了一个`nonce`查询参数， 和`/api/v1/contracts/results/:transactionId`能够区分用户提交的交易和因该交易而产生的内部交易。 请注意，`/api/v1/transactions/:transactionId` 没有`nonce`参数将默认返回所有交易，而其他API将默认`nonce`到\`0'。

The new `/api/v1/contracts/{id}/results/logs` REST API provides a search API to query for logs across contract executions for a particular contract. 支持以协商一致方式搜索时间戳和主题。 请注意，由于性能原因，它目前不支持分页，并且要求按主题提供时间戳或时间戳范围。

## [v0.46](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.46.0)

{% hint style="success" %}
**MAINNET更新：十二月 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 17, 2021**
{% endhint %}

0.46 是一个功能包裹的版本，包括支持三个新的HIP，三个新的REST API， 一个重新设计的监视仪表板，一个新的 Rosetta APIBD测试套件和一个新模块。 让我们进去！

[HIP-222](https://hips.hedera.com/hip/hip-222) 添加支持Hedera 网络的 ECDSA (secp256k1) 密钥，以帮助提高开发者将dapp 迁移到Hedera的体验。 镜像节点已更新，以便能够解析和存储新的密钥类型及其相应的新签名。 因为这个密钥类型也被认为是一个原始密钥，例如ED25519(例如) 不是一个复杂的密钥列表或阈值密钥，您现在可以通过它们的66个字符的 ECDSA 公共密钥搜索帐户和代币。 作为额外的奖金， 我们现在还允许使用复杂的公用钥匙搜索，这些公用钥匙是一个关键列表或门槛键，只是一个原始钥匙。

[HIP-32](https://hips.hedera.com/hip/hip-32) 自动帐户创建允许新用户通过CryptoTransfer _without_已经在网络上创建了一个帐户来接收配额。 镜像节点已更新，以存储用户的别名和包含一个别名到新创建的帐户ID的地图传输。 在即将发布的版本中，我们将添加通过别名查询帐户的能力。

[HIP-206](https://hips.hedera. om/hip/hip-206将Hedera Token Service (HTS) 整合到Hedera Smart Contract Service (HSCS)，允许合同可以通过程序转让、轻微、烧毁、关联和分离代币。 虽然对协商一致节点的支持仍在进行，但镜像节点现在已经初步支持HTS 预先编译的交易。 这为交易ID添加了一个新的 `noce` 字段以及交易之间的父母/子女关系概念。 在即将发布的版本中，我们将添加 "nonce" 支持，并将父母/子女关系曝光到 REST API。

REST API 看到了三个新的REST API以支持新的智能合同 2.0 功能。 After executing a smart contract, developers can use the `/api/v1/contracts/{id}/results` API to search for a contract's execution results by timestamp range or payer account (e.g. `from`). Once the result is located, the new `/api/v1/contracts/{id}/results/{timestamp}` API can retrieve detailed information about the smart contract call. If you already know the transaction's ID, you can directly retrieve the smart contract results via the new `/api/v1/contracts/results/{transactionId}` API. 在即将发布的版本中，我们将增加对日志、状态变化以及更多此 API 的支持。 下面是一个示例响应：

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

其他杂项更改包括现在验证REST API 测试与 OpenAPI 规格。 这将有助于更好地保持规格文件与代码同步，直到我们能够完全整合规格。 基于 Node.js 的监视仪表板进行了视觉刷新，以便更容易使用。 Rosetta API 看到了一套新的加密和代币行为驱动开发 (BDD) 测试。 由于这些测试，发现并处理了一些bug。 最后，我们将一些常见类重新设置为一个 `hedera-miror-common` 模块，以便与未来的 API 模块共享代码。

#### 数据库迁移

由于上述功能，我们不得不对数据库方案进行更改，这可能需要一些时间才能完成。 我们已经测试了一个包含1.9B+交易的主网数据库的迁移，并在大约5个小时内完成。 镜像节点操作员可能会看到根据其数据大小、数据库硬件和数据库配置标志进行的迁移所需的大致时间。

## [v0.45.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.45.0)

{% hint style="success" %}
**MAINNET更新：DecEMBER 8, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 6, 2021**
{% endhint %}

镜像节点现在捕捉了随着时间的推移对帐户和合约发生的更改的整个历史。 在此之前，镜像节点只能保持Hedera实体的当前状态。 随着此更改，所有人都会创建， 更新和删除一个实体发生的交易将导致创建一个快照来表示它是如何在其中每个时间点出现的。 此信息可以用来在过去某个特定协商一致的时间戳上查询一个实体的 API。

目前，这个历史查询选项仅在REST API合同中被支持。 例如，您现在可以搜索`/api/v1/contracts/0.0.1000?timestamp=lte:1609480800`，查看2021年1月1日合同`0.0.1000`的状态。 关于合同，我们增加了与合同有关的API的新验收测试。 [设计文档](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/design/smart-contracts.md)已更新，以详细介绍我们提议实施的新API。 这些变化还详见Hedera Improvement Proposals (HIPs) for [contract result](https://github.com/hashgraph/hedera-improvement-proposal/pull/226) and [contract execution logs](https://github.com/hashgraph/hedera-improvement-proposal/pull/227) REST API。 如果您有任何反馈，请看看看并告诉我们。

在CitusDB方面，我们继续取得进展。 我们的所有参考表格现在都被移除，以利于数据库或应用程序列出。 这将有助于改进业绩和简化数据库迁移。 我们已经更新了我们的测试利用来使用使用 PostgreSQL 14 的 CitusDB 的最新版本。 最后，我们现在创建分布式表，使用实体ID作为分配列，酌情将其与其他表格合用一处。

Rosetta API 也有一些改进，包括在“/construction/submit”中创建在线帐户的能力。 会议还讨论了象征性平衡调节问题。

## [v0.44.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.44.0)

创建 [progress](https://github.com/hashgraph/hedera-miror-node/issues/2675) 将我们的数据库过渡到 [CitusDB](https://citusdata.com/), 这个版本添加了一个新的 `v2` 方案, 初始支持 CitusDB。 CitusDB自动化测试被添加到我们的CI管道中，以便它与 `v1` PostgreSQL模式同时运行。 交易支付者帐户ID已添加到相关的转账表。 这将被用作一个分布列，用于在一个非基于时间的尺寸之间进行数据库分配。 这允许镜像节点在更多交易支付者使用系统时缩放读取和写入。

释放的其余部分主要集中在改进业绩方面。 我们不再坚持交易中遇到的每个实体ID的最低实体信息。 这是一个性能拖拉，但也造成了我们在即将发布时跟踪实体历史的计划问题。 我们的一些参考表已被删除，改用应用程序枚举来映射原始值到描述字符串。

在REST API上，通过公钥检索账户以提高其性能。 如果您的应用程序不需要余额信息， 您可以通过设置新的 `balance` 参数为 `false` 账户API调用来看到额外的性能收益。 代码被优化以将 `Array.concat` 替换为 `Array.pus` 并缓存实体ID 构造。 最大的变化可能是对`limit`参数的潜在突破。

### 打破更改

REST API 可以返回的最大行数已从500更改为100。 同样，如果`limit`参数未指定则REST API 返回的默认行数从500改为25。 如果一个请求发出超过 100 个请求，它不会失败。 相反，它将透明地使用两种价值中较小的一种。 因此，除非您的应用程序对返回结果的确切数量作出假设，这不应是一个突破性的变化。 我们今后可能会出于性能原因调整这些值，因此更新您的应用程序以处理任意限制和结果的良好做法。

## [v0.43.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.43.0)

{% hint style="success" %}
**MAINNET Upeted: NOVEMBER 18, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 12, 2021**
{% endhint %}

### 智能合同

Hedera加大了对[智能合同](https://hedera)的重视。 om/blog/hedera-evm-smart-contracts-now-bring-west speed-programability-tokenalization), 我们花了时间来更新镜像节点的智能合同支持，并为今后的增强奠定基础。 如[设计文档](https://github.com/hashgraph/hedera-miror-node/blob/main/docs/design/smart-contracts.md)所详述的那样，今后的计划包括新的针对合同的REST API和Etherum-compatible API。

为此目的，更新了数据库方案和进口商，以便使所有与合同有关的信息正常化和储存。 修复长期的错误，如不存储合同字节码和儿童合同。 合同表与通用实体表分开，并已开始着手使所有实体表保持所有变动的历史。 REST API 现在支持搜索和检索特定合同。 下面是检索合同的一个例子：

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

### 数据结构

在过去几个月内 目前正在分析可能替换的 `PostgreSQL` 方法，因为处理数量不断增加的数据对现有镜像节点数据库造成压力。 在商定接受标准之后， 优先级已放置在 PostgreSQL 兼容的分布式数据库上，该数据库能够在许多节点中截断我们的时间序列数据，用于缩放读和写。 这将确保Hedera和其他使用开源镜像节点软件的人能够最快地销售和方便地转换。 我们为证实我们的概念而选择的四个分布式数据库包括： [CitusDB](https://citusdata.com/)、 [CockroachDB](https://cockroachlabs.com/)、 [TimescaleDB](https://www.timce.com/)和 [YugabyteDB](https://www.yugabyte.com/)。

在对每一个项目进行了详细分析之后， 我们选择了我们下一个数据库的 CitusDB ，因为它的 PostgreSQL 兼容性极好(它是 PostgreSQL 扩展)，以及它对缩短时间序列数据的成熟支持。 其分布式查询引擎路由和平行的 DDL、DL以及分布式表格上的其他操作。 它的columnar 存储可以压缩多达8x的数据，加速表扫描并支持快速预测。 这个版本包含一些基础工作来使我们的方案准备好分区。 您可以追踪我们的 [progress](https://github.com/hashgraph/hedera-miror-node/issues/2675)，以便在今后几个月内将 CitusDB 整合到我们的代码库中。 我们计划在工作完成后一段时间内维持对这两个数据库的支持。

### 性能改进

同通常情况一样，我们花时间优化了系统中的各种部分。 我们的交易 REST API 通过使用通用表表达式(CTE)重写而看到一些性能改进。 这将使用 CitusDB 支付未来红利，因为它允许查询以平行方式运行更容易。 An issue with `/api/v1/topics/{id}/messages` timing out for some topics was addressed and the realm and topic number columns were combined to reduce the table and index size. `/api/v1/tokens/{id}/balances` also saw some performance improvements that decreased its average response time. 记录了快速历史摄入的配置选项，以便镜像节点操作员能够更快地获得历史数据。

## [v0.42.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.42.0)

{% hint style="success" %}
**MAINNET Update Competed: OCTOBER 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：OCTOBER 18，2021**
{% endhint %}

此版本显示镜像节点的 Hedera Token 服务功能有很多改进。 支持 [HIP-24](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-24.md) 已完成Hedera Token 服务的暂停功能。 导入器可以避免新的令牌暂停并取消暂停交易类型并适当更新令牌。 同样，REST API 已更新，以显示新的暂停密钥和暂停状态。

按照这些思路，REST API 也被更新以显示令牌备注和标记以显示是否已删除。 现在当一个帐户与一个令牌脱离关联时，它的供应将被适当更新以显示负转移。 如果该分隔中的令牌是 NFT类型，那么该帐户拥有的所有NFT都将被正确标记为已删除。 我们还解决了一些特殊的负转移金额问题，这些金额显示在REST API交易中。

添加了一个新的网络供应REST API 来显示已发布的供应。 开源镜像节点计算并显示释放源可以避免当前系统的任何一个失败点，因为用户可以询问多个镜像节点并比较他们的答案 (或运行他们自己的镜像节点)。

`GET /api/v1/network/supply`

```
{
	"timestamp": "123456870.854775807",
	"released_supply": 1800000000000000000,
	"total_supply": 5000000000000000000
}
```

继续我们改进Rosetta API的主题，在数据和构建API中增加了NFT支持。 我们花了时间将其转换为标准的配置库，并重新整理包结构以使其更加简洁和连贯。 每一层都增加了上下文，以便能够适当取消和超时支持。

## [v0.41.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.41.0)

{% hint style="success" %}
**MAINNET更新：OCTOBER 6, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 30, 2021**
{% endhint %}

这份版本的工作重点是改进我们的[Rosetta API](https://www.rosetta-api.org/)并使其可用于生产。 增加了一张新的Rosetta Helm图，用于向Kubernetes部署生产。 可观测性的改进包括健康探测、计量、请求日志、警报和Grafana仪表板。 增加了Postman整编测试，以核实部署后的功能。 最后，一些重要的缺陷已经解决，包括缺失的对等IP地址和令牌平衡调节失败。

进口商构成部分在15 000个或更高的设备上得到最优化的交易。 这种变化包括改进以降低CPU和内存使用率，同时增加分配给该进程的内存。

其他增强措施包括定期在监视器中重新验证主节点，并为REST API数据库连接添加TLS支持。

## [v0.40.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.40.0)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 27, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 16, 2021**
{% endhint %}

这个版本增加了 [HIP-23](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-23.md) 自动令牌协会的支持。 此功能允许用户选择自动接受可替换或不可替代的代币作为转让的一部分，而无需事先与代币相关联。 镜像节点现在存储这些含蓄创建的关联，并通过其REST API返回它们。 此外，我们会在帐户REST API中显示 `max_automatic_token_associations` 。

除了为HIP-23更新它外，REST API 还看到了其他一些修复和改进。 帐户 API 现在显示它的备忘录和 `receverSigRequireed` 字段。 REST API 软件包重命名为使用 `@hashgraph` NPM 软件包范围。 这不应该是一个突破性的变化，因为我们目前不会将这些软件包发布到NPM。 一些API已经确定，以确保清单按照确定性排序归还。 另外，OpenAPI 规范已经确定，这样它可以准确地反映当前API，并且可以用于生成客户端代码。 最后，计划 API 的性能有所改进。

在监测方面，我们通过添加数据源和集群下拉，加强了我们的格拉法那仪表板，使其与Grafana 云兼容。

## [v0.39.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.39.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: AUGUST 31, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 30，2021**
{% endhint %}

这种版本提供了与Hedera Services 0.17的兼容性，包括支持非可变代币并提高其对关税的支持。 关于后者。 NFT 创建者可以设置一种使用费，在其创作之一的可换值被交换时收取，并更新镜像节点以跟踪这种新类型的自定义费用。 另外还支持在分摊的关税中有效的支付者帐户和以部分费用储存转账净额。

大多数未使用的数据生成器模块已被删除，导致代码覆盖面大为增加。 覆盖率从84%增加到92%。

修复了大量的 bug ，包括如果数据库已经关闭，REST API 启动时出现崩溃的问题。 监视过长无法启动，OpenAPI 修复等等。

## [v0.38.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.38.1)

{% hint style="success" %}
**MAINNET更新：AUGUST 17，2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 17，2021**
{% endhint %}

这个版本是一个小型错误修复版本，它包含了我们镜像节点监测组件的一些重要修复。 我们在监视器中添加了一个新的集群健康检查，该检查考虑到发布状态。 负载平衡器使用此健康检查来确定路线到哪个集群。 旧的健康检查终点没有考虑到交易发布是否有效，以及在主要节点升级期间不会将流量导入公共镜像节点。

除了新的健康检查外，监测员还在低TPS中对其比率计算方法进行了修正。 不会在空闲、节点验证和它生成的警报时进行采样。 监视器的主网配置现在指向公共镜像节点，我们已经将新的预览网节点添加到预览网网络配置。

还有其他一些修复方法来清理代码和修复测试。 我们已经做出努力来减少我们在 [SonarCloud](https://sonarcloud.io/dashboard?id=hedera-miror-node)中看到的代码熔炉。

## [v0.38.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.38.0)

{% hint style="success" %}
**MAINNET更新：AUGUST 16，2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 13, 2021**
{% endhint %}

这个版本通过添加额外的测试覆盖面和修复任何剩余bug来包装NFT和自定义费用支持。 具体而言，我们的监测工具和验收测试中增加了NFT支持。 自定义费用也被添加到验收测试中，并有一些错误修复。

使公众能够看到一些监测改进，包括在我们的外部监测仪表板中添加HTTPS支持，以及添加一个没有主动警报的平台以抑制所有其他警报。

在这个版本中有一些错误修复。 由于错误在上次发布时被禁用的流文件健康检查已修复并重新启用。 地址簿更新流程也看到了一些重要的修复。

#### 打破更改

交易中的付款人帐户 ID 已被移除。 这是服务0的变化。 6 现在从发送触发令牌的账户中收取自定义费用，而不一定是交易的支付者。

## [v0.37.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.37.2)

{% hint style="success" %}
**MAINNET更新：AUGUST 4, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：AUGUST 5, 2021**
{% endhint %}

一个解决我们的 [HIP-18]问题的小错误修复版本(https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-18.md)

## [v0.37.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.37..0)

{% hint style="success" %}
**MAINNET更新：29, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：2005年7月15, 2021**
{% endhint %}

这个版本扩大了我们对[不可互换令牌](https://github.com/hashgraph/hedera-reforestation-proposal/blob/master/HIP/hip-17.md) (NFTs)的支持，新的 NFTspecific REST API。 一个新的 API 已被添加，返回一个特定令牌ID的 NFT 列表。 我们还添加了一个新的 API ，通过其令牌ID和序列号返回一个单一的 NFT 。 最后，我们添加了一个 API 来查看特定的 NFT 的交易历史。 为了使REST API 代码更容易管理，我们现在采用了一种更面向对象的方法，使用模型、视图模型和服务。 下面是三个新的 API 的示例：

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

## [v0.36.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.36.0)

{% hint style="success" %}
**MAINNET Upeted: July 19, 2021**
{% endhint %}

We are happy to [announce](https://hedera.com/blog/now-available-public-mainnet-mirror-node-managed-by-hedera) the availability of a publicly accessible, free-to-use, mainnet Mirror Node operated by the Hedera team. 作为其中的一部分，我们作出了大量努力来调整我们的库贝内特部署工作。 我们迁移到 [Flux 2](https://fluxcd)。 o/)基于 GitOps 的部署工具，让我们能够声明镜像节点在git 中的预期状态并管理我们的滚动。 您可以浏览我们的[部署分支](https://github.com/hashgraph/hedera-mirror-node/tree/depl) 并查看到各种群组和环境的精确配置和版本。 头盔图已更新，以增加`PodDisruptionBudgets`，调整警报规则和其他改进，使部署更容易自动化。

此版本是第一个镜像节点版本，初步支持不可互换代币。 NFT 支持正被添加到[HIP 17](https://github.com/hashgraph/hedera-reform-proposal/blob/master/HIP/hip-17.md)中的Hedera 节点。 We spent time [designing](https://github.com/hashgraph/hedera-mirror-node/blob/v0.36.0/docs/design/nft.md) how that NFT support will look like for the Mirror Node. 对模式进行了修改，以添加新的表格和字段，导入器更新到最少的 NFT 交易。 更新了现有的REST API，以便在响应中添加 NFT 相关字段。 This includes adding a `type` field to the token related APIs to indicate fungibility and a`nft_transfers` to `/api/v1/transactions/{id}`:

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

有一点值得注意，即我们没有在清单交易终点添加NFT转让，以努力减少该端点的大小并提高其性能。 在下次发布中，我们将添加新的 NFT specific REST API。

继续讨论上次发布的主题， 我们对Rosetta API 作了更多的修改，以使其与其他组件相匹配。 Rosetta现在包括通过数据和构造API支持HTS。

进口商看到很大程度上注重提高业绩和复原力。 Kubernetes内运行时，它现在非常可用 (HA)。 这允许一个以上的实例一次运行，并且在初级教育不健康的情况下不能进入中等教育。 一个名为 `leaders` 的 Kubernetes 特殊配置映射用于随机选择领先者。

我们正在极大地改善我们的摄取时间以创建实体。 以前是数据库中发现的更新数据。 因为插入总比找到和更新速度快， 我们已经实现了最佳化以将更新插入临时表格，并在最后支持最后表格。 有6 000个新实体的记录文件从21秒到600秒，达到35倍改进。 平衡文件处理被优化，以通过每次只将一个文件存入内存来大大减少内存。

### 打破更改

为了纪念 [Juneteenth](https://en.wikipedia.org/wiki/Junetenth)，并且作为整个工业运动的一部分，我们将我们的`master`分支更名为`main`。 如果你有一个 Mirror Node Git 仓库的克隆或叉， 您需要采取以下步骤来更新它才能使用 `main`：

```
git branch -m main
git 获取来源
git branch -u origin/main
git 远程设置头来源 -a
```

作为我们减少记忆使用的最优化的一部分，我们现在在生命周期早些处理一些事情。 由于这种情况，我们不得不重命名一些属性以反映这一变化。 如果您正在使用 `keepFiles` (现改名为 `writeFiles`) 属性，我们也会更改磁盘结构，以便在下载后将流文件写入磁盘。 它不再逐日归档到文件夹中。 相反，文件夹结构将与桶中的结构完全匹配。 这就为镜像节点提供了使用一个 [MinIO](https://min.io/)S3 兼容的 API 下载和镜像bucket 本身的可能性。 下面是重命名属性的摘要：

- 重命名`hedera.mirror.importter.downloader.balance.signatures` 到 `hedera.mirror.importter.downloader.balance.writeSignatures`
- 重命名`hedera.mirror.importter.parser.balance.keepfiles`为`hedera.mirror.importter.downloader.balance.writeFiles`
- 重命名`hedera.mirror.importter.parser.balance.persistBytes`为`hedera.mirror.importter.downloader.balance.persistBytes`
- 将 `hedera.mirror.importter.downloader.event.keeplignatures` 重命名为 `hedera.mirror.importter.downloader.event.writeSignatures`
- 重命名`hedera.mirror.importter.parser.event.keepFiles`为`hedera.mirror.importter.downloader.event.writeFiles`
- 重命名`hedera.mirror.importter.parser.event.disturbytes`为`hedera.mirror.importter.downloader.event.distolBytes`
- 重命名`hedera.mirror.importter.downloader.record.signatures` 到 `hedera.mirror.importter.downloader.record.writeSignatures`
- 重命名`hedera.mirror.importter.parser.record.keepfiles`为`hedera.mirror.importter.downloader.record.writeFiles`
- 重命名`hedera.mirror.importter.parser.record.disturbytes`为`hedera.mirror.importter.downloader.record.disturbytes`

## [v0.35.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.35.0)

{% hint style="success" %}
**MAINNET Upeted: July 8, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 21, 2021**
{% endhint %}

这次释放中的大多数变动都是我们Kubernetes部署周围的行动改进。 在我们开始将更多的环境从虚拟机器转换为以Kubernetes为基地时，这些改变是必要的。 我们在头盔图中添加了我们的验收测试，以便它能够在升级期间自动触发并验证部署成功。 关于导入器，我们添加了一个新的健康检查来验证流文件是否被成功解析。 我们修复了进口商，以便在长期运行的数据库迁移之前开始探索，从而使我们能够最终启用它的主动性。 图表有很多较小的修复，所以请查看链接的PR以了解更多详情。

监视器看到一个全新的 REST API，它列出了活动订阅。 我们的集群通过负载平衡器来确定集群的总体健康状况和航线流量。 我们也为此API添加了一个 OpenAPI spec 和 Swagger 界面。

特别感谢[@si618](https://github.com/si618) 修复Windows上的构建并添加一个 GitHub 工作流以确保它保持修复。

### 打破更改

REST API 最大值和默认限制已从1000降到500。 如果您明确发送了超过 500 个数量，您的请求将失败。 请适当更新您的客户端代码。

## [v0.34.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.34.0)

{% hint style="success" %}
**MAINNET更新：16，2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JUNE 11, 2021**
{% endhint %}

In Hedera Mirror Node v0.34.0, we started work on [designing](https://github.com/hashgraph/hedera-mirror-node/blob/master/docs/design/nft.md) support for [NFTs](https://github.com/hashgraph/hedera-improvement-proposal/blob/master/HIP/hip-17.md) that will come in a future Hedera Services release.

默认， 镜像节点将验证地址簿中至少三分之一的节点在导入到其数据库之前签署了一个流文件。 这确保了主要节点就档案中的交易达成了三分之二的共识。 出于性能或验证原因，您可能想要减少或增加此默认百分比。 为了支持此用法，我们添加了一个 `hedera.mirror.importter.downloader. 控制已验证节点(用于就签名文件哈希达成共识的节点)与可用节点总数的比率的onsensusRatio`属性。

我们花了时间对Rosetta API进行了一些重要的依赖升级。 这包括对Hedera和Rosetta SDK的重大更新，两者都需要进行大量重新计算。 研究了Rosetta的一些bug以及Rosetta的CI 工作流程的改进。 这些变化为今后释放Rosetta的进一步改进奠定了基础。

为了避免重复，我们希望统一我们的JMeter和监测性能测试。 为了做到这一点，我们需要更新的监测工具来使我们的JMeter测试具有同等功能。 为了实现这一点，我们已将发布拆分为 HAPI 并订阅在监视器中的镜像节点流以便只允许订阅。 在这个迭代中，只有gRPC API 只支持订阅。 通过这个更改，我们能够删除我们的 JMeter 代码，并优化`hedera-mirror-test` 图像从1.5G 变为0.5G 图像。

我们对我们的头盔图作了一些操作上的改进，包括警报依赖性。 警报依赖有助于避免大量的警报，这些警报都与相同的根源有关。 我们还对图表进行了一些错误修复，当启用或禁用某些组件有利于外部数据库或消息客车时可能发生这种错误。

## [v0.33.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.3.30)

{% hint style="success" %}
**MAINNET更新：JUNE 10, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：21, 2021**
{% endhint %}

这个版本为HAPI 0.13.2添加了支持。 这将带来一个新的地址簿文件格式，更紧凑且不会重复IP地址和端口信息。 我们花了时间调整我们的数据库，以反映较新的格式，同时保持与旧格式的兼容性。

这次发布的一个主要重点是改进用于生产部署的头盔图。 我们现在通过使用 Helm 的 [lookup](https://helm.sh/docs/chart\_template\_guide/functions\_and\_pelines/#using-the-查询功能) 功能，自动生成需要一个组件的密码，并确保它们在升级时保持不变。 我们将`env`、`envFrom`、`volumes`、`volumeMounts`属性添加到所有图表中以更灵活的配置中。 我们添加了一个 `global.image.tag` 图形属性，以方便测试自定义版本。 我们更容易使用可以在集群之外的依赖关系，如Redis和 PostgreSQL。

一些内部改进使我们的发布过程自动化，以便可以通过 GitHub 踢掉版本缓冲区和发布笔记的生成。 现在这还包括生成一个变迁和随时提供最新版本说明。 最后，我们更新了我们的接受测试，以便自动拉取和使用最新的地址簿以及验证所有节点以确保只是最新的, 有效的节点用于验证。

## [v0.32.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.32.0)

{% hint style="success" %}
**MAINNET更新：至少19、2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：至少11, 2021**
{% endhint %}

在这次发布中，我们花了时间对进口商和监测员进行了一些性能优化。 如果您使用一个容器镜像节点，Java 应用程序现在使用了更多已分配给它的可用内存。 我们优化了一些内部队列的大小，以减少失去内存错误的可能性。 我们现在使用更有效的流媒体方法将实体写入数据库并避免大量的内存分配。 所有这些都大大减少了总的内存使用量，提高了进口商的总体性能。 监测员还看到业绩的改善，使其能够以10 000个TPS的费率公布交易。

这次发布更新了我们更多的系统来处理即将在主网上提供的修订的交易设计。 为了能够公布新的交易，更新了验收测试和监视。

我们现在暴露了 Base64 格式的 REST API 中的原始交易字节。 在数据库中保持`Transaction`原生的字节是一个可使用一段时间的选项，但直到现在还没有通过 API 访问。 保留数据是默认关闭的，因为确实会增加数据库的大小。 Hedera管理的镜像节点不会开启该功能来减少存储。

## [v0.31.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.31.0)

{% hint style="success" %}
**MAINNET更新：APRIL 30，2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：APRIL 26，2021**
{% endhint %}

在预览网上提供预定交易后，我们听取了用户的反馈，并进一步重复设计，使其更容易使用。 此版本增加了对此[修订的交易计划](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md)设计的支持，计划在 HAPI v0.13 中发布。 对于我们的 REST API 格式没有影响，只有导入器需要更新才能解析和采用新的探索格式。 一旦SDK增加了对新设计的支持，我们的监视API和验收测试将在下一次版本中进行调整。

这个版本还会为新发布的 HAPI v0.12 帐户余额文件格式添加支持。 新的 [protobuf](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/account\_balanc\_file.proto)格式将最终在2021年7月取代CSV 格式。 在此之前，这两种格式将同时存在于桶中，以便用户能够在闲暇时过渡。 除了更有效率解析外，新文件还使用Gzip压缩以降低存储和下载成本。 我们还花了一些时间来改进不论格式的平衡文件解析性能。 平均解析时间应减少约27%。

对于我们的REST API，我们现在在我们与交易相关的API上暴露了一个 `entity_id` 字段。 此字段代表与该交易类型相关的主要实体。 例如，如果它是一个 HCS 交易，它会被创建、更新或删除。

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

我们继续朝着改用TimmetimeDB的目标取得进展。 我们修复了用户和数据库初始化问题，测试了从 PostgreSQL 进行的迁移。 我们已经将TimmethodDB Helm图转换为更稳定的图，并探索了我们生产的托管选项。 最后，我们已切换到SCRAM-SHA-256，以改善我们数据库用户认证的安全。

### 打破更改：

为了了解这个版本的一些突破性更改。 如果你正在使用我们的头盔图表， 我们已经将进口商从`StatefulSet`转换为`部署`，因为它不再需要持久的数量。 我们还将Traefik依赖从 "部署" 切换到 "DaemonSet" 这两项工作都需要人工干预，才能在升级之前删除旧的工作量。 社区在2020年11月13日之后放弃了对Helm 2的支持，因为它不再是 [supported](https://helm.sh/blog/helm-v2-deposition-timeine/)。 如果你直接从我们的数据库读取，重命名`t_entities`表及其列可能也会影响到你。

## [v0.30.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.30.0)

镜像节点v0.30随着我们持续整合和监测部分的改变而带来操作上的改进。

通过发布，我们已经完成了从 CircleCI 迁移到 GitHub 动作。 CircleCI 由于我们使用 [Testcontainers](https://www.testcontainers.org/)进行针对第三方依赖性的单元测试而有一些限制。 我们先前有一个GitHub Actions and CircleCI 与后者使用的命令略不同于本地测试的混合物。 合并到 GitHub 动作使我们能够减少这种差异并进一步平行我们的核对。

为了提高我们的运行状态可观察性和测试覆盖面，我们在这个周期内继续投资于我们的监测工具。 计划交易支持最近被添加为 `ScheduleCreate` 和 `ScheduleSign` 两个操作。 我们添加了显示器默认配置的三个新主机节点。 监视器无法在多个场景中达到预期的TPS错误已被修复。

REST API 还看到了一些错误修复，包括对信用/借记参数查询的修复，现在只能检索到令牌传输。 交易 API 现在包含所有交易类型的令牌传输列表，而不仅仅局限于加密传输。

## [v0.29.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.29.0)

{% hint style="success" %}
**MAINNET更新：APRIL 5, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 26, 2021**
{% endhint %}

这个版本会在多个REST API的改进模块和改进下带来一系列灯塔。

OA之前的历史实体信息现已提供。 在这个版本中，我们添加了一个可重复的 Java 迁移，它将从主网快照导入实体信息。 此操作在升级期间运行，可配置(`hedera.mirror.importter.importHistoricalAccountInfo`)，并与“hedera.mirror.importter.startDate\`setting”结合使用。

REST API 现在扩展了它的过滤选项支持，具体围绕转让和令牌。 此前只支持 HBAR 转账的 `account.id` 和 `credit/debit` 过滤选项，这个版本会扩展两个过滤器以包括令牌。

REST API 和 `check-state-proof` 包也得到改进。 API现在支持通过`/api/v1/transactions/:transactionId/stateproof?scheduled=true` 以及更紧凑的响应格式为计划中的交易过滤。 对于使用较新的改进的 HAPI v5 版本的记录流，通过状态验证的 API 响应，发送元数据哈希而不是完整的原始字节。 这样，反应就更加轻重。

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

由于增加了一些更强有力的自动分析工具，如“gosec”，以及执行了第三方代码审计的建议，许多模块还改进了安全和标准化工作。

这个版本还看到了一个步骤来支持新的和改进的 v2 优惠(Java SDK)\[https://github.com/hashgraph/hedera-sdk-java\\](https://github.com/hashgraph/hedera-sdk-java/)]。 监测模块和验收测试都已更新，以使用新的 SDK，并使用内部重试和支持计划交易等功能。

## [v0.28.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.28.2)

{% hint style="success" %}
**MAINNET更新：MARCH 17, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：MARCH 10, 2021**
{% endhint %}

这个版本将最终支持计划交易和 HAPI 样本v0.12。 添加了两个针对特定REST API，包括`/api/v1/schedules`和`/api/v1/schedules/:id`。 前者列出了所有有各种筛选选项的计划表，后者按计划ID返回了一个具体计划。

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

在 HAPI v0.12中，在所有实体类型中都添加了新的备忘录字段，使所有服务具有均等。 镜像节点现在支持新的字段，包括更新备注字段可以设置为 `null` 的操作， 空字符串或非空字符串分别保持、清除或替换现有的备注。

历史， 导入器应用程序总是下载流文件并将其保存在文件系统中，然后阅读这些文件并在另一个线程中将它们输入数据库。 这有时导致数据库和文件系统无法同步，需要手动修复。 它还使进口商处于状态，因此不能支持多次运行以获取大量物品。

通过这个版本，我们已经删除了导入器读写到文件系统的需要。 相反，下载器和解析器线程现在通过内存队列进行交流。 为了做到这一点，我们还必须从流文件表中直接移除“t_application_status”表格，以利于计算最后一个成功的文件。 除了解决上述问题外，拆除文件系统还改善了5%的延迟状态。

其他更改包括将 `index` 字段添加到 `record_file` 表，以模拟区块链索引，并将我们的 [Google Marketplace] (https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) 更新到 v0.27。 此外，我们还在 `chec-state-proof` 客户端应用程序中支持v5流文件。

## [0.27.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.27..0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: FEBRUARY 22, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET Upeted: FEBRUARY 11, 2021**
{% endhint %}

这个版本添加了一个新的 REST API 组件实现[Rosetta API](https://www.rosetta-api.org/)。 Rosetta API 是一个与blockchain为导向的系统整合的开放标准。 实施《Rosetta AP》提供了若干好处。 它减少了钱包、交易等所需的时间和精力。 如果他们过去已经与Rosetta集成，就可以与Hedera网络集成。 即使系统集成器以前没有使用Rosetta API，使用Rosetta API代替我们单独的 [REST API](https://docs.hedera)。 om/guides/docs/miror-node-api/cryptocurrency-api可能有助于减少使用像Hedera这样的非区块链DLT的摩擦。

[scheduled transactions](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/spec.md) 是一个新的功能，在未来发布中添加到主节点。 计划中的交易允许在没有所有必要签名的情况下提交交易，并将在所有要求的当事方签署后执行。 镜像节点已更新，以了解和存储这些新类型的交易。 此外，我们已经更新了我们现有的REST API以暴露此信息。 下次发布将添加附加计划特定的 REST API。

我们作出了协调一致的努力，以改进我们的考验。 我们的大多数测试都是固定的，因此我们的连续整合更加顺利。 我们还改善了我们接受测试的稳定性。 REST API 监视器也有一些日志和可用性修复方法来帮助生产的可观察性。

## [v0.26.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.26.0)

{% hint style="success" %}
**MAINNET Upeted: FEBRUARY 1, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JANUARY 22, 2021**
{% endhint %}

这次发布的主要重点是增加对主节点中即将出现的功能的支持。 我们在 HAPI `0.10.0` 中增加了对 `newTotalSupply` 字段的支持。 我们还记录了我们的 [design](https://github.com/hashgraph/hedera-miror-node/blob/master/docs/design/scheduled-transactions.md) 即将发布的 HAPI (https://github.com/hashgraph/hedera-services/blob/master/docs/sched-transactions/spec.md) 服务。 我们的下一个小版本将得到初步支持。

但迄今最大的改动是支持新的记录文件V5和签名文件V5格式。 这些文件被上传到云存储，并被镜像节点拉动以存取其数据库。 因为它是主要节点和镜像节点之间的核心通信格式， 它需要一些重新排序和新代码来支持新格式，同时保留与以前的流文件的兼容性。

#### 警告！ 如果您没有在 HAPI v0.11之前将镜像节点升级到 v0.26.0。 在几周后发布，您的镜像节点将无法处理新的交易。

我们继续在转换到TimmetimeDB方面取得进展。 我们已将时间级数据库头盔图集集成到我们的Kubernetes部署中，并添加了迁移脚本，从PostgreSQL转换到TimmetimeDB。 我们仍然处于测试阶段，所以我们仍然建议现在继续使用 v1 schema (默认)。

## [v0.25.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.25.0)

{% hint style="success" %}
**MAINNET UPDATE COMPLETED: JANUARY 12, 2021**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：JANUARY 8, 2021**
{% endhint %}

这次发布见到了我们新的监测模块的一波增强效果。 [monitor](https://github.com/hashgraph/hedera-miror-node/blob/v0.25.0/docs/monitor.md) 是一个独立的组成部分，可以发布和订阅来自Hedera API的交易，以衡量系统的健康状况。 此版本的新功能是在启动时使用新的表达式语法自动创建实体的能力。 这有助于避免锅炉配置和手动创建实体的步骤因环境而异。

在监视器中添加了一个样品百分比属性，以控制验证REST API 的频率。 我们花了时间适当记录监测工具，详细说明其配置和操作步骤。 最后，我们添加了一些新的指标和Grafana [dashboard](https://github.com/hashgraph/hedera-miror-node/blob/v0.25.0/docs/monitor.md#dashboard--metrics) 以查看它们。

我们在实现使用 [TimescaleDB]取代PostgreSQL 的目标方面取得了进展(https://www.timcee.com/)。 这个版本包含初始数据库迁移，用时间计数器从零开始设置镜像节点。 这些迁移隐藏在特征旗帜下。 在即将发布的版本中，我们将添加更多功能，包括数据迁移脚本和 Helm 支持。

## [v0.24.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.24.0)

{% hint style="success" %}
**MAINNET更新：DecEMBER 28, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：DecEMBER 10, 2020**
{% endhint %}

此版本为我们的 REST API添加了[OpenAPI 3.0](https://swagger.io/规格) 规格支持。 OpenAPI 规格可在 `/api/v1/openapi.yml`上查找，作为我们API的正式规格。 客户端可以通过生成代码或测试工具来缩短它与我们的 API 集成所需的时间。 它还为我们提供了一个新的自动生成的 API 文档网站，可在 `/api/v1/docs` 中查看。

我们现在支持[[AWS 默认凭据提供者链](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/creditals.html)。 现在不是只能在配置中提供静态访问和密钥， 您可以依靠默认的提供商链自动从环境中检索您的凭据(环境变量，"~/")。 (w/creditals\`等)。 更多信息请参阅我们的 [documentation](https://github.com/hashgraph/hedera-miror-node/blob/master/docs/configuration.md#connect-to-s3-with-the-default-credentials-provider)

我们已经加强了我们的监测工具，以便为镜像节点的运作提供更大的观察能力。 除了发布，我们的监测工具现在支持订阅gRPC 和 REST API，以验证Hedera结束功能。 它还将产生这种信息的衡量标准。 我们利用Loki的新日志提醒功能，现在可以提醒我们在日志中看到的可能引起关注的任何错误。

## [v0.23.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.23.0)

{% hint style="success" %}
**MAINNET更新：DecEMBER 2, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET Update COMPLETED: NOVEMBER 2020**
{% endhint %}

本版本的重点是最后确定我们对Hedera API [v0.9.0]提供的新的 Hedera Token 服务 (HTS) 的支持 (https://github.com/hashgraph/hedera-services/releases/tag/v0.9.0)。 没有新的 HTS 功能，只需各种修复就能与最新的原生兼容。 HTS 目前在预览网上启用，应很快在测试网上启用， 所以请尝试一下，如果您有任何反馈，请告诉我们。

一个新的 Helm [chart](https://github.com/hashgraph/hedera-mirror-node/tree/master/charts/hedera-mirror-monitor) 被添加来运行监视应用。 监测工作仍处于繁重的发展阶段，所以一直受到关注。

大多数其他更改都是错误修复。 我们现在使用 SonarCloud 扫描易受伤害性和漏洞，并处理了所有主要项目。 您可以查看我们的SonarCloud [dashboard](https://sonarcloud.io/dashboard?id=hedera-miror-node) 来跟踪我们的进度。 现在只为成功的交易插入实体，并且我们修复了正在更新的错误地址簿。 解决了与状态验证 alpha API 的多个问题。 对于gRPC API，我们提高了它的性能并降低了它的 CPU 使用率。 也与gRPC相关，我们现在启用了服务器发送的存活消息，并允许较低的客户端发送的消息保持在90秒钟内， 希望能够解决一些用户报告的超时问题。

## [v0.22.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.22.0)

这份版本继续改进我们对Kubernetes的支持，并监测和改进各单元的业绩。

我们改进了我们自定义的导入器、GRPC 和 REST API 模块的PromeusRule警报， 此外还为我们的 gRPC 和 REST API 模块添加了仪表板。 此外，我们增加了我们的pod 资源限制，以优化导入器的摄入和gRPC在Kubernetes集群中的流媒体性能。 我们现有的基于js的监视器和REST性能测试都得到了更新，以包括高频医疗系统支持。

我们还改进了我们的数据生成器模块，支持大部分HAPI交易镜像节点导入器进入。 此外，我们还添加了一个基于java的监视模块，支持生成和发布交易。

这个版本还包括一个改进，以避免由于收到余额流文件而产生的陈旧帐户信息错误，其频率比记录流文件要慢一些。 现在帐户创建和帐户信息更改将会在REST API 调用中反映，即使未收到更新的余额。
我们还扩展了我们的 REST API 支持，以包括大小写不敏感的支持查询参数。 `/api/v1/transactions?transfers` 和 `/api/v1/transactions?transactiontype=tokentransfers` 现在都是可以接受的。

## [v0.21.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.21.0)

{% hint style="success" %}
**MAINNET Upeted: NOVEMBER 24, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 13, 2020**
{% endhint %}

这个版本通过添加三个新的令牌REST API来继续关注Hedera Token Service (HTS)。 REST API `/api/v1/tokens` 的代码显示了网络上可用的令牌。 A token REST API `/api/v1/tokens/${tokenId}` shows details for a token on the network. A token supply distribution REST API `/api/v1/tokens/${tokenId}/balances` shows token distribution across accounts. 这些API已经开始预览网，所以请检查它们！

继续我们的 HTS 主题，我们以象征性的支持加强了我们的测试框架。 我们的接受测试可以将 HTS 交易发送到 HAPI ，并等待这些交易通过镜像节点REST API显示。 此外，我们的性能测试可以模拟高频系统负载来测试系统如何响应高频系统交易。

我们通过添加一种按交易类型过滤的方式来改进我们现有的REST API。 当搜索交易或显示特定帐户的交易时，您现在可以通过可选的 `transactionType` 查询参数过滤。 此功能可以在 `/api/v1/transactions?transaction? Type=tokentransfers` 中使用，而账户 API 的格式是 `/api/v1/accounts/0.0.8?transactionType=TOKENTRANS`。

我们通过AlertManager整合，改善了我们的Kubernetes的支持。 现在每个组件都有自定义的 `PrometheusRule` 警报，它们会根据Prometheus 计量触发通知。 创建了一个自定义 Grafana 仪表板，显示当前的射击警报。

## [v0.20.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.20.0)

{% hint style="success" %}
**MAINNET更新：NOVEMBER 11, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：NOVEMBER 3, 2020**
{% endhint %}

这是一个包含支持新的 HAPI 服务和整个新的运行时组件的大版本，可以极大地提高性能。 由于变化幅度之大， 我们确实需要花费更长的时间才能将它作为我们想要确保它在尽可能早的时间内得到测试。

首先支持最近 [announced]的 Hedera 令牌服务 (HTS) (https://hedera.com/blog/previewnet-hedera-token-service-early access) 并扩展到预览网。 在解析方支持新的交易类型方面做了大量工作，包括通过新的表格加强方案并通过记录流获取。 HTS 还需要一个新的平衡文件版本，向CSV添加令牌信息。 现在已为我们现有的REST API返回了令牌信息，而下一次发布将包含令牌特定的REST API以供进一步颗粒化。 在预览网中查看它，让我们知道您是否有任何反馈！

我们在改进以前版本的摄取性能方面取得了长足的进展。 但由于我们也想要确保通过我们的 gRPC API来结束HCS 延迟的低端，我们不得不牺牲其中一些速度。 因此，在延迟超过10秒之前，我们只能摄取每秒大约3,000笔交易。 这完全是由于我们使用 PostgreSQL 通知/监听来通知gRPC API新数据。

在这次发布中，我们在我们支持Redis pub/sub的情况下，添加一个新的通知机制，但不牺牲速度或延迟。 使用Redis，镜像节点现在至少可以摄取10。 00 TPS仍然不足10秒无法提交主题消息并通过镜像节点流API接收。 Redis 默认启用， 但如果HCS延迟不是一个问题并且您想避免另一个运行时依赖，它可以被关闭。

我们还添加了对 v0.9.0中的 HAPI protobuf [changes](https://hedera.com/blog/changesto hedera-api-for-v0-8-0-v0-9-0)的支持。 原始文件正在删除两个废弃的字段，同时添加一个新的 `signedTransactionBytes` 字段。 由于镜像节点仍然需要支持历史交易，我们仍然支持分析包含旧有效载荷格式的交易。

## [v0.19.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.19.0)

{% hint style="success" %}
**MAINNET更新：OCTOBER 6, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 29, 2020**
{% endhint %}

该版本完成了状态校验的 Alpha REST API 并使其[普遍可用](http://www.hedera.com/blog/introducing-state-pro-of-alpha)。 作为其中的一部分，我们对检查状态防空命令行工具作了许多改进，可以查询API并验证本地文件。 我们现在还存储用于验证记录文件的节点帐户，确保更准确地确定状态证据的出处。

最近公共Hedera 环境发生了一些变化，我们已经更新镜像节点以反映这一点。 我们增加了对新预览网环境的支持，我们更新了配置以指明在最近重置后的新测试网桶。 在更新到此版本之前，请确保您的镜像节点包含上一个bucket中的所有数据。 假定您没有手动指定 bucket 名称。

我们为我们的所有组成部分增加了适当的忠诚度和随时准备的终点。 如果你不熟悉亮度和准备状态探测的概念，请参阅Kubernetes [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 如果外部依赖关系像数据库一样关闭，我们的新的主动性端点现在就不会失败，确保应用程序不会不必要地重新启动。 即使您不使用 Kubernetes 也值得注意，以确保您的镜像节点使用适当的终点进行健康检查， 基于您的需要。

## [v0.18.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.18.2)

{% hint style="success" %}
**MAINNET更新：SEPTEMBER 22, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET更新：SEPTEMBER 15, 2020**
{% endhint %}

在0.18号释放火车中修复两次倒退。

## [v0.18.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.18.1)

包含对状态Proof Alpha REST API 的小改动，现在只返回当前地址簿。

## [v0.18.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.18.0)

基于[State Proof Alpha REST API](https://github.com/hashgraph/hedera-miror-node/blob/v0.18.0/docs/design/statesproofalpha.md)，我们已在上次版本中添加[样本代码](https://github)。 JavaScript中的om/hashgraph/hedera-mirror-node/blob/v0.18.0/hedera-miror-rest/state-protro-demo）从镜像节点检索状态证明并在本地进行验证。 这使得用户能够获得关于某项交易发生在黑德拉的加密证明。 可以独立检查证据的有效性，以确保Hedera的主体绝大多数已经就该项交易达成共识。 与最终状态证明的许诺相似，用户可以信任镜像节点提供的状态验证阿尔法， 即使用户不信任镜像节点。

导入器现在可以配置为通过 [AssumeRole]使用临时安全凭据连接到 Amazon (S3)(https://docs.aws.amazon.com/STS/latest/AAPIReference/API\\_AsumeRole.html)。 这样，没有权限访问AWS资源的用户可以请求一个临时角色，给予他们这个权限。 欲了解更多信息，请参阅[配置文件](https://github.com/hashgraph/hedera-miror-node/blob/v0.18.0/docs/configuration.md#connect-to-s3-with-assumerole)。

导入器还添加了两个新属性来控制它应该下载和验证的数据的子集。 `hedera.mirror.importter.startDate`属性可以用来排除此日期之前的数据和“快速”到利益相关时点。 默认， `startDate`将被设置为当前时间，以便镜像节点操作员能够以最新的数据快速启动和运行，并降低云存储检索成本。 请注意，此属性仅适用于导入器的首次启动，此后无法更改。 `hedera.mirror.importter.endDate`属性可以用来排除在此日期之后的数据并停止进口。 默认情况下，它被设置为将来的日期，因此它实际上永远不会停止。

### 打破更改

上述`startDate`属性确实改变了镜像节点操作员从先前的发布开始的方式。 默认设置为现在。 站立一个新镜像节点的用户将不再检索所有历史数据，而只检索最新数据。 当前用户升级到这个版本将不会受到影响，即使他们的数据没有完全捕获，因为这个属性只有在数据库首次启动时空时才会适用。 若要恢复到以前的行为，过去的日期可以指定为 Unix edoch 的 `1970-01-01T00:00:00Z` 。

## [v0.17.3](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.17.3)

{% hint style="success" %}
**MAINNET 升级：SEPTEMBER 14. 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：SEPTEMBER 3, 2020**
{% endhint %}

这个版本包含一个错误修复的端口，以便更好地管理 `VertxException: Thread blocked` 问题，见于 [#945](https://github.com/hashgraph/hedera-mirror-node/issues/945)

## [v0.17.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.17.2)

在网络环境中进行流重置时，一个小错误修复可以更好地支持反射镜像节点

## [v0.17.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.17.1)

一个小的修复，用来纠正性能回归，没有适当缓存大量使用的查询。

## [v0.17.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.17.0)

这个版本会从文件`0.0.101`和`0.0.102`中的镜像节点数据库中添加支持存储网络地址簿。
镜像节点现在将从数据库中检索包含节点标识符及其公钥的文件地址簿内容，而不是启动时的文件系统。

This sets the stage for an additional feature which is the State Proof alpha REST API at `/transactions/${transactionId}/stateproof`.\
With this release it is possible to request the address book, record file and signature files that contain the contents of a transaction and allow for cryptographic verification of the transaction. 镜像节点用户现在可以为自己积极验证提交的交易。

其他更改包括支持使用 [Github Actions](https://github.com/features/actions) 进行连续部署 (CD)，使用 [FluxCD](https://fluxcd.io/) 将主版本部署到 Kubernetes 集群。 此外，这种版本包括修复数据库复制操作最优化和更好地处理复制大主题信息时使用的缓冲区大小。

## [v0.16.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.16..0)

{% hint style="success" %}
**TESTNET 升级: AUGUST 18, 2020**
{% endhint %}

这个版本包括未来一些更大功能的基础。 值得注意的是，云雾剂名称现在是根据网络规格设定的，用户不再需要明确说明云雾剂名称来演示， 测试和主网络。 `record_file`表内容也扩展到包括其包含交易的开始和结束协商一致的时间戳。 `record_file`表也看到了清理以移除文件路径。

此外，这个版本将使用共享资源的通用图精简头盔图形结构。 它还添加依赖关系，以方便依赖关系更新管理。 解析器还更新了在多个时间桶群组中处理签名文件，以便更大程度地解析抢劫。

解析器中的内存改进也是为了提高摄取性能。 由于性能，为了支持更快流传入的主题消息，也删除了直接psql通知。

## [v0.15.3](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.15.3)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

通过pg\_notification 发送大宗JSON有效载荷的问题，现在忽略它们。 当一个信息超过5824字节时就会出现这种情况，这个信息也非常接近原始布局限制。

## [v0.15.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.15.2)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：7月20, 2020**
{% endhint %}

这次发布提高了上次发布时出现倒退的主题信息速率。 这只是一个制止的空白，今后的释放将进一步增加。

## [v0.15.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.15.1)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：2005年7月15, 2020**
{% endhint %}

一个热修复版本，用新的共识消息区块头解决两个高度优先解析错误。

## [v0.15.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.15.0)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：2005年7月15, 2020**
{% endhint %}

这个版本增加了对HCS主题碎裂的支持，它很快将会在 0.6.0 版本中推出到主节点。 对于不适合最大交易大小的6144字节的较大共识信息， 一个标准的区块信息头可以提供来说明如何将该消息分割成较小的消息。 镜像节点现在理解这个区块信息并将其存储在数据库中。 Additionally, it will return this [data](https://github.com/hashgraph/hedera-mirror-node/blob/a2f69ee1243fbbbfbc133549f9162bfc3a08f464/hedera-mirror-protobuf/src/main/proto/com/hedera/mirror/api/proto/ConsensusService.proto#L58) when subscribing to the topic via the gRPC API. 正在更新 Java SDK 以酌情自动拆分和重建此消息。

其他变化包括端口优化以结束gRPC API的延迟。 这主要是通过添加一个使用 PostgreSQL 的 ListEN/NOTIFY 功能的 `NotifyingTopicListener` 来实现的。

## [v0.14.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.14.1)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：2005年7月15, 2020**
{% endhint %}

该版本进一步优化摄入率。 初步测试表明有2x至3x的改进。

## [v0.14.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.14.0)

{% hint style="success" %}
**MAINNET 升级：2005年7月29, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：2005年7月15, 2020**
{% endhint %}

这个版本完全是关于性能优化。 我们重新研制了一些外国钥匙，以改善摄入性能，每秒几千笔交易。 我们还解决了 gRPC API 的内存问题，并在该领域进行了一些优化。

除了业绩外，我们还作出了一些小的改进。 我们现在设置 `topicRunningHashV2AddedTimestamp` 为主网的默认值，如果没有提供一个值，它不会在启动时失败。 增加了容器化验和性能测试，使得更容易在规模上进行测试。

#### 打破更改

我们删除了 `hedera.mirror.grpc.listener.bufferInitial` 和 `hedera.mirror.grpc.listener.bufferSize` 属性，因为我们删除了共享的 poller's 缓冲区。

我们还重命名了一些表格和列，如果您直接使用数据库结构，会影响您。 我们将`t_transactions`更名为`transferlists`，`t_crypto_transfer`，`non_fee_transfers`，更名为`non_fee_transfer`。

## [v0.13.2](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.13.2)

{% hint style="success" %}
**MAINNET 升级：第二天，2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：JUNE 23, 2020**
{% endhint %}

错误修复释放以解决与 gRPC API的内存问题。

## [v0.13.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.13.1)

{% hint style="success" %}
**MAINNET 升级：第二天，2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：JUNE 23, 2020**
{% endhint %}

小错误修复释放以解决Grpc NETTY 阻止验收测试

## [v0.13.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.13.0)

{% hint style="success" %}
**MAINNET 升级：第二天，2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：JUNE 23, 2020**
{% endhint %}

这个版本是一个较小的版本，主要侧重于微小增强功能的错误修复。 我们添加了一个用于测试的新属性 'hedera.mirror.importter.downloader.endpointOverride'。 我们还添加 `hedera.mirror.importter.downloader.gcpProjectId` 以支持指定请求者使用个人帐户支付凭据。 最后，我们改进了我们的市场支持，使我们更接近于提供这种支持。

## [v0.12.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.12.0)

{% hint style="success" %}
**TESTNET 升级：至少29, 2020**
{% endhint %}

这个功能版本包含了一些精彩的添加，同时修复了一些关键的漏洞。 我们在将我们的应用程序添加到[Google 云平台市场](https://console.cloud.google.com/marketplace)方面取得了良好进展。 这将很快结束并启用镜像节点“一次点击部署”到 Google的云端。 此外，我们的API添加了一些额外的字段。 我们在 REST 和 GRPC API中添加 `runningHashVersion` 。 最后，我们在 REST API中添加 "transactionHash" 。

在我们的性能测试环境中，我们将进口商的摄取率从每秒3 400次提高到5 600次。 还有改进的余地，我们计划在即将发布的版本中实现更多的性能优化。

### 打破更改

我们添加了一个选项，在验证后保留签名文件。 默认情况下，我们不再在文件系统上存储签名。 如果你想恢复旧的行为并保留签名，你可以设置 `hedera.mirror.importter.downloader.record.keeping Signatures=true` 和 `hedera.mirror.importter.downloader.balance.keeplignatures=true`。

我们改变了此版本中的旁路散列不匹配的行为。 Bypassing 散列不匹配可以与其他参数结合使用，以快速向前镜像节点到较新的数据或克服流重置。 以前，您必须在`t_application_status`中通过数据库值指定这一点。 由于此数据不是应用程序状态，但被认为是更多用户提供的值，因此我们为此目的添加了一个新属性`hedera.mirror.importter.verifyHashafter=2020-06-05T17:16:00.384877454Z`。

## [v0.11.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.11.0)

{% hint style="success" %}
**MAINNET 升级：JUNE 10, 2020**
{% endhint %}

{% hint style="success" %}
**TESTNET 升级：至少29, 2020**
{% endhint %}

这次发布的主要重点是重新计算代码和属性，作为今后增强能力的必要步骤。 我们还继续改进我们对Kubernetes的支持。 为此目的，我们添加了Prometheus REST 计量、Helm 测试和镜像节点现在可以在GKE中运行。

我们在所有主题相关的REST API中添加了一个新参数，返回一个纯文本而不是二进制的主题消息。 提交给HAPI的消息以二进制形式提交，并且也以这种方式存储在镜像节点中。 如果您知道消息实际上是以 UTF-8 编码的字符串， 然后您可以设置 `encoding=utf-8`，REST API 将尽最大努力转换为字符串。 默认情况下，或者如果您通过 `encoding=base64`的查询参数，它将返回以 base64 编码的二进制函数。

**打破变更**

请注意升级时我们对我们配置属性的命名进行了重大的突破。 我们已将所有`hedera.mirror.api`属性重命名为`hedera.mirror.rest`。 我们还将属性`apiUsername`重命名为`restUsername`和`apiPassword`命名为`restPassword`也是为了反映这一点。 导入器模块使用的任何属性被重命名为`hedera.mirror.importter`。 我们对任何不便表示歉意。

我们已经删除了 `hedera.mirror.addressBookPath` 属性，以利于“hedera.mirror.importter.initialAddressBook`属性。 前者加载过多，既是最初的引导地址簿，也是正在通过文件交易更新的 '0.0.102' 现场地址簿。 The live address book is now hardcoded to`${hedera.mirror.importer.dataPath}/addressbook.bin\` and cannot be changed.

通过协商一致的时间戳检索主题消息的 REST API 现在支持复数("/topics/messages/:consend Timestamp")和单数("/topic/message/:consensionTimestamp") URI 路径。 单数格式已经废弃，不久将会被淘汰，因此请尽快更新到复数格式。

我们删除了少数alpha主题REST API的单一形式。 `/topic/:id/message` API已经被移除，以方便复数格式 `/topics/:id/messages` 。 同样，`/topic/:id/message/:sequencenber`API 被移除，以利其复数形式`/topics/:id/messages/:sequencenber`。 请相应更新。

## [v0.10.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.10.1)

小错误修复版本以解决REST API 包装问题。

## [v0.10.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.10.0)

为了准备Hedera Node release 0.5.0，我们正在释放v0.10.0来支持最新版本的 [HAPI](https://docs.hedera.com/guides/docs/hedera-api)。 更改包括重命名索赔为Livehash和新的响应码。 一个重要的 HAPI 更改是在交易记录中添加 `topicRunningHashVersion` 。 此更改是必要的，因为运行散列的主题会随着0.5.0的发布而改变。 结果， Hedera Mirror Node 将此新字段添加到数据库中，并根据发布日期0，正在进行迁移以使用新版本或旧版本。 0。

不幸的是，这需要添加一个必需的字段 `hedera.mirror.topicrunningHashV2AddedTimestamp` 来控制这种行为，如果不被使用，启动时将失败。 这只是一项临时措施。 一旦Hedera Node 0.5.0发布到测试网和主网，我们将更新这个节点，以便它自动填充已知日期。

其它更改包括添加 Google PubSubSubb 支持发布JSON 代表`Transaction` 和 `TransactionRecord` 的信息队列，供外部消费。 我们还添加了REST API 计量并添加了 Traefik 作为我们头盔图的 API 网关。

#### 打破更改

我们必须移除我们的事件流支持。 该守则的这个领域从未启用，未经测试，而且在没有提供任何好处的情况下举债。 如果今后有必要，我们可以在我们新近重新审议的框架内重新添加。

在 0.9 中添加的新的 `/api/v1/topics/:id` alpha REST API 已更改为 `/api/v1/topics/:id/messages` 。 进行此更改是为了使API与其他主题消息 API一致，因为它是指消息实体，而不是主题实体。

## [v0.9.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.9.1)

小错误修复版本，地址无法处理涉及多个交易的地址簿更新。

## [v0.9.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.9.0)

此版本包含了我们[协商一致服务](https://www.hedera.com/consensus-service)的另一个新的 REST API。 您现在可以检索某个特定主题中的所有话题信息，并按序列号和协商一致的时间戳进行过滤。 下面是一个示例：

`GET /api/v1/topic/7?sequencenber=gt:2&timestamp=lte:123456789.0000006&limit=2`

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

此版本的另一个主要功能是 [Kubernetes](https://kubernetes.io/) 支持。 我们已经创建了一个 [Helm](https://helm.sh/) 图，可以用来部署一个高度可用的 Mirror 节点和单个命令。 在我们努力将我们目前的部署转向这种新办法时，这一特点仍然处于很大的发展阶段。

## [v0.8.1](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.8.1)

小错误修复释放以修复包装问题。

## [v0.8.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.8.0)

镜像节点 v0.8.0 在这里！ 我们在使镜像节点更容易运行和管理方面取得了长足进展。 我们特别增加了对 [requester pays] (https://docs.aws.amazon.com/AmazonS3/latest/dev/RequestterPaysBuckets.html) 的支持。 这将允许任何人运行镜像节点，只要他们愿意支付检索数据的费用。 目前只有赫德拉和几个伙伴能够访问该桶，所以使它能够为我们的社区打开该数据。 我们仍然在努力将桶迁移到这种模式，因此我们将继续给予关注。

我们还添加了两个新的实验REST API以检索HCS数据。 Firstly, we added `/api/v1/topic/message/${consensusTimestamp}` to retrieve a topic message by its consensus timestamp. Secondly, we added `/api/v1/topic/${topic}/message/${seqNum}` to retrieve a particular topic message by its sequence number from a topic. 这些API被认为是透明的，并可能在将来改变或删除。 我们还大大增加了REST API的测试覆盖面，并且在过程中冲洗了一些漏洞。

对于我们的GRPC API，我们必须从R2DBC切换到休眠，以达到我们所需要的规模和稳定性。 在这样做的时候，我们现在可以支持更多的同时订阅者，提高输送量。 它还应最终消除与抵抗阵线有关的任何稳定关切。

我们不得不作出一些突破性的改变。 在文件系统插入数据库后，我们现在不再写入和存储记录或平衡文件。 如果你仍然需要这些文件，你可以设置 `hedera.mirror.parser.balance.keepFiles` 和 `hedera.mirror.parser.record.keepFiles` 为真。

此外，我们将持续存在的属性移动到一个新的路径下。 这是我们移动的选项，如`hedera.mirror.parser.record.dur.transactionBytes` 到 `hedera.mirror.parser.record.dur.transactionBytes` 。 请相应更新您的本地配置。

## [v0.7.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.7.0)

0.7.0侧重于重新排列记录文件解析，将解析与持续的数据分离。 这种重新计算正在为进一步改进性能奠定基础，并使更多的下游组成部分能够登记交易通知。

## [v0.6.0](https://github.com/hashgraph/hedera-miror-node/releases/tag/v0.6.0)

- 新闻稿的重点是稳定性和业绩的改善。
- 结束测试覆盖。

这次发布的主要重点是加强镜像节点的稳定性和性能。 我们提高了交易接收速度，从每秒600次提高到大约4000次。 与此同时，我们大大提高了GRPC模块的复原力和性能。 我们还增加了接受测试，以测试结束时的氢氟碳化合物。

**打破变化**

请注意，此版本中可能出现的一个突破性变化是拒绝订阅不存在的主题。 这就避免了服务器不得不反复投票，直到它创建并为一个可能永远不存在的主题占用资源。 预期客户端或 [SDK](https://github)。 om/hashgraph/hedera-sdk-java/issues/367将在创建主题后定期投票，直到该主题转到镜像节点。 此功能隐藏在功能标志上，但将在下个月缓慢推出。

## v0.5.3

- 现在支持所有的 HCS 功能，包括流媒体gRPC API 用于消息主题订阅。
- 镜像节点如何验证主机共识。 镜像节点现在等待至少三分之一的节点签名，而不是三分之二以上的节点签名来核实共识。
- 添加新的 mainnet 节点到镜像节点地址簿。
- 访问仍然仅限白名单上的IP地址。 请求访问 [here](https://learn.hedera.com/l/576593/2020-01-13/7z5jb)。
- 请查看完整的更改列表 [here]的 Mirror 节点发布页面(https://github.com/hashgraph/hedera-mirror-node/releases)。
- 我们偶尔会遇到这样一种情况：消息往返旅行时间又出现15-20秒的延迟，用户连接被放弃。 没有丢失信息，协商一致的时间不受影响。 鼓励客户端重新连接。 这个问题将在Hedera 镜像节点随后的发布中解决。 一些第三方镜像节点不应受到这个问题的影响。 我们也不期望它对交易产生影响，使用镜像节点的 REST 终端点。
