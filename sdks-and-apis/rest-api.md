---
description: The mirror node REST API offers the ability to query transaction information.
---

# REST API

Hedera Mirror Nodes store the history of transactions that occurred on mainnet, testnet, and previewnet. Each transaction generates a record that is stored in a record file. The transaction contents can be accessed by the mirror node REST APIs

To make a request, use the network endpoint and the REST API of choice. For example, to get a list of transactions on mainnet you would make the following request.

{% embed url="https://mainnet.mirrornode.hedera.com/api/v1/transactions" %}

<details>

<summary>Hedera Mirror Node Swagger UI environments</summary>

\
[Mainnet](https://mainnet-public.mirrornode.hedera.com/api/v1/docs#/)

[Testnet](https://testnet.mirrornode.hedera.com/api/v1/docs/#/)

[Previewnet](https://previewnet.mirrornode.hedera.com/api/v1/docs/#/)

</details>

{% hint style="info" %}
**MAINNET BASEURL**\
`https://mainnet.mirrornode.hedera.com/`

**TESTNET BASEURL**\
`https://testnet.mirrornode.hedera.com/`

**PREVIEWNET BASEURL**\
`https://previewnet.mirrornode.hedera.com/`

You may also check out [Validation Cloud](https://validationcloud.io), [DragonGlass](https://app.dragonglass.me/hedera/pricing), [Arkhia](https://www.arkhia.io/features/#api-services) or [Ledger Works](https://lworks.io) as alternatives.‌
{% endhint %}

{% hint style="warning" %}
Public mainnet mirror node requests per second (RPS) are currently throttled at **50 per IP address**. These configurations may change in the future depending on performance or security considerations. At this time, no authentication is required.
{% endhint %}

## Accounts <a href="#accounts" id="accounts"></a>

The **accounts** object represents the information associated with an account and returns a list of account information.‌

Account IDs take the following format: **0.0.\<account number>**.‌

Example: 0.0.1000‌

Account IDs can also take the account number as an input value. For example, for account ID 0.0.1000, the number 1000 can be specified in the request.

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details <a href="#response-details" id="response-details"></a>

| **Response Item**                       | **Description**                                                                        |
| --------------------------------------- | -------------------------------------------------------------------------------------- |
| **account**                             | The ID of the account                                                                  |
| **allowances**                          | The allowances granted to this account                                                 |
| **alias**                               | RFC4648 no-padding base32 encoded account alias                                        |
| **auto\_renew\_period**                 | The period in which the account will auto renew                                        |
| **balance**                             | The timestamp and account balance of the account                                       |
| **created\_timestamp**                  | The timestamp for the creation of that account                                         |
| **decline\_reward**                     | Whether or not the account has opted to decline a staking reward                       |
| **deleted**                             | Whether the account was deleted or not                                                 |
| **ethereum\_nonce**                     | The ethereum transaction nonce associated with this account                            |
| **evm\_address**                        | A network entity encoded as an EVM encoded hex                                         |
| **expiry\_timestamp**                   | The expiry date for the entity as set by a create or update transaction                |
| **key**                                 | The public key associated with the account                                             |
| **links.next**                          | Hyperlink to the next page of results                                                  |
| **max\_automatic\_token\_associations** | The number of automatic token associations, if any                                     |
| **memo**                                | The account memo, if any                                                               |
| **nfts**                                | List of nfts informations belonging to this account                                    |
| **pending\_reward**                     | The account's pending staking reward that has not been transferred to the account      |
| **receiver\_sig\_required**             | Whether or not the account requires a signature to receive a transfer into the account |
| **rewards**                             | List of rewards which of the account                                                   |
| **staked\_account\_id**                 | The account ID the account is staked to, if set                                        |
| **staked\_node\_id**                    | The node ID the account is staked to, if set                                           |
| **stake\_period\_start**                | The start of the staking period                                                        |
| **tokens**                              | The tokens and their balances associated to the specified account                      |

#### Optional Filtering <a href="#optional-filtering" id="optional-filtering"></a>

| Operator                               | Example                                                                                               | Description                                                                                                 |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `lt` (less than)                       | `/api/v1/accounts?account.id=lt:0.0.1000`                                                             | Returns account IDs less then 1000                                                                          |
| `lte` (less than or equal to)          | `/api/v1/accounts?account.id=lte:0.0.1000`                                                            | Returns account IDs less than or equal to 1000                                                              |
| `gt` (greater than)                    | `/api/v1/accounts?account.id=gt:0.0.1000`                                                             | Returns account IDs greater than 1000                                                                       |
| `gte` (greater than or equal to)       | `/api/v1/accounts?account.id=gte:0.0.1000`                                                            | Returns account IDs greater than or equal to 1000                                                           |
| `order` (order `asc` or `desc` values) | <p><code>/api/v1/accounts?order=asc</code></p><p>​</p><p><code>/api/v1/accounts?order=desc</code></p> | <p>Returns account information in ascending order</p><p>Returns account information in descending order</p> |

#### Additional Examples <a href="#additional-examples" id="additional-examples"></a>

| Example Requests                                                                                       | Description                                                                                                     |
| ------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| `/api/v1/accounts?account.id=0.0.1001`                                                                 | Returns the account information of account 1001                                                                 |
| `/api/v1/accounts?account.balance=gt:1000`                                                             | Returns all account information that have a balance greater than 1000 tinybars                                  |
| `/api/v1/accounts?account.publickey=2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed 15e301049517438d61864` | Returns all account information for 2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed15e301049517438d61864 public key |
| `/api/v1/accounts/2?transactionType=cryptotransfer`                                                    | Returns the crypto transfer transactions for account 2.                                                         |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}/allowances/crypto" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}/tokens" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}/nfts" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}/allowances/tokens" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/accounts/{idOrAliasOrEvmAddress}/rewards" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

## Balances <a href="#balances" id="balances"></a>

The **balance** object represents the balance of accounts on the Hedera network. You can retrieve this to view the **most recent** balance of all the accounts on the network at that given time. The balances object returns the account ID and the balance in hbars. Balances are checked on a periodic basis and thus return the most recent snapshot of time captured prior to the request.

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/balances" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details <a href="#response-details-1" id="response-details-1"></a>

| Response Item        | Description                                                                                          |
| -------------------- | ---------------------------------------------------------------------------------------------------- |
| **timestamp**        | The seconds.nanoseconds of the timestamp at which the list of balances for each account are returned |
| **balances**         | List of balances for each account                                                                    |
| **account**          | The ID of the account                                                                                |
| **balance**          | The balance of the account                                                                           |
| **tokens**           | The tokens that are associated to this account                                                       |
| **tokens.token\_id** | The ID of the token associated to this account                                                       |
| **tokens.balance**   | The token balance for the specified token associated to this account                                 |
| **links.next**       | Hyperlink to the next page of results                                                                |

#### Optional Filtering <a href="#optional-filtering-1" id="optional-filtering-1"></a>

| Operator                               | Example                                                                                               | Description                                                                               |
| -------------------------------------- | ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `lt` (less than)                       | `/api/v1/balances?account.id=lt:0.0.1000`                                                             | Returns the balances of account IDs less than 1,000                                       |
| `lte` (less than or equal to)          | `/api/v1/balances?account.id=lte:0.0.1000`                                                            | Returns the balances account IDs less than or equal to 1,000                              |
| `gt` (greater than)                    | `/api/v1/balances?account.id=gt:0.0.1000`                                                             | Returns the balances of account IDs greater than to 1,000                                 |
| `gte` (greater than or equal to)       | `/api/v1/balances?account.id=gte:0.0.1000`                                                            | Returns the balances of account IDs greater than or equal to 1,000                        |
| `order` (order `asc` or `desc` values) | <p><code>/api/v1/balances?order=asc</code></p><p>​</p><p><code>/api/v1/balances?order=desc</code></p> | <p>Lists balances in ascending order</p><p>​</p><p>Lists balances in descending order</p> |

#### Additional Examples <a href="#additional-examples" id="additional-examples"></a>

| Example Requests                                                                                       | Description                                                                                                                      |
| ------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `/api/v1/balances?account.id=0.0.1000`                                                                 | Returns balance for account ID 1,000                                                                                             |
| `/api/v1/balances?account.balance=gt:1000`                                                             | Returns all account IDs that have a balance greater than 1000 tinybars                                                           |
| `/api/v1/balances?timestamp=1566562500.040961001`                                                      | Returns all account balances referencing the latest snapshot that occurred prior to 1566562500 seconds and 040961001 nanoseconds |
| `/api/v1/balances?account.publickey=2b60955bcbf0cf5e9ea880b52e5b6 3f664b08edf6ed15e301049517438d61864` | Returns balance information for 2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed 15e301049517438d61864 public key                     |

## Transactions <a href="#transactions" id="transactions"></a>

The **transaction** object represents the transactions processed on the Hedera network. You can retrieve this to view the transaction metadata information including transaction id, timestamp, transaction fee, transfer list, etc. If a transaction was submitted to multiple nodes, the successful transaction and duplicate transaction(s) will be returned as separate entries in the response with the same transaction ID. Duplicate transactions will still be assessed [network fees](https://www.hedera.com/fees).

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/transactions" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/transactions/{transactionId}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details <a href="#response-details-2" id="response-details-2"></a>

| Response Item              | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **consensus timestamp**    | The consensus timestamp in seconds.nanoseconds                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **transaction hash**       | The hash value of the transaction processed on the Hedera network                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **valid start timestamp**  | The time the transaction is valid                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **charged tx fee**         | The transaction fee that was charged for that transaction                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **transaction id**         | The ID of the transaction                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| **memo base64**            | The memo attached to the transaction encoded in Base64 format                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **result**                 | Whether the cryptocurrency transaction was successful or not                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **entity ID**              | The entity ID that is created from create transactions (AccountCreateTransaction, TopicCreateTransaction, TokenCreateTransaction, ScheduleCreateTransaction, ContractCreateTransaction, FileCreateTransaction).                                                                                                                                                                                                                                                                                                                                                          |
| **name**                   | The type of transaction                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **max fee**                | The maximum transaction fee the client is willing to pay                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| **valid duration seconds** | The seconds for which a submitted transaction is to be deemed valid beyond the start time. The transaction is invalid if consensusTimestamp is greater than transactionValidStart + valid\_duration\_seconds.                                                                                                                                                                                                                                                                                                                                                            |
| **node**                   | The ID of the node that submitted the transaction to the network                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **transfers**              | A list of the account IDs the crypto transfer occurred between and the amount that was transferred. A negative (-) sign indicates a debit to that account. The transfer list includes the transfers between the from account and to account, the transfer of the [node fee](https://www.hedera.com/fees), the transfer of the [network fee](https://www.hedera.com/fees), and the transfer of the [service fee](https://www.hedera.com/fees) for that transaction. If the transaction was not processed, a [network fee](https://www.hedera.com/fees) is still assessed. |
| **token transfers**        | The token ID, account, and amount that was transferred to by this account in this transaction. This will not be listed if it did not occur in the transaction                                                                                                                                                                                                                                                                                                                                                                                                            |
| **assessed custom fees**   | The fees that were charged for a custom fee token transfer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **links.next**             | A hyperlink to the next page of responses                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |

#### Optional Filtering <a href="#optional-filtering-2" id="optional-filtering-2"></a>

| Operator                               | Example                                                                                                       | Description                                                                              |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `lt` (less than)                       | `/api/v1/transactions?account.id=lt:0.0.1000`                                                                 | Returns account.id transactions less than 1,000                                          |
| `lte` (less than or equal to)          | `/api/v1/transactions?account.id=lte:0.0.1000`                                                                | Returns account.id transactions less than or equal to 1,000                              |
| `gt` (greater than)                    | `/api/v1/transactions?account.id=gt:0.0.1000`                                                                 | Returns account.id transactions greater than 1,000                                       |
| `gte` (greater than or equal to)       | `api/v1/transactions?account.id=gte:0.0.1000`                                                                 | Returns account.id transactions greater than or equal to 1,000                           |
| `order` (order `asc` or `desc` values) | <p><code>/api/v1/transactions?order=asc</code></p><p>​</p><p><code>/api/v1/transactions?order=desc</code></p> | <p>Lists transactions in ascending order Lists transactions descending order</p><p>​</p> |

**Note**: It is recommended that the **account.id** query should not select no more than 1000 accounts in a query. If the range specified in the query results in selecting more than 1000 accounts, the API will automatically only search for the first 1000 accounts that match the query in the database and return the transactions for those. For example, if you use `?account.id=gt:0.0.15000` or if you use`?account.id=gt:0.0.15000&account.id=lt:0.0.30000`, then the API will only return results or some 1000 accounts in this range that match the rest of the query filters.‌

A single transaction can also be returned by specifying the transaction ID in the request. If a transaction was submitted to multiple nodes, the response will return entries for the successful transaction along with separate entries for the duplicate transaction(s). The "result" key indicates "success" for the node that processed the transaction and "DUPLICATE\_TRANSACTION" for each additional node submission. Duplicate entries are still charged network fees.

| Parameter          | Description                                                           |
| ------------------ | --------------------------------------------------------------------- |
| `{transaction_ID}` | A specific transaction can be returned by specifying a transaction ID |

#### Additional Examples <a href="#additional-examples-1" id="additional-examples-1"></a>

| Example Request                                                                                                | Description                                                                                                                                                    |
| -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/api/v1/transactions/?account.id=0.0.1000`                                                                    | Returns transaction for account ID 1,000                                                                                                                       |
| `/api/v1/transactions?timestamp=1565779209.711927001`                                                          | Returns transactions at 1565779209 seconds and 711927001 nanoseconds                                                                                           |
| `/api/v1/transactions?result=fail`                                                                             | Returns all transactions that have failed                                                                                                                      |
| `/api/v1/transactions?account.id=0.0.13622&type=credit` `/api/v1/transactions?account.id=0.0.13622&type=debit` | <p>Returns all transactions that deposited into an account ID 0.0.13622</p><p>​</p><p>Returns all transactions that withdrew from account ID 0.0.13622<br></p> |
| `/api/v1/transactions?transactionType=cryptotransfer`                                                          | Returns all cryptotransfer transactions                                                                                                                        |

## Topics

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/topics/{topicId}/messages" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item            | Description                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------ |
| **consensus\_timestamp** | The consensus timestamp of the message in seconds.nanoseconds                        |
| **topic\_id**            | The ID of the topic the message was submitted to                                     |
| **payer\_account\_id**   | The account ID that paid for the transaction to submit the message                   |
| **message**              | The content of the message                                                           |
| **running\_hash**        | The new running hash of the topic that received the message                          |
| **sequence\_number**     | The sequence number of the message relative to all other messages for the same topic |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/topics/{topicId}/messages/{sequenceNumber}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/topics/messages/{timestamp}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

## Tokens

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item  | Description                                  |
| -------------- | -------------------------------------------- |
| **token\_Id**  | The ID of the token in x.y.z format          |
| **symbol**     | The symbol of the token                      |
| **admin\_key** | The admin key for the token                  |
| **type**       | The type of token (fungible or non-fungible) |

#### Additional Examples

| Example Request                                                                              | Description                                      |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| `/api/v1/tokens?publickey=3c3d546321ff6f63d70 1d2ec5c277095874e19f4a235bee1e6bb19258bf362be` | All tokens with matching admin key               |
| `/api/v1/tokens?account.id=0.0.8`                                                            | All tokens for matching account                  |
| `/api/v1/tokens?token.id=gt:0.0.1001`                                                        | All tokens in range                              |
| `/api/v1/tokens?order=desc`                                                                  | All tokens in descending order of `token.id`     |
| `/api/v1/tokens?limit=x`                                                                     | All tokens taking the first `x` number of tokens |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens/{tokenId}/balances" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item | Description                                                   |
| ------------- | ------------------------------------------------------------- |
| **timestamp** | The timestamp of the recorded balances in seconds.nanoseconds |
| **balances**  | The balance of the tokens in those accounts                   |
| **account**   | The ID of the account that has the token balance              |
| **balance**   | The balance of the token associated with the account          |

#### Additional Examples

| Example Request                                                     | Description                                      |
| ------------------------------------------------------------------- | ------------------------------------------------ |
| `/api/v1/tokens/<token_id>/balances?order=asc`                      | The balance of the token in ascending order      |
| `/api/v1/tokens/<token_id>/balances?account.id=0.0.1000`            | The balance of the token for account ID 0.0.1000 |
| `/api/v1/tokens/<token_id>/balances?account.balance=gt:1000`        | The balance for the token greater than 1000      |
| `/api/v1/tokens/<token_id>/balances?timestamp=1566562500.040961001` | The token balances for the specified timestamp   |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens/{tokenId}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item             | Description                                                              |
| ------------------------- | ------------------------------------------------------------------------ |
| **admin\_key**            | The token's admin key, if specified                                      |
| **auto\_renew\_account**  | The auto renew account ID                                                |
| **auto\_renew\_period**   | The period at which the auto renew account will be charged a renewal fee |
| **created\_timestamp**    | The timestamp of when the token was created                              |
| **decimals**              | The number of decimal places a token is divisible by                     |
| **expiry\_timestamp**     | The epoch second at which the token should expire                        |
| **freeze\_default**       | Whether or not accounts created                                          |
| **fee\_schedule\_key**    | The fee schedule key, if any                                             |
| **freeze\_key**           | The freeze key for the token, if specified                               |
| **initial\_suppl**y       | The initial supply of the token                                          |
| **kyc\_key**              | The KYC key for the token, if specified                                  |
| **modified\_timestamp**   | The last time the token properties were modified                         |
| **name**                  | The name of the token                                                    |
| **supply\_key**           | The supply key for the token, if specified                               |
| **symbol**                | The token symbol                                                         |
| **token\_id**             | The token ID                                                             |
| **total\_supply**         | The total supply of the token                                            |
| **treasury\_account\_id** | The treasury account of the token                                        |
| **type**                  | whether a token is a fungible or non-fungible token                      |
| **wipe\_key**             | The wipe key for the token, if specified                                 |
| **custom\_fees**          | The custom fee schedule for the token, if any                            |
| **pause\_key**            | The pause key for a token, if specified                                  |
| **pause\_status**         | Whether or not the token is paused                                       |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens/{tokenId}/nfts" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item           | Description                                           |
| ----------------------- | ----------------------------------------------------- |
| **account\_id**         | The account ID of the account associated with the NFT |
| **created\_timestamp**  | The timestamp of when the NFT was created             |
| **deleted**             | Whether the token was deleted or not                  |
| **metadata**            | The meta data of the NFT                              |
| **modified\_timestamp** | The last time the token properties were modified      |
| **serial\_number**      | The serial number of the NFT                          |
| **token\_id**           | The token ID of the NFT                               |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens/{tokenId}/nfts/{serialNumber}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item           | Description                                           |
| ----------------------- | ----------------------------------------------------- |
| **account\_id**         | The account ID of the account associated with the NFT |
| **created\_timestamp**  | The timestamp of when the NFT was created             |
| **deleted**             | Whether the token was deleted or not                  |
| **metadata**            | The meta data of the NFT                              |
| **modified\_timestamp** | The last time the token properties were modified      |
| **serial\_number**      | The serial number of the NFT                          |
| **token\_id**           | The token ID of the NFT                               |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/tokens/{tokenId}/nfts/{serialNumber}/transactions" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details

| Response Item             | Description                       |
| ------------------------- | --------------------------------- |
| **created\_timestamp**    | The timestamp of the transaction  |
| **id**                    | The timestamp of the transaction  |
| **receiver\_account\_id** | The account that received the NFT |
| **sender\_account\_id**   | The account that sent the NFT     |
| **type**                  | The type of transaction           |
| **token\_id**             | The token ID of the NFT           |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/supply" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

## Schedule Transactions

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/schedules" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details <a href="#response-details-1" id="response-details-1"></a>

| Response Item                      | Description                                                                    |
| ---------------------------------- | ------------------------------------------------------------------------------ |
| **schedules**                      | List of schedules                                                              |
| **admin\_key**                     | The admin key on the schedule                                                  |
| **admin\_key\_type**               | The type of key                                                                |
| **admin\_key\_key**                | The admin public key                                                           |
| **consensus\_timestamp**           | The consensus timestamp of when the schedule was created                       |
| **creator\_account\_id**           | The account ID of the creator of the schedule                                  |
| **executed\_timestamp**            | The timestamp at which the transaction that was scheduled was executed at      |
| **memo**                           | A string of characters associated with the memo if set                         |
| **payer\_account\_id**             | The account ID of the account paying for the execution of the transaction      |
| **schedule\_id**                   | The ID of the schedule entity                                                  |
| **signatures**                     | The list of keys that signed the transaction                                   |
| **signatures.public\_key\_prefix** | The signatures public key prefix                                               |
| **signatures.signature**           | The signature of the key that signed the schedule transaction                  |
| **signatures.type**                | The type of signature (ED5519 or ECDSA)                                        |
| **transaction\_body**              | The transaction body of the transaction that was scheduled                     |
| **wait\_for\_expiry**              | Whether or not the schedule transaction specified a specific time to expire by |
| **links.next**                     | Hyperlink to the next page of results                                          |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/schedules/{scheduleId}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

#### Response Details <a href="#response-details-1" id="response-details-1"></a>

| Response Item                       | Description                                                                    |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| **adminKey**                        | The admin key on the schedule                                                  |
| **adminKey.\_type**                 | The type of key                                                                |
| **adminKey.key**                    | The admin public key                                                           |
| **consensus\_timestamp**            | The consensus timestamp of when the schedule was created                       |
| **creator\_account\_id**            | The account ID of the creator of the schedule                                  |
| **executed\_timestamp**             | The timestamp at which the transaction that was scheduled was executed at      |
| **memo**                            | A string of characters associated with the memo if set                         |
| **payer\_account\_id**              | The account ID of the account paying for the execution of the transaction      |
| **schedule\_id**                    | The ID of the schedule entity                                                  |
| **signatures**                      | The list of keys that signed the transaction                                   |
| **signatures.consensus\_timestamp** | The consensus timestamp at which the signature was added                       |
| **signatures.public\_key\_prefix**  | The signatures public key prefix                                               |
| **signatures.signature**            | The signature of the key that signed the schedule transaction                  |
| **signatures.type**                 | The type of signature (ED5519 or ECDSA)                                        |
| **transaction\_body**               | The transaction body of the transaction that was scheduled                     |
| **wait\_for\_expiry**               | Whether or not the schedule transaction specified a specific time to expire by |

## Smart Contracts

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

| Response Item                           | Description                                                                                            |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **admin\_key**                          | The admin key of the contract, if specified                                                            |
| **auto\_renew\_account**                | The account paying the auto renew fees, if set                                                         |
| **auto\_renew\_period**                 | The period at which the contract auto renews                                                           |
| **bytecode**                            | The bytecode of the contract                                                                           |
| **contract\_id**                        | The contract ID                                                                                        |
| **created\_timestamp**                  | The timestamp the contract was created at                                                              |
| **deleted**                             | Whether or not the contract is deleted                                                                 |
| **evm\_address**                        | The EVM address of the contract                                                                        |
| **expiration\_timestamp**               | The timestamp of when the contract is set to expire                                                    |
| **file\_id**                            | The ID of the file that stored the contract bytecode                                                   |
| **max\_automatic\_token\_associations** | The number of automatic token association slots                                                        |
| **memo**                                | The memo of the contract, if specified                                                                 |
| **obtainer\_id**                        | The ID of the account or contract that will receive any remaining balance when the contract is deleted |
| **permanent\_removal**                  | Set to `true` when the system expires a contract                                                       |
| **proxy\_account\_id**                  | The proxy account ID (disabled)                                                                        |
| **solidity\_address**                   | The solidity address                                                                                   |
| **timestamp**                           | The period for which the attributes are valid for                                                      |

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/{contractIdOrAddress}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/{contractIdOrAddress}/results" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/{contractIdOrAddress}/state" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/{contractIdOrAddress}/results/{timestamp}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/results" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/results/{transactionIdOrHash}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/results/{transactionIdOrHash}/actions" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/{contractIdOrAddress}/results/logs" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/results/logs" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/contracts/call" method="post" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

## Blocks

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/blocks" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/blocks/{hashOrNumber}" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

## State Proof Alpha

The Hedera Mirror Node state proof alpha api provides the ability to cryptographically prove a transaction is valid on Hedera network. The request returns the content of the address book file, signature files, and record file that can be used to validate the transaction occurred on the Hedera network. The address book file contains the consensus node account IDs and their public key files. The signature files are of the supermajority consensus nodes that signed the record file the transaction is contained in.

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/transactions/{transactionId}/stateproof" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

| Response Item        | Description                                                                      | Format          |
| -------------------- | -------------------------------------------------------------------------------- | --------------- |
| **record\_file**     | The record file content for the specified transaction                            | base64 encoding |
| **signature\_files** | The signatures file content from the consensus nodes that signed the transaction | base64 encoding |
| **address\_books**   | The address book file contents                                                   | base64 encoding |

Save the response to a json file and use the [check-state-proof](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof) cli commands to confirm the validity of the transaction. You can find the instructions [here](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof).

## Network

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/supply" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/fees" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/exchangerate" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/nodes" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}

{% swagger src="https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml" path="/api/v1/network/stake" method="get" %}
[https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml](https://raw.githubusercontent.com/hashgraph/hedera-mirror-node/main/hedera-mirror-rest/api/v1/openapi.yml)
{% endswagger %}
