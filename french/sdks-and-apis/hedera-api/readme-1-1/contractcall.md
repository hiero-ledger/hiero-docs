# ContractCall

## **ContractCallTransactionBody**

Call a function of the given smart contract instance, giving it functionParameters as its inputs. The call can use at maximum the given amount of gas – the paying account will not be charged for any unspent gas.\
\
If this function results in data being stored, an amount of gas is calculated that reflects this storage burden.\
\
The amount of gas used, as well as other attributes of the transaction, e.g. size, number of signatures to be verified, determine the fee for the transaction – which is charged to the paying account.

| Field                | Type                                       | Description                                                               |
| -------------------- | ------------------------------------------ | ------------------------------------------------------------------------- |
| `contractID`         | [ContractID](../basic-types/contractid.md) | The contract instance to call                                             |
| `gas`                | int64                                      | The maximum amount of gas to use for the call                             |
| `amount`             | int64                                      | Number of tinybars sent (the function must be payable if this is nonzero) |
| `functionParameters` | bytes                                      | Which function to call, and the parameters to pass to the function        |
