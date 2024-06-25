# Network Response Messages

Network response messages and their descriptions.

| Network Response                                            | Description                                                                                            |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `ACCOUNT_FROZEN_FOR_TOKEN`                                  | The account is frozen and cannot transact with the token                                               |
| `TOKENS_PER_ACCOUNT_LIMIT_EXCEEDED`                         | The maximum number of token relations for a given account is exceeded                                  |
| `INVALID_TOKEN_ID`                                          | The token is invalid or does not exist                                                                 |
| `INVALID_TOKEN_DECIMALS`                                    | Invalid token decimals                                                                                 |
| `INVALID_TOKEN_INITIAL_SUPPLY`                              | Invalid token initial supply                                                                           |
| `INVALID_TREASURY_ACCOUNT_FOR_TOKEN`                        | Treasury account does not exist or is deleted                                                          |
| `INVALID_TOKEN_SYMBOL`                                      | Token Symbol is not UTF-8 capitalized alphabetical string                                              |
| `TOKEN_HAS_NO_FREEZE_KEY`                                   | Freeze key is not set on a token                                                                       |
| `TRANSFERS_NOT_ZERO_SUM_FOR_TOKEN`                          | Amounts in the transfer list are not net-zero                                                          |
| `MISSING_TOKEN_SYMBOL`                                      | Token Symbol is not provided                                                                           |
| `TOKEN_SYMBOL_TOO_LONG`                                     | Token Symbol is too long                                                                               |
| `ACCOUNT_KYC_NOT_GRANTED_FOR_TOKEN`                         | KYC must be granted and the account does not have KYC granted                                          |
| `TOKEN_HAS_NO_KYC_KEY`                                      | KYC key is not set on a token                                                                          |
| `INSUFFICIENT_TOKEN_BALANCE`                                | Token balance is not sufficient for the transaction                                                    |
| `TOKEN_WAS_DELETED`                                         | Token transactions cannot be executed on deleted token                                                 |
| `TOKEN_HAS_NO_SUPPLY_KEY`                                   | The supply key is not set on a token                                                                   |
| `TOKEN_HAS_NO_WIPE_KEY`                                     | The wipe key is not set on a token                                                                     |
| `INVALID_TOKEN_MINT_AMOUNT`                                 | Invalid mint amount                                                                                    |
| `INVALID_TOKEN_BURN_AMOUNT`                                 | Invalid burn amount                                                                                    |
| `TOKEN_NOT_ASSOCIATED_TO_ACCOUNT`                           | Account has not been associated with an account                                                        |
| `CANNOT_WIPE_TOKEN_TREASURY_ACCOUNT`                        | Cannot execute wipe operation on treasury account                                                      |
| `INVALID_KYC_KEY`                                           | Invalid kyc key                                                                                        |
| `INVALID_WIPE_KEY`                                          | Invalid wipe key                                                                                       |
| `INVALID_FREEZE_KEY`                                        | Invalid freeze key                                                                                     |
| `INVALID_SUPPLY_KEY`                                        | Invalid supply key                                                                                     |
| `MISSING_TOKEN_NAME`                                        | Token Name is not provided                                                                             |
| `TOKEN_NAME_TOO_LONG`                                       | Token Name is too long                                                                                 |
| `INVALID_WIPING_AMOUNT`                                     | The provided wipe amount must not be negative, zero or bigger than the token holder balance            |
| `TOKEN_IS_IMMUTABLE`                                        | The token does not have Admin key set, thus update/delete transactions cannot be performed             |
| `TOKEN_ALREADY_ASSOCIATED_TO_ACCOUNT`                       | An associateToken operation specified a token already associated with the account                      |
| `TRANSACTION_REQUIRES_ZERO_TOKEN_BALANCES`                  | An attempted operation is invalid until all token balances for the target account are zero             |
| `ACCOUNT_IS_TREASURY`                                       | An attempted operation is invalid because the account is a treasury                                    |
| `TOKEN_ID_REPEATED_IN_TOKEN_LIST`                           | Same TokenIDs present in the token list                                                                |
| `TOKEN_TRANSFER_LIST_SIZE_LIMIT_EXCEEDED`                   | Exceeded the number of token transfers (both from and to) allowed for token transfer list              |
| `EMPTY_TOKEN_TRANSFER_BODY`                                 | TokenTransfersTransactionBody has no TokenTransferList                                                 |
| `EMPTY_TOKEN_TRANSFER_ACCOUNT_AMOUNTS`                      | TokenTransfersTransactionBody has a TokenTransferList with no AccountAmounts                           |
| `FRACTION_DIVIDES_BY_ZERO`                                  | A custom fractional fee set a denominator of zero                                                      |
| `INSUFFICIENT_PAYER_BALANCE_FOR_CUSTOM_FEE`                 | The transaction payer could not afford a custom fee                                                    |
| `CUSTOM_FEES_LIST_TOO_LONG`                                 | The customFees list is longer than allowed limit 10                                                    |
| `INVALID_CUSTOM_FEE_COLLECTOR`                              | Any of the feeCollector accounts for customFees is invalid                                             |
| `INVALID_TOKEN_ID_IN_CUSTOM_FEES`                           | Any of the token Ids in customFees is invalid                                                          |
| `TOKEN_NOT_ASSOCIATED_TO_FEE_COLLECTOR`                     | Any of the token Ids in customFees are not associated to feeCollector                                  |
| `CUSTOM_FEE_NOT_FULLY_SPECIFIED`                            | A custom fee schedule entry did not specify either a fixed or fractional fee                           |
| `CUSTOM_FEE_MUST_BE_POSITIVE`                               | Only positive fees may be assessed at this time                                                        |
| `TOKEN_HAS_NO_FEE_SCHEDULE_KEY`                             | Fee schedule key is not set on token                                                                   |
| `CUSTOM_FEE_OUTSIDE_NUMERIC_RANGE`                          | A fractional custom fee exceeded the range of a 64-bit signed integer                                  |
| `INVALID_CUSTOM_FRACTIONAL_FEES_SUM`                        | The sum of all custom fractional fees must be strictly less than 1                                     |
| `FRACTIONAL_FEE_MAX_AMOUNT_LESS_THAN_MIN_AMOUNT`            | Each fractional custom fee must have its maximum\_amount, if specified, at least its minimum\_amount   |
| `CUSTOM_SCHEDULE_ALREADY_HAS_NO_FEES`                       | A fee schedule update tried to clear the custom fees from a token whose fee schedule was already empty |
| `CUSTOM_FEE_DENOMINATION_MUST_BE_FUNGIBLE_COMMON`           | Only tokens of type FUNGIBLE\_COMMON can be used as fee schedule denominations                         |
| `CUSTOM_FRACTIONAL_FEE_ONLY_ALLOWED_FOR_FUNGIBLE_COMMON`    | Only tokens of type FUNGIBLE\_COMMON can have fractional fees                                          |
| `INVALID_CUSTOM_FEE_SCHEDULE_KEY`                           | The provided custom fee schedule key was invalid                                                       |
| `ACCOUNT_AMOUNT_TRANSFERS_ONLY_ALLOWED_FOR_FUNGIBLE_COMMON` | An AccountAmount token transfers list referenced a token type other than FUNGIBLE\_COMMON              |
| `INVALID_TOKEN_MINT_METADATA`                               | The requested token mint metadata was invalid                                                          |
| `INVALID_TOKEN_BURN_METADATA`                               | The requested token burn metadata was invalid                                                          |
| `PAYER_ACCOUNT_DELETED`                                     | The payer account has been marked as deleted                                                           |
| `CUSTOM_FEE_CHARGING_EXCEEDED_MAX_RECURSION_DEPTH`          | The reference chain of custom fees for a transferred token exceeded the maximum length of 2            |
| `CUSTOM_FEE_CHARGING_EXCEEDED_MAX_ACCOUNT_AMOUNTS`          | More than 20 balance adjustments were to satisfy a CryptoTransfer and its implied custom fee payments  |
| `INSUFFICIENT_SENDER_ACCOUNT_BALANCE_FOR_CUSTOM_FEE`        | The sender account in the token transfer transaction could not afford a custom fee                     |
| `SERIAL_NUMBER_LIMIT_REACHED`                               | Currently no more than 4,294,967,295 NFTs may be minted for a given unique token type                  |
| `CUSTOM_ROYALTY_FEE_ONLY_ALLOWED_FOR_NON_FUNGIBLE_UNIQUE`   | Only tokens of type NON\_FUNGIBLE\_UNIQUE can have royalty fees                                        |
| `TOKEN_IS_PAUSED`                                           | Token is paused. This Token cannot be a part of any kind of Transaction until unpaused.                |
| `TOKEN_HAS_NO_PAUSE_KEY`                                    | Pause key is not set on token                                                                          |
| `INVALID_PAUSE_KEY`                                         | The provided pause key was invalid                                                                     |
