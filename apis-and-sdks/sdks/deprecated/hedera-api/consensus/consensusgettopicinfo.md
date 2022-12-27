# ConsensusGetTopicInfo

## ConsensusGetTopicInfoQuery

| Field     | Type                                                                                                                                                 | Description                                                                                                                                         |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`  | ​[QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md)​ | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `topicID` | ​[TopicID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md)​     | The Topic for which information is being requested                                                                                                  |

‌

## ConsensusGetTopicInfoResponse <a href="#consensusgettopicinforesponse" id="consensusgettopicinforesponse"></a>

‌

Retrieve the parameters of and state of a consensus topic.

| Field       | Type                                                                                                                                                        | Description                                                                                                       |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `header`    | ​[ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md)​     | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither. |
| `topicID`   | ​[TopicID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md)​            | Topic identifier.                                                                                                 |
| `topicInfo` | ​[ConsensusTopicInfo](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/consensus-service/broken-reference/README.md)​ | Current state of the topic                                                                                        |

​\\
