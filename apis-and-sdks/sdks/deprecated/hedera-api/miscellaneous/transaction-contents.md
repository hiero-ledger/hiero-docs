# TransactionContents

## SignedTransaction

| Field       | Type         | Description                                                                  |
| ----------- | ------------ | ---------------------------------------------------------------------------- |
| `bodyBytes` |              | TransactionBody serialized into bytes, which needs to be signed              |
| `sigMap`    | SignatureMap | The signatures on the body with the new format, to authorize the transaction |
