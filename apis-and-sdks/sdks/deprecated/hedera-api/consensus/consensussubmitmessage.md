# ConsensusSubmitMessage

## ConsensusMessageChunkInfo

| Field                  | Type                                                                                                                                                 | Description                                                                                      |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `initialTransactionID` | [TransactionID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md) | TransactionID of the first chunk, gets copied to every subsequent chunk in a fragmented message. |
| `total`                | int32                                                                                                                                                | The total number of chunks in the message.                                                       |
| `number`               | int32                                                                                                                                                | The sequence number (from 1 to total) of the current chunk in the message.                       |

## ConsensusSubmitMessageTransactionBody

| Field       | Type                                                                                                                                           | Description                                                                         |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| `topicID`   | [TopicID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md) | Topic to submit message to.                                                         |
| `message`   | bytes                                                                                                                                          | Message to be submitted. Max size of the Transaction (including signatures) is 6kB. |
| `chunkInfo` | [ConsensusMessageChunkInfo](consensussubmitmessage.md#consensusmessagechunkinfo)                                                               | Optional information of the current chunk in a fragmented message.                  |
