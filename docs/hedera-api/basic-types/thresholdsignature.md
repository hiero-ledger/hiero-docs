# ThresholdSignature

{% hint style="info" %}
This message is deprecated and succeeded by [SignaturePair](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md) and [SignatureMap ](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)messages.
{% endhint %}

A signature corresponding to a ThresholdKey. For an N-of-M threshold key, this is a list of M signatures, at least N of which must be non-null.

| Field    | Type                                                                                                                                             | Description                                                                                       |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| `option` | ​deprecated=true                                                                                                                                 | ​                                                                                                 |
| `sigs`   | ​[SignatureList](https://github.com/theekrystallee/hedera-style-guide/blob/sdk-v1/deprecated/hedera-api/basic-types/broken-reference/README.md)​ | for an N-of-M threshold key, this is a list of M signatures, at least N of which must be non-null |
