# Custom token fees

When creating a token, you can configure up to 10 custom fees, automatically disbursed to specified [fee collector accounts](../../../support-and-community/glossary.md#fee-collector-account) each time the token is transferred programmatically. These fees can be _fixed_, _fractional_, or _royalty_-based, offering revenue generation, profit-sharing, and behavior incentivization for creators. This guide is your comprehensive resource for understanding types, implementation, and best practices for custom fees on Hedera.

***

## Types of Custom Fees

**Fixed Fee:** Paid by the _sender_ of the fungible or non-fungible tokens. A fixed fee transfers a set amount to a fee collector account each time a token is transferred, independent of the transfer size. This fee can be collected in HBAR or another Hedera token but not in NFTs.&#x20;

**Fractional Fee:** Take a specific portion of the transferred fungible tokens, with optional minimum and maximum limits. The token _receiver_ (fee collector account) pays these fees by default. However, if [`net_of_transfers`](../../hedera-api/token-service/customfees/fractionalfee.md) is set to true, the sender pays the fees and the receiver collects the full token transfer amount. If this field is set to false, the receiver pays for the token custom fees and gets the remaining token balance.&#x20;

**Royalty Fee:** Paid by the _receiver_ account that is exchanging the fungible value for the NFT. When the NFT sender does not receive any fungible value, the [fallback fee](../../../support-and-community/glossary.md#fallback-fee) is charged to the NFT receiver.&#x20;

{% hint style="info" %}
**Note:** In addition to the custom token fee payment, the sender account must pay for the token transfer transaction fee in HBAR. The "[_Payment of Custom Fees & Transaction Fees in HBAR_](custom-token-fees.md#payment-of-custom-fees-vs.-transaction-fees-in-hbar)_"_ section below covers the distinction between custom fees and transaction fees.
{% endhint %}

***

## Implementation Methods

### Fixed Fee

A [fixed fee](../../hedera-api/token-service/customfees/fixedfee.md) entails transferring a specified token amount to predefined fee collector accounts each time a token transfer is initiated. This fee amount doesn't depend on the volume of tokens being transferred. The creator has the flexibility to collect the fee in HBAR or another fungible Hedera token. However, it's important to note that NFTs cannot be used as a token type to collect this fee. A custom fixed fee can be set for fungible and non-fungible token types.

<table><thead><tr><th width="409">Constructor</th><th>Description</th></tr></thead><tbody><tr><td><code>new CustomFixedFee()</code></td><td>Initializes the <code>CustomFixedFee</code> object</td></tr></tbody></table>

```java
new CustomFixedFee()
```

<table data-full-width="false"><thead><tr><th width="306.3333333333333">Methods</th><th width="213">Description</th><th width="118" align="center">Type</th><th align="center">Requirement</th></tr></thead><tbody><tr><td><code>setFeeCollectorAccountId</code></td><td>Sets the fee collector account ID that collects the fee.</td><td align="center"><a href="../specialized-types.md#accountid">AccountId</a></td><td align="center">Required</td></tr><tr><td><code>setHbarAmount</code></td><td>Set the amount of HBAR to be collected.</td><td align="center"><a href="../hbars.md">HBAR</a></td><td align="center">Optional</td></tr><tr><td><code>setAmount</code></td><td>Sets the amount of tokens to be collected as the fee.</td><td align="center">int64</td><td align="center">Optional</td></tr><tr><td><code>setDenominatingTokenId</code></td><td>The ID of the token used to charge the fee. The denomination of the fee is taken as HBAR if left unset.</td><td align="center"><a href="token-id.md">TokenID</a></td><td align="center">Optional</td></tr><tr><td><code>setAllCollectorsAreExempt</code></td><td>If true, exempts all the token's fee collector accounts from this fee.</td><td align="center">boolean</td><td align="center">Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create a custom token fixed fee
new CustomFixedFee()
    .setAmount(1) // 1 token is transferred to the fee collecting account each time this token is transferred
    .setDenominatingTokenId(tokenId) // The token to charge the fee in 
    .setFeeCollectorAccountId(feeCollectorAccountId); // 1 token is sent to this account everytime it is transferred

//Version: 2.0.143
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a custom token fixed fee
new CustomFixedFee()
    .setAmount(1) // 1 token is transferred to the fee collecting account each time this token is transferred
    .setDenominatingTokenId(tokenId) // The token to charge the fee in
    .setFeeCollectorAccountId(feeCollectorAccountId); // 1 token is sent to this account everytime it is transferred

//Version: 2.0.30
```
{% endtab %}

{% tab title="Go" %}
```go
//Create a custom token fixed fee
[]hedera.Fee{
		hedera.NewCustomFixedFee().
		SetAmount(1). // 1 token is transferred to the fee collecting account each time this token is transferred
		SetDenominatingTokenID(tokenId). // The token to charge the fee in 
		SetFeeCollectorAccountID(feeCollectorAccountId) // 1 token is sent to this account everytime it is transferred
   },
}
//Version: 2.1.16
```
{% endtab %}
{% endtabs %}

### **Fractional Fee**

[Fractional fees](../../hedera-api/token-service/customfees/fractionalfee.md) involve the transfer of a specified fraction of the tokens' total value to the designated fee collector account. You can set a custom fractional fee and impose minimum and maximum fee limits per transfer transaction. The fractional fee has to be less than or equal to 1. It cannot exceed the fractional range of a 64-bit signed integer. Applicable to fungible tokens only.

<table><thead><tr><th width="280.33333333333326">Methods</th><th width="222">Description</th><th width="110" align="center">Type</th><th align="center">Requirement</th><th data-hidden>Type</th></tr></thead><tbody><tr><td><code>setFeeCollectorAccountId</code></td><td>Sets the fee collector account ID that collects the fee.</td><td align="center"><a href="../specialized-types.md#accountid">AccountId</a></td><td align="center">Required</td><td><a href="../specialized-types.md#accountid">AccountId</a></td></tr><tr><td><code>setNumerator</code></td><td>Sets the numerator of the fraction.</td><td align="center">long</td><td align="center">Required</td><td>long</td></tr><tr><td><code>setDenominator</code></td><td>Sets the denominator of the fraction. Cannot be zero.</td><td align="center">long</td><td align="center">Required</td><td>long</td></tr><tr><td><code>setMax</code></td><td>The maximum fee that can be charged, regardless of the fractional value.</td><td align="center">long</td><td align="center">Optional</td><td>long</td></tr><tr><td><code>setMin</code></td><td>The minimum fee that can be charged, regardless of the fractional value.</td><td align="center">long</td><td align="center">Optional</td><td>long</td></tr><tr><td><code>setAssessmentMethod</code></td><td>If true, sender pays fees and the receiver collects the full token transfer amount. If false, receiver pays fees and gets remaining token balance.</td><td align="center">boolean</td><td align="center">Optional</td><td><code>FeeAssessmentMethod</code></td></tr><tr><td><code>setAllCollectorsAreExempt</code></td><td>If true, exempts all the token's fee collector accounts from this fee.</td><td align="center">boolean</td><td align="center">Optional</td><td>boolean</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create a custom token fractional fee
new CustomFractionalFee()
    .setNumerator(1) // The numerator of the fraction
    .setDenominator(10) // The denominator of the fraction
    .setFeeCollectorAccountId(feeCollectorAccountId); // The account collecting the 10% custom fee each time the token is transferred

//Version: 2.0.14
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a custom token fractional fee
new CustomFractionalFee()
    .setNumerator(1) // The numerator of the fraction
    .setDenominator(10) // The denominator of the fraction
    .setFeeCollectorAccountId(feeCollectorAccountId); // The account collecting the 10% custom fee each time the token is transferred

//Version: 2.0.30    
```
{% endtab %}

{% tab title="Go" %}
```go
//Create a custom token fractional fee
[]hedera.Fee{
		hedera.NewCustomFractionalFee().
		SetNumerator(1). // The numerator of the fraction
		SetDenominator(10). // The denominator of the fraction
		SetFeeCollectorAccountID(feeCollectorAccountId), // The account collecting the 10% custom fee each time the token is transferred
	}

//Version: 2.1.16
```
{% endtab %}
{% endtabs %}

### **Royalty Fee**

The [royalty fee](../../hedera-api/token-service/customfees/royaltyfee.md) is assessed and applied each time the ownership of an NFT is transferred and is a fraction of the value exchanged for the NFT. If no value is exchanged for the NFT, a [fallback fee](../../../support-and-community/glossary.md#fallback-fee) can be imposed on the receiving account. This fee type only applies to non-fungible tokens.

{% hint style="info" %}
🔔 **NOTE:** Royalty fees are _strictly_ a convenience feature. The network can't enforce royalties if counterparties decide to split their NFT exchange into separate transactions. The NFT sender and receiver must both sign a single `CryptoTransfer` to ensure the proper application of royalties. There is an [open HIP discussion](https://github.com/hashgraph/hedera-improvement-proposal/discussions/578) about broadening the class of transactions for which the network automatically collects royalties. If this topic interests or concerns you, your participation in the discussion is welcome.
{% endhint %}

<table><thead><tr><th width="394">Constructor</th><th>Description</th></tr></thead><tbody><tr><td><code>new CustomRoyaltyFee()</code></td><td>Initializes the <code>CustomRoyaltyFee</code> object</td></tr></tbody></table>

```java
new CustomRoyaltyFee()
```

<table><thead><tr><th width="287.33333333333326">Methods</th><th width="223">Description</th><th width="110" align="center">Type</th><th align="center">Requirement</th></tr></thead><tbody><tr><td><code>setFeeCollectorAccountId</code></td><td>Sets the fee collector account ID that collects the fee.</td><td align="center"><a href="../specialized-types.md#accountid">AccountId</a></td><td align="center">Required</td></tr><tr><td><code>setNumerator</code></td><td>Sets the numerator of the fraction.</td><td align="center">long</td><td align="center">Required</td></tr><tr><td><code>setDenominator</code></td><td>Sets the denominator of the fraction.</td><td align="center">long</td><td align="center">Required</td></tr><tr><td><code>setFallbackFee</code></td><td>If present, the fixed fee to assess to the NFT receiver when no fungible value is exchanged with the sender</td><td align="center"><a href="../../hedera-api/token-service/customfees/fixedfee.md">FixedFee</a></td><td align="center">Optional</td></tr><tr><td><code>setAllCollectorsAreExempt</code></td><td>If true, exempts all the token's fee collector accounts from this fee.</td><td align="center">boolean</td><td align="center">Optional</td></tr></tbody></table>

{% tabs %}
{% tab title="Java" %}
```java
//Create a royalty fee
new CustomRoyaltyFee()
     .setNumerator(1) // The numerator of the fraction
     .setDenominator(10) // The denominator of the fraction
     .setFallbackFee(new CustomFixedFee().setHbarAmount(new Hbar(1)) // The fallback fee
     .setFeeCollectorAccountId(feeCollectorAccountId))) // The account that will receive the royalty fee

// v2.0.14 
```
{% endtab %}

{% tab title="JavaScript" %}
```javascript
//Create a royalty fee
new CustomRoyaltyFee()
     .setNumerator(1) // The numerator of the fraction
     .setDenominator(10) // The denominator of the fraction
     .setFallbackFee(new CustomFixedFee().setHbarAmount(new Hbar(1)) // The fallback fee
     .setFeeCollectorAccountId(feeCollectorAccountId))) // The account that will receive the royalty fee
     
 // v2.0.30 
```
{% endtab %}

{% tab title="Go" %}
```go
//Create a royalty fee 
[]hedera.Fee{
		hedera.NewCustomRoyaltyFee().
		SetFeeCollectorAccountID(feeCollectorAccountId). // The account that will receive the royalty fee
		SetNumerator(1). // The numerator of the fraction
		SetDenominator(10). // The denominator of the fraction
		SetFallbackFee( // The fallback fee
			hedera.NewCustomFixedFee().
			SetFeeCollectorAccountID(feeCollectorAccountId).
			SetAmount(1),
		),
	}
	
// v2.1.16
```
{% endtab %}
{% endtabs %}

***

## Payment of Custom Fees vs. Transaction Fees in HBAR

Understanding the difference between custom fees and standard transaction fees in HBAR is crucial for token issuers and developers working with Hedera. **Custom fees** are designed to enforce complex fee structures, such as royalties and fractional ownership. These fees ca n be fixed, fractional, or royalty-based and are usually paid in the token being transferred, although other Hedera tokens or HBAR can also be used. You can configure up to 10 custom fees automatically disbursed to designated fee collector accounts.

On the other hand, **transaction fees** in HBAR serve a different purpose: they compensate network nodes for processing transactions. These fees are uniform across all transaction types and are paid exclusively in HBAR. Unlike custom fees, which can be configured by the user, transaction fees are fixed by the network.

The key differences lie in their flexibility, payee, currency, and configurability. Custom fees offer greater flexibility and can be paid to any account in various tokens, and are user-defined. Transaction fees are network-defined, less flexible, and go solely to network nodes, paid only in HBAR.

#### Fee Exemptions

Fee collector accounts can be exempt from paying custom fees. To enable this, you need to set the exemption during the creation of the custom fees ([HIP-573](https://hips.hedera.com/hip/hip-573)). If not enabled, custom fees will only be exempt for an account if that account is set as a fee collector.

#### Limits and Constraints

When it comes to setting custom fees, there are a few limits and constraints to keep in mind:

* First, fees cannot be set to a negative value.&#x20;
* Each token can have up to 10 different custom fees.&#x20;
* Additionally, the treasury account for a given token is automatically exempt from paying these custom transaction fees.&#x20;
* The system also permits, at most, two "levels" of custom fees. That means a token being transferred might require fees in another token that also has its own fee schedule; however, this can only be nested two layers deep to prevent excessive complexity.
