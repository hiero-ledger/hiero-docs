# TransactionResponse

When the client sends the node a transaction of any kind, the node replies with this, which simply says that the transaction passed the precheck (so the node will submit it to the network) or it failed (so it won't). To learn the consensus result, the client should later obtain a receipt (free), or can buy a more detailed record (not free).

| Field             | Type                                             | Description           |
| ----------------- | ------------------------------------------------ | --------------------- |
| `nodeId`          | [AccountId](../basic-types/accountid.md)         | The node ID.          |
| `transactionHash` | byte                                             | The transaction hash. |
| `transactionId`   | [TransactionId](../basic-types/transactionid.md) | The transaction ID.   |
