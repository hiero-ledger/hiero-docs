# State and History

A "state machine" is a way of thinking about how a program works: the program maintains a "state" and then changes that state as specified by "transactions". A “replicated state machine” distributes the burden and responsibility for maintaining that changing state across multiple computers in order to provide fault tolerance.

Hedera enables a replicated state machine. Multiple, and potentially adversarial, nodes are able to maintain a consistent representation of the state of a set of data. For instance the amount of HBAR in a set of accounts. As explained in the previous section, transactions are submitted to the network and then the hashgraph algorithm assigns them a consensus timestamp and a place in consensus order. Once all nodes agree on the order for a set of transactions, they apply them to state in that order – one after the other. Each node’s copy of the state thus remains consistent– each node applies (for instance, adjusts the payer & recipient balances for an HBAR payment) the transactions to the state in the agreed-upon order and so maintains an identical state to other nodes at a given moment in time.

The latest state (e.g. the HBAR balances of each account) and the history of the transactions that changed that state are two different data structures with different properties. State is by definition mutable, it constantly changes as transactions are applied to it. It is the history of transactions that is generally envisaged as immutable and irreversible. Separately, state and history present very different storage burdens. At the high throughput that Hedera can support, history will grow very quickly, and so also the burden of storing it. State will grow as well, as new accounts, files, and smart contracts are created, but more slowly.

There are three mostly independent functions that a distributed ledger technology (DLT) node can perform - 1) contribute to consensus 2) persist history of transactions, and 3) persist state. As nodes have limited resources, it is generally the case that a node cannot optimally perform all roles – and choices must be made as to which functions to prioritize.

For Hedera mainnet nodes, contributing to consensus and persisting state are the priorities. The hashgraph, which carries within it all the transactions that change the state, is constantly pruned after transactions are assigned a place in consensus order. Mainnet nodes can delete older portions of the hashgraph because the algorithm delivers finality – once a transaction has been assigned a timestamp and ordered and then applied to the state there is no chance that that will be reversed. Consequently, there is no need to keep historical transactions around in case they might be necessary to apply them in a different order. To prevent such historical transactions from filling up the node’s storage, mainnet nodes delete historical transactions.

But there is value in the history being persisted, even if not by mainnet nodes. An auditor might want to determine the identities of the parties that sent HBAR to a given account, or the times of those transfers, neither of which would be available from the state (e.g. the balances of the accounts) alone.

It is the mirror nodes in the Hedera architecture that, in addition to maintaining state, can also store transaction history. A particular mirror can choose whether to store all history, no history, or possibly only a fraction of the history, perhaps only for particular transaction types, particular accounts, etc. In addition to the history, mirror nodes store information that allows them to prove that their history is correct, even for some kinds of partial histories. So a malicious mirror node is unable to lie about what it is storing. A client seeking a transaction from the past would query an appropriate mirror for the record of that transaction. As the burden of storing history is borne by mirrors and not mainnet nodes, the latter can be optimized for the more fundamental role of consensus and state storage.

## FAQ

<details>

<summary>What is the concept of <code>state</code> in the Hedera Network?</summary>

The state in the Hedera Network is the current status of all data, like the amount of HBAR in a set of accounts. It is maintained across multiple nodes in a consistent representation, providing fault tolerance. The state constantly changes as transactions are applied to it.

</details>

<details>

<summary>How does Hedera handle history?</summary>

The history of transactions is maintained as a separate data structure from the state. It provides a record of transactions that have changed the state over time. It is usually envisaged as immutable and irreversible. Mirror nodes in the Hedera architecture store the transaction history, while mainnet nodes focus on consensus and state storage.

</details>

<details>

<summary>What are the roles of mainnet nodes and mirror nodes?</summary>

Mainnet nodes prioritize contributing to consensus and persisting state. They delete historical transactions after they are assigned a place in the consensus order. Mirror nodes, on the other hand, store the transaction history and maintain state, providing a record of past transactions for audit purposes.

</details>
