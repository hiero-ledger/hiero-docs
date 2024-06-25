# FileGetContents

## FileGetContentsQuery

Get the contents of a file. The content field is empty (no bytes) if the file is empty.

| Field    | Type                                                                                                                                          | Description                                                                                                                                                                            |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [QueryHeader](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `fileID` | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md)      | The file ID of the file whose contents are requested                                                                                                                                   |

## FileGetContentsResponse

Response when the client sends the node FileGetContentsQuery

| Field          | Type                                                                                               | Description                                                                                                                      |
| -------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `header`       | [ResponseHeader](../miscellaneous/responseheader.md)                                               | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `fileContents` | [FileGetContentsResponse.FileContents](filegetcontents.md#filegetcontentsresponse) | the file ID and contents (a state proof can be generated for this)                                            |

## FileGetContentsResponse

Response when the client sends the node FileGetContentsQuery

| Field      | Type                               | Description                                               |
| ---------- | ---------------------------------- | --------------------------------------------------------- |
| `fileID`   | [FileID](../basic-types/fileid.md) | The file ID of the file whose contents are being returned |
| `contents` | FileContents                       | The bytes contained in the file                           |
