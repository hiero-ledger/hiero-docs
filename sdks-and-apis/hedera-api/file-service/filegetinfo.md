# FileGetInfo

## FileGetInfoQuery

Get all of the information about a file, except for its contents. When a file expires, it no longer exists, and there will be no info about it, and the fileInfo field will be blank. If a transaction or smart contract deletes the file, but it has not yet expired, then the fileInfo field will be non-empty, the deleted field will be true, its size will be 0, and its contents will be empty. Note that each file has a FileID, but does not have a filename.

| Field    | Type                                                                                                                                          | Description                                                                                                                                         |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `fileID` | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md)      | The file ID of the file for which information is requested                                                                                          |

## FileGetInfoResponse

Response when the client sends the node FileGetInfoQuery

| Field      | Type                                                                                                                                             | Description                                                                                                      |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `header`   | [ResponseHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md) | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `fileInfo` | [FileGetInfoResponse.FileInfo](filegetinfo.md#filegetinforesponse-fileinfo)                                                                      | The information about the file (a state proof can be generated for this)                                         |

## FileGetInfoResponse

Response when the client sends the node FileGetInfoQuery

| Field            | Type                                       | Description                                                                                                                          |
| ---------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `fileID`         | [FileID](../basic-types/fileid.md)         | The file ID of the file for which information is requested                                                                           |
| `size`           | int64                                      | Number of bytes in contents                                                                                                          |
| `expirationTime` | [Timestamp](../miscellaneous/timestamp.md) | The current time at which this account is set to expire                                                                              |
| `deleted`        | bool                                       | True if deleted but not yet expired                                                                                                  |
| `keys`           | [KeyList](../basic-types/keylist.md)       | One of these keys must sign in order to modify or delete the file                                                                    |
| `memo`           | string                                     | The memo associated with the file (UTF-8 encoding max 100 bytes)                                                                     |
| `ledger_id`      | bytes                                      | The ledger ID the response was returned from; please see [HIP-198](https://hips.hedera.com/hip/hip-198) for the network-specific IDs |
