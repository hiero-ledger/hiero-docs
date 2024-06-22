# NodeAddress

The metadata for a Node – including IP Address, and the crypto account associated with the Node.

| Field             | Type                                           | Description                                                                                                                                                                       |
| ----------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ipAddress`       | bytes                                          | The ip address of the Node with separator & octets \[deprecated v0.13.0] |
| `portno`          | int32                                          | The port number of the grpc server for the node \[deprecated v0.13.0]                        |
| `memo`            | bytes                                          | The memo field of the node \[deprecated v0.13.0]                                             |
| `RSA_PubKey`      | string                                         | The RSA public key of the node.                                                                                                                                   |
| `nodeId`          | int64                                          | A non-sequential identifier for the node                                                                                                                                          |
| `nodeAccountId`   | [AccountId](accountid.md)                      | The account to be paid for queries and transactions sent to this node                                                                                                             |
| `nodeCertHash`    | bytes                                          | A hash of the X509 cert used for gRPC traffic to this node                                                                                                                        |
| `serviceEndpoint` | repeated [ServiceEndpoint](serviceendpoint.md) | A node's service IP addresses and ports \[Added v0.13.0]                                     |
| `description`     | string                                         | A description of the node, with UTF-8 encoding up to 100 bytes \[Added v0.13.0]              |
| `stake`           | int64                                          | The amount of tinybars staked to the node \[Added v0.13.0]                                   |
