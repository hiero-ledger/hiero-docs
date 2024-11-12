# Staking Program

The staking feature will be rolled out in four phases. The first two phases are described below, and the final two phases will be available at the start of Phase I.

## **Phase I: Technical Availability \[Complete]**

The staking functionality is now available and live on both the Hedera Testnet and Mainnet as of July 21, 2022. In phase I, users will technically be able to stake their account to mainnet nodes but this will not contribute to a node’s consensus weight (voting power). This initial technical availability release does not reward participants for staking but enables a level playing field whereby all market participants have the possibility to join the staking program and avoids giving an unfair advantage to the first few who stake.

## **Phase II: Ecosystem Development \[Complete]**

During this phase, supported exchanges and wallets will be able to integrate the staking functionality to provide account holders an easy way to stake their HBAR, but will not distribute rewards. In addition, web applications for delegating stake will likely be built for utilization by the retail ecosystem. During this phase, there will be visibility of stake per node, and staking to a node will affect its consensus weight (voting power) with monthly updates.

## **Phase III: Staking Rewards Program Launch \[Complete]**

The Hedera Governing Council will determine when the Hedera ecosystem has reached a minimum viable set of integrations to enable staking rewards. Once this is determined, the council (through CoinCom) will vote to update the reward rate, and subsequently, the mainnet will be updated with the agreed-upon reward rate. The latest staking reward rate voted on by CoinComm can be found [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).\
\
Once updated, the staking reward account (0.0.800) will be eligible to distribute rewards earned by stakers, once the rewards threshold of 250M total HBAR has been met. Rewards will continue to be distributed even if, after this time, the balance of account 0.0.800 goes below 250M.

## Phase IV: Complete Staking Implementation

In this phase, 24-hour updates will be released for visibility into the stake per node, and the node uptime feature will be released. This means that instead of updating node stake visibility on a monthly basis, node stake visibility will be updated on a 24-hour epoch interval. When the uptime feature takes effect, staked accounts will not earn rewards when nodes cannot participate in consensus (unavailable or offline).

## **Staking Nodes**

{% hint style="info" %}
The Hedera Governing Council voted to change the min stake value from half of the max node stake value to 1/4 of the max node stake value.
{% endhint %}

All consensus nodes run by the Hedera Governing Council distribute rewards to the accounts staked to them. You can find information about each node in the network by visiting one of the Hedera network explorers or getting the network [address book](../../sdks-and-apis/rest-api.md#api-v1-network-nodes). In the future, network participation will open up to community nodes and eventually to the public as part of Hedera’s decentralization efforts.

Nodes have a **minimum stake** and **maximum stake**. The node's minimum stake must be met for the accounts staked to that node to be eligible to earn staking rewards. Staked tokens that go over the maximum stake will no longer impact the proportion of rewards returned. The maximum stake threshold for each node will be the total number of HBAR divided by the total number of nodes in the network. The minimum node stake threshold value will be 1/4 of the maximum node stake value. These values will change as more nodes are added to the network or can change by vote of the Hedera Governing Council.

#### Example:

Minimum Stake: ​50,000,000,000 hbars\*(1/26nodes)\*(1/4)

Maximum Stake: ​50,000,000,000 hbars\*(1/26nodes)

## **Lockup Period**

There is **no lock-up period** when accounts are staked to a node. Stakers do not need to choose an amount of HBAR to stake from their account. The account's entire balance is staked automatically to the selected node or account. There is no concept of “bonding” or “slashing” of your tokens. The staked account balance is liquid at all times.

## **Staking Reward Account**

The staking reward account distributes rewards to eligible staked accounts. The staking reward account ID is [0.0.800](https://hashscan.io/#/mainnet/account/0.0.800?type=) on mainnet. Anyone in the community can contribute to the rewards pool by transferring HBAR into that account. This account has no keys, and therefore, any HBAR transferred into this account cannot be returned to the owner. If you choose to contribute to the rewards pool, please make sure to double-check your transfer transaction details.

The staking reward account needs to meet a minimum balance before rewards can begin to distribute rewards earned to the eligible staked accounts. The minimum HBAR balance threshold for the reward account is 250 million HBAR voted on by the Hedera Governing Council. If this balance is not met staking rewards will not be distributed. You can view the balance of this account by visiting any of the Hedera network explorers.

Once the minimum threshold is met, rewards will continue to be distributed to staked accounts as long as there is a balance in the rewards account even if it falls below the initial minimum threshold. The reward rate will initially be set to zero. The Hedera Governing Council will vote and update the reward rate when the Hedera Staking Reward Program goes live. The latest reward rate can be found [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).&#x20;

## **Staking Rewards**

In Phase I, the staking reward rate will initially be zero. The Hedera Governing Council will determine when the Hedera ecosystem has reached a minimum viable set of integrations to enable staking rewards. Once this is determined, the council (through CoinCom) will vote to update the reward rate, and subsequently, the mainnet will be updated with the agreed-upon reward rate.

Any account can elect to stake to a node or another account. The **minimum staking period** is the minimum amount of time an account needs to be staked to a consensus node before the account is eligible to earn rewards. The minimum staking period is **one day (24 hours).** The staking period begins at midnight UTC and ends at midnight UTC. The Hedera Governing Council defines the staking period. The earned rewards are not transferred to the staked account immediately after an account has been staked for one full staking period. Please see the Staking Reward Distribution section for what scenarios trigger the payment of a reward.

Accounts staked for less than the defined minimum staking period are not eligible to earn rewards for that period. Nodes and accounts accumulate stake and rewards per whole HBAR. Fractions are rounded down. When a node is deleted, it returns zero rewards.

For a staked account to be eligible to earn rewards, the following must be true:

* The staking reward account needs to have met the initial threshold balance of HBAR
  * Once the minimum threshold value has been met, the rewards account will continue to reward staked accounts even if the balance falls below the initial threshold
* The account the node is staked to meets the minimum node stake threshold value
* The account needs to be staked for the minimum staking period
* The reward rate is voted on by the Hedera Governing Council and updated on mainnet

Rewards will continue to be earned when a node is down or inactive in the first phase. The Council (through CoinCom) has voted to implement a maximum cap of [2.5% annual reward rate](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm). The actual reward rate will vary depending on how many HBAR are staked for rewards, but the rate will not exceed the cap. In the future, when nodes are down or inactive the staked account will not be eligible to earn rewards.

This staking system offers an additional unique functionality: **indirect staking**. If account A stakes to node N, then the stake increases the consensus weight of N, and account A is rewarded for every 24-hour period that it stakes. If account A stakes to account B, and account B stakes to node N, then the stake from both A and B will increase the consensus weight of N, but the rewards for both A and B will be received by B.

An account can optionally decline to earn rewards when staked. The account will still be counted towards meeting the node’s minimum stake value.

**📣 If you're interested in checking out the wallets and exchanges supporting staking HBAR, head to the** [**Stake HBAR**](stake-hbar.md) **page.**
