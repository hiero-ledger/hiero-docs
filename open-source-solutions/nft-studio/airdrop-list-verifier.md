# Airdrop List Verifier

The [Airdrop List Verifier](https://nft-studio.hashgraph.com/airdrop-list-verifier/) is a tool that streamlines organizing airdrops for Hedera tokens. By inputting a token ID and a list of account IDs, users can quickly verify which accounts are either associated with the token or have available auto-association slots. This tutorial will guide you through generating a refined list of eligible accounts, enabling efficient and targeted airdrops.

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td align="center"><a href="https://nft-studio.hashgraph.com/airdrop-list-verifier/"><strong>Airdrop List Verifier Web App</strong></a></td><td><a href="https://nft-studio.hashgraph.com/airdrop-list-verifier/">https://nft-studio.hashgraph.com/airdrop-list-verifier/</a></td><td><a href="../../.gitbook/assets/nft-studio-airdrop-list-verifier-icon.png">nft-studio-airdrop-list-verifier-icon.png</a></td></tr><tr><td align="center"><a href="https://github.com/hedera-dev/airdrop-list-verifier"><strong>Airdrop List Verifier Repo</strong></a></td><td><a href="https://github.com/hedera-dev/airdrop-list-verifier">https://github.com/hedera-dev/airdrop-list-verifier</a></td><td><a href="../../.gitbook/assets/github-cards-icon.png">github-cards-icon.png</a></td></tr></tbody></table>

***

## How to Use the Airdrop List Verifier

{% embed url="https://youtu.be/3nzizeimiy0?si=bclmRAF1ewzQh5g-" %}

### **Enter the Token and Account Details**

To demonstrate the functionality of the Airdrop List Verifier, we'll use the [SIWAS by Kabila](https://market.kabila.app/en/collections/2179656) collection (token ID: [`0.0.2179656`](https://hashscan.io/mainnet/token/0.0.2179656)) to verify which accounts are eligible to receive an airdrop. Open your browser and navigate to the [Airdrop List Verifier](https://nftstudio.hashgraph.com/airdrop-list-verifier/) web app. In the _Token ID_ field, enter the ID of the token you want to verify for airdrop eligibility. Next, in the _Account IDs List_ field, input the account IDs of the users you plan to airdrop the token to.

### **Build the List**

Once all details are entered, click **"Build List."** The verifier will process each account ID against the specified token ID to determine eligibility. Once complete, a list of eligible accounts will be generated, enabling seamless token distribution.

<figure><img src="../../.gitbook/assets/nft-studio-airdrop-list-verifier.png" alt=""><figcaption></figcaption></figure>

<details>

<summary><strong>Use Cases &#x26; Best Practices</strong></summary>

The Airdrop List Verifier can be applied in different scenarios, such as:

* _C_ommunity Reward Drops
  * Verify the eligibility of community members based on their token associations before distributing rewards or exclusive NFTs.
* New Token Launches
  * Before launching a new token, filter potential recipients who already hold related tokens or meet certain criteria (e.g., association with other collections).
* Filtered Airdrops Based on Ownership
  * Target airdrops to specific groups of token holders, such as those who have held a token for a certain duration or meet minimum holding amounts.

**Best Practices**

To make the most out of the Airdrop List Verifier, consider the following best practices:

* Before uploading, make sure the list of account IDs is accurate and up to date, as any invalid entries will affect your results.
* If your token requires a specific association, ensure the correct token ID is input to avoid distributing it to unintended recipients.
* Ensure the token's settings (like KYC requirements and association rules) match your intended airdrop criteria so that only eligible accounts are verified.

These tips help prevent common issues like distributing to incorrect accounts or missing eligible recipients.

</details>

#### 🎉 Great job! You've successfully verified the eligible recipients for your airdrop. Next, use the Metadata Validator to ensure your NFT metadata is compliant.

***

## Additional Resources

* [**SIWAS by Kabila Collection** ](https://market.kabila.app/en/collections/2179656)
* [**HashScan Network Explorer**](https://hashscan.io/)
* [**Hedera Testnet HBAR Faucet**](https://portal.hedera.com/faucet)
* [**Hedera Developer Portal Account**](https://portal.hedera.com/)
* [**Create Your First Frictionless Airdrop Campaign**](../../tutorials/token/create-your-first-frictionless-airdrop-campaign.md)
