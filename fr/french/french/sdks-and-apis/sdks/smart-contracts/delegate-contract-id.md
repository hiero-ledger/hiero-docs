# Delegate Contract ID

A smart contract that, if the recipient of the active message frame, should be treated as having signed. Note that this does not mean the _code being executed in the frame_ will belong to the given contract since it could be running another contract's code via `delegatecall`. Setting this key is a more permissive version of the `contractID` key, which also requires the code in the active message frame to belong to the contract with the given id. See [HIP-206: Hedera Token Service Precompiled Contract for Hedera SmartContract Service](https://hips.hedera.com/hip/hip-206#contract-key) for more details.

The delegate contract ID can be set as `Key`.

### Constructor

| **Constructor**                               | **Type**         | **Description**                                                                                                                               |
| --------------------------------------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `new DelegateContractId(<shard, realm, num>)` | long, long, long | Constructs a DelegateContractId with `0` for `shardNum` and `realmNum` (e.g., `0.0.<Num>`) |

### Methods

| **Methods**                                       | **Type** | **Description**                                         |
| ------------------------------------------------- | -------- | ------------------------------------------------------- |
| `DelegateContractId.fromString(<id>)`             | String   | Constructs a delegate contract ID from a String         |
| `DelegateContractId.fromBytes(<bytes>)`           | bytes    | Constructs a delegate contract ID from bytes            |
| `DelegateContractId.fromSolidityAddress(address)` | address  | Constructs a delegate contract ID from Solidity address |
