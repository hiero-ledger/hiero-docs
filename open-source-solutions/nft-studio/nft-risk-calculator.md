# NFT Risk Calculator

The [NFT Risk Calculator](https://nft-studio.hashgraph.com/nft-risk-calculator/) evaluates the potential risk associated with tokens on the Hedera network, focusing on the likelihood of a "[rug pull](../../support-and-community/glossary.md#rug-pull)" based on specific token properties. The risk calculator generates a comprehensive risk score and risk level by analyzing key factors, such as the Admin Key, Supply Key, and KYC Key. The calculator can be used to assess both existing tokens and those you plan to create, gaining insights into how specific properties may make a collection more or less risky. This helps developers and token creators make informed decisions about token security and governance.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="https://nft-studio.hashgraph.com/nft-risk-calculator/"><strong>NFT Risk Calculator Web App</strong> </a></td><td><a href="../../.gitbook/assets/nft-risk-calculator-icon-3.png">nft-risk-calculator-icon-3.png</a></td><td><a href="https://nft-studio.hashgraph.com/nft-risk-calculator/">https://nft-studio.hashgraph.com/nft-risk-calculator/</a></td></tr><tr><td align="center"><a href="https://github.com/hedera-dev/risk-calculator-for-hedera-tokens"><strong>NFT Risk Calculator Repo</strong></a>   </td><td><a href="../../.gitbook/assets/github-cards-icon.png">github-cards-icon.png</a></td><td><a href="https://github.com/hedera-dev/risk-calculator-for-hedera-tokens">https://github.com/hedera-dev/risk-calculator-for-hedera-tokens</a></td></tr></tbody></table>

***

## How to Use the NFT Risk Calculator

{% embed url="https://www.youtube.com/watch?v=rjfk0ztRnhM" %}

### **Select Token Type and Enter Token Details**

Start by choosing the type of token you want to evaluate. The application offers an easy-to-use interface that lets you toggle between assessing a new token or an existing token on the Hedera network.&#x20;

{% tabs %}
{% tab title=" Existing Token" %}
For **existing tokens**, input the token ID you wish to analyze on the field labeled "Token ID." We'll use the [Wild Tigers](https://x.com/wildtigers\_nft) collection (id: [`0.0.6024491`](https://hashscan.io/mainnet/token/0.0.6024491)) as an example.

<figure><img src="../../.gitbook/assets/nft-studio-risk-calculator-existing-wild-tigers.png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title=" New Token " %}
For a **new token**, this would be the [proposed token’s ID](nft-risk-calculator.md#what-is-a-proposed-token-id)\*.&#x20;

<figure><img src="../../.gitbook/assets/nft-studio-risk-calculator-new-token.png" alt=""><figcaption></figcaption></figure>

#### Customize Token Control Levels

Each key grants a specific level of control over token operations, letting you customize how your token is managed and interacts with users. Toggling these keys (also see [risk factors table ](nft-risk-calculator.md#risk-factors)in the next step) provides granular control over features and security, allowing configurations that range from flexible to highly governed.&#x20;

<figure><img src="../../.gitbook/assets/nft-studio-risk- calculator-factors-keys.png" alt="" width="375"><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

### **Key Factors Evaluated**

The key factors evaluated include whether a token has control over its operations through specific keys. For example:

* _Admin Keys_ grant control over modifying the token's properties or configurations. If enabled, the admin key significantly influences the risk score since it gives the holder broad control over the token.
* _Supply Keys_ control the minting and burning of the token's supply. Tokens with a Supply Key enabled have higher flexibility in adjusting supply but also pose potential risks, as the supply could be manipulated.&#x20;
* _KYC Keys_ determine whether accounts need to be verified (KYC’d) before transacting with the token. A token requiring KYC may indicate stricter control and lower risk but also restricts participation.

This detailed risk factor score table below breaks down the potential vulnerabilities and shows how each key influences the overall risk score.

<table><thead><tr><th width="139">Key</th><th width="109" align="center">Risk Score</th><th>Description</th></tr></thead><tbody><tr><td><strong>ADMIN</strong> </td><td align="center">🔴  <strong>200</strong> </td><td>The <code>ADMIN</code> key can be used to delete the entire collection as they have control over the token’s administration.</td></tr><tr><td><strong>WIPE</strong></td><td align="center">🔴  2<strong>00</strong> </td><td>The <code>WIPE</code> key can be used to delete all tokens within that collection for a specific account. </td></tr><tr><td><strong>FREEZE</strong></td><td align="center"><strong>🟠 50</strong> </td><td>A collection with a <code>FREEZE</code> key has a moderate risk because it can freeze an account for token transfers.</td></tr><tr><td><strong>KYC</strong></td><td align="center"><strong>🟠 50</strong> </td><td>The <code>KYC</code> key can mark an account as KYC-approved.</td></tr><tr><td><strong>PAUSE</strong></td><td align="center"><strong>🟠 50</strong> </td><td>The <code>PAUSE</code> key can pause or unpause a collection. Pausing a collection prevents all transfers of the tokens within the collection.</td></tr><tr><td><strong>SUPPLY</strong></td><td align="center">🟡 <strong>20</strong> </td><td>The <code>SUPPLY</code> key can change the total supply of a token within a collection and must be set to mint additional tokens. </td></tr><tr><td><strong>FEE SCHEDULE</strong></td><td align="center"><strong>🟡 20</strong> </td><td>The <code>FEE SCHEDULE</code> key has the ability to change the Collection’s royalty fees after it has been minted. Changing the royalty fees of a Collection will impact all tokens within the Collection (fixed, royalty and fallback).</td></tr></tbody></table>

<details>

<summary><strong>Understanding the Results</strong></summary>

The tool provides two main outputs: a risk score and a risk level. Here's how to interpret these results:

**Risk Score**

The risk score is a numerical value that indicates the overall potential risk associated with a token.

* <mark style="background-color:yellow;">**Lower Scores (0-25)**</mark>: Tokens with low risk, indicating that the settings and properties of the token minimize potential vulnerabilities.
* <mark style="background-color:orange;">**Medium Scores (26-50)**</mark>: Moderate risk, suggesting some factors may need to be reviewed for secure token management.
* <mark style="background-color:red;">**High Scores (51+)**</mark>: This indicates significant risk, indicating that several factors make the token susceptible to governance issues or potential "rug pulls."

**Risk Level**

The risk level provides a qualitative assessment (e.g., Low, Medium, or High) to give a more general understanding of the token’s risk profile.

<img src="../../.gitbook/assets/nft-studio-risk- calculator-risk-levels.png" alt="" data-size="original">

**Breakdown of Risk Factors**

* The calculator provides a detailed breakdown showing how each key and token property contributes to the final risk score.
* You can use this breakdown to pinpoint high-risk settings, allowing you to make targeted adjustments to your token configuration.

**Using the Results**

* If you're evaluating an _existing token_, use the results to inform investment decisions or suggest governance changes.
* For _new tokens_, adjust key settings to achieve a balanced risk level that fits your project’s goals for security and flexibility.

</details>

{% hint style="info" %}
**Use Risk Calculator in Combination with Other Tools**

The NFT Risk Calculator pairs well with the [Token Holders List Builder](nft-token-holders-list-builder.md) to evaluate the risk of tokens held by specific wallet addresses and understand the potential exposure of those holders. Moreover, after identifying the list of token holders, you can use the [Airdrop List Verifier](airdrop-list-verifier.md) to ensure smooth distribution and airdrop eligibility.
{% endhint %}

#### 🎉 Congratulations! You've successfully assessed the risk factors for your NFT collection. On the next page, capture a detailed view of your token distribution using the Token Balance Snapshot.

***

## Additional Resources

* [**HashScan Network Explorer**](https://hashscan.io/)
* [**Testnet HBAR Faucet**](https://portal.hedera.com/)
* [**Wild Tigers Collection**](https://x.com/wildtigers\_nft)
* [**Create and Transfer Your First Hedera NFT**](../../tutorials/token/create-and-transfer-your-first-nft.md)
