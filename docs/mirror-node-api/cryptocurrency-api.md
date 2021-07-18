---
description: >-
  The mirror node REST API offers the ability to query cryptocurrency
  transactions and account information from a Hedera managed mirror node.
---

# REST API

{% hint style="info" %}
**MAINNET  
Public:** [https://mainnet-public.mirrornode.hedera.com](https://mainnet-public.mirrornode.hedera.com/)  
**Whitelisted Partners:**[ ****https://mainnet.mirrornode.hedera.com](https://mainnet.mirrornode.hedera.com/)  
  
**TESTNET**  
[https://testnet.mirrornode.hedera.com](https://testnet.mirrornode.hedera.com/)
  
**PREVIEWNET**  
[https://previewnet.mirrornode.hedera.com](https://previewnet.mirrornode.hedera.com/api/v1/transactions)  
  
You may also check out [DragonGlass](https://app.dragonglass.me/hedera/home) and [Kabuto](https://kabuto.sh/) as alternatives.‌
{% endhint %}

{% hint style="warning" %}
Public mainnet mirror node requests are throttled at 100 requests per second \(rps\). This may change in the future depending upon performance or security considerations. At this time,  no authentication is required.
{% endhint %}

## Accounts <a id="accounts"></a>

The **accounts** object represents the information associated with an account and returns a list of account information.‌

Account IDs take the following format: **0.0.&lt;account number&gt;**.‌

Example: 0.0.1000‌

Account IDs can also take the account number as an input value. For example, for account ID 0.0.1000, the number 1000 can be specified in the request.

{% api-method method="get" host="" path=" /api/v1/accounts" %}
{% api-method-summary %}
accounts
{% endapi-method-summary %}

{% api-method-description %}
The account information for 0.0.1562
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="{account ID}" type="string" %}
Returns information for a specific account ID
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="transactionType" type="string" required=false %}
Filter the account ID by transaction type. Please see the reference to the transaction types you can query for below.
{% endapi-method-parameter %}

{% api-method-parameter name="account.id" type="string" required=false %}
The ID of the account to return account information for
{% endapi-method-parameter %}

{% api-method-parameter name="account.balance" type="number" %}
Returns a list of account IDs that have the specified balance
{% endapi-method-parameter %}

{% api-method-parameter name="account.publickey" type="string" %}
Returns the account information for the specified public key
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
    "accounts": [
        {
            "balance": {
                "timestamp": "1602804267.903201001",
                "balance": 1809568709,
                "tokens": [
                    {
                        "token_id": "0.0.1561",
                        "balance": 100000
                    },

                    {
                        "token_id": "0.0.2003",
                        "balance": 999997650
                    }
                ]
            },
            "account": "0.0.1562",
            "expiry_timestamp": null,
            "auto_renew_period": 7890000,
            "key": {
                "_type": "ED25519",
                "key": "ecda99c4c366e8069743d6fbb70c99dd3785c257e4e6f7567e795f3f5c77cb3b"
            },
            "deleted": false
        }
    ],
    "links": {
        "next": null
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

**Note:** The list of transaction types that you can query can be found [here](https://github.com/hashgraph/hedera-mirror-node/blob/master/hedera-mirror-rest/constants.js#L104).

### Response Details <a id="response-details"></a>

| Response Item | Description |
| :--- | :--- |
| **accounts** | The list of information returned for all accounts |
| **balance** | The timestamp and account balance of the account |
| **tokens** | The tokens and their balances associated to the specified account |
| **account** | The ID of the account |
| **expiry timestamp** | The expiry date for the entity as set by a create or update transaction |
| **auto renew period** | The period in which the account will autorenew |
| **key** | The key associated with the account |
| **deleted** | Whether the account was deleted or not \(Boolean\) |
| **entity type** | The type of Hedera service: account, file, or contract |
| **links.next** | Hyperlink to the next page of results |

### Optional Filtering <a id="optional-filtering"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">Operator</th>
      <th style="text-align:left">Example</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>lt</code> (less than)</td>
      <td style="text-align:left"><code>/api/v1/accounts?account.id=lt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account IDs less then 1000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>lte</code> (less than or equal to)</td>
      <td style="text-align:left"><code>/api/v1/accounts?account.id=lte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account IDs less than or equal to 1000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gt</code> (greater than)</td>
      <td style="text-align:left"><code>/api/v1/accounts?account.id=gt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account IDs greater than 1000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gte</code> (greater than or equal to)</td>
      <td style="text-align:left"><code>/api/v1/accounts?account.id=gte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account IDs greater than or equal to 1000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>order</code> (order <code>asc</code> or <code>desc</code> values)</td>
      <td
      style="text-align:left">
        <p><code>/api/v1/accounts?order=asc</code>
        </p>
        <p>&#x200B;</p>
        <p><code>/api/v1/accounts?order=desc</code>
        </p>
        </td>
        <td style="text-align:left">
          <p>Returns account information in ascending order</p>
          <p>Returns account information in descending order</p>
        </td>
    </tr>
  </tbody>
</table>

### Additional Examples <a id="additional-examples"></a>

| Example Requests | Description |
| :--- | :--- |
| `/api/v1/accounts?account.id=0.0.1001` | Returns the account information of account 1001 |
| `/api/v1/accounts?account.balance=gt:1000` | Returns all account information that have a balance greater than 1000 tinybars |
| `/api/v1/accounts?account.publickey=2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed 15e301049517438d61864` | Returns all account information for 2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed15e301049517438d61864 public key |
| `/api/v1/accounts/2?transactionType=cryptotransfer` | Returns the crypto transfer transactions for account 2. |

## Balances <a id="balances"></a>

The **balance** object represents the balance of accounts on the Hedera network. You can retrieve this to view the **most recent** balance of all the accounts on the network at that given time. The balances object returns the account ID and the balance in hbars. Balances are checked on a periodic basis and thus return the most recent snapshot of time captured prior to the request.

{% api-method method="get" host="" path=" /api/v1/balances" %}
{% api-method-summary %}
balances
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="account.id" type="string" required=false %}
The ID of the account to return the balance for
{% endapi-method-parameter %}

{% api-method-parameter name="account.balance" type="number" required=false %}
Returns a list of account IDs that have the specified balance \(tinybars\)
{% endapi-method-parameter %}

{% api-method-parameter name="timestamp" type="string" required=false %}
The timestamp the user would like to return account balances for. The timestamp can be provided in seconds format \(15000000000\) or in seconds.nanoseconds \(15000000000.010000000\).
{% endapi-method-parameter %}

{% api-method-parameter name="account.publickey" type="string" required=false %}
Returns the balance object for a specific public key
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
The balance for account 0.0.2004
{% endapi-method-response-example-description %}

```text
{
    "timestamp": "1602804267.903201001",
    "balances": [
        {
            "account": "0.0.2004",
            "balance": 10000000,
            "tokens": [
                {
                    "token_id": "0.0.2003",
                    "balance": 2350
                }
            ]
        }
    ],
    "links": {
        "next": "/api/v1/balances?limit=1&account.id=lt:0.0.2004"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details <a id="response-details-1"></a>

| Response Item | Description |
| :--- | :--- |
| **timestamp** | The seconds.nanoseconds of the timestamp at which the list of balances for each account are returned |
| **balances** | List of balances for each account |
| **account** | The ID of the account |
| **balance** | The balance of the account |
| **tokens** | The tokens that are associated to this account |
| **tokens.token\_id** | The ID of the token associated to this account |
| **tokens.balance** | The token balance for the specified token associated to this account |
| **links.next** | Hyperlink to the next page of results |

### Optional Filtering <a id="optional-filtering-1"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">Operator</th>
      <th style="text-align:left">Example</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>lt</code> (less than)</td>
      <td style="text-align:left"><code>/api/v1/balances?account.id=lt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns the balances of account IDs less than 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>lte</code> (less than or equal to)</td>
      <td style="text-align:left"><code>/api/v1/balances?account.id=lte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns the balances account IDs less than or equal to 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gt</code> (greater than)</td>
      <td style="text-align:left"><code>/api/v1/balances?account.id=gt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns the balances of account IDs greater than to 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gte</code> (greater than or equal to)</td>
      <td style="text-align:left"><code>/api/v1/balances?account.id=gte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns the balances of account IDs greater than or equal to 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>order</code> (order <code>asc</code> or <code>desc</code> values)</td>
      <td
      style="text-align:left">
        <p><code>/api/v1/balances?order=asc</code>
        </p>
        <p>&#x200B;</p>
        <p><code>/api/v1/balances?order=desc</code>
        </p>
        </td>
        <td style="text-align:left">
          <p>Lists balances in ascending order</p>
          <p>&#x200B;</p>
          <p>Lists balances in descending order</p>
        </td>
    </tr>
  </tbody>
</table>

### Additional Examples <a id="additional-examples"></a>

| Example Requests | Description |
| :--- | :--- |
| `/api/v1/balances?account.id=0.0.1000` | Returns balance for account ID 1,000 |
| `/api/v1/balances?account.balance=gt:1000` | Returns all account IDs that have a balance greater than 1000 tinybars |
| `/api/v1/balances?timestamp=1566562500.040961001` | Returns all account balances referencing the latest snapshot that occurred prior to 1566562500 seconds and 040961001 nanoseconds |
| `/api/v1/balances?account.publickey=2b60955bcbf0cf5e9ea880b52e5b6 3f664b08edf6ed15e301049517438d61864` | Returns balance information for 2b60955bcbf0cf5e9ea880b52e5b63f664b08edf6ed 15e301049517438d61864 public key |

## Transactions <a id="transactions"></a>

The **transaction** object represents the transactions processed on the Hedera network. You can retrieve this to view the transaction metadata information including transaction id, timestamp, transaction fee, transfer list, etc. If a transaction was submitted to multiple nodes, the successful transaction and duplicate transaction\(s\) will be returned as separate entries in the response with the same transaction ID. Duplicate transactions will still be assessed [network fees](https://www.hedera.com/fees).

{% api-method method="get" host="" path="/api/v1/transactions" %}
{% api-method-summary %}
transactions
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="transactionType" type="string" required=false %}
Filter transactions by transaction type. Please see link below to view all the transaction types you can query by.
{% endapi-method-parameter %}

{% api-method-parameter name="account.id" type="string" required=false %}
The ID of the account the user would like to return transactions for within the transferlist.
{% endapi-method-parameter %}

{% api-method-parameter name="timestamp" type="string" required=false %}
The specific timestamp the user would like to return transactions for
{% endapi-method-parameter %}

{% api-method-parameter name="result" type="string" required=false %}
If `result=fail`l the query returns all failed cryptocurrency transactions  
If the `result=success` the query returns all successful cryptocurrency transactions
{% endapi-method-parameter %}

{% api-method-parameter name="type" type="string" required=false %}
If `type=credit` the query returns all transactions that deposited into an account ID  
If `type=debit` the query returns all transactions that withdrew from an account ID
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
A request to return information for a transaction that was provided a specific timestamp
{% endapi-method-response-example-description %}

```text
{
    "transactions": [
        {
            "consensus_timestamp": "1602718525.642096000",
            "transaction_hash": "rcJfUqXgznzsV5p9SYW0BsmzmQ4jEmIjDJ4Xgl8R+j5d1uk95XWYhSyG00KAbAXl",
            "valid_start_timestamp": "1602718515.568165000",
            "charged_tx_fee": 85551,
            "memo_base64": "VHJhbnNmZXIgdG9rZW5fMjAyMC0xMC0xNFQyMzozNToyNS41NjEwODVa",
            "result": "SUCCESS",
            "name": "TOKENTRANSFERS",
            "max_fee": "1000000",
            "valid_duration_seconds": "120",
            "node": "0.0.3",
            "transaction_id": "0.0.1562-1602718515-568165000",
            "transfers": [
                {
                    "account": "0.0.3",
                    "amount": 5232
                },
                {
                    "account": "0.0.98",
                    "amount": 80319
                },
                {
                    "account": "0.0.1562",
                    "amount": -85551
                }
            ],
            "token_transfers": [
                {
                    "token_id": "0.0.1588",
                    "account": "0.0.1589",
                    "amount": 2350
                },
                {
                    "token_id": "0.0.1588",
                    "account": "0.0.1562",
                    "amount": -2350
                }
            ]
        }
    ],
    "links": {
        "next": null
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

**Note:** The list of transaction types that you can query can be found [here](https://github.com/hashgraph/hedera-mirror-node/blob/master/hedera-mirror-importer/src/main/java/com/hedera/mirror/importer/domain/TransactionTypeEnum.java#L32).

### Response Details <a id="response-details-2"></a>

| Response Item | Description |
| :--- | :--- |
| **consensus timestamp** | The consensus timestamp in seconds.nanoseconds |
| **transaction hash** | The hash value of the transaction processed on the Hedera network |
| **valid start timestamp** | The time the transaction is valid |
| **charged tx fee** | The transaction fee that was charged for that transaction |
| **transaction id** | The ID of the transaction |
| **memo base64** | The memo attached to the transaction encoded in Base64 format |
| **result** | Whether the cryptocurrency transaction was successful or not |
| **name** | The type of transaction |
| **max fee** | The maximum transaction fee the client is willing to pay |
| **valid duration seconds** | The seconds for which a submitted transaction is to be deemed valid beyond the start time. The transaction is invalid if consensusTimestamp is greater than transactionValidStart + valid\_duration\_seconds. |
| **node** | The ID of the node that submitted the transaction to the network |
| **transfers** | A list of the account IDs the crypto transfer occurred between and the amount that was transferred. A negative \(-\) sign indicates a debit to that account. The transfer list includes the transfers between the from account and to account, the transfer of the [node fee](https://www.hedera.com/fees), the transfer of the [network fee](https://www.hedera.com/fees), and the transfer of the [service fee](https://www.hedera.com/fees) for that transaction. If the transaction was not processed, a [network fee](https://www.hedera.com/fees) is still assessed. |
| **token transfers** | The token ID, account, and amount that was transferred to by this account in this transaction. This will not be listed if it did not occur in the transaction |
| **links.next** | A hyperlink to the next page of responses |

### Optional Filtering <a id="optional-filtering-2"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">Operator</th>
      <th style="text-align:left">Example</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>lt</code> (less than)</td>
      <td style="text-align:left"><code>/api/v1/transactions?account.id=lt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account.id transactions less than 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>lte</code> (less than or equal to)</td>
      <td style="text-align:left"><code>/api/v1/transactions?account.id=lte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account.id transactions less than or equal to 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gt</code> (greater than)</td>
      <td style="text-align:left"><code>/api/v1/transactions?account.id=gt:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account.id transactions greater than 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>gte</code> (greater than or equal to)</td>
      <td style="text-align:left"><code>api/v1/transactions?account.id=gte:0.0.1000</code>
      </td>
      <td style="text-align:left">Returns account.id transactions greater than or equal to 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>order</code> (order <code>asc</code> or <code>desc</code> values)</td>
      <td
      style="text-align:left">
        <p><code>/api/v1/transactions?order=asc</code>
        </p>
        <p>&#x200B;</p>
        <p><code>/api/v1/transactions?order=desc</code>
        </p>
        </td>
        <td style="text-align:left">
          <p>Lists transactions in ascending order Lists transactions descending order</p>
          <p>&#x200B;</p>
        </td>
    </tr>
  </tbody>
</table>

**Note**: It is recommended that the **account.id** query should not select no more than 1000 accounts in a query. If the range specified in the query results in selecting more than 1000 accounts, the API will automatically only search for the first 1000 accounts that match the query in the database and return the transactions for those. For example, if you use `?account.id=gt:0.0.15000` or if you use`?account.id=gt:0.0.15000&account.id=lt:0.0.30000`, then the API will only return results or some 1000 accounts in this range that match the rest of the query filters.‌

A single transaction can also be returned by specifying the transaction ID in the request. If a transaction was submitted to multiple nodes, the response will return entries for the successful transaction along with separate entries for the duplicate transaction\(s\). The "result" key indicates "success" for the node that processed the transaction and "DUPLICATE\_TRANSACTION" for each additional node submission. Duplicate entries are still charged network fees.

| Parameter | Description |
| :--- | :--- |
| `{transaction_ID}` | A specific transaction can be returned by specifying a transaction ID |

### Additional Examples <a id="additional-examples-1"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">Example Request</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>/api/v1/transactions/?account.id=0.0.1000</code>
      </td>
      <td style="text-align:left">Returns transaction for account ID 1,000</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>/api/v1/transactions?timestamp=1565779209.711927001</code>
      </td>
      <td style="text-align:left">Returns transactions at 1565779209 seconds and 711927001 nanoseconds</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>/api/v1/transactions?result=fail</code>
      </td>
      <td style="text-align:left">Returns all transactions that have failed</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>/api/v1/transactions?account.id=0.0.13622&amp;type=credit</code>  <code>/api/v1/transactions?account.id=0.0.13622&amp;type=debit</code>
      </td>
      <td style="text-align:left">
        <p>Returns all transactions that deposited into an account ID 0.0.13622</p>
        <p>&#x200B;</p>
        <p>Returns all transactions that withdrew from account ID 0.0.13622
          <br />
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>/api/v1/transactions?transactionType=cryptotransfer</code>
      </td>
      <td style="text-align:left">Returns all cryptotransfer transactions</td>
    </tr>
  </tbody>
</table>

{% api-method method="get" host="" path="/api/v1/topics/:topicId/messages/" %}
{% api-method-summary %}
topic messages
{% endapi-method-summary %}

{% api-method-description %}
Returns topic messages for a given consensus timestamp
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="sequenceNumber" type="number" required=false %}
The sequence number of the message relative to other messages submitted to the same topic
{% endapi-method-parameter %}

{% api-method-parameter name="topicID" type="string" required=true %}
The ID of the topic in x.y.z or z format
{% endapi-method-parameter %}

{% api-method-parameter name="consensusTimestamp" type="string" required=false %}
The consensus timestamp of a message in seconds.nanoseconds
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
  "responseJson": {
    "consensus_timestamp": "1234567890.000000002",
    "topic_id": "0.0.7",
    "message": "bWVzc2FnZQ==",
    "running_hash": "cnVubmluZ19oYXNo",
    "sequence_number": 2
  }
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details

| Response Item | Description |
| :--- | :--- |
| **consensus\_timestamp** | The consensus timestamp of the message in seconds.nanoseconds |
| **topic\_id** | The ID of the topic the message was submitted to |
| **message** | The content of the message |
| **running\_hash** | The new running hash of the topic that received the message |
| **sequence\_number** | The sequence number of the message relative to all other messages for the same topic |

## Tokens

{% api-method method="get" host="" path="/api/v1/tokens" %}
{% api-method-summary %}
tokens
{% endapi-method-summary %}

{% api-method-description %}
Returns the list of tokens created in a Hedera network.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="tokens" type="string" required=false %}
The tokens created on a Hedera network.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="publickey" type="string" required=false %}
The public key to return all tokens for
{% endapi-method-parameter %}

{% api-method-parameter name="token.id" type="string" required=false %}
The ID of the token to return the query for
{% endapi-method-parameter %}

{% api-method-parameter name="account.id" type="string" required=false %}
The ID of the account to return the tokens for
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
    "tokens": [
        {
            "token_id": "0.0.2844",
            "symbol": "VCIQSEQBONTOMXCBEWUG",
            "admin_key": {
                "_type": "ED25519",
                "key": "f3cfc6e46bf3234db7d9c6bb462014cc86362a04e606c02ea634883ae69284d7"
            }
        }
    ],
    "links": {
        "next": "/api/v1/tokens?limit=1&token.id=gt:0.0.2844"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details

| Response Item | Description |
| :--- | :--- |
| **token\_Id** | The ID of the token in x.y.z format |
| **symbol** | The symbol of the token |
| **adminKey** | The admin key for the token |

### Additional Examples

| Example Request | Description |
| :--- | :--- |
| `/api/v1/tokens?publickey=3c3d546321ff6f63d70 1d2ec5c277095874e19f4a235bee1e6bb19258bf362be` | All tokens with matching admin key |
| `/api/v1/tokens?account.id=0.0.8` | All tokens for matching account |
| `/api/v1/tokens?token.id=gt:0.0.1001` | All tokens in range |
| `/api/v1/tokens?order=desc` | All tokens in descending order of `token.id` |
| `/api/v1/tokens?limit=x` | All tokens taking the first `x` number of tokens |

{% api-method method="get" host="" path="/api/v1/tokens/:token\_id/balances" %}
{% api-method-summary %}
token balances
{% endapi-method-summary %}

{% api-method-description %}
Returns all the accounts and token balance for the specified token ID.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="token\_id" type="string" required=true %}
The token ID to get the balances for
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="account.publickey" type="string" required=false %}
The public key
{% endapi-method-parameter %}

{% api-method-parameter name="account.id" type="string" required=false %}
The ID of the account
{% endapi-method-parameter %}

{% api-method-parameter name="account.balance" type="string" required=false %}
The balance to query for
{% endapi-method-parameter %}

{% api-method-parameter name="timestamp" type="string" required=false %}
The timestamp to query for
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
The request response
{% endapi-method-response-example-description %}

```text
   {
        "timestamp": "0.000002345",
        "balances": [
          {
            "account": "0.15.10",
            "balance": 100
          },
          {
            "account": "0.15.9",
            "balance": 90
          },
          {
            "account": "0.15.8",
            "balance": 80
          }
        ],
        "links": {
          "next": null
        }
    }
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details

| Response Item | Description |
| :--- | :--- |
| **timestamp** | The timestamp of the recorded  balances in seconds.nanoseconds |
| **balances** | The balance of the tokens in those accounts |
| **account** | The ID of the account that has the token balance |
| **balance** | The balance of the token associated with the account |

### Additional Examples

| Example Request | Description |
| :--- | :--- |
| `/api/v1/tokens/<token_id>/balances?order=asc` | The balance of the token in ascending order |
| `/api/v1/tokens/<token_id>/balances?account.id=0.0.1000` | The balance of the token for account ID 0.0.1000 |
| `/api/v1/tokens/<token_id>/balances?account.balance=gt:1000` | The balance for the token greater than 1000 |
| `/api/v1/tokens/<token_id>/balances?timestamp=1566562500.040961001` | The token balances for the specified timestamp |

{% api-method method="get" host="" path="/api/v1/tokens/:tokenId" %}
{% api-method-summary %}
token info
{% endapi-method-summary %}

{% api-method-description %}
Returns the specified token information.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="token\_id" type="object" required=true %}
The ID of the token to return the information for in x.y.z format.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
    "admin_key": {
        "_type": "ED25519",
        "key": "ae73aced5f817a772d9db4389d198cfccd73496912a3d2581efba9638c6558a2"
    },
    "auto_renew_account": "0.0.2848",
    "auto_renew_period": null,
    "created_timestamp": "1605365995.140259000",
    "decimals": "3",
    "expiry_timestamp": null,
    "freeze_default": false,
    "freeze_key": {
        "_type": "ED25519",
        "key": "5446f4d87f8091dadcf2c290c52dcfe433276abbdc9153b797c50845b4917d1c"
    },
    "initial_supply": "1300000000",
    "kyc_key": {
        "_type": "ED25519",
        "key": "4882f24d220b9e595e99a5ab6cd50c55b95cde3ccaaf9146d503e901bb513133"
    },
    "modified_timestamp": "1605365995.140259000",
    "name": "CwOE78V-ainb0MNHPeIc4Sd9nYS9*PCJAWjTW1SkB75!tGFdyR",
    "supply_key": {
        "_type": "ED25519",
        "key": "84dfe9ec8519a4f1a8aa3e2e771e3025fd1c3b4b3b5ad41622e202602e63832d"
    },
    "symbol": "WWHNTTGINMPLZELLJLRX",
    "token_id": "0.0.2849",
    "total_supply": "1300000000",
    "treasury_account_id": "0.0.2847",
    "wipe_key": {
        "_type": "ED25519",
        "key": "074f7d073c6e910ee29f55dca78494da54216ec528eae10c448888c5d8e22e8f"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details

| Response Item | Description |
| :--- | :--- |
| **admin\_key** | The token's admin key |
| **auto\_renew\_account** | The auto renew account ID |
| **auto\_renew\_period** | The period at which the auto renew account will be charged a renewal fee |
| **created\_timestamp** | The timestamp of when the token was created |
| **decimals** | The number of decimal places a token is divisible by |
| **expiry\_timestamp** | The epoch second at which the token should expire |
| **freeze\_default** | Whether or not accounts created |
| **freeze\_key** | The freeze key for the token |
| **initial\_suppl**y | The initial supply of the token |
| **kyc\_key** | The KYC key for the token |
| **modified\_timestamp** | The last time the token properties were modified |
| **name** | The name of the token |
| **supply\_key** | The supply key for the token |
| **symbol** | The token symbol |
| **token\_id** | The token ID |
| **total\_supply** | The total supply of the token |
| **treasury\_account\_id** | The treasury account of the token |
| **wipe\_key** | The wipe key for the token |

## Schedule Transactions

{% api-method method="get" host="" path="/api/v1/schedules" %}
{% api-method-summary %}
schedule list
{% endapi-method-summary %}

{% api-method-description %}
Returns a list of all scheduled transactions for a network.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-query-parameters %}
{% api-method-parameter name="schedule.id" type="string" required=false %}

{% endapi-method-parameter %}

{% api-method-parameter name="account.id" type="string" required=false %}
All schedule transactions matching the creator account
{% endapi-method-parameter %}

{% api-method-parameter name="executed" type="boolean" required=false %}
If equal to true, returns all schedule transactions that were executed
{% endapi-method-parameter %}

{% api-method-parameter name="limit" type="integer" required=false %}
Limits results to the first N schedule transactions
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
  "schedules": [
    {
      "admin_key": {
        "_type": "ProtobufEncoded",
        "key": "7b2233222c2233222c2233227d"
      },
      "consensus_timestamp": "1234567890.000000001",
      "creator_account_id": "0.0.100",
      "executed_timestamp": "1234567890.000000002",
      "memo": "Created per council decision dated 1/21/21",
      "payer_account_id": "0.0.101",
      "schedule_id": "0.0.102",
      "signatures": [
        {
          "consensus_timestamp": "1234567890.000000001",
          "public_key_prefix": "H0vpig==",
          "signature": "0o0gC7p9SPUH4UD6Yiirp/Kf+LKj8qjuuFdC3AU87HE="
        },
        {
          "consensus_timestamp": "1234567890.000000002",
          "public_key_prefix": "GvxuXg==",
          "signature": "w9mHyHQpTrlbLfn9NrBlZiMxV2mvLvNEw1hoeAECtcA="
        }
      ],
      "transaction_body": "KcyxTMX2XFL+t0KSsB1S/c8t5kXTlLU3BGgNttEy7Gw="
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details <a id="response-details-1"></a>

| Response Item | Description |
| :--- | :--- |
| **schedules** | List of schedules |
| **adminKey** | The admin key on the schedule |
| **adminKey.\_type** | The type of key |
| **adminKey.key** | The admin public key |
| **consensus\_timestamp** | The consensus timestamp of when the schedule was created |
| **creator\_account\_id** | The account ID of the creator of the schedule |
| **executed\_timestamp** | The timestamp at which the transaction that was scheduled was executed at |
| **memo** | A string of characters associated with the memo if set |
| **payer\_account\_id** | The account ID of the account paying for the execution of the transaction |
| **schedule\_id** | The ID of the schedule entity |
| **signatures** | The list of keys that signed the transaction |
| **signatures.consensus\_timestamp** | The consensus timestamp at which the signature was added |
| **signatures.public\_key\_prefix** | The signatures public key prefix |
| **signatures.signature** | The signature of the key that signed the schedule transaction |
| **transaction\_body** | The transaction body of the transaction that was scheduled |
| **links.next** | Hyperlink to the next page of results |

{% api-method method="get" host="" path="/api/v1/schedules/{scheduleId}" %}
{% api-method-summary %}
schedule transaction
{% endapi-method-summary %}

{% api-method-description %}
Returns the specified schedule entity information.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="scheduleId" type="string" required=true %}
The ID of the schedule to return the information for.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
{
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2233222c2233222c2233227d"
  },
  "consensus_timestamp": "1234567890.000000001",
  "creator_account_id": "0.0.100",
  "executed_timestamp": "1234567890.000000002",
  "memo": "Created per council decision dated 1/21/21",
  "payer_account_id": "0.0.101",
  "schedule_id": "0.0.102",
  "signatures": [
    {
      "consensus_timestamp": "1234567890.000000001",
      "public_key_prefix": "H0vpig==",
      "signature": "0o0gC7p9SPUH4UD6Yiirp/Kf+LKj8qjuuFdC3AU87HE="
    },
    {
      "consensus_timestamp": "1234567890.000000002",
      "public_key_prefix": "GvxuXg==",
      "signature": "w9mHyHQpTrlbLfn9NrBlZiMxV2mvLvNEw1hoeAECtcA="
    }
  ],
  "transaction_body": "KcyxTMX2XFL+t0KSsB1S/c8t5kXTlLU3BGgNttEy7Gw="
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

### Response Details <a id="response-details-1"></a>

| Response Item | Description |
| :--- | :--- |
| **adminKey** | The admin key on the schedule |
| **adminKey.\_type** | The type of key |
| **adminKey.key** | The admin public key |
| **consensus\_timestamp** | The consensus timestamp of when the schedule was created |
| **creator\_account\_id** | The account ID of the creator of the schedule |
| **executed\_timestamp** | The timestamp at which the transaction that was scheduled was executed at |
| **memo** | A string of characters associated with the memo if set |
| **payer\_account\_id** | The account ID of the account paying for the execution of the transaction |
| **schedule\_id** | The ID of the schedule entity |
| **signatures** | The list of keys that signed the transaction |
| **signatures.consensus\_timestamp** | The consensus timestamp at which the signature was added |
| **signatures.public\_key\_prefix** | The signatures public key prefix |
| **signatures.signature** | The signature of the key that signed the schedule transaction |
| **transaction\_body** | The transaction body of the transaction that was scheduled |

## State Proof Alpha

The Hedera Mirror Node state proof alpha api provides the ability to cryptographically prove a transaction is valid on Hedera network. The request returns the content of the address book file, signature files, and record file that can be used to validate the transaction occurred on the Hedera network. The address book file contains the consensus node account IDs and their public key files. The signature files are of the supermajority consensus nodes that signed the record file the transaction is contained in.

{% api-method method="get" host="" path="/api/v1/transactions/:transaction-id/stateproof" %}
{% api-method-summary %}
transaction state proof
{% endapi-method-summary %}

{% api-method-description %}
Returns a state proof \(alpha\) for a given transaction.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="transaction-id" type="string" required=true %}
The ID of the transaction to return the state proof in shard.realm.num-sss-nnn format where sss is in seconds and nnn is in nanoseconds of the transaction valid start time.
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```text
 "record_file": "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",
    "signature_files": {
        "0.0.4": "BOyLjxj4hYhMfvVVYcd4ogJhJlMkWVo3PFeUugIKsGo3imdL/ja9ByL1VQ87cDk76QMAAAGAsc5rXqosU0Pnqsnri3ZOwIT0GcY/sy9ssS9b824ArjM/P5X3JM5AvZ4LVbjOxeACnw1oTAW5Ym1h24Yh54G/BfUMmBqQ7byG0tAj3/6n717tg186G+20j4un8JUHyMClr8NIkP3wHTY78HBQjGEnlhtp1rulR8iEHJGFd04hBqy59abEVdZP2vRmwAVG2qw26RBYMCZZhubeZz4wrhY/TziK+degZDU8J75l8MtyHmBdbGipWDkIdNwcEaT0HWnd/jG0VDIYjQPAehdxcQarWmi5M4x9RJKbGB2w5i+TLH6UPW/YgYU3YicNf1BMRRHLMBxAB9K+AkXM7Shtb6d0neNZmPthXI88l8uSo3lUZ5LsbgOG2jA95VStftgR+qOd3/Udgj9TZDIHjy3nSENScedrNwUeJX5Pd4Z9/sAsvAza87XpXpuuBY+FbWJbYgvmHI932RNuCdYVPoxYuyR2o32g0f6Ytv5dWcU4FqYrzqe/B6IO+4wnL5JcubQuqnJO",
        "0.0.3": "BOyLjxj4hYhMfvVVYcd4ogJhJlMkWVo3PFeUugIKsGo3imdL/ja9ByL1VQ87cDk76QMAAAGAFTCsteCePAehXH4vwKOkTEXq0wQNRxXtvWLbtoICN/8xAicYROvKcePftw+ghi1eNVo3znMBTj9K73LxknNrb0eesRtKD/rSfBUggivQpn21wWuOtlE5UNF5QwXKBEAYVeQJ8YC7+RRCzWuBa8+Il7I/ygrRIlgSYOqmJ5zH4LmZ7gC7IqtPhi7+pjasm2VdrUzX35xECTatZSCr3SKjlXDwSHn5vwNlzVxBWkCRD5v+1nQGXKuY7+7BtN4lN7yxrV5HWdaN95A3UVayN71KyObMWf+LLQhiMt3zquUw030ZGsTdD33dlbm6/z3xMNTLpeL5m2fBJN0NwS/L/sCOFd+IGzuPisySyD4sZffMwRgNtQDgau0qEVVp762EARt+2y7Ed4rNYTBTYg1l21/PBnXoETCyNjGYxnyQ3TE4/vv0IaL/WCyGn9ydGBi/tIC8bJhaHnoTmfpboNnw5irWICZkf+gxgKmwUZx/hoh0v3AsU10GpTplezM7FnY59Kk9",
        "0.0.6": "BOyLjxj4hYhMfvVVYcd4ogJhJlMkWVo3PFeUugIKsGo3imdL/ja9ByL1VQ87cDk76QMAAAGAslgsTaBTB8xkBAoxcCyY1eVCnXO23AcisL1iIOWIrkx33OJKw27PRpJqAffVFpuy0lLN/yY62ZBUNmgWrnL+mX72goWOGB7WvMlttzoJDMwiXvjwt30cDwbXsk0ePDCP68eyoipsGs5EhtljWautn/SvrJlqiO2uyLurqafkd7Ztpqj9EMykYa7GpjUxLuofay29Rhtqv0lSRopiThYz8I5EOAPFJjz9zW1K0UNu6ix7LmcHnY0udAduMFP/Uz4jubV+KLMltiqyGBmvtTad1va5BwmW1IG7uXqyJnvWDj5Pd1DER3jGPg9nr3zoowgBcNGknfVJNoD6EnzOpaOIlBYYgtntdFfm+jCUxVxfsn7UlI3nbU8Sa1UjKdXOhr0Jk9M9v8o7rqlMcy+2m1Z6gRFkbIASkgYAl7pHxsUkTB2leTW+HAAkQ8YgwssFXvBYSkx5l14yF7wi4Yt1y39oc/oTnvdVASY81qE8LUlwzkl2VuHkQR4vJbnkThQltg+T",
        "0.0.5": "BOyLjxj4hYhMfvVVYcd4ogJhJlMkWVo3PFeUugIKsGo3imdL/ja9ByL1VQ87cDk76QMAAAGAlrVy6z4RZ+us4BFXrWI+hWClE51wEckzBw3HZCsJasV9OINuoqD5pRYQ7o0L6FUyOTxLQ8g/bl5sBES7drI5RKo1DS8LFCp8cQHw4//j+Cvvyxr4V465puSyMvsq2WAVUEXmQwL40D9eB5mjKqpvr+NoKME+Hf8qkU6r2c3gEuEOcQywxwTJyzyPaNsQ6dLRgdmbsRdXx5Y8I6BmlZXUShJxfjqleth0noeGrOqL2U0lh0tBm5qDHNHTLzILerkFUf9eBlCO9YX+wwWH5uvb61pOvqkgFNbGmF6fkP3PID7XAQm4Dk9shMkxfa8s4tMPCX5F/Mt3mvcBEodOKrBAl++YMGZowOxudF3NbxZT4XtrT1VhP9WvPmjabneOoAABhDdtXPxLk5XwE27ZNqqxTk3rqBOBky3LZ8e4hGcanJe/9ZCgfnFKXMWFjfYbSieF10eTo6nQVbyuibHTrQaYGb+HrbroJBT12bA2yOx5E4LvGfweWfm6VZTBx2u+fPYW"
    },
    "address_books": [
        "CtYGGgUwLjAuNSLMBjMwODIwMWEyMzAwZDA2MDkyYTg2NDg4NmY3MGQwMTAxMDEwNTAwMDM4MjAxOGYwMDMwODIwMThhMDI4MjAxODEwMDliYTQ1N2I3MzMwNWYwNGE5MWNjNDZiMWI5NjVjNGU4NDE3NTFhYmM4YjE0MTVhMGJhZGZkMWYzMmMyNDgyMzg2YTIyNzI1ZWI3ZWM3NGRlYTIxZTUwNjE3ZDY0OGVhNWFjMzkzNzQxYWIwMWI4ZWZiMzIxMjM5YjhkNGZkYjFkZmJlYjllM2YzOWFhNDY1ODBkZDA0NWQxOGNhNDRkMDAyYzM3ZGRiNTI3Y2NlNGRkYzMyYmZjNzM0MTk2NzFmNGNhNDQ2NGEzZjJhODRmYzg1YzcxYWNmMGU1YTg5NjI2ZGY2OWE4MTQ3NGVkMTY1MjlmODAxYThhZmE5N2U0MzVjNGUwNGE5NjRhMzU3NTI3Mjg4ODQzZTU4ZjBhMDVjZjUxNTNlZTQ1MDdiMmM2OGIzZDdmYjU0YWU2YTk1YTk1OWM4N2ExMmY2MzBlOTVjN2IxYjNjMzY5NWU4NTg2NjI0MTc5MjZkNzZjMTY5ODNmYWY2MTIyNTAzODc0NTkwN2U5Y2YxM2Q2N2MyYWNkNTAzY2E0NTFjODU5MzNhYzQxMThhY2MyNzk4MDFjYjk2ODM0OTkwMzE0NWNlZDI3NjI5ZGQwODkxNjMxNzA5MzU4N2E3N2MyMjA1Y2ZhNTI1NDNiNTNjM2I2ZWExNWI4NGUzZDJjMzBjMWVkNzUyYTQ2MzNjMzZiMjViOTg5M2VhMDJhZDU2MmViOWI3ODY4YjNiNGY0N2Y0YTI1ZTM1NjA2NDk2MmFjN2IyNWU1ODI5NDRmMDBkMzA3OThhMjYyZjkyMTRkOGM1ZTc0ZDBhODM3NmNjMmQ2YmE2NGUxOGY1ZTRhNDBhZmFjNjI1MDYyZDJjYTIzY2QyODAwNzA4MzIxZDM4MzQzMTRmMGU1ODQ0ODU5MjMyNjczYTMyZTcwYWUwZDcxMWUzMTA1ODFiY2RiMTRlODcxMzQ2OTRjNmUwOTMwZjQ2YjM3Yjk2ZDQ5YTY0NTczOTQ3MzMxZTdlNTA3ZDllNTZkZTVlNjE0NmYyZjAyMDMwMTAwMDEK1gYaBTAuMC42IswGMzA4MjAxYTIzMDBkMDYwOTJhODY0ODg2ZjcwZDAxMDEwMTA1MDAwMzgyMDE4ZjAwMzA4MjAxOGEwMjgyMDE4MTAwYzQyY2NhYzVmYmM2OTFmYmJlYmRhODdmZmQxZTc1YmRjZDg5MjI0OTRjZjQ0ZmRiY2NlZTQ5Nzg4NTIxYzM3OGJmNzdkYjA5MzRlYzBkMjE4M2Q3YzUxZGI2NmY4NjRjMTFhYjdkZTFhYzNjNGNmZGMxZjA5M2EyZDZmMzdlMmIzNGNiZTRjODEzMWY5NjgzYWQ0Mjg3OGM4M2QzNTU0YzY0NWFhMTY3YmNmYjA2NGE4M2RjNDVjNWIxMTU4NDk5ZjlkOTI1ODdmZmY3YWJjZDVmMjIxY2Q4MTUwNTQ4NDEzMDAwZmE2ZTU2NTkwODliMWRmZDY1NzY2ZWE3OGVhZWRmY2E2YjQ1NDU1ZmQ4YWI1OTg0ZGJlMzVlNTc5NWQyYzYzNWVhNzk3NGQ0M2U4ZWFlNGZlYmZmZTQ5MmU3MDdiNDhiMWIwZmM2NDgxYWU5ZTA5ZDM5MTMzMDA5YjdkMjY0MDJlNmU1MmU1ZTkxYjJiMzgwZDg4ZjBiZTdmYjRiMzAzZTcwMjE5Nzg1MDU3YWE5NGNlOTI0YzQ5MjZlOTE2NTY5Mjg2ZTg2YjNiYTY1MWNhMmEwYTYzZGY0ZjY5MDdmZWZlMzQ4M2Q5M2I0Y2UxZDRkMDNjNzE0MjExMTM3NWIyYzJjNTFkNGViODM5ZTM3YWY1MzBiMmNiZDZmNTBkNGNiMzZlMjc5MzcxNzBkOWNkZGFjMGFjZTJjYzI0YjgwNGIwYTI3MzUxY2Y4MzBiNzY1MjVlMjZkZmI5ZGJmNDlhMDU2NjI0YTc2ODYyNDk0ZTcyNjNkMGQ3MGNlYmFlOTUyOTQzZTU1ODQyZjVjYWQxM2ZjZjYwYTJlNmRjZjdhMWQ1MzNmM2E1YmI1NGVjMjE5MThjNzZlNTI1YmEyOTE0NjY3NTgzMWUxN2UzNmM2MWZlODU0OTg4MjhkMDliNzYyMDE1NDEyYjJlNTI3ODQ5YmFlYzFjZmZjNzdkZTRjMjk0YzU1MDgxMWU1OThmZjI0ZGExNWEzNDU2OWRkMDIwMzAxMDAwMQrWBhoFMC4wLjMizAYzMDgyMDFhMjMwMGQwNjA5MmE4NjQ4ODZmNzBkMDEwMTAxMDUwMDAzODIwMThmMDAzMDgyMDE4YTAyODIwMTgxMDA5ZjFmOGExMjFjMmZkNmM3NmZkNTA4ZDNlNDI5ZjBjNjRiY2I0NGM4MmE3MDU3MzU1MmFhZGNhZDA3MTU2OWU3MjE5NThmNWE1ZDA5Zjk1ODdmZmFmY2ZiZTUzNDFhMmYwMTE0YWNhZTM0NmVmM2M5MDIxM2QzNDM2ZWJiMjdmNDM1MGM5OTBjNWM4YzNmOGUxZTM2NzA3YmMwOGQ0MjU2MDgyM2UzZjI0ZTA5YTAzYWQwOTU1YTUwOTgwMTk2MjlkZDA0YjI3YjI1MWRjZTA1NWYzZGRjYjBhNDFkNjZmMDk0MWIwYjg3Y2RmZTM0OThkNDYwMzhhYjVkZjA2ZjYyYTVhZGUwODU5ODU3M2E4OGM4ZjU4NjBkYzE0OTJhNmUxODY0ODVhOWIxMzI1MGU2ZDE3YjgwY2QzOWM1YzgxOTEwOWU3M2NhNzMyZGIyM2VmOGJhYTc3NmVjODVjZTAwOTFiZWNiMmVkZWZiYWE1ZWQzZTVkYmZiZDFmODg1YTRmYTg4MWFmM2YxNDRhOGE1NjU4NTM1MzNkODkzOTM1OTIwODZiMmQxZDM2MmU0NWJmZTFmYjQ1NjgzYWJhNmM2NDA5NzlhZDZiNDY4NzcxODQ3MjZjNmViZDU4YjJlYWU4NWM3Y2ZlM2ZiYWJlZjVmNmNjZWQ4NTAwMzRiMzg0NzIwNmMyZDY3OGMzNjE4NzYwMjZiOGQzNTFlMDAyYWY1ZTBmZmU2ZjViMWYyOTVmZGMyZjQ2OWNhYTJkMjM4MWVhMGI0OGNhOTg3Y2MyYzhlNjM1ZThiMTljZTVlMTcyYTkzNzYxYThkNDkwYTlhNDUxOGQ3MjU1ODgwYTE0ZDc3YjdiYTc3NDg5MmI5MmE0MGJiODEzNjJlMzRmYzZkNTE3OGQ5YjMwMTEyOTM0MjA1Y2I3N2ZiOWEyODI0MjczOTQ1NjRhODU1NGVhNDcyODZhNDdmODYyMzllNzVjOTQ3ODljZTk4Yzk5ODQ0NzgyNDYyOTQ0ZjYxMzE2N2Q3YjUwMjAzMDEwMDAxCtYGGgUwLjAuNCLMBjMwODIwMWEyMzAwZDA2MDkyYTg2NDg4NmY3MGQwMTAxMDEwNTAwMDM4MjAxOGYwMDMwODIwMThhMDI4MjAxODEwMGM1NTdhZjU3OWZhODM1MDFiZTg5OWIyODkwNzc2NWJmZGZjZDUyYWI0MzJiMDE5NWExZjFlY2Q4NmZjMDBhYjZjNTUwOWIwZmRkOTdlZGQzY2I1Y2VhNTZhMjk1ZjMxMmFiYjU1MDgzMWRiZjk2M2Y0NTAxMThiNGZjYzZlMjJjZjQ2NzYyMDBjZTljYzhlZGZiYmY1NThkYzY5ZjAyNDI2NGFkN2QzZGFiMjNiZWQyMTMzYzI3NGU2OTM0NDg5MTU1ZGIxMDg3ZjkwMzcwOTA1YzY0MTg1YTYyMTFkYzc0MmZiOWE2OTA5ZDgyMTg2OTQ3YjI3NzQ2M2RmYjNmZjBhY2Q0N2VmZjEyZWFkMWY2OTcyZWYyYzEyMDM3OTNjNDVlNzc1NzViZTRmYTExMGM3ZTQwZmE4ZGI5YzYxODdkMTEzZjQ3MDQwMTQxNzkwNzFhYmY1OWJlN2QyYjBkZTgyZGU0MjE1ZGMyNTUwNmIxYzljMjZlNDkxNzQwMWM5OTc1MDZlMzc3ZTZiZjAzYjY4ODcyN2U3OTQwZmFkNjljNWUwZGEzY2Q1Y2JkMmJlNzc3MzUwYWVhMmQwZDQ3ZTk3YTQ0OGM4NGJlNmNlMTM0ZDY0YmVlMDk4NWMyOTE2MmY0YzFlNTY3Y2NhOTNkMDZhM2MxYmU4YWJjZTM1YjU1N2ZiNzdmNGZlNjcxYTY2ZGVjNzkwNzU2ZDBlODgxODE2NWYyYmFjYWE4OTFhYWU3YWM3NDM3ZmM3MTc1YjZlYjZkZWI3NDcyMzc4NzUxYmI2YmY5YjBlMTQ4M2Y5NjY4ZTlmZGJkNTYwNGMzOWIxNGQ5ZTJiZWRlZWM4NDZhOTgwZDcwNGQxNzFlN2JhNGI3ZmNkMWEzMGQ5NDVjYTEyZjQ3YTMyNWQ5Mzk4YWExOGY5NzA2NjA1NGQ0ZDE1ZmM4OTk0ZTJkZWJlNzNlOTI3MWQ1NDg2ODNmNjFlYTQ0ZmIyNTA3MWUzNTE4YTc4ZWQzZWIzN2U3MWEwNjkxZjI2NzAyMDMwMTAwMDE="
    ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

| Response Item | Description | Format |
| :--- | :--- | :--- |
| **record\_file** | The record file content for the specified transaction | base64 encoding |
| **signature\_files** | The signatures file content from the consensus nodes that signed the transaction | base64 encoding |
| **address\_books** | The address book file contents | base64 encoding |

Save the response to a json file and use the [check-state-proof](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof) cli commands to confirm the validity of the transaction. You can find the instructions [here](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof).

