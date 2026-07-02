# Configuration

1. [BlockStreamConfig](#blockstreamconfig): contains the configuration for the Block Stream Simulator logic.
2. [BlockGeneratorConfig](#blockgeneratorconfig): contains the configuration for the Block Stream Simulator generation module.
3. [SimulatorStartupDataConfig](#simulatorstartupdataconfig): contains the configuration for the Block Stream Simulator startup data.
4. [UnorderedStreamConfig](#unorderedstreamconfig): contains the configuration for the Unordered Stream Simulator logic.
5. [ConsumerConfig](#consumerconfig): contains the configuration for the Consumer Simulator logic.
6. [GrpcConfig](#grpcconfig): contains the configuration for the gRPC communication with the Block-Node.
7. [PrometheusConfig](#prometheusconfig): contains the configuration for the Prometheus agent.

## BlockStreamConfig

Uses the prefix `blockStream` so all properties should start with `blockStream.`

| Key                            | Description                                                                                                                         |      Default Value |
|:-------------------------------|:------------------------------------------------------------------------------------------------------------------------------------|-------------------:|
| `simulatorMode`                | The desired simulator mode to use, it can be `CONSUMER`, `PUBLISHER_CLIENT` or `PUBLISHER_SERVER`.                                  | `PUBLISHER_CLIENT` |
| `lastKnownStatusesCapacity`    | The store capacity for the last known statuses.                                                                                     |               `10` |
| `delayBetweenBlockItems`       | The delay between each block item in nanoseconds, only applicable when streamingMode is `CONSTANT_RATE`                             |        `1_500_000` |
| `maxBlockItemsToStream`        | The maximum number of block items to stream before stopping                                                                         |          `100_000` |
| `streamingMode`                | Can either be `CONSTANT_RATE` or `MILLIS_PER_BLOCK`                                                                                 | `MILLIS_PER_BLOCK` |
| `millisecondsPerBlock`         | If streamingMode is `MILLIS_PER_BLOCK` this will be the time to wait between blocks in milliseconds                                 |            `1_000` |
| `blockItemsBatchSize`          | The number of block items to send in a single batch, however if a block has less block items, it will send all the items in a block |            `1_000` |
| `midBlockFailType`             | The type of failure to occur while streaming. It can be `NONE`, `ABRUPT` or `EOS`                                                   |             `NONE` |
| `midBlockFailOffset`           | The index where the failure will occur, only applicable if midBlockFailType is not `NONE`                                           |                `0` |
| `endStreamMode`                | The mode to use when ending the stream                                                                                              |             `NONE` |
| `endStreamEarliestBlockNumber` | The earliest block number for EndStream                                                                                             |                `0` |
| `endStreamLatestBlockNumber`   | The latest block number for EndStream                                                                                               |                `0` |
| `endStreamFrequency`           | The frequency of EndStream in terms of number of blocks                                                                             |                `0` |

## BlockGeneratorConfig

Uses the prefix `generator` so all properties should start with `generator.`

| Key                       | Description                                                                                                 | Default Value |
|:--------------------------|:------------------------------------------------------------------------------------------------------------|--------------:|
| `generationMode`          | The desired generation Mode to use, it can only be `DIR` or `CRAFT`                                         |       `CRAFT` |
| `minEventsPerBlock`       | The minimum number of events per block                                                                      |           `1` |
| `maxEventsPerBlock`       | The maximum number of events per block                                                                      |          `10` |
| `minTransactionsPerEvent` | The minimum number of transactions per event                                                                |           `1` |
| `maxTransactionsPerEvent` | The maximum number of transactions per event                                                                |          `10` |
| `folderRootPath`          | If the generationMode is `DIR` this will be used as the source of the recording to stream to the Block-Node |            `` |
| `paddedLength`            | On the `BlockAsFileLargeDataSets` implementation, the length of the padded left zeroes `000001.blk.gz`      |          `36` |
| `fileExtension`           | On the `BlockAsFileLargeDataSets` implementation, the extension of the files to be streamed                 |     `.blk.gz` |
| `startBlockNumber`        | The block number to start streaming from                                                                    |           `0` |
| `endBlockNumber`          | The block number to stop streaming at                                                                       |          `-1` |
| `invalidBlockHash`        | If set to true, will send invalid block root hash                                                           |       `false` |

## SimulatorStartupDataConfig

Uses the prefix `simulator.startup.data` so all properties should start with `simulator.startup.data.`

| Key                        | Description                                                      |                              Default Value |
|:---------------------------|:-----------------------------------------------------------------|-------------------------------------------:|
| `enabled`                  | Whether the startup data functionality is enabled                |                                    `false` |
| `latestAckBlockNumberPath` | Path to the file containing the latest acknowledged block number | `/opt/simulator/data/latestAckBlockNumber` |
| `latestAckBlockHashPath`   | Path to the file containing the latest acknowledged block hash   |   `/opt/simulator/data/latestAckBlockHash` |

## UnorderedStreamConfig

Uses the prefix `unorderedStream` so all properties should start with `unorderedStream.`

| Key                      | Description                                                              | Default Value |
|:-------------------------|:-------------------------------------------------------------------------|--------------:|
| `enabled`                | Whether to enable the unordered streaming mode                           |       `false` |
| `availableBlocks`        | The numbers of the blocks which will be considered available for sending |      `[1-10]` |
| `sequenceScrambleLevel`  | The coefficient used to randomly scramble the stream                     |           `0` |
| `fixedStreamingSequence` | The numbers of the blocks which will form the steam                      |     `1,2,4,3` |

## ConsumerConfig

Uses the prefix `consumer` so all properties should start with `consumer.`

| Key                     | Description                                                                                             | Default Value |
|:------------------------|:--------------------------------------------------------------------------------------------------------|--------------:|
| `startBlockNumber`      | The block number from which to start consuming                                                          |          `-1` |
| `endBlockNumber`        | The block number at which to stop consuming                                                             |          `-1` |
| `slowDownType`          | The type of slow down to apply while consuming, it can be `NONE`, `FIXED`, `RANDOM`, `RANDOM_WITH_WAIT` |        `NONE` |
| `slowDownMilliseconds`  | The slowdown in milliseconds                                                                            |           `2` |
| `slowDownForBlockRange` | The range of blocks to apply the slowdown                                                               |       `10-30` |

## GrpcConfig

Uses the prefix `grpc` so all properties should start with `grpc.`

| Key             | Description                | Default Value |
|:----------------|:---------------------------|--------------:|
| `serverAddress` | The host of the Block-Node |   `localhost` |
| `port`          | The port of the Block-Node |       `40840` |

## PrometheusConfig

Uses the prefix `prometheus` so all properties should start with `prometheus.`

| Key                  | Description                            | Default Value |
|:---------------------|:---------------------------------------|--------------:|
| `endpointEnabled`    | Whether Prometheus endpoint is enabled |       `false` |
| `endpointPortNumber` | Port number for Prometheus endpoint    |       `16007` |
