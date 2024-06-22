# FileUpdate

## FileUpdateTransactionBody

Modify the metadata and/or contents of a file. If a field is not set in the transaction body, the corresponding file attribute will be unchanged. This transaction must be signed by all the keys in the key list of the file being updated. If the keys themselves are being update, then the transaction must also be signed by all the new keys.

| Field            | Type                                       | Description                                                                           |
| ---------------- | ------------------------------------------ | ------------------------------------------------------------------------------------- |
| `fileID`         | [FileID](../basic-types/fileid.md)         | The ID of the file to update                                                          |
| `expirationTime` | [Timestamp](../miscellaneous/timestamp.md) | The new expiry time (ignored if not later than the current expiry) |
| `keys`           | [KeyList](../basic-types/keylist.md)       | The new list of keys that can modify or delete the file                               |
| `contents`       | bytes                                      | The new contents that should overwrite the file's current contents                    |
| `memo`           | string                                     | The memo associated with the file (UTF-8 encoding max 100 bytes)   |
