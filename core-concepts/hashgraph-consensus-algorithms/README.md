---
description: Distributed consensus algorithm
---

# Hashgraph Consensus Algorithm

The hashgraph consensus algorithm enables distributed consensus in an innovative, efficient way. Hashgraph is a distributed consensus algorithm and data structure that is fast, fair, and secure. This indirectly creates a trusted community, even when members do not necessarily trust each other.

The [hashgraph consensus algorithm](./) and platform code are open-source under an Apache 2.0 license.

{% embed url="https://www.youtube.com/watch?v=cje1vuVKhwY&t=5s" %}



## Performance

### Cost

The hashgraph is inexpensive, in the sense of avoiding proof-of-work. Individuals and organizations running hashgraph nodes do not need to purchase expensive custom mining rigs. Instead, they can run readily available, cost-effective hardware. The hashgraph is 100% efficient, wasting no resources on computations that slow it down.

### Efficiency

The hashgraph is 100% efficient, as that term is used in the blockchain community. In blockchain, work is sometimes wasted mining a block that later is considered stale and is discarded by the community. In hashgraph, the equivalent of a “block” never becomes stale. Hashgraph is also efficient in its use of bandwidth. Whatever is the amount of bandwidth required merely to inform all the nodes of a given transaction (even without achieving consensus on a timestamp for that transaction), hashgraph adds only a very small overhead beyond that absolute minimum. Additionally, hashgraph’s voting algorithm does not require any additional messages be sent in order for nodes to vote (or those votes to be counted) beyond those messages by which the community learned of the transaction itself.

### Throughput

The hashgraph is fast. It is limited only by the bandwidth. If each member has enough bandwidth to download and upload a given number of transactions per second, the system as a whole can handle close to that many. Even a fast home internet connection could be fast enough to handle all of the transactions of the entire VISA card network, worldwide.

### **State Efficiency**

Once an event occurs, within seconds everyone in the community will know where it should be placed in history with 100% certainty. More importantly, everyone will know that everyone else knows this. At that point, they can just incorporate the effects of the transaction and, unless needed for future audit or compliance, then discard it. So in a minimal cryptocurrency system, each member would only need to store the current balance of each account that isn’t empty. They wouldn’t need to remember the full history of the transactions that resulted in those balances all the way back to ‘genesis’.

## Security

### Asynchronous Byzantine Fault Tolerance

The hashgraph is asynchronous Byzantine Fault Tolerant. This means that no single member (or small group of members) can prevent the community from reaching a consensus. Nor can they change the consensus once it has been reached. Each member will eventually reach a point where they know for sure that they have reached consensus. Blockchain does not have a guarantee of Byzantine agreement, because a member never reaches certainty that agreement has been achieved (there’s just a probability that rises over time). Blockchain is also non-Byzantine because it doesn’t automatically deal with network partitions. If a group of miners is isolated from the rest of the internet, that can allow multiple chains to grow, which conflict with each other on the order of transactions.

It is worth noting that the term “Byzantine Fault Tolerant” (BFT) is sometimes used in a weaker sense by other consensus algorithms. But here, it is used in its original, stronger sense that (1) every member eventually knows consensus has been reached, (2) attackers may collude, and (3) attackers even control the internet itself (with some limits). Hashgraph is Byzantine, even by this stronger definition.

There are different degrees of BFT, depending on the assumptions made about the network and transmission of messages. The strongest form of BFT is asynchronous BFT- meaning that it can achieve consensus even if malicious actors are able to control the network and delete or slow down messages of their choosing. The only assumptions made are that more than 2⁄3 are following the protocol correctly and that if messages are repeatedly sent from one node to another over the internet, eventually one will get through, and then eventually another will, and so on. Some systems are partially asynchronous, which are secure only if the attackers do not have too much power and do not manipulate the timing of messages too much. For instance, a partially asynchronous system could prove Byzantine under the assumption that messages get passed over the internet in ten seconds. This assumption ignores the reality of botnets, Distributed Denial of Service attacks, and malicious firewalls.

### ACID Compliance

The hashgraph is ACID compliant. ACID (Atomicity, Consistency, Isolation, Durability) is a database term and applies to the hashgraph when it is used as a distributed database. A community of nodes uses it to reach a consensus on the order in which transactions occurred. After reaching consensus, each node feeds those transactions to that node’s local copy of the database, sending in each one in the consensus order. If the local database has all the standard properties of a database (ACID), then the community as a whole can be said to have a single, distributed database with those same properties. In blockchain, there is never a moment when you know that consensus has been reached, so it would not be ACID compliant.

### Distributed Denial of Service (DDoS) Attack Resilience

One form of Denial of Service (DoS) attack occurs when an attacker is able to flood an honest node on a network with meaningless messages, preventing that node from performing other (valid) duties and roles. A Distributed Denial of Service (DDoS) uses public services or devices to unwittingly amplify that DoS attack - making them an even greater threat.

In a distributed ledger, a DDoS attack could target the nodes that contribute to the definition of consensus and, potentially, prevent that consensus from being established.

Hashgraph is DDoS resilient as it empowers no single node or a small number of nodes with special rights or responsibilities in establishing consensus. Both Bitcoin and hashgraph are distributed in a way that resists DDoS attacks. An attacker might flood one member or miner with packets, to temporarily disconnect them from the internet. But the community as a whole will continue to operate normally. An attack on the system as a whole would require flooding a large fraction of the members with packets, which is more difficult. There have been a number of proposed alternatives to blockchain-based on leaders or round-robin. These have been proposed to avoid the proof-of-work costs of Bitcoin. But they have the drawback of being sensitive to DDoS attacks. If the attacker attacks the current leader, and switches to attacking the new leader as soon as one is chosen, then the attacker can freeze the entire system while still attacking only one computer at a time. Hashgraph avoids this problem, while still not needing proof-of-work.

## Fairness

Hashgraph is fair because there is no leader or miner given special permissions for determining the consensus timestamp assigned to a transaction. Instead, the consensus timestamp for transactions are calculated via a voting process in which the nodes collectively and democratically establish the consensus. We can distinguish between three aspects of fairness.

### Fair Access

Hashgraph is fundamentally fair because no individual can stop a transaction from entering the system, or even delay it very much. If one (or few) malicious nodes attempts to prevent a given transaction from being delivered to the rest of the community and so be added into consensus, then the random nature of the gossip protocol will ensure that the transaction flows around that blockage.

### Fair Timestamps

Hashgraph gives each transaction a consensus timestamp that reflects when the majority of the network members received that transaction. This consensus timestamp is “fair”, because it is not possible for a malicious node to corrupt it and make it differ by very much from that time. Every transaction is assigned a consensus time, which is the median of the times at which each member says it first received it. Received here refers to the time that a given node was first passed the transaction from another node through gossip. This is part of the consensus, and so has all the guarantees of being Byzantine. If more than 2⁄3 of participating members are honest and have reliable clocks on their computer, then the timestamp itself will be honest and reliable, because it is generated by an honest and reliable member or falls between two times that were generated by honest and reliable members. Because hashgraph takes the median of all these times, the consensus timestamp is robust. Even if a few of the clocks are a bit off, or even if a few of the nodes maliciously give times that are far off, the consensus timestamp is not significantly impacted.

This consensus timestamping is useful for things such as a legal obligation to perform some action by a particular time. There will be a consensus on whether an event happened by a deadline, and the timestamp is resistant to manipulation by an attacker. In a blockchain, each block contains a timestamp, but it reflects only a single clock: the one on the computer of the miner who mined that block.

### Fair Transaction Order

Transactions are put into order according to their timestamps. Because the timestamps assigned to individual transactions are fair, so is the resulting order. This is critically important for some use cases. For example, imagine a stock market, where Alice and Bob both try to buy the last available share of a stock at the same moment for the same price. In a blockchain, a miner might put both of those transactions in a single block, and have complete freedom to choose what order they occur. Or the miner might choose to only include Alice’s transaction, and delay Bob’s to a future block. In hashgraph, there is no way for an individual to unduly affect the consensus order of those transactions. The best Alice can do is to invest in a better internet connection so that her transaction reaches everyone before Bob’s. That’s the fair way to compete.

## FAQ

<details>

<summary>What is the hashgraph consensus algorithm? How does it work?</summary>

The hashgraph consensus algorithm is a distributed consensus mechanism used by Hedera. It uses a data structure called a [hashgraph](../../support-and-community/glossary.md#hashgraph), and a consensus mechanism called the Gossip protocol. This combination allows for fast, fair, and secure consensus. The algorithm works by each node in the network sharing information (or “gossiping”) about the transactions it knows about with other nodes in random order.

</details>

<details>

<summary>How secure is the hashgraph consensus algorithm?</summary>

Hashgraph is secure because it is asynchronous Byzantine Fault Tolerant (aBFT). This means that no single member or small group of members can prevent the community from reaching a consensus or changing the consensus once it has been reached. It is also ACID compliant when used as a distributed database, and it is resilient to [Distributed Denial of Service (DDoS)](../../support-and-community/glossary.md#distributed-denial-of-service-ddos) attacks.

</details>

<details>

<summary>What is virtual voting?</summary>

Virtual voting is an integral part of the hashgraph consensus algorithm. It allows nodes to know what others would vote for without needing actual votes sent over the internet. This is accomplished by examining the history of gossip (who spoke to whom and in what order) to determine how a node would vote based on the information it is likely to have.

</details>
