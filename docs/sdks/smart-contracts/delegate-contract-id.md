# Delegate Contract ID

A smart contract that, if the recipient of the active message frame, should be treated as having signed. (Note this does not mean the code being executed in the frame code being executed in the frame `delegatecall`. So setting this key is a more permissive version of setting the `contractID` key, which also requires the code in the active message frame belong to the contract with the given id. The delegate contract ID can be set as `Key`.

### Constructor

| **Constructor**                               | **Type**         | **Description**                                                                            |
| --------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| `new DelegateContractId(<shard, realm, num>)` | long, long, long | Constructs a DelegateContractId with `0` for `shardNum` and `realmNum` (e.g., `0.0.<Num>`) |

### Methods

| **Methods**                                       | **Type** | **Description**                                         |
| ------------------------------------------------- | -------- | ------------------------------------------------------- |
| `DelegateContractId.fromString(<id>)`             | String   | Constructs a delegate contract ID from a String         |
| `DelegateContractId.fromBytes(<bytes>)`           | bytes    | Constructs a delegate contract ID from bytes            |
| `DelegateContractId.fromSolidityAddress(address)` | address  | Constructs a delegate contract ID from Solidity address |
