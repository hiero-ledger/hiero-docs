# Governance Token DAO

## Token DAO Creation

[After creating and funding a Hedera account](./#getting-started-with-hashiodao), navigate to the HashioDAO web application dashboard and connect your wallet. Once your wallet is connected, click **Create a new DAO** and this will guide you through a series of setup steps where you will define the details of your DAO including name and governance type.&#x20;

<figure><img src="../../.gitbook/assets/hashiodao-landing (1).png" alt=""><figcaption></figcaption></figure>

Read and and accept the disclaimer and click  **Next**.

<figure><img src="../../.gitbook/assets/hashiodao-accept-disclaimer.png" alt="" width="563"><figcaption></figcaption></figure>

Define the details of your DAO by filling out each field. These details are important because it creates your DAO's public profile and how the community will be identified.

<figure><img src="../../.gitbook/assets/hashiodao-create-details.png" alt="" width="563"><figcaption></figcaption></figure>

***

## Governance Models

Choose the governance model that works best with your organizations's goals. There are three models to choose from: governance token, multisig, and NFT.&#x20;

<figure><img src="../../.gitbook/assets/hashiodao-create-type.png" alt="" width="563"><figcaption></figcaption></figure>

The **`createDAO()`** function is called on the [FTDaoFactory](https://github.com/hashgraph/hedera-accelerator-defi-dex/blob/main/contracts/dao/FTDAOFactory.sol) factory contract when you create a token DAO. This initiates a new DAO treasury smart contract based on the parameters you set for you token. These parameters will be set in the next step.

***

## Define Governance Token and Voting Details

Define the DAO's governance token and voting details. You have the option to create a new governance token or use an existing one. For the purposes of this tutorial, we will create a new one. Define the details for your new governance token:&#x20;

<figure><img src="../../.gitbook/assets/hashiodao-governance-token-details.png" alt="" width="563"><figcaption></figcaption></figure>

Once you click **Create Token** and approve the transaction in your wallet a Governor smart contract is deployed for managing proposals, voting, and executing decisions. This contract streamlines proposal handling and ensures a fair voting process based on community consensus. This transaction will also generate a token ID for your governance token.

<figure><img src="../../.gitbook/assets/hashiodao-token-treasury.png" alt=""><figcaption></figcaption></figure>

Set the voting quorum percentage, voting duration, and delay period for votes. To avoid delays, starting with a low quorum percentage is recommended. This can adjusted over time as the community's needs change.

{% hint style="info" %}
DAOs set their quorum percentage depending on their goals and needs. If the quorum is set too high, decisions are only made with a large number of participation from the members. If the quorum is set low, this allows more flexibility in decision making and the chances of proposals being approved would be higher than with a higher quorum.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/hashiodao-voting-quorum.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="warning" %}
Be cautious when depositing governance tokens in to the treasury. Since the treasury does not have voting rights, depositing a large portion of your governance tokens may result in being locked out of creating and voting on proposals and accessing treasury funds. Always check that these allocations don't limit your ability to reach a voting quorum.&#x20;
{% endhint %}

***

## Review and Submit to Create New DAO

Review the DAO details, governance type, and submit the transaction. Approve the DAO creation transaction in your wallet to proceed.&#x20;

<figure><img src="../../.gitbook/assets/hashiodao-review-details.png" alt="" width="563"><figcaption></figcaption></figure>

Your new DAO should show up on the HashioDAO dashboard once the transaction is approved and confirmed. If the DAO was successfully creation, a "Created new public Governance Token DAO \_\_\_\_" message will pop-up on the top-right corner of your browser (see below for example).&#x20;

<figure><img src="../../.gitbook/assets/hashiodao-dashboard-new-dao.png" alt=""><figcaption></figcaption></figure>

Lastly, send in some HBAR to the treasury to finish setting up the DAO by clicking on the **Deposit** button under the **Assets** tab. Approve the transaction in your wallet.

{% hint style="info" %}
When a DAO is created, its financial resources are managed through the DAO treasury smart contract. This contract facilitates the distribution of funds by allowing transactions to be initiated by the admin. In this structure, the role of the admin is fulfilled by the voting (Governo&#x72;_)_ contract.
{% endhint %}

<figure><img src="../../.gitbook/assets/hashiodao-deposit-assets.png" alt=""><figcaption></figcaption></figure>

***

## Conclusion and Additional Resources

Congrats on successfully creating your first DAO using the governance token model! In this guide, you defined and customized the details of the new DAO, governance token, and voting processes. Happy DAO creating!

➡ [**HashioDAO Repository**](https://github.com/hashgraph/hedera-accelerator-defi-dex-ui)

➡ [**HashScan Network Explorer**](https://hashscan.io/testnet/dashboard)

➡ [**HashioDAO Web Application**](https://hashiodao.swirldslabs.com/)

**➡** [**What is a Goverance Token?**](https://blockchainjournal.com/glossary/what-is-a-governance-token/)
