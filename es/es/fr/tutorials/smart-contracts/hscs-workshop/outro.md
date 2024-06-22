---
description: Outro - HSCS workshop. Learn how to enable custom logic & processing on Hedera through smart contracts.
---

# Outro

## Video

{% embed url="https://www.youtube.com/watch?v=s1xkAl9_IEc" %}
Hedera Smart Contract Service Workshop Part 6/6 | Conclusion
{% endembed %}

## Congrats!

You've now completed this workshop, and you're now able to:

- Write the code for a smart contract using solidity.
- Compile the solidity code into EVM bytecode.
- Deploy the smart contract onto HSCS.
- Interact with the smart contract on HSCS to query and change its state.

If you've completed this entire tutorial, wondering what Trogdor was all about...

- [Watch the Trogdor video](https://youtu.be/90X5NJleYJQ?t=31)
- [Play the Trogdor game](https://old.homestarrunner.com/trogdor-canvas/)

## Where to go from here

There's much more involved in developing smart contracts - here are several things to consider looking into after completing this workshop.

Solidity is a fairly compact language in terms of syntax, and we've covered the essentials already. However there is still quite a bit that was not covered here:

- constructor
- function parameters
- structs
- conditional logic
- iterative logic
- custom function modifiers
- interface
- import
- inheritance
- smart contracts interacting with other smart contracts
- ... and more

The [Solidity language documentation](https://docs.soliditylang.org/en/v0.8.19/) is a great reference to explore on this front

Since a smart contract's code is immutable once deployed, it is extremely important to "get it right" prior to deployment. Therefore testing smart contracts becomes even more important. Hardhat has a built-in testing facility (test runner plus various test utilities and conveniences) that we have not explored here, and has a great [smart contract testing tutorial](https://hardhat.org/tutorial/testing-contracts).

Owing to the same immutable aspect mentioned above, smart contract security is also very important. Look into security audits, static analysis, and dynamic analysis methods.

Finally, verifying smart contracts such that their code and ABI are publicly available on the network explorer is important in building trust in the ecosystem. However, this feature is still a work in progress on Hashscan at the time of writing (July 2023). When this feature lands, we'll have other niceties as well, for example event logs displayed on transactions can show their parsed values, instead of their raw form.
