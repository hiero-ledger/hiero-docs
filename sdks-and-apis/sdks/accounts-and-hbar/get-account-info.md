# Get account info

A query that returns the current state of the account. This query **does not** include the list of records associated with the account. Anyone on the network can request account info for a given account. Queries do not change the state of the account or require network consensus. The information is returned from a single node processing the query.

In Services release 0.50, returning token balance information from the consensus node was deprecated with HIP-367. This query now returns token information by requesting the information from the Hedera Mirror Node APIs via  [/api/v1/accounts/{id}/tokens](https://mainnet-public.mirrornode.hedera.com/api/v1/docs/#/accounts/listTokenRelationshipByAccountId). Token symbol is not returned in the response.\
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

<table><thead><tr><th width="395.3333333333333">Method</th><th width="217">Type</th><th>Requirement</th></tr></thead><tbody><tr><td><code>setAccountId(&#x3C;accountId>)</code></td><td>AccountId</td><td>Required</td></tr><tr><td><code>&#x3C;AccountInfo>.accountId</code></td><td>AccountId</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.contractAccountId</code></td><td>String</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.isDeleted</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.key</code></td><td>Key</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.balance</code></td><td>HBAR</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.isReceiverSignatureRequired</code></td><td>boolean</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.ownedNfts</code></td><td>long</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.maxAutomaticTokenAssociations</code></td><td>int</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.accountMemo</code></td><td>String</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.expirationTime</code></td><td>Instant</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.autoRenewPeriod</code></td><td>Duration</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.ledgerId</code></td><td>LedgerId</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.ethereumNonce</code></td><td>long</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.stakingInfo</code></td><td>StakingInfo</td><td>Optional</td></tr><tr><td><code>&#x3C;AccountInfo>.tokenRelationships</code></td><td>Map&#x3C;TokenId, TokenRelationships></td><td>Optional </td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

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
