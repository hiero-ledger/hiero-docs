# ThresholdKey

A set of public keys that are used together to form a threshold signature. If the threshold is N and there are M keys, then this is an N of M threshold signature. If an account is associated with ThresholdKeys, then a transaction to move cryptocurrency out of it must be signed by a list of M signatures, where at most M-N of them are blank, and the other at least N of them are valid signatures corresponding to at least N of the public keys listed here.

| Field       | Type                    | Description                                                   |
| ----------- | ----------------------- | ------------------------------------------------------------- |
| `threshold` | ​                       | A valid signature set must have at least this many signatures |
| `keys`      | ​[KeyList](keylist.md)​ | List of all the keys that can sign                            |

#### \&#x20 <a href="#undefined" id="undefined"></a>
