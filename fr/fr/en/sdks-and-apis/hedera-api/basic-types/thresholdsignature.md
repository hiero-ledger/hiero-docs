# ThresholdSignature

{% hint style="info" %}
This message is deprecated and succeeded by [SignaturePair](signature-pair.md) and [SignatureMap ](signaturemap.md)messages.
{% endhint %}

A signature corresponding to a ThresholdKey. For an N-of-M threshold key, this is a list of M signatures, at least N of which must be non-null.

| Field    | Type                                 | Description                                                                                       |
| -------- | ------------------------------------ | ------------------------------------------------------------------------------------------------- |
| `option` | ​deprecated=true                     | ​                                                                                                 |
| `sigs`   | ​[SignatureList](signature-list.md)​ | for an N-of-M threshold key, this is a list of M signatures, at least N of which must be non-null |
