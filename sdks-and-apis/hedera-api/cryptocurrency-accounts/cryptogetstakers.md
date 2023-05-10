# CryptoGetStakers

## AllProxyStakers

all of the accounts proxy staking to a given account, and the amounts proxy staked

| Field         | Type                                           | Description                                                               |
| ------------- | ---------------------------------------------- | ------------------------------------------------------------------------- |
| `accountID`   | [AccountID](../basic-types/accountid.md)       | The Account ID that is being proxy staked to                              |
| `proxyStaker` | [ProxyStaker](cryptogetstakers.md#proxystaker) | Each of the proxy staking accounts, and the amount they are proxy staking |

## CryptoGetStakersQuery

Get all the accounts that are proxy staking to this account. For each of them, give the amount currently staked. This is not yet implemented, but will be in a future version of the API.

| Field       | Type                                           | Description                                                                                                                                         |
| ----------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header`    | [QueryHeader](../miscellaneous/queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |
| `accountID` | [AccountID](../basic-types/accountid.md)       | The Account ID for which the records should be retrieved                                                                                            |

## CryptoGetStakersResponse

Response when the client sends the node CryptoGetStakersQuery

| Field     | Type                                                   | Description                                                                                                      |
| --------- | ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| `header`  | [ResponseHeader](../miscellaneous/responseheader.md)   | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `stakers` | [AllProxyStakers](cryptogetstakers.md#allproxystakers) | List of accounts proxy staking to this account, and the amount each is currently proxy staking                   |

## ProxyStaker

information about a single account that is proxy staking

| Field       | Type                                     | Description                                         |
| ----------- | ---------------------------------------- | --------------------------------------------------- |
| `accountID` | [AccountID](../basic-types/accountid.md) | The Account ID that is proxy staking                |
| `amount`    | int64                                    | The number of hbars that are currently proxy staked |
