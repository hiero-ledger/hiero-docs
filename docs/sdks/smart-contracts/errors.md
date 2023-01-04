# Network Response Messages

Network response messages and their descriptions.

| **Network Response**                | **Description**                                                                                                                          |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CONTRACT_BYTECODE_EMPTY`           | Bytecode for smart contract is of length zero                                                                                            |
| `CONTRACT_DELETED`                  | Contract is marked as deleted                                                                                                            |
| `CONTRACT_EXECUTION_EXCEPTION`      | For any contract execution-related error not handled by specific error codes listed above.                                               |
| `CONTRACT_FILE_EMPTY`               | File to create a smart contract was of length zero                                                                                       |
| `CONTRACT_NEGATIVE_GAS`             | Negative gas was offered in the smart contract call                                                                                      |
| `CONTRACT_NEGATIVE_VALUE`           | Negative value / initial balance was specified in a smart contract call / create                                                         |
| `CONTRACT_REVERT_EXECUTED`          | Contract REVERT OPCODE executed                                                                                                          |
| `CONTRACT_SIZE_LIMIT_EXCEEDED`      | Contract byte code size is over the limit                                                                                                |
| `CONTRACT_UPDATE_FAILED`            | Update of the contract failed                                                                                                            |
| `ERROR_DECODING_BYTESTRING`         | Decoding the smart contract binary to a byte array failed. Check that the input is a valid hex string.                                   |
| `EXPIRATION_REDUCTION_NOT_ALLOWED`  | The expiration date/time on a smart contract may not be reduced                                                                          |
| `INSUFFICIENT_GAS`                  | Not enough gas was supplied to execute the transaction                                                                                   |
| `INSUFFICIENT_LOCAL_CALL_GAS`       | Payment tendered for contract local call cannot cover both the fee and the gas                                                           |
| `INVALID_CONTRACT_ID`               | The contract id is invalid or does not exist                                                                                             |
| `INVALID_PAYER_ACCOUNT_ID`          | The response code when a smart contract id is passed for a crypto API request                                                            |
| `INVALID_SOLIDITY_ID`               | The solidity id is invalid or an entity with this solidity id does not exist                                                             |
| `OBTAINER_DOES_NOT_EXIST`           | TransferAccountId or transferContractId specified for contract delete does not exist                                                     |
| `OBTAINER_REQUIRED`                 | When deleting smart contract that has crypto balance either transfer account or transfer smart contract is required                      |
| `OBTAINER_SAME_CONTRACT_ID`         | When deleting smart contract that has crypto balance you can not use the same contract id as transferContractId as the one being deleted |
| `LOCAL_CALL_MODIFICATION_EXCEPTION` | Local execution (query) is requested for a function that changes state                                                                   |
| `MAX_CONTRACT_STORAGE_EXCEEDED`     | Contract permanent storage exceeded the currently allowable limit                                                                        |
| `MODIFYING_IMMUTABLE_CONTRACT`      | Attempting to modify (update or delete an immutable smart contract, i.e. one created without an admin key)                               |
| `NULL_SOLIDITY_ADDRESS`             | Null solidity address                                                                                                                    |
| `RESULT_SIZE_LIMIT_EXCEEDED`        | Smart contract result size greater than specified maxResultSize                                                                          |
