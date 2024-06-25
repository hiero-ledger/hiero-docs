# Virtual Voting

It is not enough to ensure that every member knows every event. It is also\
necessary to agree on a linear ordering of the events, and thus of the transactions\
recorded inside the events. Most Byzantine fault tolerance protocols without a\
leader depend on members sending each other votes...Some of these\
protocols require receipts on votes sent to everyone...And they\
may require multiple rounds of voting, which further increases the number of voting\
messages sent.

This pure voting approach becomes bandwidth prohibitive and impractical in a network of any significant size but has the properties of being the fairest and most secure method of reaching consensus. The hashgraph algorithm implements voting that achieves the same fair and secure properties but is also very fast and practical. It accomplishes this through **virtual voting**.

The hashgraph algorithm does not require any votes to be sent across the network to calculate the votes of each member. Members can calculate every other member’s votes by internally looking at each of their copies of the hashgraph and applying the virtual voting algorithm. Votes are calculated locally as a function of the ancestors of a given event.

This virtual voting has several benefits. In addition to saving bandwidth, it ensures that members always calculate their votes according to the rules. If Alice is honest, she will calculate virtual votes for the virtual Bob that are honest. Even if the real Bob is a cheater, he cannot attack Alice by making the virtual Bob vote incorrectly.

With this virtual voting algorithm, Byzantine agreement is guaranteed.

Virtual voting happens in 3 steps:

1. Divide Rounds
2. Decide Fame
3. Find Order

## Divide Rounds

To begin the process of virtual voting, we must first define rounds and witnesses. In the hashgraph history, the first event for a member’s node is that node’s first **witness**. The first witness is the beginning of the first round (r) for that node. All subsequent events are part of that first round until a new witness is discovered. A witness is discovered when a node creates a new event that can **strongly see** ⅔ of the witnesses in the current round. For example, event w can strongly see event x when w can trace its ancestry through parent relationships that pass through other events that reside on at least ⅔ of the member nodes. When an event is determined to strongly see ⅔ of the witness of the current round, that event is considered the next witness for that node. That new witness is the first event in the next round (r+1) for that node. Each event is assigned a round as the event is added to the hashgraph.

```
procedure divideRounds

for each event x
    r <- max round of parents of x (or 1 if none exist)
    if x can strongly see more than 2n/3 round r witnesses
        x.round <- r+1
    else
      x.round <- r
    x.witness <- (x has no self parent)
                or (x.round > x.selfParent.round)
```

## Decide Fame

The next step is deciding whether a witness is a famous witness or not. A witness is famous if many of the witnesses in the next round can see it, and it is not famous if many can’t. Event A can **see** Event B if Event B is an ancestor of Event A. When deciding the fame of Witness A, we must look at the witnesses of the following round. If the witnesses of the following round can see Witness A, they count as a vote in favor of Witness A’s fame. Likewise, if a witness in the next round can not see Witness A, then that witness’ vote is that Witness A is not famous. In order for Witness A to be considered famous, a future witness must be able to strongly see that at least ⅔ of voting witnesses have voted in favor of Witness A being famous. If ⅔ of voting witnesses have voted that Witness A is not famous, then Witness A will be decided to be not famous.

## Find Order

Now that we have calculated the all witnesses of a round to be famous or not famous, we can determine the order of events that occurred before the famous witness events. This is done by calculating:

1. The **round received** for all events that have yet to be ordered and that have occurred before a round where the fame of all witnesses has been decided. The event’s round received is the first round where all famous witnesses of that round can see (or are descendants of) the event in question.
2. The **timestamp** for each event. This is done by gathering the earliest ancestors of the famous witnesses of the round received that are also descendants of the event in question, and taking the median timestamp of those gathered events.
3. The **ordering of events** first by: round received, consensus timestamp, then signature.
