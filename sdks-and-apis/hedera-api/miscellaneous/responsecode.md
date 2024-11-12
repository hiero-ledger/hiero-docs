# ResponseCode

<details>

<summary><strong>OK</strong></summary>

The transaction passed the precheck validations.

</details>

<details>

<summary><strong>INVALID_TRANSACTION</strong></summary>

For any error not handled by specific error codes listed below.

</details>

<details>

<summary>PAYER_ACCOUNT_NOT_FOUND</summary>

Payer account does not exist.

</details>

<details>

<summary>INVALID_NODE_ACCOUNT</summary>

Node Account provided does not match the node account of the node the transaction was submitted to.

</details>

<details>

<summary>TRANSACTION_EXPIRED</summary>

Pre-Check error when TransactionValidStart + transactionValidDuration is less than current consensus time.

</details>

<details>

<summary>INVALID_TRANSACTION_START</summary>

Transaction start time is greater than current consensus time

</details>

<details>

<summary>INVALID_TRANSACTION_DURATION</summary>

Invalid transaction duration is a positive non-zero number that does not exceed 120 seconds

</details>

<details>

<summary>INVALID_SIGNATURE</summary>

The transaction signature is not valid

</details>

<details>

<summary>MEMO_TOO_LONG</summary>

Transaction memo size exceeded 100 bytes

</details>

<details>

<summary>INSUFFICIENT_TX_FEE</summary>

The fee provided in the transaction is insufficient for this type of transaction

</details>

<details>

<summary>INSUFFICIENT_PAYER_BALANCE</summary>

The payer account has insufficient cryptocurrency to pay the transaction fee

</details>

<details>

<summary>DUPLICATE_TRANSACTION</summary>

This transaction ID is a duplicate of one that was submitted to this node or reached consensus in the last 180 seconds (receipt period)

</details>

<details>

<summary>BUSY</summary>

If API is throttled out

</details>

<details>

<summary>NOT_SUPPORTED</summary>

The API is not currently supported

</details>

<details>

<summary>INVALID_FILE_ID</summary>

The file id is invalid or does not exist

</details>

<details>

<summary>INVALID_ACCOUNT_ID</summary>

The account id is invalid or does not exist

</details>

<details>

<summary>INVALID_CONTRACT_ID</summary>

The contract id is invalid or does not exist

</details>

<details>

<summary>INVALID_TRANSACTION_ID</summary>

Transaction id is not valid

</details>

<details>

<summary>RECEIPT_NOT_FOUND</summary>

Receipt for given transaction id does not exist

</details>

<details>

<summary>RECORD_NOT_FOUND</summary>

Record for given transaction id does not exist

</details>

<details>

<summary>INVALID_SOLIDITY_ID</summary>

The solidity id is invalid or entity with this solidity id does not exist

</details>

<details>

<summary>UNKNOWN</summary>

This node has submitted this transaction to the network. Status of the transaction is currently unknown.

</details>

<details>

<summary>SUCCESS</summary>

The transaction succeeded

</details>

<details>

<summary>FAIL_INVALID</summary>

There was a system error and the transaction failed because of invalid request parameters.

</details>

<details>

<summary>FAIL_FEE</summary>

There was a system error while performing fee calculation, reserved for future.

</details>

<details>

<summary>FAIL_BALANCE</summary>

There was a system error while performing balance checks, reserved for future.

</details>

<details>

<summary>KEY_REQUIRED</summary>

Key not provided in the transaction body

</details>

<details>

<summary>BAD_ENCODING</summary>

Unsupported algorithm/encoding used for keys in the transaction

</details>

<details>

<summary>INSUFFICIENT_ACCOUNT_BALANCE</summary>

When the account balance is not sufficient for the transfer

</details>

<details>

<summary>INVALID_SOLIDITY_ADDRESS</summary>

During an update transaction when the system is not able to find the Users Solidity address

</details>

<details>

<summary>INSUFFICIENT_GAS</summary>

Not enough gas was supplied to execute transaction

</details>

<details>

<summary>CONTRACT_SIZE_LIMIT_EXCEEDED</summary>

contract byte code size is over the limit

</details>

<details>

<summary>LOCAL_CALL_MODIFICATION_EXCEPTION</summary>

local execution (query) is requested for a function which changes state

</details>

<details>

<summary>CONTRACT_REVERT_EXECUTED</summary>

Contract REVERT OPCODE executed

</details>

<details>

<summary>CONTRACT_EXECUTION_EXCEPTION</summary>

For any contract execution-related error not handled by the specific error codes listed above.

</details>

<details>

<summary>INVALID_RECEIVING_NODE_ACCOUNT</summary>

In Query validation, an account with +ve(amount) value should be a Receiving node account, the receiver account should be only one account in the list.

</details>

<details>

<summary>MISSING_QUERY_HEADER</summary>

The header is missing in the Query request.

</details>

<details>

<summary>ACCOUNT_UPDATE_FAILED</summary>

The update of the account failed.

</details>

<details>

<summary>INVALID_KEY_ENCODING</summary>

Provided key encoding was not supported by the system.

</details>

<details>

<summary>NULL_SOLIDITY_ADDRESS</summary>

null solidity address

</details>

<details>

<summary>CONTRACT_UPDATE_FAILED</summary>

update of the contract failed

</details>

<details>

<summary>INVALID_QUERY_HEADER</summary>

the query header is invalid

</details>

<details>

<summary>INVALID_FEE_SUBMITTED</summary>

Invalid fee submitted

</details>

<details>

<summary>INVALID_PAYER_SIGNATURE</summary>

Payer signature is invalid

</details>

<details>

<summary>KEY_NOT_PROVIDED</summary>

The keys were not provided in the request.

</details>

<details>

<summary>INVALID_EXPIRATION_TIME</summary>

Expiration time provided in the transaction was invalid.

</details>

<details>

<summary>NO_WACL_KEY</summary>

WriteAccess Control Keys are not provided for the file

</details>

<details>

<summary>FILE_CONTENT_EMPTY</summary>

The contents of file are provided as empty.

</details>

<details>

<summary>INVALID_ACCOUNT_AMOUNTS</summary>

The crypto transfer credit and debit do not sum equal to 0

</details>

<details>

<summary>EMPTY_TRANSACTION_BODY</summary>

Transaction body provided is empty

</details>
