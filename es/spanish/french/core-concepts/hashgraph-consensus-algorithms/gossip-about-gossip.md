# Gossip About Gossip

Hashgraph consensus uses a **gossip protocol**. This means that a member such as Alice will choose another member at random, such as Bob, and then Alice will tell Bob all of the information she knows so far. Alice then repeats with a different random member. Bob repeatedly does the same, and all other members do the same. In this way, if a single member becomes aware of new information, it will spread exponentially fast through the community until every member is aware of It.

The synchronization of information between two members through the gossip protocol is called a **gossip sync**. Upon completion of a gossip sync, each participating member commemorates the gossip sync with an event. An **event** is stored in memory as a data structure composed of a timestamp, an array of zero or more transactions, two parent hashes, and a cryptographic signature. The two parent hashes are the hash of the last event created by the self-parent prior to the gossip sync and the hash of the last event created by the other-parent prior to the gossip sync. For example, if Alice and Bob perform a gossip sync, Alice would create a new event commemorating the gossip sync where the self-parent hash would be the hash of the last event Alice created prior to the gossip sync and the other-parent hash would be the hash of the last event Bob created prior to the gossip sync. Bob would also create an event commemorating the gossip sync, but the self-parent hash would be the hash of the last event he created before the gossip sync and the other-parent hash would be the hash of the last event Alice created before the gossip sync. Gossip continues until all members have received the newly created event.

## Gossip About Gossip

The history of how these events are related to each other through their parent hashes is called **gossip about gossip**. This history expresses itself as a type of directed acyclic graph (DAG), a graph of hashes, or a hashgraph. The hashgraph records the history of how members communicated. It grows directionally over time as more gossip syncs take place and events are created. All members keep a local copy of the hashgraph which continues to update as members sync with one another.

These hashgraphs may be slightly different at any given moment, but they will always be consistent. Consistent means that if \[Alice] and \[Bob] both contain event x, then they will both contain exactly the same set of ancestors for x, and will both contain exactly the same set of edges between those ancestors.

Each event contains the following:

- Timestamp
- Two hashes of two events below itself
  - Self-parent
  - Other-parent
- Transactions
- Digital signature

| Item                                   | Description                                                                      |
| -------------------------------------- | -------------------------------------------------------------------------------- |
| **Timestamp:**         | The timestamp of when the member created the event commemorating the gossip sync |
| **Transactions:**      | The event can hold zero or more transactions                                     |
| **Hash 1:**            | Self-parent hash                                                                 |
| **Hash 2:**            | Other-parent hash                                                                |
| **Digital Signature:** | Cryptographically signed by the creator of the event                             |
