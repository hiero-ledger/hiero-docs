# FileUpdate

## FileUpdateTransactionBody

Modify the metadata and/or contents of a file. If a field is not set in the transaction body, the corresponding file attribute will be unchanged. This transaction must be signed by all the keys in the key list of the file being updated. If the keys themselves are being update, then the transaction must also be signed by all the new keys.

| Field            | Type                                                                                                                                        | Description                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `fileID`         | [FileID](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md)    | The ID of the file to update                                       |
| `expirationTime` | [Timestamp](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md) | The new expiry time (ignored if not later than the current expiry) |
| `keys`           | [KeyList](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/file-service/broken-reference/README.md)   | The new list of keys that can modify or delete the file            |
| `contents`       | bytes                                                                                                                                       | The new contents that should overwrite the file's current contents |
| `memo`           | string                                                                                                                                      | The memo associated with the file (UTF-8 encoding max 100 bytes)   |
