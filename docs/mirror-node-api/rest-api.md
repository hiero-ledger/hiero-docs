---
description: The mirror node REST API offers the ability to query transaction information.
---

# REST API

Hedera Mirror Nodes store the history of transactions that occurred on mainnet, testnet, and previewnet. Each transaction generates a record that is stored in a record file. The transaction contents can be accessed by the mirror node Rest APIs

To make a request, use the network endpoint and the Rest API of choice. For example, to get a list of transactions on mainnet you would make the following request.

{% embed url="https://mainnet-public.mirrornode.hedera.com/api/v1/transactions" %}

You can also view the Hedera Mirror Node Swagger UI documentation where you can submit requests and view responses for previewnet, testnet, and mainnet.

{% embed url="https://testnet.mirrornode.hedera.com/api/v1/docs#/" %}

{% hint style="info" %}
**MAINNET**\
****[https://mainnet-public.mirrornode.hedera.com](https://mainnet-public.mirrornode.hedera.com/)

**TESTNET**\
[https://testnet.mirrornode.hedera.com](https://testnet.mirrornode.hedera.com/)

**PREVIEWNET**\
[https://previewnet.mirrornode.hedera.com](https://previewnet.mirrornode.hedera.com/api/v1/transactions)

You may also check out [DragonGlass](https://app.dragonglass.me/hedera/home) and [Kabuto](https://kabuto.sh/) as alternatives.‌
{% endhint %}

{% hint style="warning" %}
Public mainnet mirror node requests are throttled at 100 requests per second (rps). This may change in the future depending upon performance or security considerations. At this time, no authentication is required.
{% endhint %}

## Accounts <a href="#accounts" id="accounts"></a>

The **accounts** object represents the information associated with an account and returns a list of account information.‌

Account IDs take the following format: **0.0.\<account number>**.‌

Example: 0.0.1000‌

Account IDs can also take the account number as an input value. For example, for account ID 0.0.1000, the number 1000 can be specified in the request.

{% swagger baseUrl="" path=" /api/v1/accounts" method="get" summary="accounts" %}
{% swagger-description %}
Returns a list of all account entity items on the network
{% endswagger-description %}

{% swagger-parameter in="query" name="account.id" type="string" %}
The ID of the account to return account information for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.balance" type="number" %}
Returns a list of account IDs that have the specified balance
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.publickey" type="string" %}
Returns the account information for the specified public key
{% endswagger-parameter %}

{% swagger-parameter in="query" name="balance" type="boolean" %}
Whether to include balance information or not

\




_Default value_

: true
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

\




_Default value_

: 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
   "account":"0.0.1",
   "auto_renew_period":null,
   "balance":{
      "balance":0,
      "timestamp":"1631864700.327170000",
      "tokens":[
         
      ]
   },
   "deleted":null,
   "expiry_timestamp":null,
   "key":null,
   "max_automatic_token_associations":null,
   "memo":null,
   "receiver_sig_required":null
}

```
{% endswagger-response %}
{% endswagger %}

#### Response Details <a href="#response-details" id="response-details"></a>

| **Response Item**                       | **Description**                                                                        |
| --------------------------------------- | -------------------------------------------------------------------------------------- |
| **account**                             | The ID of the account                                                                  |
| **auto renew period**                   | The period in which the account will auto renew                                        |
| **balance**                             | The timestamp and account balance of the account                                       |
| **tokens**                              | The tokens and their balances associated to the specified account                      |
| **deleted**                             | Whether the account was deleted or not (Boolean)                                       |
| **expiry timestamp**                    | The expiry date for the entity as set by a create or update transaction                |
| **key**                                 | The public key associated with the account                                             |
| **max\_automatic\_token\_associations** | The number of automatic token associations, if any                                     |
| **memo**                                | The account memo, if any                                                               |
| **receiver\_sig\_required**             | Whether or not the account requires a signature to receive a transfer into the account |
| **links.next**                          | Hyperlink to the next page of results                                                  |

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

{% swagger method="get" path="/api/v1/accounts/{accountId}" baseUrl="" summary="account by account ID" %}
{% swagger-description %}
Return the account transactions and balance information given an account id
{% endswagger-description %}

{% swagger-parameter in="path" name="accountId" type="String" required="true" %}
The ID of the account in 0.0.accountNumber format
{% endswagger-parameter %}

{% swagger-parameter in="query" name="transactionType" type="String" %}
_Available values_

 : CONSENSUSCREATETOPIC, CONSENSUSDELETETOPIC, CONSENSUSSUBMITMESSAGE, CONSENSUSUPDATETOPIC, CONTRACTCALL, CONTRACTCREATEINSTANCE, CONTRACTDELETEINSTANCE, CONTRACTUPDATEINSTANCE, CRYPTOADDLIVEHASH, CRYPTOCREATEACCOUNT, CRYPTODELETE, CRYPTODELETELIVEHASH, CRYPTOTRANSFER, CRYPTOUPDATEACCOUNT, FILEAPPEND, FILECREATE, FILEDELETE, FILEUPDATE, FREEZE, SCHEDULECREATE, SCHEDULEDELETE, SCHEDULESIGN, SYSTEMDELETE, SYSTEMUNDELETE, TOKENASSOCIATE, TOKENBURN, TOKENCREATION, TOKENDELETION, TOKENDISSOCIATE, TOKENFEESCHEDULEUPDATE, TOKENFREEZE, TOKENGRANTKYC, TOKENMINT, TOKENPAUSE, TOKENREVOKEKYC, TOKENUNFREEZE, TOKENUNPAUSE, TOKENUPDATE, TOKENWIPE, UNCHECKEDSUBMIT
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "transactions": [
    {
      "consensus_timestamp": "1234567890.000000007",
      "transaction_hash": "vigzKe2J7fv4ktHBbNTSzQmKq7Lzdq1/lJMmHT+a2KgvdhAuadlvS4eKeqKjIRmW",
      "valid_start_timestamp": "1234567890.000000006",
      "charged_tx_fee": 7,
      "memo_base64": null,
      "bytes": null,
      "result": "SUCCESS",
      "entity_id": "0.0.2281979",
      "name": "CRYPTOTRANSFER",
      "nft_transfers": [
        {
          "receiver_account_id": "0.0.121",
          "sender_account_id": "0.0.122",
          "serial_number": 1,
          "token_id": "0.0.123"
        },
        {
          "receiver_account_id": "0.0.321",
          "sender_account_id": "0.0.422",
          "serial_number": 2,
          "token_id": "0.0.123"
        }
      ],
      "max_fee": 33,
      "valid_duration_seconds": 11,
      "node": "0.0.3",
      "transaction_id": "0.0.8-1234567890-000000006",
      "scheduled": false,
      "transfers": [
        {
          "account": "0.0.3",
          "amount": 2
        },
        {
          "account": "0.0.8",
          "amount": -3
        },
        {
          "account": "0.0.98",
          "amount": 1
        }
      ],
      "token_transfers": [
        {
          "token_id": "0.0.90000",
          "account": "0.0.9",
          "amount": 1200
        },
        {
          "token_id": "0.0.90000",
          "account": "0.0.8",
          "amount": -1200
        }
      ],
      "assessed_custom_fees": [
        {
          "amount": 100,
          "collector_account_id": "0.0.10",
          "effective_payer_account_ids": [
            "0.0.8",
            "0.0.72"
          ],
          "token_id": "0.0.90001"
        }
      ]
    }
  ],
  "balance": {
    "timestamp": "0.000002345",
    "balance": 80,
    "tokens": [
      {
        "token_id": "0.0.200001",
        "balance": 8
      }
    ]
  },
  "account": "0.1.2",
  "expiry_timestamp": "1586567700.453054000",
  "auto_renew_period": "string",
  "key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "deleted": true,
  "max_automatic_token_associations": 0,
  "memo": "string",
  "receiver_sig_required": true,
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/accounts/{accountId}/nfts" baseUrl="" summary="NFTs by account ID" %}
{% swagger-description %}
Specify the account ID or account alias you would like to return the NFTs for. Please reference this 

[doc](https://testnet.mirrornode.hedera.com/api/v1/docs/#/accounts/listNftByAccountId)

 for additional filtering guidelines. 
{% endswagger-description %}

{% swagger-parameter in="query" name="token.id" type="String" %}
The ID of the token to return information for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="serialNumber" type="String" %}
The nft serial number (64 bit type). Requires a tokenId value also be populated.
{% endswagger-parameter %}

{% swagger-parameter in="path" name="accountAliasOrAccountId" required="true" %}
The account ID or account alias (0.0.accountNum or 0.0.accountAlias)
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The maximum number of items to return. 

\


Default: 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : desc

_Example_ : asc\

{% endswagger-parameter %}

{% swagger-response status="200: OK" description="NFTs for account 0.1.2" %}
```javascript
{
  "nfts": [
    {
      "account_id": "0.1.2",
      "created_timestamp": "1234567890.000000001",
      "deleted": false,
      "metadata": "VGhpcyBpcyBhIHRlc3QgTkZU",
      "modified_timestamp": "1610682445.003266001",
      "serial_number": 124,
      "token_id": "0.0.222"
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/accounts/{accountId}/allowances/crypto" baseUrl="" summary="Hbar allowances for an account" %}
{% swagger-description %}
Returns hbar allowances for an account.
{% endswagger-description %}

{% swagger-parameter in="path" name="account.id" required="true" %}
The account ID or account alias to return the hbar allowances for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="spender.id" %}
The ID of the spender to return information for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The maximum number of items to return

_Default value_ : 25

_Example_ : 2
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : desc
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="The hbar allowances returned for account 0.0.1000." %}
```javascript
{
  {
  "allowances": [
    {
      "amount_granted": 10,
      "owner": "0.0.1000",
      "spender": "0.0.8488",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": "1633466568.31556926"
      }
    },
    {
      "amount_granted": 5,
      "owner": "0.0.1000",
      "spender": "0.0.9857",
      "timestamp": {
        "from": "1633466229.96874612",
        "to": null
      }
    }
  ],
  "links": {}
}
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/accounts/{accountId}/allowances/tokens" baseUrl="" summary="Fungible token allowances for an account" %}
{% swagger-description %}
Returns information for fungible token allowances for an account. Please refer to 

[this](https://testnet.mirrornode.hedera.com/api/v1/docs/#/accounts/listTokenAllowancesByAccountId)

 document regarding filtering restrictions.
{% endswagger-description %}

{% swagger-parameter in="path" name="account.id" type="String" required="true" %}
Account ID or account alias to return the fungible token allowances for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="spender.id" type="String" %}
The ID of the spender to return information for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="token.id" type="String" %}
The ID of the token to return information for.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The maximum number of items to return

_Default value_ : 25

_Example_ : 2
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc

_Example_ : desc
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
  "allowances": [
    {
      "amount_granted": 100,
      "owner": "0.1.2",
      "spender": "0.1.2",
      "timestamp": {
        "from": "1586567700.453054000",
        "to": "1586567700.453054000"
      },
      "token_id": "0.1.2"
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

## &#x20;<a href="#balances" id="balances"></a>

## Balances <a href="#balances" id="balances"></a>

The **balance** object represents the balance of accounts on the Hedera network. You can retrieve this to view the **most recent** balance of all the accounts on the network at that given time. The balances object returns the account ID and the balance in hbars. Balances are checked on a periodic basis and thus return the most recent snapshot of time captured prior to the request.

{% swagger baseUrl="" path=" /api/v1/balances" method="get" summary="account balances" %}
{% swagger-description %}
Returns a timestamped list of account balances on the network. Balances for an account are updated every 15 minutes. You can refer to the timestamp for when the balance was last updated for the account. If you need to return the balance of an account more frequently you can do so by using the free account balance query via the SDK.
{% endswagger-description %}

{% swagger-parameter in="query" name="account.id" type="String" %}
The ID of the account to return the balance for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.balance" type="String" %}
Returns a list of account IDs that have the specified balance (tinybars)
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The timestamp the user would like to return account balances for. The timestamp can be provided in seconds format (15000000000) or in seconds.nanoseconds (15000000000.010000000).
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.publickey" type="String" %}
The account's public key to compare against

_Example_ : 3c3d546321ff6f63d701d2ec5c277095874e19f4a235bee1e6bb19258bf362be
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-response status="200" description="The balance for account 0.0.2004" %}
```
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
{% endswagger-response %}
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

{% swagger baseUrl="" path="/api/v1/transactions" method="get" summary="transactions" %}
{% swagger-description %}
Lists transactions on the network. This includes successful and unsuccessful transactions.
{% endswagger-description %}

{% swagger-parameter in="query" name="transactionType" type="String" %}
Filter transactions by transaction type. 

_Available values_

 : CONSENSUSCREATETOPIC, CONSENSUSDELETETOPIC, CONSENSUSSUBMITMESSAGE, CONSENSUSUPDATETOPIC, CONTRACTCALL, CONTRACTCREATEINSTANCE, CONTRACTDELETEINSTANCE, CONTRACTUPDATEINSTANCE, CRYPTOADDLIVEHASH, CRYPTOCREATEACCOUNT, CRYPTODELETE, CRYPTODELETELIVEHASH, CRYPTOTRANSFER, CRYPTOUPDATEACCOUNT, FILEAPPEND, FILECREATE, FILEDELETE, FILEUPDATE, FREEZE, SCHEDULECREATE, SCHEDULEDELETE, SCHEDULESIGN, SYSTEMDELETE, SYSTEMUNDELETE, TOKENASSOCIATE, TOKENBURN, TOKENCREATION, TOKENDELETION, TOKENDISSOCIATE, TOKENFEESCHEDULEUPDATE, TOKENFREEZE, TOKENGRANTKYC, TOKENMINT, TOKENPAUSE, TOKENREVOKEKYC, TOKENUNFREEZE, TOKENUNPAUSE, TOKENUPDATE, TOKENWIPE, UNCHECKEDSUBMIT
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.id" type="String" %}
The ID of the account the user would like to return transactions for within the transferlist.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The specific timestamp the user would like to return transactions for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="result" type="" %}
If 

`result=fail`

l the query returns all failed cryptocurrency transactions

\


If the 

`result=success`

 the query returns all successful cryptocurrency transactions
{% endswagger-parameter %}

{% swagger-parameter in="query" name="type" type="" %}
If 

`type=credit`

 the query returns all transactions that deposited into an account ID

\


If 

`type=debit`

 the query returns all transactions that withdrew from an account ID
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : desc
{% endswagger-parameter %}

{% swagger-response status="200" description="A request to return information for a transaction that was provided a specific timestamp" %}
```
  {
  "transactions": [
    {
      "consensus_timestamp": "1234567890.000000007",
      "transaction_hash": "vigzKe2J7fv4ktHBbNTSzQmKq7Lzdq1/lJMmHT+a2KgvdhAuadlvS4eKeqKjIRmW",
      "valid_start_timestamp": "1234567890.000000006",
      "charged_tx_fee": 7,
      "memo_base64": null,
      "bytes": null,
      "result": "SUCCESS",
      "entity_id": "0.0.2281979",
      "name": "CRYPTOTRANSFER",
      "nft_transfers": [
        {
          "receiver_account_id": "0.0.121",
          "sender_account_id": "0.0.122",
          "serial_number": 1,
          "token_id": "0.0.123"
        },
        {
          "receiver_account_id": "0.0.321",
          "sender_account_id": "0.0.422",
          "serial_number": 2,
          "token_id": "0.0.123"
        }
      ],
      "max_fee": 33,
      "valid_duration_seconds": 11,
      "node": "0.0.3",
      "transaction_id": "0.0.8-1234567890-000000006",
      "scheduled": false,
      "transfers": [
        {
          "account": "0.0.3",
          "amount": 2
        },
        {
          "account": "0.0.8",
          "amount": -3
        },
        {
          "account": "0.0.98",
          "amount": 1
        }
      ],
      "token_transfers": [
        {
          "token_id": "0.0.90000",
          "account": "0.0.9",
          "amount": 1200
        },
        {
          "token_id": "0.0.90000",
          "account": "0.0.8",
          "amount": -1200
        }
      ],
      "assessed_custom_fees": [
        {
          "amount": 100,
          "collector_account_id": "0.0.10",
          "effective_payer_account_ids": [
            "0.0.8",
            "0.0.72"
          ],
          "token_id": "0.0.90001"
        }
      ]
    }
  ],
  "links": {
    "next": null
  }
}

```
{% endswagger-response %}
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

{% swagger method="get" path="/api/v1/transactions/{transactionId}" baseUrl="" summary="transaction by transaction ID" %}
{% swagger-description %}
Return the information about a specific transaction by specifying the transaction ID
{% endswagger-description %}

{% swagger-parameter in="path" name="transactionId" required="true" type="String" %}
The transaction ID 

\




_Example_

 : 0.0.10-1234567890-000000000
{% endswagger-parameter %}

{% swagger-parameter in="query" name="nonce" type="integer" %}
Filter the query result by the nonce of the transaction. The filter honors the last value. If not specified, all transactions with the specified payer account ID and valid start timestamp match.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="scheduled" type="boolean" %}
Filter transactions by the scheduled flag. If true, return information for the scheduled transaction. If false, return information for the non-scheduled transaction. If not present, return information for all transactions matching transactionId.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "transactions": [
    {
      "consensus_timestamp": "1234567890.000000007",
      "transaction_hash": "vigzKe2J7fv4ktHBbNTSzQmKq7Lzdq1/lJMmHT+a2KgvdhAuadlvS4eKeqKjIRmW",
      "valid_start_timestamp": "1234567890.000000006",
      "charged_tx_fee": 7,
      "memo_base64": null,
      "bytes": null,
      "result": "SUCCESS",
      "entity_id": "0.0.2281979",
      "name": "CRYPTOTRANSFER",
      "nft_transfers": [
        {
          "receiver_account_id": "0.0.121",
          "sender_account_id": "0.0.122",
          "serial_number": 1,
          "token_id": "0.0.123"
        },
        {
          "receiver_account_id": "0.0.321",
          "sender_account_id": "0.0.422",
          "serial_number": 2,
          "token_id": "0.0.123"
        }
      ],
      "max_fee": 33,
      "valid_duration_seconds": 11,
      "node": "0.0.3",
      "transaction_id": "0.0.8-1234567890-000000006",
      "scheduled": false,
      "transfers": [
        {
          "account": "0.0.3",
          "amount": 2
        },
        {
          "account": "0.0.8",
          "amount": -3
        },
        {
          "account": "0.0.98",
          "amount": 1
        }
      ],
      "token_transfers": [
        {
          "token_id": "0.0.90000",
          "account": "0.0.9",
          "amount": 1200
        },
        {
          "token_id": "0.0.90000",
          "account": "0.0.8",
          "amount": -1200
        }
      ],
      "assessed_custom_fees": [
        {
          "amount": 100,
          "collector_account_id": "0.0.10",
          "effective_payer_account_ids": [
            "0.0.8",
            "0.0.72"
          ],
          "token_id": "0.0.90001"
        }
      ]
    }
  ]
}
```
{% endswagger-response %}
{% endswagger %}

## Topics

{% swagger baseUrl="" path="/api/v1/topics/{topicId}/messages/" method="get" summary="topic messages" %}
{% swagger-description %}
Returns the list of topic messages for the given topic id.
{% endswagger-description %}

{% swagger-parameter in="path" name="topicId" type="string" required="true" %}
The ID of the topic in x.y.z or z format
{% endswagger-parameter %}

{% swagger-parameter in="query" name="sequenceNumber" type="integer" %}
The sequence number of the message relative to other messages submitted to the same topic
{% endswagger-parameter %}

{% swagger-parameter in="query" name="consensusTimestamp" type="array[string]" %}
The consensus timestamp of a message in seconds.nanoseconds
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
  "responseJson": {
    "consensus_timestamp": "1234567890.000000002",
    "topic_id": "0.0.7",
    "message": "bWVzc2FnZQ==",
    "running_hash": "cnVubmluZ19oYXNo",
    "sequence_number": 2
  }
```
{% endswagger-response %}
{% endswagger %}

#### Response Details

| Response Item            | Description                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------ |
| **consensus\_timestamp** | The consensus timestamp of the message in seconds.nanoseconds                        |
| **topic\_id**            | The ID of the topic the message was submitted to                                     |
| **message**              | The content of the message                                                           |
| **running\_hash**        | The new running hash of the topic that received the message                          |
| **sequence\_number**     | The sequence number of the message relative to all other messages for the same topic |

{% swagger method="get" path="/api/v1/topics/{topicId}/messages/{sequenceNumber}" baseUrl="" summary="topic message by ID and sequence number" %}
{% swagger-description %}
Returns a single topic message the given topic id and sequence number.
{% endswagger-description %}

{% swagger-parameter in="path" required="true" name="topicId" type="String" %}
The topic ID
{% endswagger-parameter %}

{% swagger-parameter in="path" required="true" name="sequenceNumber" type="integer" %}
Topic message sequence number
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "messages": [
    {
      "consensus_timestamp": "1234567890.000000001",
      "topic_id": "0.0.7",
      "message": "bWVzc2FnZQ==",
      "running_hash": "cnVubmluZ19oYXNo",
      "running_hash_version": 2,
      "sequence_number": 1
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/topics/messages/{consensusTimestamp}" baseUrl="" summary="topic messages by consensus timestamp" %}
{% swagger-description %}
Returns a topic message the given the consensus timestamp
{% endswagger-description %}

{% swagger-parameter in="path" required="true" type="String" name="consensusTimestamp" %}
The timestamp at which the associated transaction reached consensus
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "consensus_timestamp": "1234567890.000000001",
  "topic_id": "0.0.7",
  "message": "bWVzc2FnZQ==",
  "running_hash": "cnVubmluZ19oYXNo",
  "running_hash_version": 2,
  "sequence_number": 1
}
```
{% endswagger-response %}
{% endswagger %}

## Tokens

{% swagger baseUrl="" path="/api/v1/tokens" method="get" summary="tokens" %}
{% swagger-description %}
Returns the list of tokens created in a Hedera network
{% endswagger-description %}

{% swagger-parameter in="query" name="publickey" type="String" %}
The public key to return all tokens for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="token.id" type="String" %}
The ID of the token to return the query for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.id" type="String" %}
The ID of the account to return the tokens for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="type" type="String" %}
The token type to return

\




_Example_

 : List [ "ALL", "FUNGIBLE_COMMON", "NON_FUNGIBLE_UNIQUE" ]
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "tokens": [
    {
      "token_id": "0.0.1",
      "symbol": "FIRSTMOVERLPDJH",
      "admin_key": null,
      "type": "FUNGIBLE_COMMON"
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

#### Response Details

| Response Item | Description                                  |
| ------------- | -------------------------------------------- |
| **token\_Id** | The ID of the token in x.y.z format          |
| **symbol**    | The symbol of the token                      |
| **adminKey**  | The admin key for the token                  |
| **type**      | The type of token (fungible or non-fungible) |

#### Additional Examples

| Example Request                                                                              | Description                                      |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| `/api/v1/tokens?publickey=3c3d546321ff6f63d70 1d2ec5c277095874e19f4a235bee1e6bb19258bf362be` | All tokens with matching admin key               |
| `/api/v1/tokens?account.id=0.0.8`                                                            | All tokens for matching account                  |
| `/api/v1/tokens?token.id=gt:0.0.1001`                                                        | All tokens in range                              |
| `/api/v1/tokens?order=desc`                                                                  | All tokens in descending order of `token.id`     |
| `/api/v1/tokens?limit=x`                                                                     | All tokens taking the first `x` number of tokens |

{% swagger baseUrl="" path="/api/v1/tokens/{tokenId}/balances" method="get" summary="token balances" %}
{% swagger-description %}
Returns all the accounts and token balance for the specified token ID.
{% endswagger-description %}

{% swagger-parameter in="path" name="tokenId" type="String" required="true" %}
The token ID to get the balances for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.publickey" type="String" %}
The public key
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.id" type="String" %}
The ID of the account
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.balance" type="String" %}
The balance to query for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="String" %}
The consensus timestamp to query for
{% endswagger-parameter %}

{% swagger-response status="200" description="The request response" %}
```
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
{% endswagger-response %}
{% endswagger %}

#### Response Details

| Response Item | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| **timestamp** | The timestamp of the recorded  balances in seconds.nanoseconds |
| **balances**  | The balance of the tokens in those accounts                    |
| **account**   | The ID of the account that has the token balance               |
| **balance**   | The balance of the token associated with the account           |

#### Additional Examples

| Example Request                                                     | Description                                      |
| ------------------------------------------------------------------- | ------------------------------------------------ |
| `/api/v1/tokens/<token_id>/balances?order=asc`                      | The balance of the token in ascending order      |
| `/api/v1/tokens/<token_id>/balances?account.id=0.0.1000`            | The balance of the token for account ID 0.0.1000 |
| `/api/v1/tokens/<token_id>/balances?account.balance=gt:1000`        | The balance for the token greater than 1000      |
| `/api/v1/tokens/<token_id>/balances?timestamp=1566562500.040961001` | The token balances for the specified timestamp   |

{% swagger baseUrl="" path="/api/v1/tokens/{tokenId}" method="get" summary="token info" %}
{% swagger-description %}
Returns the specified token information.
{% endswagger-description %}

{% swagger-parameter in="path" name="tokenId" type="String" required="true" %}
The ID of the token to return the information for in x.y.z format.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="String" %}
The consensus timestamp to query for
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
 {
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "auto_renew_account": "0.1.2",
  "auto_renew_period": null,
  "created_timestamp": "1234567890.000000001",
  "deleted": false,
  "decimals": 1000,
  "expiry_timestamp": null,
  "freeze_default": false,
  "freeze_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "initial_supply": 1000000,
  "kyc_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "max_supply": 9223372036854776000,
  "memo": "token memo",
  "modified_timestamp": "1234567890.000000001",
  "name": "Token name",
  "pause_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "pause_status": "UNPAUSED",
  "supply_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "supply_type": "INFINITE",
  "symbol": "ORIGINALRDKSE",
  "token_id": "0.10.1",
  "total_supply": 1000000,
  "treasury_account_id": "0.1.2",
  "type": "FUNGIBLE_COMMON",
  "wipe_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "custom_fees": {
    "created_timestamp": "1234567890.000000001",
    "fixed_fees": [
      {
        "amount": 100,
        "collector_account_id": "0.1.5",
        "denominating_token_id": "0.10.8"
      }
    ],
    "fractional_fees": [
      {
        "amount": {
          "numerator": 12,
          "denominator": 29
        },
        "collector_account_id": "0.1.6",
        "denominating_token_id": "0.10.9",
        "maximum": 120,
        "minimum": 30,
        "net_of_transfers": true
      }
    ]
  }
}

```
{% endswagger-response %}
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

{% swagger baseUrl="" path="/api/v1/tokens/{tokenId}/nfts" method="get" summary="NFTs" %}
{% swagger-description %}
Returns the list of non-fungible tokens.
{% endswagger-description %}

{% swagger-parameter in="path" name="tokenId" type="String" required="true" %}
The ID of token
{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.id" type="String" %}
The ID of the account to return the tokens for
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
  {
    "account_id": "0.1.2",
    "created_timestamp": "1234567890.000000001",
    "deleted": false,
    "metadata": "VGhpcyBpcyBhIHRlc3QgTkZU",
    "modified_timestamp": "1610682445.003266001",
    "serial_number": 124,
    "token_id": "0.0.222"
  }
]
```
{% endswagger-response %}
{% endswagger %}

#### Response Details

| Response Item           | Description                                            |
| ----------------------- | ------------------------------------------------------ |
| **account\_id**         | The  account ID of the account associated with the NFT |
| **created\_timestamp**  | The timestamp of when the NFT was created              |
| **deleted**             | Whether the token was deleted or not                   |
| **metadata**            | The meta data of the NFT                               |
| **modified\_timestamp** | The last time the token properties were modified       |
| **serial\_number**      | The serial number of the NFT                           |
| **token\_id**           | The token ID of the NFT                                |

{% swagger baseUrl="" path="/api/v1/tokens/{tokenId}/nfts/{serialNumber}" method="get" summary="NFT info" %}
{% swagger-description %}
Returns the information associated for specific NFT.
{% endswagger-description %}

{% swagger-parameter in="path" name="tokenId" type="string" required="true" %}
The token ID of the NFT
{% endswagger-parameter %}

{% swagger-parameter in="path" name="serialNumber" type="integer" required="true" %}
The serial number of the NFT
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```

  "account_id": "0.1.2",
  "created_timestamp": "1234567890.000000001",
  "deleted": false,
  "metadata": "VGhpcyBpcyBhIHRlc3QgTkZU",
  "modified_timestamp": "1610682445.003266001",
  "serial_number": 124,
  "token_id": "0.0.222"
}
```
{% endswagger-response %}
{% endswagger %}

#### Response Details

| Response Item           | Description                                            |
| ----------------------- | ------------------------------------------------------ |
| **account\_id**         | The  account ID of the account associated with the NFT |
| **created\_timestamp**  | The timestamp of when the NFT was created              |
| **deleted**             | Whether the token was deleted or not                   |
| **metadata**            | The meta data of the NFT                               |
| **modified\_timestamp** | The last time the token properties were modified       |
| **serial\_number**      | The serial number of the NFT                           |
| **token\_id**           | The token ID of the NFT                                |

{% swagger baseUrl="" path="/api​/v1​/tokens​/{tokenId}​/nfts​/{serialNumber}​/transactions" method="get" summary="NFT transaction history" %}
{% swagger-description %}
Returns the history of transactions for a NFT.
{% endswagger-description %}

{% swagger-parameter in="path" name="tokenId" type="string" required="true" %}
The token ID
{% endswagger-parameter %}

{% swagger-parameter in="path" name="serialNumber" type="integer" required="true" %}
The NFT serial number
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
[
  {
    "consensus_timestamp": "1618591023.997420021",
    "id": "0.0.19789-1618591023-997420021",
    "receiver_account_id": "0.0.11",
    "sender_account_id": "0.0.10",
    "type": "CRYPTOTRANSFER",
    "token_id": "0.0.1000"
  }
]
```
{% endswagger-response %}
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

{% swagger method="get" path="/api/v1/network/supply" baseUrl="" summary="network supply" %}
{% swagger-description %}
This query returns the number of hbars that are released along with the timestamp and total supply.
{% endswagger-description %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The timestamp to return the information for
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
    "released_supply":"922619772920027381",
    "timestamp":"1634787900.311842000",
    "total_supply":"5000000000000000000"
}
```
{% endswagger-response %}
{% endswagger %}



## Schedule Transactions

{% swagger baseUrl="" path="/api/v1/schedules" method="get" summary="schedule list" %}
{% swagger-description %}
Returns a list of all scheduled transactions for a network.
{% endswagger-description %}

{% swagger-parameter in="query" name="schedule.id" type="String" %}

{% endswagger-parameter %}

{% swagger-parameter in="query" name="account.id" type="String" %}
All schedule transactions matching the creator account
{% endswagger-parameter %}

{% swagger-parameter in="query" name="executed" type="boolean" %}
If equal to true, returns all schedule transactions that were executed
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
Limits results to the first N schedule transactions
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
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
{% endswagger-response %}
{% endswagger %}

#### Response Details <a href="#response-details-1" id="response-details-1"></a>

| Response Item                       | Description                                                               |
| ----------------------------------- | ------------------------------------------------------------------------- |
| **schedules**                       | List of schedules                                                         |
| **adminKey**                        | The admin key on the schedule                                             |
| **adminKey.\_type**                 | The type of key                                                           |
| **adminKey.key**                    | The admin public key                                                      |
| **consensus\_timestamp**            | The consensus timestamp of when the schedule was created                  |
| **creator\_account\_id**            | The account ID of the creator of the schedule                             |
| **executed\_timestamp**             | The timestamp at which the transaction that was scheduled was executed at |
| **memo**                            | A string of characters associated with the memo if set                    |
| **payer\_account\_id**              | The account ID of the account paying for the execution of the transaction |
| **schedule\_id**                    | The ID of the schedule entity                                             |
| **signatures**                      | The list of keys that signed the transaction                              |
| **signatures.consensus\_timestamp** | The consensus timestamp at which the signature was added                  |
| **signatures.public\_key\_prefix**  | The signatures public key prefix                                          |
| **signatures.signature**            | The signature of the key that signed the schedule transaction             |
| **transaction\_body**               | The transaction body of the transaction that was scheduled                |
| **links.next**                      | Hyperlink to the next page of results                                     |

{% swagger baseUrl="" path="/api/v1/schedules/{scheduleId}" method="get" summary="schedule transaction" %}
{% swagger-description %}
Returns the specified schedule entity information.
{% endswagger-description %}

{% swagger-parameter in="path" name="scheduleId" type="String" required="true" %}
The ID of the schedule to return the information for.
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
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
{% endswagger-response %}
{% endswagger %}

#### Response Details <a href="#response-details-1" id="response-details-1"></a>

| Response Item                       | Description                                                               |
| ----------------------------------- | ------------------------------------------------------------------------- |
| **adminKey**                        | The admin key on the schedule                                             |
| **adminKey.\_type**                 | The type of key                                                           |
| **adminKey.key**                    | The admin public key                                                      |
| **consensus\_timestamp**            | The consensus timestamp of when the schedule was created                  |
| **creator\_account\_id**            | The account ID of the creator of the schedule                             |
| **executed\_timestamp**             | The timestamp at which the transaction that was scheduled was executed at |
| **memo**                            | A string of characters associated with the memo if set                    |
| **payer\_account\_id**              | The account ID of the account paying for the execution of the transaction |
| **schedule\_id**                    | The ID of the schedule entity                                             |
| **signatures**                      | The list of keys that signed the transaction                              |
| **signatures.consensus\_timestamp** | The consensus timestamp at which the signature was added                  |
| **signatures.public\_key\_prefix**  | The signatures public key prefix                                          |
| **signatures.signature**            | The signature of the key that signed the schedule transaction             |
| **transaction\_body**               | The transaction body of the transaction that was scheduled                |

## Smart Contracts

{% swagger method="get" path="/api/v1/contracts" baseUrl="" summary="contracts" %}
{% swagger-description %}
Returns a list of all contract entity items on the network.
{% endswagger-description %}

{% swagger-parameter in="query" name="contractId" type="String" %}
The ID of the contract to return information for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

\




_Default value_

 : 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : desc
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```
{
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2233222c2233222c2233227d"
  },
  "auto_renew_period": 7776000,
  "bytecode": "0xc896c66db6d98784cc03807640f3dfd41ac3a48c",
  "contract_id": "0.0.10001",
  "created_timestamp": "1633466229.96874612",
  "deleted": false,
  "expiration_timestamp": "1633466229.96874612",
  "file_id": "0.0.1000",
  "memo": "First contract",
  "obtainer_id": "0.0.101",
  "proxy_account_id": "0.0.100",
  "solidity_address": "0x00000000000000000000000000000000000003E9",
  "timestamp": {
    "from": "1633466229.96874612",
    "to": "1633466568.31556926"
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Item             | Description                                                                                            |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| **admin\_key**            | The admin key of the contract, if specified                                                            |
| **auto\_renew\_period**   | The period at which the contract auto renews                                                           |
| **bytecode**              | The bytecode of the contract                                                                           |
| **contract\_id**          | The contract ID                                                                                        |
| **created\_timestamp**    | The timestamp the contract was created at                                                              |
| **deleted**               | Whether or not the contract is deleted                                                                 |
| **expiration\_timestamp** | The timestamp of when the contract is set to expire                                                    |
| **file\_id**              | The ID of the file that stored the contract bytecode                                                   |
| **memo**                  | The memo of the contract, if specified                                                                 |
| **obtainer\_id**          | The ID of the account or contract that will receive any remaining balance when the contract is deleted |
| **proxy\_account\_id**    | The proxy account ID (disabled)                                                                        |
| **solidity\_address**     | The solidity address                                                                                   |
| **timestamp**             | The period for which the attributes are valid for                                                      |

{% swagger method="get" path="/api/v1/contracts/{contractId}" baseUrl="" summary="contract by ID" %}
{% swagger-description %}
Return the contract information given an id
{% endswagger-description %}

{% swagger-parameter in="path" name="contractId" required="true" type="String" %}
The ID of the contract in 0.0.contractNumber format
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The timestamp to query the results with
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```
{
  "admin_key": {
    "_type": "ProtobufEncoded",
    "key": "7b2231222c2231222c2231227d"
  },
  "auto_renew_period": null,
  "contract_id": "0.1.2",
  "created_timestamp": "1586567700.453054000",
  "deleted": false,
  "expiration_timestamp": "1586567700.453054000",
  "file_id": "0.1.2",
  "memo": "contract memo",
  "obtainer_id": "0.1.2",
  "proxy_account_id": "0.1.2",
  "solidity_address": "0x0000000000000000000000000000000000001f41",
  "timestamp": {
    "from": "1586567700.453054000",
    "to": "1586567700.453054000"
  },
  "bytecode": "0x01021a1fdc9b"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/contracts/{contractId}/results" baseUrl="" summary="contract results" %}
{% swagger-description %}
Returns a list of all ContractResults for a contract's function executions.
{% endswagger-description %}

{% swagger-parameter in="path" type="String" name="contractId" required="true" %}
The ID of the contract in 0.0.contractNumber format
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The timestamp to query the results for
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" %}
The limit of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "contracts": [
    {
      "amount": 10,
      "call_result": "0x2b048531b38d2882e86044bc972e940ee0a01938",
      "contract_id": "0.1.2",
      "created_contract_ids": [
        "0.1.2"
      ],
      "error_message": "Out of gas",
      "from": "0x0000000000000000000000000000000000001f41",
      "function_parameters": "0xbb9f02dc6f0e3289f57a1f33b71c73aa8548ab8b",
      "gas_limit": 100000,
      "gas_used": 1000,
      "timestamp": "1586567700.453054000",
      "to": "0x0000000000000000000000000000000000001f41"
    }
  ],
  "links": {
    "next": null
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/contracts/{contractId}/results/{timestamp}" baseUrl="" summary="contract result by timestamp" %}
{% swagger-description %}
Returns a single ContractResult for a contract's function executions at a specific timestamp
{% endswagger-description %}

{% swagger-parameter in="path" name="contractId" type="String" required="true" %}
The ID of the contract in 0.0.contractNumber format

\




_Example_

 : 0.0.10
{% endswagger-parameter %}

{% swagger-parameter in="path" name="timestamp" type="String" required="true" %}
The timestamp at which the associated transaction reached consensus

_Example_: 1234567890.0000007
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "contracts": {
    "amount": 10,
    "call_result": "0x2b048531b38d2882e86044bc972e940ee0a01938",
    "contract_id": "0.1.2",
    "created_contract_ids": [
      "0.1.2"
    ],
    "error_message": "Out of gas",
    "from": "0x0000000000000000000000000000000000001f41",
    "function_parameters": "0xbb9f02dc6f0e3289f57a1f33b71c73aa8548ab8b",
    "gas_limit": 100000,
    "gas_used": 1000,
    "timestamp": "1586567700.453054000",
    "to": "0x0000000000000000000000000000000000001f41",
    "block_hash": "0x6ceecd8bb224da491",
    "block_number": 10,
    "hash": "0x3531396130303866616264653464"
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/contracts/{contractId}/results/logs" baseUrl="" summary="contract logs" %}
{% swagger-description %}
Returns a list of all ContractLogs for a single specified contract's function executions. Chained logs are not included but can be found by calling 

`/api/v1/contracts/{contractId}/results/{timestamp}`

 or 

`/api/v1/contracts/results/{transactionId}`
{% endswagger-description %}

{% swagger-parameter in="path" name="contractId" type="String" required="true" %}
The ID of the contract in 0.0.contractNumber format

\




_Example_

 : 0.0.10
{% endswagger-parameter %}

{% swagger-parameter in="query" name="index" type="integer" %}
Contract log index
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The limit of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timestamp" type="array[string]" %}
The timestamp at which the associated transaction reached consensus

_Example_: 1234567890.0000007
{% endswagger-parameter %}

{% swagger-parameter in="query" name="topic0" type="String" %}
The topic in topic0 of a ContractLog

_Example_ : 4.027126854939018e+76
{% endswagger-parameter %}

{% swagger-parameter in="query" name="topic1" type="String" %}
The topic in topic1 of a ContractLog

_Example_ : 4.027126854939018e+76
{% endswagger-parameter %}

{% swagger-parameter in="query" name="topic2" type="String" %}
The topic in topic2 of a ContractLog

_Example_ : 4.027126854939018e+76
{% endswagger-parameter %}

{% swagger-parameter in="query" name="topic3" type="String" %}
The topic in topic3 of a ContractLog

_Example_ : 4.027126854939018e+76
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
  "logs": [
    {
      "address": "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
      "bloom": "0x549358c4c2e573e02410ef7b5a5ffa5f36dd7398",
      "contract_id": "0.1.2",
      "data": "0x00000000000000000000000000000000000000000000000000000000000000fa",
      "index": 0,
      "topics": [
        "0xf4757a49b326036464bec6fe419a4ae38c8a02ce3e68bf0809674f6aab8ad300"
      ],
      "root_contract_id": "0.1.2",
      "timestamp": "1586567700.453054000"
    }
  ]
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/api/v1/contracts/results/{transactionId}" baseUrl="" summary="contract result by transaction ID" %}
{% swagger-description %}
Returns a single ContractResult for a contract's function executions for a given transactionId.
{% endswagger-description %}

{% swagger-parameter in="path" name="transactionId" type="String" required="true" %}
The transaction ID
{% endswagger-parameter %}

{% swagger-parameter in="query" name="nonce" type="integer" %}
Filter the query result by the nonce of the transaction. The filter honors the last value. Default is 0 when not specified.

_Default value_ : 0
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
{
  "contracts": {
    "amount": 10,
    "call_result": "0x2b048531b38d2882e86044bc972e940ee0a01938",
    "contract_id": "0.1.2",
    "created_contract_ids": [
      "0.1.2"
    ],
    "error_message": "Out of gas",
    "from": "0x0000000000000000000000000000000000001f41",
    "function_parameters": "0xbb9f02dc6f0e3289f57a1f33b71c73aa8548ab8b",
    "gas_limit": 100000,
    "gas_used": 1000,
    "timestamp": "1586567700.453054000",
    "to": "0x0000000000000000000000000000000000001f41",
    "block_hash": "0x6ceecd8bb224da491",
    "block_number": 10,
    "hash": "0x3531396130303866616264653464"
  }
}
```
{% endswagger-response %}
{% endswagger %}

## State Proof Alpha

The Hedera Mirror Node state proof alpha api provides the ability to cryptographically prove a transaction is valid on Hedera network. The request returns the content of the address book file, signature files, and record file that can be used to validate the transaction occurred on the Hedera network. The address book file contains the consensus node account IDs and their public key files. The signature files are of the supermajority consensus nodes that signed the record file the transaction is contained in.

{% swagger baseUrl="" path="/api/v1/transactions/{transactionId}/stateproof" method="get" summary="transaction state proof" %}
{% swagger-description %}
Returns the contents of the address book file, signature files, and record file that can be used to validate the transaction occurred on the Hedera network given transaction id.
{% endswagger-description %}

{% swagger-parameter in="path" name="transactionId" type="string" required="true" %}
The ID of the transaction to return the state proof in shard.realm.num-sss-nnn format where sss is in seconds and nnn is in nanoseconds of the transaction valid start time.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="nonce" type="integer" %}
Filter the query result by the nonce of the transaction. The filter honors the last value. Default is 0 when not specified.

_Default value_ : 0
{% endswagger-parameter %}

{% swagger-parameter in="query" name="scheduled" type="boolean" %}
Filter transactions by the scheduled flag. If true, return information for the scheduled transaction. If false, return information for the non-scheduled transaction.

\




_Default value_

 : false
{% endswagger-parameter %}

{% swagger-response status="200" description="" %}
```
{
  "transactions": {
    "record_file": "YzNkOTg3Yzg3NDI5NGViOTViMmRmOWZkMzZiMDY1NjYyMzMxNTc2OWFmMmVmMzQ0YzM1ODY4NzgwMTAyYjVjMA==",
    "address_books": [
      "MjljY2IxNGNjNWY2NWM1MmZlYjc0MjkyYjAxZDUyZmRjZjJkZTY0NWQzOTRiNTM3MDQ2ODBkYjZkMTMyZWM2Yw=="
    ],
    "signature_files": {
      "0.0.3": "MWFmYzZlNWVjNGJiZTg0ZWJjNTVkMGViNDViMjE2Mzg2ZTQ2NjUzZTYyYmM1NjE2YThmZGJjNzQ1ZWIyMTQxNQ==",
      "0.0.4": "ZDI4ZDIwMGJiYTdkNDhmNTA3ZTE0MGZhNjIyOGFiYTdmMjlmZjhiMmEzZjJhOGVlYjg1NzQyZGMwNTNjZWM3MQ==",
      "0.0.5": "MWY0YmU5OGE3NWE4N2NkNjQ5ZTNlMWE1ODI1OTI3NTgwNWM1Y2RmNjNmYjA0YjMzMTdlZmI1MTEwNzQ2NDVjNw==",
      "0.0.6": "NzNiZTVmMTZkNmY4NTQ4MjdiN2M4MTEzZmFlY2I1YzIwNGI5ZjFkNjhiMzdkMzczNTg4OTRkOWFiZmM5N2U4Mw==",
      "0.0.7": "NDJiZTVmMTZkNmY4NTQ4MjdiN2M4MTEzZmFlY2I1YzIwNGI5ZjFkNjhiMzdkMzczNTg4OTRkOWFiZmM5N2U4Mw=="
    }
  }
}
```
{% endswagger-response %}
{% endswagger %}

| Response Item        | Description                                                                      | Format          |
| -------------------- | -------------------------------------------------------------------------------- | --------------- |
| **record\_file**     | The record file content for the specified transaction                            | base64 encoding |
| **signature\_files** | The signatures file content from the consensus nodes that signed the transaction | base64 encoding |
| **address\_books**   | The address book file contents                                                   | base64 encoding |

Save the response to a json file and use the [check-state-proof](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof) cli commands to confirm the validity of the transaction. You can find the instructions [here](https://github.com/hashgraph/hedera-mirror-node/tree/master/hedera-mirror-rest/check-state-proof).

## Network

{% swagger method="get" path="/api/v1/network/nodes" baseUrl="" summary="Get the network address book" %}
{% swagger-description %}
Returns the network's list of nodes used in consensus.
{% endswagger-description %}

{% swagger-parameter in="query" name="file.id" %}
0.0.101 or 0.0.102 address book files
{% endswagger-parameter %}

{% swagger-parameter in="query" name="node.id" %}
The node account ID (ex. 0.0.10)
{% endswagger-parameter %}

{% swagger-parameter in="query" name="limit" type="integer" %}
The maximum number of items to return

_Default value_ : 25
{% endswagger-parameter %}

{% swagger-parameter in="query" name="order" %}
The order in which items are listed

_Available values_ : asc, desc

_Default value_ : asc

_Example_ : desc
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
    {
  "nodes": [
    {
      "description": "",
      "file_id": "0.0.102",
      "memo": "0.0.3",
      "node_account_id": "0.0.3",
      "node_cert_hash": "0x3334...",
      "node_id": 0,
      "public_key": "0x308201...",
      "service_endpoints": [
        {
          "ip_address_v4": "13.124.142.126",
          "port": 50211
        }
      ],
      "timestamp": {
        "from": "1636052707.740848001",
        "to": null
      }
    }
  ],
  "links": {
    "next": null
  }
}
}
```
{% endswagger-response %}
{% endswagger %}
