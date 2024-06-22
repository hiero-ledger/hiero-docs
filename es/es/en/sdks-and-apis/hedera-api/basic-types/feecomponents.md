# FeeComponents

The different components used for fee calculation

| Field      | Description                                                                                                      |
| ---------- | ---------------------------------------------------------------------------------------------------------------- |
| `min`      | The minimum fees that needs to be paid                                                                           |
| `max`      | The maximum fees that can be submitted                                                                           |
| `constant` | A constant determined by the business to calculate the fees                                                      |
| `bpt`      | Bytes per transaction                                                                                            |
| `vpt`      | Verifications per transaction                                                                                    |
| `rbh`      | Ram byte seconds                                                                                                 |
| `sbh`      | Storage byte seconds                                                                                             |
| `gas`      | Gas for the contract execution                                                                                   |
| `tv`       | Transaction value (crypto transfers amount, tv is in tiny bars divided by 1000, rounded down) |
| `bpr`      | Bytes per response                                                                                               |
| `sbpr`     | Storage bytes per response                                                                                       |

####
