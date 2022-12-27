# CryptoGetAccountBalance

## CryptoGetAccountBalanceQuery

Get the balance of a cryptocurrency account. This returns only the balance, so it is a smaller and faster reply than CryptoGetInfo, which returns the balance plus additional information.

| Field        | Type                                                                                                                                                     | Description                                                                                                                                         |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`     | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| **one of:**  |                                                                                                                                                          |                                                                                                                                                     |
| `accountID`  | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)   | The account ID for which information is requested                                                                                                   |
| `contractID` | [ContractID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)  | The contract ID for which information is requested                                                                                                  |

## CryptoGetAccountBalanceResponse

Response when the client sends the node CryptoGetAccountBalanceQuery

| Field           | Type                                                                                                                                                        | Description                                                                                                      |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header`        | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md) | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `accountID`     | [AccountID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)      | The account ID that is being described (this is useful with state proofs, for proving to a third party)          |
| `balance`       | uint64                                                                                                                                                      | The current balance, in tinybars                                                                                 |
| `tokenBalances` | [TokenBalance](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/cryptocurrency-accounts/broken-reference/README.md)   | The array of tokens that the account possesses                                                                   |
