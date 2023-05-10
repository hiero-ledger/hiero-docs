# Get account info

A query that returns the current state of the account. This query **does not** include the list of records associated with the account. Anyone on the network can request account info for a given account. Queries do not change the state of the account or require network consensus. The information is returned from a single node processing the query.\
\
**Account Properties**

{% content-ref url="../../../core-concepts/accounts/account-properties.md" %}
[account-properties.md](../../../core-concepts/accounts/account-properties.md)
{% endcontent-ref %}

**Query Fees**

* Please see the transaction and query [fees](../../../networks/mainnet/fees/#transaction-and-query-fees) table for the base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your query fee cost

**Query Signing Requirements**

* The client operator private key is required to sign the query request.

### Methods

| Method                                        | Type                              | Requirement |
| --------------------------------------------- | --------------------------------- | ----------- |
| `setAccountId(<accountId>)`                   | AccountId                         | Required    |
| `<AccountInfo>.accountId`                     | AccountId                         | Optional    |
| `<AccountInfo>.contractAccountId`             | String                            | Optional    |
| `<AccountInfo>.isDeleted`                     | boolean                           | Optional    |
| `<AccountInfo>.key`                           | Key                               | Optional    |
| `<AccountInfo>.balance`                       | HBAR                              | Optional    |
| `<AccountInfo>.isReceiverSignatureRequired`   | boolean                           | Optional    |
| `<AccountInfo>.tokenRelationships`            | Map\<TokenId, TokenRelationships> | Optional    |
| `<AccountInfo>.ownedNfts`                     | long                              | Optional    |
| `<AccountInfo>.maxAutomaticTokenAssociations` | int                               | Optional    |
| `<AccountInfo>.accountMemo`                   | String                            | Optional    |
| `<AccountInfo>.expirationTime`                | Instant                           | Optional    |
| `<AccountInfo>.autoRenewPeriod`               | Duration                          | Optional    |
| `<AccountInfo>.ledgerId`                      | LedgerId                          | Optional    |
| `<AccountInfo>.ethereumNonce`                 | long                              | Optional    |
| `<AccountInfo>.stakingInfo`                   | StakingInfo                       | Optional    |

{% tabs %}
{% tab title="Java" %}
```java
//Create the account info query
AccountInfoQuery query = new AccountInfoQuery()
    .setAccountId(newAccountId);

//Submit the query to a Hedera network
AccountInfo accountInfo = query.execute(client);
    
//Print the account key to the console
System.out.println(accountInfo);

//v2.0.0
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create the account info query
const query = new AccountInfoQuery()
    .setAccountId(newAccountId);

//Sign with client operator private key and submit the query to a Hedera network
const accountInfo = await query.execute(client);

//Print the account info to the console
console.log(accountInfo);

//v2.0.0
```
{% endtab %}

{% tab title="Go" %}
```go
//Create the account info query
query := hedera.NewAccountInfoQuery().
     SetAccountID(newAccountId)

//Sign with client operator private key and submit the query to a Hedera network
accountInfo, err := query.Execute(client)
if err != nil {
    panic(err)
}

//Print the account info to the console
fmt.Println(accountInfo)

//v2.0.0
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Sample Output:" %}
```
{ 
     accountId=0.0.96928, 
     contractAccountId=0000000000000000000000000000000000017aa0, 
     "deleted=false", 
     "proxyAccountId=null", //deprecated
     proxyReceived=0 tℏ, //deprecated
     key=302a300506032b65700321001a5a62bb9f35990d3fea1a5bb7ef6f1df0a297697adef1e04510c9d4ecc5db3f, 
     balance=1 ℏ, 
     sendRecordThreshold=92233720368.54775807 ℏ,
     receiveRecordThreshold=9223372 0368.54775807 ℏ, 
     "receiverSignatureRequired=false",
     expirationTime=2021-02-02T19:29:36Z, 
     autoRenewPeriod=PT2160H, 
     liveHashes="[],
     tokenRelationships={ //deprecated
          0.0.27335=TokenRelationship{
               tokenId=0.0.27335, symbol=F, balance=5, kycStatus=null,
               freezeStatus=null, automaticAssociation=true
          } 
     },
     accountMemo=, 
     ownedNfts=0,
     maxAutomaticTokenAssociations=10
     ledgerId=previewnet
}
```
{% endtab %}
{% endtabs %}
