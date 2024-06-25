# Transaction

A single signed transaction, including all its signatures. The SignatureList will have a Signature for each Key in the transaction, either explicit or implicit, in the order that they appear in the transaction. For example, a CryptoTransfer will first have a Signature corresponding to the Key for the paying account, followed by a Signature corresponding to the Key for each account that is sending or receiving cryptocurrency in the transfer. Each Transaction should not have more than 50 levels.\
The SignatureList field is deprecated and succeeded by SignatureMap.

## Transaction

| Field                    | Type                                           | Description                                                                  |                   |
| ------------------------ | ---------------------------------------------- | ---------------------------------------------------------------------------- | ----------------- |
| `signedTransactionBytes` | bytes                                          | SignedTransaction serialized into bytes                                      |                   |
| `bodyBytes`              | bytes                                          | TransactionBody serialized into bytes, which needs to be signed              | deprecated = true |
| `sigMap`                 | [SignatureMap](../basic-types/signaturemap.md) | The signatures on the body with the new format, to authorize the transaction | deprecated = true |
