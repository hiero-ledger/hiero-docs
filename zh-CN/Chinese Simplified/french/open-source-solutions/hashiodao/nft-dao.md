# NFT DAO

## NFT DAO Creation

[After creating and funding a Hedera account](./#getting-started-with-hashiodao), navigate to the HashioDAO web application dashboard and connect your wallet. Once your wallet is connected, click **Create a new DAO** and this will guide you through a series of setup steps where you will define the details of your DAO including name and governance type.

<figure><img src="../../.gitbook/assets/hashiodao-landing (1).png" alt=""><figcaption></figcaption></figure>

Read and and accept the disclaimer and click **Next**.

<figure><img src="../../.gitbook/assets/hashiodao-accept-disclaimer.png" alt="" width="563"><figcaption></figcaption></figure>

Define the details of your DAO by filling out each field. These details are important because it creates your DAO's public profile and how the community will be identified.

<figure><img src="../../.gitbook/assets/hashiodao-create-details.png" alt="" width="563"><figcaption></figcaption></figure>

***

## Governance Models

Choose the governance model that works best with your organizations's goals. There are three models to choose from: governance token, multisig, and NFT.

<figure><img src="../../.gitbook/assets/hashiodao-nft-create.png" alt="" width="563"><figcaption></figcaption></figure>

The **`createDAO()`** function is called on the [NFTDaoFactory](https://github.com/hashgraph/hedera-accelerator-defi-dex/blob/main/contracts/dao/NFTDAOFactory.sol) factory contract when you create an NFT DAO. This initiates a new DAO treasury smart contract based on the parameters you set for you token. These parameters will be set in the next step.

***

## Define NFT and Voting Details

Define the DAO's NFT and voting details. You have the option to create a new governance token or use an existing one. For the purposes of this tutorial, we will create a new one. Define the details for your new governance token:

<figure><img src="../../.gitbook/assets/hashiodao-governance-token-details.png" alt="" width="563"><figcaption></figcaption></figure>

Once you click **Create Token** and approve the transaction in your wallet a Governor smart contract is deployed for managing proposals, voting, and executing decisions. This contract streamlines proposal handling and ensures a fair voting process based on community consensus. This transaction will also generate a token ID for your governance token.

<figure><img src="../../.gitbook/assets/hashiodao-token-treasury.png" alt=""><figcaption></figcaption></figure>

Set the voting quorum percentage, voting duration, and delay period for votes. To avoid delays, starting with a low quorum percentage is recommended. This can adjusted over time as the community's needs change.

{% hint style="info" %}
DAOs set their quorum percentage depending on their goals and needs. If the quorum is set too high, decisions are only made with a large number of participation from the members. If the quorum is set low, this allows more flexibility in decision making and the chances of proposals being approved would be higher than with a higher quorum.
{% endhint %}

<figure><img src="../../.gitbook/assets/hashiodao-voting-quorum.png" alt="" width="563"><figcaption></figcaption></figure>

Be cautious when depositing governance tokens into the treasury since it does not have voting rights. If you deposit a large portion it could lock you out of proposal creation, voting, and treasury access.

***

## Review and Submit to Create New DAO

Review the DAO details, governance type, and submit the transaction. Approve the DAO creation transaction in your wallet to proceed.

<figure><img src="../../.gitbook/assets/hashiodao-review-details.png" alt="" width="563"><figcaption></figcaption></figure>

***

## Mint and Deposit NFT

Once your DAO is created, you need to mint the NFT and deposit to the treasury to finish setting up. Click the **Mint NFT** button, enter the NFT URL, and click the **Mint NFT Tokens** button.

<figure><img src="../../.gitbook/assets/hashiodao-nft-mint.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
When a DAO is created, its financial resources are managed through the DAO treasury smart contract. This contract facilitates the distribution of funds by allowing transactions to be initiated by the admin. In this structure, the role of the admin is fulfilled by the voting (Governor\_)\_ contract.
{% endhint %}

When the "Successfully minted NFT tokens" window pops up, click on the **Deposit** button and select the NFT and serial number from dropdown, and **Deposit Fund**.

<figure><img src="../../.gitbook/assets/hashiodao-nft-deposit.png" alt=""><figcaption></figcaption></figure>

***

## Conclusion and Additional Resources

Congrats on successfully creating your first DAO using the NFT governance model! In this guide, you defined and customized the details of the new DAO, including minting governance NFTs. Happy DAO creating!

➡ [**HashioDAO Repository**](https://github.com/hashgraph/hedera-accelerator-defi-dex-ui)

➡ [**HashScan Network Explorer**](https://hashscan.io/testnet/dashboard)
