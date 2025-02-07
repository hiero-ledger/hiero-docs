---
description: Hedera & Web3 Glossary - Comprehensive Guide for Developers
---

# Glossary

This glossary intends to provide a reference for Hedera and general web3 key terms. The purpose is to assist developers, particularly those new to the field or non-specialists, in understanding essential definitions related to various aspects of this technology. It covers basic to complex concepts and essential development tools and is an accessible resource for developers.

## [#](glossary.md#undefined) [A](glossary.md#a) [B](glossary.md#b) [C](glossary.md#c) [D](glossary.md#d) [E](glossary.md#e) [F](glossary.md#f) [G](glossary.md#g) [H](glossary.md#h) [I](glossary.md#i) [J](glossary.md#j) [K](glossary.md#k) [L](glossary.md#l) [M](glossary.md#m) [N](glossary.md#n) [O](glossary.md#o) [P](glossary.md#p) [Q](glossary.md#q) [R](glossary.md#r) [S](glossary.md#s) [T](glossary.md#t) [U](glossary.md#v) [V](glossary.md#v) [W](glossary.md#w) [X](glossary.md#z) [Y](glossary.md#z) [Z](glossary.md#z)

***

## \#

### 51% and 1/3 Attacks

***

A 51% attack, also known as a majority or double-spend attack, is a recognized threat in distributed networks like blockchains. This occurs when a miner or group gains control of more than half the network's mining power, thereby gaining the ability to disrupt transactions. While this is a widely acknowledged issue in the blockchain community, all DLTs are susceptible to 1/3 attacks when an attacker can establish a firewall.

A 1/3 attack is a less discussed threat in distributed networks such as Hedera, which occurs when over 1/3 of the network's nodes are compromised by bad actors using malicious code. This can potentially halt the acceptance of the correct block, making it impossible for others to identify the longest, correct chain and thereby preventing the processing and recording of valid transactions.

📹 For a more comprehensive breakdown of these attacks, check out Dr. Leemon Baird’s talk at Harvard [here](https://youtu.be/IjQkag6VOo0?t=3668).

## A

### Account Alias

***

An account alias is a user-friendly identifier that can be classified as either a public key or an [Ethereum Virtual Machine (EVM) address](../core-concepts/accounts/account-properties.md#evm-address-account-alias). It serves as a reference to the account object, in addition to its account number, and is assigned during the auto account creation process. The purpose of an account alias is to facilitate easier management and recall of accounts, particularly in distributed ledger technology and cryptocurrency contexts where addresses are often complex and difficult to remember. Instead of inputting a long string of characters, users can use simpler and more memorable aliases.

📒 For more information, refer to the [Account Alias section](../core-concepts/accounts/account-properties.md#account-alias) on the [Account Properties](../core-concepts/accounts/account-properties.md) page.

### Account ID

***

A unique identifier associated with an account on a distributed ledger. Each account on the ledger has a unique [account ID](../sdks-and-apis/hedera-api/basic-types/accountid.md), which is used to track and manage all transactions associated with that account.

### Address

***

A unique identifier that represents a user or a destination on the network. It's similar to how an email address works: it's a location where cryptocurrency can be sent. In Hedera, an address could refer to an account ID associated with a specific user or smart contract.

### Algorithm

***

An algorithm is a sequence of instructions that ensures a task or computation is completed. It can also predict results based on a specific input. Distributed ledger technology uses [consensus](glossary.md#consensus) algorithms to help reconcile a customer’s bank account.

### Anti-Money Laundering (AML)

***

A set of regulations and laws to help prevent crimes that produce monetary gain, such as tax evasion, fraud, selling stolen goods, drug and human trafficking, and corruption. Similar laws are being created for the blockchain industry to prevent money laundering activity.

### Application Binary Interface (ABI)

***

The contract Application Binary Interface (ABI) is the standard way to interact with contracts in the Ethereum ecosystem, both from outside the blockchain and for contract-to-contract interaction. Data is encoded according to its type, as described in this specification.

### Asset Tokenization Studio (ATS)

***

Asset Tokenization Studio (ATS) is an open-source solution for tokenizing real-world assets (RWAs) on the Hedera network. It enables issuers to create, manage, and operate digital securities in compliance with global regulatory standards. ATS provides a user-friendly web interface, a customizable Software Development Kit (SDK), and secure, audited smart contracts to streamline the tokenization process. Learn more [here](../open-source-solutions/asset-tokenization-studio-ats/).

### Asynchronous Byzantine Fault Tolerance (aBFT)

***

Asynchronous Byzantine fault tolerance (aBFT) is a property of Byzantine fault tolerant consensus algorithms, which allow for honest nodes of a network to guarantee to agree on the timing and order of a set of transactions fairly and securely. It's considered the highest degree of security in distributed systems.

In the context of Hedera, aBFT means that the network can reach [consensus](glossary.md#consensus) on the order and validity of transactions, even if some nodes aren't trustworthy or become compromised. This is achieved through the hashgraph consensus algorithm, which allows all nodes to agree on the order of transactions in a fair and secure way.

## B

### Balance Files

***

A snapshot of the state of all accounts within the Hedera Network at the time of their creation. These files, which are stored and made accessible by mirror nodes, include details such as account balances and account status information. They are created at regular intervals and can be used to independently verify the state of the network.

### BIP-39

***

The BIP-39 standard utilizes a 2048-word list, from which 12, 15, 18, 21, or 24 words are randomly selected to form the seed phrase. Each word corresponds to a specific number, and the combination of these numbers generates a unique private key.

### Bitcoin (BTC)

***

The first cryptocurrency based on a [Proof of Work (PoW) ](glossary.md#proof-of-work-pow)blockchain. Bitcoin was created in 2009 by Satoshi Nakamoto — a pseudonym for an individual or group whose real identity is unknown — and the concept of cryptocurrency was outlined in a white paper titled “[Bitcoin: A Peer-to-Peer Electronic Cash System.](https://bitcoin.org/bitcoin.pdf)”

### Blob

A large, unstructured data object. In Ethereum, blobs are introduced to enhance data availability for rollups ([EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)), but Hedera currently does not support blobs.

### Block

***

A block is a batch of transactions that are linked together using cryptographic hashes. Each block contains a reference to its parent block, preserving the transaction history in a strictly ordered manner. Blocks are created approximately every 12 seconds to allow consensus on the network and contain a wide range of information.

In the context of Hedera, as per [HIP-415](https://hips.hedera.com/hip/hip-415), a "block" is a record file that contains all Record Stream Objects within a specific timeframe. Key properties of a block include the block number, block hash, and block timestamp. The concept of blocks is introduced to enhance [interoperability](glossary.md#interoperable) with [Ethereum Virtual Machine (EVM)](glossary.md#ethereum-virtual-machine-evm) based tools and platforms.

### Blockchain

***

A type of distributed ledger technology (DLT) that maintains a growing list of records, called blocks, which are linked using cryptography. Each block contains a cryptographic hash of the previous block, a timestamp, and transaction data. The design of a blockchain is inherently resistant to data modification, making it secure and reliable for recording transactions across many computers. Please note that Hedera is a [directed acyclic graph (DAG)](glossary.md#directed-acyclic-graph-dag) and _not_ a blockchain.

### Block Hash

***

A 32-byte prefix of the running hash of the last Record Stream Object from the previous [record file](glossary.md#record-file).

### Block Number

***

A successive number assigned to each record file, incremented by one for each new file. For pre-existing networks, this value is bootstrapped through [mirror nodes](glossary.md#mirror-nodes) and subsequently maintained by service nodes.

### Block Timestamp

***

The [consensus](glossary.md#consensus) [timestamp](glossary.md#timestamp) of the first transaction in the Record file.

### Bytecode

***

Bytecode is the information that Solidity code gets "translated" into. It contains instructions to the computer in binary. Bytecode is generally compact numeric codes, constants, and other pieces of information.

* **Compiled Bytecode**: This is the result of compiling your high-level code (like Solidity for Ethereum smart contracts). This compiled bytecode is what the Ethereum Virtual Machine (EVM) can understand and execute. It includes everything your contract needs to run, including constructor logic, which is the code that runs when you deploy your contract.
* **Deployed Bytecode**: After you deploy your contract to the blockchain, the deployed bytecode is what resides on the blockchain. It's a version of the compiled bytecode but without the constructor logic. The deployed bytecode represents the final, immutable code of your contract as it lives on the blockchain.

## C

### Call Trace

***

Contract call trace information captures the input, output, and gas details of all the nested smart contracts functions executed in a transaction. On Ethereum, these are occasionally called inner transactions but they simply capture snapshots of the message frame consideration the EVM encounters when processing a smart contract execution at each depth for all involved functions.

### Central Bank Digital Currency (CBDC)

***

A digital currency that’s issued by a central, nation-state-backed bank. The US Federal Reserve, the European Central Bank, and the Bank of England are examples of central banks. A nation’s CBDC should be considered the digital equivalent of the nation’s fiat currency.

### Centralized Exchange (CEX)

***

A marketplace for buying, selling, and trading cryptocurrencies. A CEX is owned and operated by a centralized authority that maintains control over accounts and transactions. Some examples of a CEX are Coinbase and Crypto.com.

### Chain

***

Another term for a [blockchain](glossary.md#blockchain).

### Chain ID

***

***

A unique identifier for a specific chain within a network. In networks that support multiple chains or side chains, the chain ID helps distinguish between different chains.

### Client

***

In the context of the [Hedera SDK](../sdks-and-apis/), a client is an object that allows you to interact with the Hedera Network by submitting transactions and queries. It is used to set up the operator account, configure the network, and set default transaction fees and query payments. [Here](../sdks-and-apis/sdks/client.md) is how to build a Hedera Client.

### Cold Wallet

***

Sometimes referred to as a hardware wallet, and is a physical [cryptocurrency](glossary.md#cryptocurrency) device _not_ connected to the internet. A cold wallet is considered to be more secure than a [hot wallet](glossary.md#hot-wallet).

### Compliance

***

In the context of asset tokenization, compliance is adherence to legal, regulatory, and procedural standards that govern the issuance, management, and trading of [digital securities](glossary.md#digital-security). Compliance ensures that tokenized assets, such as [security tokens](glossary.md#security-token), meet the necessary requirements set by financial regulators and adhere to laws such as [Know Your Customer (KYC)](glossary.md#know-your-customer-kyc), [Anti-Money Laundering (AML)](glossary.md#anti-money-laundering-aml), and securities regulations.

### Consensus

***

In the context of distributed ledger technologies (like blockchain and Hedera), consensus refers to the agreement of all nodes in the network on the validity and order of transactions. Hedera uses an algorithm called the [Hashgraph Consensus Algorithm](../core-concepts/hashgraph-consensus-algorithms/). Other consensus mechanisms include [Proof of Work](glossary.md#proof-of-work-pow) and [Proof of Stake](glossary.md#proof-of-stake-pos).

### Contract Account

***

Contract accounts are accounts that are controlled by the code of a [smart contract](glossary.md#smart-contract). They don't have a [private key](glossary.md#private-key); instead, their behavior is determined by the smart contract's code. They can hold Ether and send transactions, but only in response to a transaction they received (i.e., as part of the execution of a smart contract's code).

### Cryptocurrency

***

A type of digital or virtual currency used for payment transactions. Unlike [fiat currencies](glossary.md#fiat-currency), such as the U.S. Dollar, it is not issued or controlled by governments or financial institutions. Instead, it is monitored on a [peer-to-peer](glossary.md#peer-to-peer-p2p) network, such as a [distributed ledger](glossary.md#distributed-ledger).

### Crypto Faucet

***

A platform, application, or website that rewards users in cryptocurrency when they complete certain tasks. Rewards tend to be in tiny increments of cryptocurrency. Rewardable tasks can include browser mining, playing games, watching videos, completing surveys, referring new users, etc.

### Cryptography/Cryptographic

***

Cryptography is the art of disguising data, so that only the intended recipient can read it. Encryption and decryption are the main components of cryptography. In blockchain technology, cryptography offers security by ensuring transactions between nodes are encrypted.

The use of cryptography in conjunction with distributed networks allows for these networks to be both public and secure. Each DLT address generated for a user is paired with a private key, which allows the user to send and receive transactions with that address.

### Crypto Wallet

***

A form of digital wallet designed for [web3](glossary.md#web3). Crypto wallets help you manage permissions with whom you share data, store, and send cryptocurrency, NFTs, and more. Your crypto wallet contains a private key that identifies and assesses the assets that are yours. In this way, you can think of a wallet like [MetaMask](https://metamask.io/) or [HashPack](https://www.hashpack.app/) as a digital identity management system.

Examples: [non-custodial walle](glossary.md#non-custodial-wallet)t, [hardware wallet](glossary.md#cold-wallet), [custodial wallet](glossary.md#custodial-wallet)

### Custodial Wallet

***

A wallet used to digitally store fiat and cryptocurrencies. A trusted third party (custodian) is empowered with their own private key to manage the wallet for the owner of the wallet holder. For example, the custodian can send and receive payments at the owner’s request.

### Custody

***

Custody refers to the secure storage of digital assets by a specialized provider on behalf of clients. It involves safeguarding private keys and providing insurance, audits, and reporting to prove holdings.

## D

### Decentralization

***

A fundamental concept in [distributed ledger technology](glossary.md#distributed-ledger-technology-dlt) that refer to the distribution of power and decision-making across a network or system rather than being controlled by a single entity or authority. Decentralization is a key feature that allows for trustless and transparent transactions without intermediaries.

Hedera, for example, is governed by a decentralized council of diverse organizations. A distributed network of nodes processes transactions on the Hedera Network, and the source code for the Hedera protocol is open review.

### **Decentralized Application (DApp)**

***

Decentralized applications that run on [peer-to-peer](glossary.md#peer-to-peer-p2p) distributed ledger technology networks rather than a centralized server. They are sometimes referred to as web3 applications.

### **Decentralized Autonomous Organization (DAO)**

***

A member-owned group or organization that operates without centralized leadership using distributed ledger technology. Instead of a centralized authority, a DAO is owned and governed by community members ([token](glossary.md#token) holders). A DAO's financial transactions and rules are encoded in smart contracts and recorded on a distributed ledger. The token-holders vote on any changes to the rules or organizational structures, and voting power is typically distributed across users based on the number of tokens they hold. All DAO activity is transparent and fully public.

Examples: [HSuite](https://www.hsuite.finance/), [Developer DAO](https://www.developerdao.com/)

### **Decentralized Exchange (DEX)**

***

A marketplace for users to buy, sell, and trade cryptocurrencies without a centralized intermediary to provide liquidity and verify transactions. Instead, other users provide liquidity, and transactions are verified through the distributed ledger.

Examples: [SaucerSwap](https://www.saucerswap.finance/), [HeliSwap](https://www.heliswap.io/), [Pangolin](https://app.pangolin.exchange/)

### **Decentralized Finance (DeFi)**

***

A peer-to-peer financial technology built on distributed ledgers without intermediaries. DeFi services cover lending, borrowing, trading, derivatives, insurance, and prediction markets. Services are decentralized and not controlled by a single authority, such as those from traditional banking systems.

Examples: [Stader Labs](https://www.staderlabs.com/hedera/), [hashport](https://www.hashport.network/)

### **Decentralized Identifiers (DIDs)**

***

[W3C Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) are a new type of identifier that enables verifiable, decentralized digital identity. A DID refers to any subject (e.g., a person, organization, thing, data model, abstract entity, etc.) as determined by the controller of the DID. DIDs are URIs that associate a DID subject with a DID document allowing trustable interactions associated with that subject.

### Digital Asset

***

A digital asset is a broad term that encompasses any asset that exists in a digital form. In the context of [asset tokenization](../open-source-solutions/asset-tokenization-studio-ats/#what-is-asset-tokenization), digital assets can include a variety of representations on a blockchain or other distributed ledger technology, such as [digital securities](glossary.md#digital-security), [digital tokens](glossary.md#digital-token), and [security tokens](glossary.md#security-token). Digital assets can represent anything from ownership in [real-world assets (RWAs) ](glossary.md#real-world-asset-rwa)to access rights within a blockchain ecosystem.

### Digital Security

***

In asset tokenization, a digital security is a digital form of a traditional security, like a stock, bond, or real estate asset. It is a subset of [digital assets](glossary.md#digital-asset) and must comply with securities regulations. When issued on a blockchain or other distributed ledger technologies (DLTs), digital securities are considered [security tokens](glossary.md#security-token). They represent tangible assets and include embedded ownership rights, such as voting and dividend payments. Unlike general [digital tokens](glossary.md#digital-token), digital securities are directly linked to financial assets and are subject to strict regulatory controls.

### Digital Token

***

A digital token is a broader term that refers to any digital representation of value, asset, or access rights on a blockchain or other forms of distributed ledger technology (DLT). Unlike [digital securities](glossary.md#digital-security), digital tokens are not necessarily tied to an investment contract or ownership of a [real-world asset](glossary.md#real-world-asset-rwa). They can serve various purposes, such as providing access to a platform, enabling transactions, or representing ownership of non-financial assets. Digital tokens can be classified into different categories, including utility tokens, which grant access to specific features or services within a blockchain or other DLT ecosystems. While some digital tokens may be subject to regulatory oversight, many are not, depending on their function and usage.

### Directed Acyclic Graph (DAG)

***

A structure used for data organization and representation of associations using circles and lines. DAGs are good for mapping an efficient process and visualizing relationship flows, such as family trees, and are viable replacements for blockchains due to their speed and data-storage capabilities. Hedera is a DAG and _not_ a blockchain.

### Distributed Denial-of-Service (DDoS)

***

A Distributed Denial-of-Service (DDoS) attack is a malicious attempt to disrupt the normal operations of a network, service, or website by overwhelming it with a flood of internet traffic from multiple sources.

In the context of Hedera, a DDoS attack would involve an attempt to overwhelm the network with a flood of transactions or requests, aiming to disrupt its normal operation. However, due to Hedera's unique consensus mechanism and its use of [asynchronous Byzantine Fault Tolerance (aBFT)](glossary.md#asynchronous-byzantine-fault-tolerance-abft), it is designed to be resilient to such attacks.

### Distributed Ledger

***

A distributed ledger is a database shared by multiple participants in which each participant maintains and updates a synchronized copy of the data. Distributed ledgers allow members to securely verify, execute, and record their own transactions without relying on an intermediary, such as a bank, broker, or auditor. A distributed ledger can also be called a DLT, the acronym for [Distributed Ledger Technology](glossary.md#distributed-ledger-technology-dlt).

### Distributed Ledger Technology (DLT)

***

A technology that allows the existence of distributed ledgers, such as blockchains and [DAGs](glossary.md#directed-acyclic-graph-dag), that use distributed ledgers stored on separate, connected devices in a network to ensure data accuracy and security. Unlike traditional databases, [distributed ledgers](glossary.md#distributed-ledger) have no central [data store](https://www.techtarget.com/whatis/definition/data-store) or administration functionality.

### Dividends

***

A portion of a company's earnings distributed to shareholders. In asset tokenization, [security tokens ](glossary.md#security-token)can include embedded rights to dividends, enabling digital securites holders to receive payouts automatically via [smart contracts](glossary.md#smart-contract).

### Double Spend Problem

***

Spending the same coins more than once, especially at the same time. This is one of the primary “attacks” distributed ledger networks have been designed to resist.

## E

### Elliptic Curve Digital Signature Algorithm (ECDSA)

***

A cryptographic algorithm for digital signatures is a crucial component in distributed ledger technology. It is a variant of the Digital Signature Algorithm (DSA) that operates on elliptic curve cryptography. Elliptic curve cryptography, and thus ECDSA, offers a high level of security with relatively small keys, which makes it more efficient than some other cryptographic systems. As a result, ECDSA is widely used in many systems that require secure digital signatures, including Bitcoin, Ethereum, and other distributed ledger networks.

### ECDSA secp256k1

***

A cryptographic algorithm used for data integrity and widely used in cryptocurrencies. In this term, "[secp256k1](../sdks-and-apis/sdks/keys/generate-a-new-key-pair.md#ecdsa-secp256k1_)" refers to the specific parameters of the elliptic curve used, making it efficient for computations. Hedera supports this signature scheme for generating cryptographic keys and signing transactions.

### Ed25519

***

In the context of Hedera, Ed25519 is one of the signature schemes supported for the creation and verification of digital signatures. It's often used for signing transactions, as it provides a strong level of security while still being efficient to compute.

### **EIP (Ethereum Improvement Proposal)**

***

A standard for proposing changes and new features to the Ethereum protocol. Some EIPs are relevant for Hedera's EVM compatibility and can be referenced [here](https://eips.ethereum.org/).

### ERC-1363

***

The [ERC-1363](https://erc1363.org/) payable token standard extends ERC-20 by enabling smart contracts to execute actions automatically after a token transfer or approval, all within a single transaction. This standard provides a streamlined API for seamless token interactions with smart contracts.

### ERC-20

***

A technical standard for fungible tokens created using the Ethereum blockchain. A fungible token is interchangeable with another token—where the well-known non-fungible tokens (NFTs) are not interchangeable. ERC-20 allows developers to create smart-contract-enabled tokens that can be used with other products and services. These tokens represent an asset, right, ownership, access, cryptocurrency, or anything else that is not unique in and of itself but can be transferred. Check out the details of the ERC-20 standards [here](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/). Hedera supports the creation and management of custom tokens through the [Hedera Token Service (HTS)](glossary.md#hedera-token-service-hts).

### ERC-3643

***

The ERC-3643 protocol is an open-source suite of smart contracts designed for the issuance, management, and transfer of permissioned tokens. It incorporates a decentralized identity framework to ensure that only users meeting pre-defined conditions can hold tokens, even on permissionless blockchains. This standard was formalized in the Ethereum Improvement Proposal (EIP) [ERC-3643](https://eips.ethereum.org/EIPS/eip-3643).

### ERC-721

***

A type of token standard — a template or format that other developers agree to follow. Following the same standards makes writing code easier, more predictable, and reusable. Each ERC-721 token is unique and not interchangeable with any other token - hence the term non-fungible. This unique characteristic allows ERC-721 tokens to represent ownership of unique items like a particular piece of real estate or a specific piece of art. Check out the details of the ERC-721 standard [here](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/). Hedera supports the creation and management of both [fungible](glossary.md#fungible-token) and [non-fungible tokens](glossary.md#non-fungible-token-nft) using the [ERC-721 token standard](https://eips.ethereum.org/EIPS/eip-721).

### Ether (ETH)

***

The [native cryptocurrency](glossary.md#native-cryptocurrency) for Ethereum.

### Ethereum

***

An open-source, [decentralized](glossary.md#decentralization) blockchain platform with [smart contract](glossary.md#smart-contract) functionality. Ether (ETH) is the native cryptocurrency of Ethereum. Developers can use Ethereum or an [Ethereum Virtual Machine (EVM)](glossary.md#ethereum-virtual-machine-evm) to create and run [decentralized applications](glossary.md#decentralized-application-dapp) containing smart contract functionality, as well as issue new crypto assets, known as [tokens](glossary.md#token).

### Ethereum Request for Comments (ERC)

***

A technical standard used on the Ethereum blockchain to propose improvements, define protocols, or establish guidelines for functionality within Ethereum’s ecosystem. Each ERC proposal is publicly accessible and open for feedback from the developer community. If widely adopted, these proposals become formal standards that enhance the interoperability, usability, and security of Ethereum-based applications and tokens.[ ERC-20](glossary.md#erc-20) and [ERC-721](glossary.md#erc-721) are popular examples, defining standards for fungible and non-fungible tokens, respectively, and are instrumental in providing a common interface for token-related smart contracts.

### Ethereum Virtual Machine (EVM)

***

The runtime environment in Ethereum, where smart contracts are executed. It is a [Turing-complete](glossary.md#turing-complete) [virtual machine](glossary.md#virtual-machine) that executes scripts used to implement certain operations on the Ethereum blockchain. The EVM is contained within the client software needed to run a node on Ethereum, and it manages the state of the blockchain and enables smart contract functionality.

### Event Files

***

Event files record the history of transactions that have occurred on the Hedera Network. They contain the details of all the transactions and the order in which they were processed. The files are created on each node in the network and are then shared with [mirror nodes](glossary.md#mirror-nodes), which are nodes that maintain a copy of the network's history but do not participate in [consensus](glossary.md#consensus).

### Externally Owned Account (EOA)

***

An account controlled by [private keys](glossary.md#private-key) that can send transactions to other accounts or deploy smart contracts onto the network. An EOA is created by generating a public-private key pair. Transactions sent from an EOA are initiated by an external actor who holds the private key. Some popular [web3](glossary.md#web3) wallets (e.g., MetaMask, HashPack) are EOAs.

## F

### Fair Order

***

The principle that all transactions on a distributed ledger should be treated equally, without preferential treatment and describes the consistency of records for the transaction queue of a ledger. Transactions in fair order are recorded on a ledger consistent with the order in which they are transmitted.

### Fallback Fee

***

A "fallback fee" in the context of Hedera and smart contracts is a type of custom fee that is charged to the recipient of a token transfer, rather than the sender. This can lead to vulnerabilities in smart contracts if they unknowingly accept a token with a high fallback fee, resulting in a loss of funds. Discussions on mitigation strategies, such as sender-pays models or limits on custom fees, are ongoing. [Here's](https://hedera.com/blog/nft-royalty-fees-hedera-hashgraph) a comprehensive blog post covering fallback fees.

### Fee Collector Account

***

A fee collector account is an account designated to receive transaction fees and [custom token fees](../sdks-and-apis/sdks/token-service/custom-token-fees.md) on the Hedera network. The account collects the fees users pay for executing transactions and queries on the network.

### Fiat Currency

***

A term widely used across the financial industry (and even beyond) to refer to government-backed national currencies such as the U.S. dollar or the British pound. Even so, the concept of fiat currency is deeply intertwined with multiple aspects of distributed ledger technology and cryptocurrency usage. One of those aspects concerns how individuals and organizations onboard themselves into the worlds of DLTs and [cryptocurrency](glossary.md#cryptocurrency).

### Finality

***

The assurance or guarantee that completed transactions or blocks can't be reversed, revoked, canceled, or changed in any way. The latency level of a ledger will ultimately affect the chain's finality rate.

### Fixed Fee

***

A fixed fee refers to a predetermined amount of tokens or HBAR that is transferred to a specified fee collection account each time a token transfer occurs. Fixed fees are one of the types of custom fees you can define when creating a token on Hedera.

### Fork

***

A fork is an event in which a [blockchain](glossary.md#blockchain) splits into two separate chains. A fork occurs when software updates to its functionality are introduced, but not all participants (miners, developers) agree on them.

### Fractional Fee

***

A fractional fee is a type of custom fee that can be set when creating a token on the Hedera network. Fractional fees are calculated as a fraction of the total value of the tokens that are being transferred in a transaction.

### Fractional Ownership

***

The concept of dividing ownership of a real-world asset into smaller fractions allowing multiple investors to own portions of the asset. In [asset tokenization](../open-source-solutions/asset-tokenization-studio-ats/#what-is-asset-tokenization), fractional ownership is facilitated by issuing security tokens that represent fractional shares of an asset.

### Fungible Token

***

A token or digital asset that is equal in value and is mutually interchangeable with assets of the same type. For example, a [Bitcoin](glossary.md#bitcoin-btc) is always valued the same as another Bitcoin.

## G

### Gas Fee

***

The cost of performing a transaction or smart contract operation.

### Gas Limit

***

The maximum amount of gas a sender is willing to spend in a transaction.

### Genesis Block

***

The first block of data that is processed and validated to form a new blockchain, often referred to as block 0 or block 1.

### Gossip about Gossip

***

The history of how events are related to each other through their parent hashes in a hashgraph. It expresses itself as a [directed acyclic graph](glossary.md#directed-acyclic-graph-dag), a graph of hashes, or a hashgraph. The hashgraph records the history of how members communicated and grow directionally over time as more gossip syncs take place and events are created. All members keep a local copy of the hashgraph, which updates as members sync with one another.

### Gossip Protocol

***

A protocol for communication between network nodes in a distributed, [peer-to-peer](glossary.md#peer-to-peer-p2p) network and sometimes used in distributed systems such as ledgers, databases, and file systems because they are secure, reliable, fault-tolerant, and efficient & decentralized.

Gossip protocol is based on the construct of "gossiping", or spreading information from one node to another. Every few seconds, each node sends a message to a random node in the network about itself and other nodes, then that message moves from node to node until its communication reaches across the entire network.

Several [DLTs](glossary.md#distributed-ledger-technology-dlt), such as [blockchains](glossary.md#blockchain), utilize the Gossip protocol. It is used in Hedera (along with [Gossip about Gossip](glossary.md#gossip-about-gossip)) for nodes to distribute transaction-related data. The Bitcoin network uses the Gossip protocol to broadcast block-mining solutions. It is also used in the Hyperledger Fabric network and Ethereum, among others.

### Governance

***

The system of proposing, managing, and implementing changes to distributed ledgers, including management of transaction fee allocation, user interface changes, developer recruitment, licensing, product roadmaps, development fund distribution, and other policies.

<details>

<summary>There are two types of DLT governance: on-chain and off-chain</summary>

_**On-chain governance:** stakeholders can vote on changes via rules encoded into the blockchain protocol. Stakeholders typically hold governance tokens, including miners, developers, and investors. Developers propose changes through code updates, and each node votes to accept or reject the proposed changes. Voting mechanisms for on-chain governance vary but likely include smart contract functionality._ [_Decentralized Autonomous Organizations (DAOs)_](glossary.md#decentralized-autonomous-organization-dao) _utilize on-chain governance to run operations without a central authority._

_**Off-chain governance:** changes are made in online forums, social media, conferences, email groups, and other public spaces. Off-chain governance is successful when all stakeholders agree and make all updates and implementations in unison. If consensus cannot be reached, the network can split, or fork, into two chains running different versions of the software, and the chain with the most transactional hashing power is considered to be the successor to the original chain._

</details>

### Governance Token

***

A governance token is a utility token that can help democratize decision-making related to managing a public ledger, decentralized apps, and other decentralized protocols. One governance token represents one vote in any decision-making process where the outcome is determined by the option that registers the most votes.

## H

### Hardhat

***

A development environment for Ethereum software. It consists of different components for editing, compiling, debugging, and deploying smart contracts and dApps, all working together to create a complete development environment. Learn more about it [here](https://hardhat.org/).

### Hash

***

A mathematical function in which an input of a string of information (numbers, letters, media files) is output into a fixed size. Hash functions are used for data integrity, generating unique identifiers, digital signature creation, and consensus mechanisms in DLTs.

### Hashgraph

***

A Hashgraph, in the context of Hedera, is a way to chart events created by members in order to achieve [consensus](glossary.md#consensus) order and consensus timestamps, where an event is a circle: a container for a blockchain transaction, and members are full nodes.

### **Hashrate**

***

A metric of cryptocurrency computing power determined by measuring how many hashes are generated by all the miners who are simultaneously trying to solve the current block or any given block on a mined distributed ledger such as Bitcoin.

### HashScan

***

[HashScan](https://hashscan.io/) is a ledger explorer and analytics platform for the Hedera network. It allows users to easily search for transactions, as well as provide information about each transaction.

### HBAR

***

The [native cryptocurrency](glossary.md#native-cryptocurrency) of the Hedera Network.

### Hedera Consensus Service (HCS)

***

This service enables developers to create a verifiable timestamp and order of events for any application. In other words, it allows any application to send messages to the Hedera Network and receive consensus timestamps and [fair order](glossary.md#fair-order). This can be useful for various applications, including supply chain tracking, fair order for marketplaces, and distributed systems coordination.

### Hedera File Service

***

The [Hedera File Service](../sdks-and-apis/deprecated/sdks/readme-1-1/) provides a decentralized file storage platform that allows developers to securely store and access files on a distributed network of computers using hash as a file identifier.

### Hedera Improvement Proposal (HIP)

***

A Hedera Improvement Proposal (HIP) is a proposal that can range from core protocol changes to the applications, frameworks, and protocols built on the Hedera public network and used by the community. HIPs are reviewed and evaluated by the Hedera Council, core developers, and editors.

### Hedera Schedule Service (HSS)

***

The Hedera Schedule Service system contract exposes the necessary scheduled transaction features to smart contracts on the Hedera network, enabling the creation, management, and execution of scheduled transactions. Scheduled transactions are actions that require approval from multiple parties (multi-sig coordination) and are executed on the network only after all required signatures are collected and verified. If all required signatures are not collected before the expiration, the transaction expires and is removed from the network.

### Hedera Smart Contract Service (HSCS)

***

The Hedera Smart Contract Service (HSCS) is a service provided by the Hedera network that integrates the features of Hedera's native entity functionality with a highly optimized and performant Ethereum Virtual Machine (EVM). It allows developers to deploy and interact with smart contracts on the Hedera network, offering high throughput, fast finality, predictable and affordable fees, and fair transaction ordering.

### Hedera Token Service (HTS)

***

This service provides the ability to issue and manage tokens on the Hedera Network. With the Hedera Token Service, users can define, mint, burn, and configure tokens without deploying a smart contract. It supports both [fungible tokens](glossary.md#fungible-token) (like [ERC-20](glossary.md#erc-20) [tokens](glossary.md#token)) and [non-fungible tokens](glossary.md#non-fungible-token-nft) (like [ERC-721](glossary.md#erc-721) tokens). The HTS is designed to be fast, secure, and efficient, with low fees and finality of transactions.

### Hot Wallet

***

A [cryptocurrency wallet](glossary.md#cryptocurrency-wallet) that is _always_ connected to the internet and can be either a mobile application, web application, or browser extension. As they are always connected to the Internet, hot wallets are not as secure as [cold wallets](glossary.md#cold-wallet), which are only connected to the Internet during transactions.

### Hyperledger Besu EVM

***

An open-source [Ethereum](glossary.md#ethereum) client developed under the Hyperledger project and a virtual machine that replaced the EthereumJ virtual machine in the [Hedera Services release 0.19](https://github.com/hashgraph/hedera-services/releases/tag/v0.19.1) as a result of [HIP-26](https://hips.hedera.com/hip/hip-26). This migration enables Hedera to maintain parity with Ethereum Mainnet evolutions, such as the EVM container formats, new opcodes, and precompiled contracts. The Besu integration is configured to use the [“London” hard fork](https://ethereum.org/en/history/#london) of Ethereum Mainnet.

## I

### Immutability

***

The property of a distributed ledger technology, such as [blockchain](glossary.md#blockchain) that prevents data from being altered or tampered with. Once cryptographically secured and stored, the ledger record cannot be altered. Because each hash on each block in a chain is unique, data cannot be tampered with after it is logged. This creates an ultra-high-integrity record of information about the whole blockchain.

### Initial Coin Offering (ICO)

***

Initial Coin Offering, or ICO (also stands for Initial Cryptocurrency Offering), is a funding opportunity for a new cryptocurrency coin, application, or service. Unlike IEOs and IDOs, tokens are purchased from a project website, not an online exchange platform.

### Interoperable

***

Interoperability on distributed ledgers means the ability to connect and communicate (exchange & comprehend data) with other distributed ledger networks, creating a pathway to the easy exchange of assets. (In a broader sense, interoperability refers to the power of communication between systems.) Interoperability protocols allow a distributed ledger to read and write messages to other networks, easily exchanging data.

### InterPlanetary File System (IPFS)

***

InterPlanetary File System (IPFS) is a hypermedia protocol and [peer-to-peer](https://www.notion.so/Peer-to-Peer-0ec8c536bb844c9a8362fae098d7079f) network for storing and sharing data files. Unlike HTTP, an IPFS network does not require a host server. Instead, IPFS is [decentralized](https://www.notion.so/Decentralization-e9b49b8b3c3c43eb88cf7a1b43ae06d7) and stores the information on hundreds of thousands of [nodes](https://www.notion.so/Node-1636ba2a9252475b93b3d291a835980f) (servers) worldwide. _IPFS was created by Juan Benet of_ [_Protocol Labs_](https://protocol.ai/) _and launched in February 2015._

### ISO 20022

***

ISO 20022 is a global standards scheme for financial industry messaging which includes methodology, process, and repository standards. It covers financial information transferred between financial institutions that includes payment transactions, securities trading and settlement information, credit and debit card transactions, and other financial information.

Currently, seven DLTs adhere to ISO 20022 standards: Algorand (ALGO), [Hedera (HBAR)](glossary.md#hbar), IOTA (MIOTA), Quant (QNT), Ripple (XRP), Stellar (XLM), XDC Network (XDC).

## J

### JSON-RPC Relay

***

An open-source project implementing the Ethereum JSON-RPC standard. The [Hedera JSON-RPC relay](https://github.com/hashgraph/hedera-json-rpc-relay) allows developers to interact with Hedera nodes using familiar Ethereum tools. This allows Ethereum developers to deploy, query, and execute contracts on the Hedera Network as they would on Ethereum.

## K

### Know Your Customer (KYC)

***

A standard for the investment industry in which an investor’s identity can be verified and evaluated for business relationships. Players in the blockchain industry must heed to KYC standards because cryptocurrency exchanges require them for [Anti-money laundering (AML)](glossary.md#anti-money-laundering-aml) compliance.

## L

### Liquidity

***

The ease of buying or selling an asset in the market without impacting its price is known as liquidity. [Tokenization](glossary.md#tokenization) seeks to enhance liquidity by enabling 24/7 trading of [fractionalized](glossary.md#fractional-ownership) [digital securities](glossary.md#digital-security) on digital exchanges.

### Liquidity Pool

***

A collection of shared cryptocurrency coins/tokens locked under a smart contract, ensuring liquidity that allows participants to trade easily. They are used for trading in [decentralized exchanges (DEX)](glossary.md#decentralized-exchange-dex), and for providing liquidity to [decentralized financed (DeFi) protocols](glossary.md#decentralized-finance-defi), which makes them an important part of yield farming.

## M

### Mainnet

***

Mainnet (main network) is a distributed ledger network that is developed, tested, and fully deployed for public use. In contrast, a [testnet](glossary.md#testnet) (test network) is a distributed ledger network used by developers to test [smart contracts](glossary.md#smart-contract) and [dApps](glossary.md#decentralized-application-dapp) before they are deployed to the live mainnet.

### Mempool

***

An Unordered Transaction Pool or Memory Pool, also referred to as mempool (a combination of the words memory and pool), is a list of cryptocurrency transactions that have not yet been processed by a node. Once a transaction is added to a block, it disappears from the mempool. The larger the mempool size, the more congestion in network traffic, and the longer the confirmation time, which results in higher transaction fees. Hedera uses aBFT consensus (aBFT) algorithms, and the most important aspect of aBFT networks is that there is no memory pool of transactions. Learn more about it [here](https://hedera.com/blog/unblocking-and-unloading-the-speed-secrets-behind-hedera-smart-contracts-2-0).

### Merkle Root

***

Merkle root is a mathematical method of confirming [Merkle tree](glossary.md#merkle-tree) hashes. It is the root hash of all other hashes in the Merkle tree.

### Merkle Tree

***

A Merkle tree, a hash tree, is a data structure used in distributed ledger technology for efficient data verification and transfer on [peer-to-peer](glossary.md#peer-to-peer-p2p) networks. It consists of leaf nodes (hashes of crypto transactions in a block), non-leaf nodes (hashes of leaf nodes), and the [Merkle root](glossary.md#merkle-root) (the final hash). Merkle Trees enhance data integrity and enable Simplified Payment Verification (SPV), allowing wallets to verify transactions without needing the entire distributed ledger network.

### MetaMask

***

A [non-custodial wallet](glossary.md#non-custodial-wallet) used to interact with [Ethereum](glossary.md#ethereum) and other EVM-compatible networks. It allows users to access their wallets through a browser extension or mobile app, which can then be used to interact with decentralized applications. Learn more about it [here](https://metamask.io/).

### Mint

***

The creation of a token on a distributed network. Most often used in the context of [non-fungible tokens (NFTs)](glossary.md#non-fungible-token-nft).

### Mirror Nodes

***

A mirror node is used to store and cost-effectively query historical data from the public ledger while minimizing the use of Hedera Network resources. Mirror nodes support the Hedera Network services currently available and can be used to retrieve [transactions](glossary.md#transaction) and records, [event files](glossary.md#event-files), and [balance files](glossary.md#balance-files).

### Multisig

***

The requirement for a transaction to have two or more signatures before it can be executed, often used in DAOs. Multisig provides more security than single-signature transactions.

## N

### Native Cryptocurrency

***

Native cryptocurrency is the digital currency inherent to a DLT such as a blockchain. For some examples, Bitcoin (BTC) is the native cryptocurrency for the Bitcoin blockchain, or Hedera (HBAR) for Hedera.

### Network Explorer

***

A network explorer, sometimes called a blockchain or ledger explorer, is an online tool or application that allows users to browse and search the blocks, transactions, addresses, and other data on a distributed network.

### Node

***

In [web3](glossary.md#web3), a node is any participating computer in a [peer-to-peer](glossary.md#peer-to-peer-p2p) network that is propagating the verification of transactions, creating blocks, and/or maintaining the distributed network.

### Non-Custodial Wallet

***

A non-custodial wallet is a [decentralized](glossary.md#decentralization) wallet where the user has complete control over their private keys, allowing them to store and manage their digital assets. Ledger, Exodus, HashPack, and MetaMask are examples of popular non-custodial wallets.

### Non-Fungible Token (NFT)

***

A unique digital asset with ownership rights that are stored on a distributed network. An NFT can be a one-of-a-kind image, video, composed music, game asset, medical record, event ticket, domain, or other creative media that is tokenized, therefore, can be bought, sold, or traded on a distributed ledger using various cryptocurrencies.

### Nonce

***

A [cryptographic](glossary.md#cryptography) nonce is an arbitrary, single-use, whole, binary number used for communication in security functions. The acronym “nonce” is an abbreviation of "number only used once." A basic nonce in [Bitcoin](glossary.md#bitcoin) is 32-bit (4-byte). A strong nonce has at least 128 bits of entropy. Solving the hash on a block is how a miner finds the nonce.

Uses for a nonce: authentication, initialization vectors, hashing, indexing, smart contracts, and block validation.

Within a distributed ledger, a nonce is proof of work for an encrypted (or hashed) block. Transaction verification and other data contained within that block can then be verified via the added nonce.

### Non-View Functions

Smart contract functions that modify the blockchain's state, such as updating variables, transferring tokens, or executing approvals. These functions require a transaction, consume gas fees, and result in permanent state changes on successful execution.

## O

### Off-Chain

***

Off-chain refers to transactions that occur _off_ of the distributed ledger. These transactions can be [peer-to-peer](glossary.md#peer-to-peer-p2p) or through a third-party intermediary, and they are not subject to the DLT's protocols. As a result, they can be faster and cheaper than on-chain transactions but might not offer the same level of security or decentralization. Off-chain transactions can also be private, as they are not necessarily visible to all nodes in the network.

### Off-Chain Storage

***

Off-chain storage is a provided service for storing data that cannot be stored on a ledger and is useful for off-chain data, such as real-world data, data that is required to be changed or deleted, and data that is too large to store on-chain efficiently.

### On-Chain

***

On-chain refers to transactions that occur _on_ the distributed ledger, and as a result, they are fully subject to the DLT's protocols. This includes the consensus protocol, and therefore these transactions are public, immutable, and auditable. The ledger secures on-chain transactions and are visible to all nodes in the network.

### **Opcodes**

***

Short for "operation code," an opcode is a single instruction that the Ethereum Virtual Machine (EVM) can execute. Each opcode performs a specific operation, such as arithmetic calculations, data storage, or control flow management. Opcodes are the low-level building blocks of EVM smart contracts written in Solidity or other EVM-compatible languages.

### Oracles

***

In distributed ledger technology, oracles are third-party services that connect distributed ledger ([on-chain](glossary.md#on-chain)) data to data coming from external ([off-chain](glossary.md#off-chain)) systems. With this function, oracle technologies can provide the off-chain data needed to meet the conditions of a [smart contract](glossary.md#smart-contract).

### Ordinals

***

Bitcoin Ordinals is a protocol that allows individual satoshis (SATs) in a Bitcoin blockchain to be assigned a unique identifier and transacted with extra data attached. This protocol became the foundation for a unique collaborative venture between [Hgraph](https://www.hgraph.io/), a developer tooling and Web3 consulting entity, and [Turtle Moon](https://twitter.com/TurtleMoonCC), a pioneer in NFT services. Together, they orchestrated the first token-gated vote on Bitcoin using the [H4NGRY](https://twitter.com/H444NGRY)'s Ordinals NFT collection titled "[Kid Pepes](https://magiceden.io/ordinals/marketplace/kidpepes)." Every vote cast during this initiative was instantaneously recorded on Hedera, serving as the trust layer of the voting system, with the final vote outcomes being inscribed back onto Bitcoin.

The integration showcases a groundbreaking application of distributed ledger technology by initiating the voting process on Bitcoin, where ballot details including start and end times, a unique identifier, and a Hedera Consensus Service topic ID are inscribed. This process transitioned to Hedera for recording votes, offering a transparent, tamper-proof voting system. The final ballot results, which are recorded back on Bitcoin, can be validated against the original data points, embodying a robust, transparent, and innovative use of blockchain technology for democratic processes. Read more about this integration [here](https://medium.com/@hgraph_io/hgraph-powers-the-first-token-gated-vote-on-ordinals-42829fd50d5b).

## P

### Peer-to-Peer (P2P)

***

A decentralized network interaction model where individual nodes ("peers") connect directly with each other instead of through centralized servers or authorities.

In a peer-to-peer network, each node can act both as a client and as a server. This contrasts with the traditional client-server model, where client nodes request resources or services, and server nodes fulfill those requests.

### Permissioned

***

Permissioned DLTs are private distributed ledgers that require user access from an entity (or entities) that control the network.

### Permissionless

***

Permissionless means a system or property accessible by anyone without permission from a central authority. Permissionless ledgers are public distributed ledgers, including public blockchains, that allow anyone to join at any time, to read, write, or participate in the ledger network. There are no entities that regulate or control the network or its users.

### PostgreSQL

***

A free and open-source relational database management system emphasizing extensibility and SQL compliance. [Mirror nodes](glossary.md#mirror-nodes) use a PostgreSQL database to store the transaction and event data organized in a structure that mirrors the Hedera Network.

### Precompile

***

In Ethereum and other smart contract platforms, precompile (or precompiled contract) is a special kind of smart contract with a fixed address that is implemented in the blockchain client itself rather than in the Ethereum Virtual Machine (EVM).

The main advantage of precompiles is that they can be executed more efficiently than regular smart contracts. This is because they are written in low-level code and don't need to be interpreted by the EVM. This makes them useful for computationally intensive operations, such as cryptographic functions or mathematical operations.

For example, Ethereum includes precompiled contracts for operations like elliptic curve multiplication and pairing, as well as for hash functions like SHA-256.

### Private Key

***

A private key is an alphanumeric string of data that corresponds to a single specific account in a wallet. Private keys can be thought of as a password that grants access to, and control over, that specific crypto account. Never reveal your private key to anyone, as [whoever controls the private key controls the account](https://youtu.be/dnC5mFaIW3Q). If you lose your private key, you lose access to that account.

### Proof-of-Reserves (PoR)

***

A Proof of Reserves (PoR) is an independent audit conducted by a third party that seeks to ensure that a custodian holds the assets it claims to on behalf of its clients.

### Proof-of-Stake (PoS)

***

A consensus mechanism in which an individual runs distributed ledger software (a “consensus node”) responsible for validating transactions, blocks, and the state of the network.

The individual must first “stake” an amount of cryptocurrency, such as ether, in a smart contract; this allows them to participate in consensus-building. If the individual’s consensus node functions within specifications, they are rewarded, usually with cryptocurrency. If, on the other hand, the node functions poorly or maliciously, the staked tokens can be “slashed” or taken away.

PoS requires a negligible amount of computing power compared to Proof of Work consensus.

### Proof-of-Work (PoW)

***

Proof of Work (PoW) is a consensus mechanism for confirming transactions and adding new blocks to blockchains.

Here’s how it works: To create a new block, a cryptocurrency miner competes with other miners to solve a cryptographic puzzle. This is done by generating a cryptographic hash that needs to match the target hash for the current block. The first miner that correctly generates the hash can add the block and once the hash solution is verified, receives block rewards in the form of cryptocurrency.

When the puzzle gets solved and verified, PoW consensus is established, as the solution itself "proves" that "work" was done to solve it.

### Proposed Token ID

***

A proposed Token ID in the context of Hedera refers to the unique identifier that is allocated by the Hedera network when a token is first issued. This Token ID is made up of three numerals separated by a dot, for example, `0.0.123344`, and it cannot change once issued. It is equivalent to a [smart contract address](../core-concepts/smart-contracts/smart-contract-addresses.md) for an [ERC token](glossary.md#erc-20).

Just like ERC tokens, there is no enforcement of the uniqueness of a token’s name or symbol on the Hedera network. This means that two tokens with the same name and/or symbol may exist on the network, but they will have different Token IDs.\
In the context of the [**Guardian project**](https://docs.hedera.com/guardian), an issue was reported where the Token IDs appeared in UUID format instead of the Hedera Token ID value. This issue was reported on a local instance of the Guardian version `2.10.0`. Learn more from the [Hedera Blog](https://hedera.com/blog/mapping-hedera-token-service-standards-to-erc20-erc721-erc1155) and the [GitHub Issue](https://github.com/hashgraph/guardian/issues/1913).

### Protocol

***

In the blockchain industry, the word “protocol” refers to any distributed ledger-based service, including the blockchains themselves and any services or applications that run on them, that can programmatically receive and respond to specially formatted requests.

For example, if a public ledger can programmatically (via software as opposed to a user interface for humans) receive a request (and respond to it accordingly) to transfer cryptocurrency from one ledger account to another (which most chains can do), then it’s a protocol.

### **Proto-Danksharding**

***

A simplified version of dank sharding aimed at improving data availability without the full complexity of sharding.

### Proxy Contract

***

A smart contract that acts as an intermediary layer wrapped around each [security token](glossary.md#security-token), directing operations to the main logic contract and allowing for upgrades and modifications. In the context of asset tokenization, proxy contracts are used to update [digital securities](glossary.md#digital-security) without disrupting their operation. If the rules or functions of a token need to change (e.g., due to new regulations), the proxy can be updated via the [resolver contract](glossary.md#resolver-contract) to point to a new logic contract. This allows all assets to be updated without interrupting their operation. Learn more in the context of asset tokenzation [here](../open-source-solutions/asset-tokenization-studio-ats/#in-depth-architecture-of-ats).

### Public Key

***

Cryptography and the modern software tools built with it often center around a key pair: public and private. You can derive a public key from a private key, but you cannot derive a private key from a public key.

In messaging, the public key is obtained and used by anyone to encrypt messages before they are sent to a known recipient with a matching private key for decryption.

By pairing a public key with a private key, transactions can be sent in public with no fear of someone “hacking” or altering them: the public key has encrypted the transaction into a format unreadable by anyone except the intended party and the intended party only.

## Q

### Queries

***

Queries are requests processed only by the single node to which they are sent. [Clients](glossary.md#client) send queries to retrieve some aspect of the current consensus state, like the balance of an account. Certain queries are free, but generally, queries are subject to [fees](../networks/mainnet/fees/).

### Quorum

***

The minimum number of members required to vote on a proposal before it can be accepted, ensuring decisions are made with sufficient participation.

## R

### Real-World Asset (RWA)

***

A real-world asset (RWA) is a tangible or intangible asset with intrinsic value, such as real estate, commodities, equities, or intellectual property. In asset tokenization, RWAs are digitized into tokens on a blockchain or other distributed ledger technologies (DLTs), enabling fractional ownership, easier transferability, and increased liquidity.

When a tokenized RWA represents ownership, investment, or entitlement to profits (like shares in real estate or equity), it is usually classified as a [security token](glossary.md#security-token) and is subject to securities regulations. However, if the token merely provides access or usage rights without investment characteristics, it may not be considered a security token.

### Record File

***

A record file is a file that contains the details of a transaction that occurred on the Hedera Network. It provides greater detail about the transaction than a receipt, such as a consensus timestamp it received or the results of a smart contract function call. Hedera Mirror Nodes store the record file and can be accessed through the [mirror node REST API](../sdks-and-apis/rest-api.md).

### Remix IDE

***

The Remix IDE is a user-friendly platform that allows you to easily write and compile your smart contracts and perform other tasks such as debugging and testing. Learn more about it [here](https://remix-project.org/).

### Resolver Contract

***

A smart contract that links to various logic modules and executes functions for [digital securities](glossary.md#digital-security). In asset tokenization, resolver contracts manage different aspects of token operations, like [compliance](glossary.md#compliance) and transfers. When a tokenized asset needs to perform an action, it refers to the resolver contract to find the right module to execute that function. Learn more about resolver contracts in the context of the [asset tokenization studio's architecture](../open-source-solutions/asset-tokenization-studio-ats/#in-depth-architecture-of-ats).

### REST API

***

REST APIs are used for interactions with services provided by Hedera, such as account balance checks, transaction submissions, and other actions related to the consensus service, token service, and file service.

Hedera's REST APIs allow developers to easily interact with the Hedera Network without needing to directly connect to the network's nodes. Instead, they can send HTTP requests to a server that acts as an intermediary between them and the Hedera Network, simplifying the process of building applications on top of Hedera.

### Royalty Fee

***

A royalty fee refers to a specific type of custom fee that is applied to the transfer of non-fungible tokens (NFTs). A royalty fee is essentially a way to ensure that the original creator of an NFT continues to earn revenue from it even after the initial sale, as it allows them to earn a fraction of the sales every time the NFT is bought or sold in the future.

### Rug Pull

***

A _rug pull_ in the [NFT](glossary.md#non-fungible-token-nft) ecosystem refers to a situation where developers or project creators abandon or withdraw support from a project unexpectedly, often after promoting it to attract investors. Typically, this involves the sudden removal of [liquidity](glossary.md#liquidity) or funds, leaving investors with worthless or devalued assets. Rug pulls are a form of fraud that can occur when creators hype up an NFT collection, collect large amounts of money from investors, and then disappear without delivering on promises, effectively "pulling the rug" out from under the community.

## S

### Security Model

***

A security model outlines network security measures designed to protect a network from threats and ongoing attacks. In distributed ledger technology, a security model includes consensus mechanisms, cryptographic techniques, permissions, and smart contract security.

For example, [Hedera's security model](../core-concepts/smart-contracts/security.md) relies on the unique Hashgraph consensus algorithm that ensures fairness, security, and speed. It utilizes digital signatures for transaction verification, and it operates a permissioned network with trusted entities as nodes. Hedera also provides services like the Hedera Token Service (HTS) and Hedera Smart Contract Service (HSCS), built with security considerations in mind.

### Security Token

***

A security token is a specific type of digital token that represents ownership or an interest in a real-world asset, such as equity in a company, a bond, or real estate. These tokens are issued on a blockchain or other forms of distributed ledger technology (DLT) and are considered securities under financial regulations, meaning they must comply with securities laws. Security tokens typically come with various embedded rights, including voting rights, dividends, or other financial incentives associated with the underlying asset. They facilitate fractional ownership and trading of traditionally illiquid assets, enhancing liquidity and accessibility. All security tokens are [digital securities](glossary.md#digital-security), but not all [digital tokens](glossary.md#digital-token) qualify as security tokens, as they may not represent ownership in a financial asset.

### Shard

***

A shard is a partition or segment of a [distributed ledger](glossary.md#distributed-ledger) network, such as a [blockchain](glossary.md#blockchain) or [Directed Acyclic Graph (DAG)](glossary.md#directed-acyclic-graph-dag). In asset tokenization, shards can contain distinct sets of tokenized digital assets and are defined by the specific properties and rules of those assets. Shards function as interconnected segments of the overall network, allowing for parallel processing and efficient data management. Sharing data between shards is essential for maintaining consistency and ensuring the network's integrity.

### Smart Contract

***

A smart contract is a program consisting of a set of logic (state variables, functions, event handlers, etc.) or rules that can be deployed, stored, and accessed on Hedera. The functions within a smart contract can update and manage the state of the contract and read data from the deployed contract. Smart contracts are secure, tamper-proof, and transparent, offering a new level of trust and efficiency.

### SDK

***

A Software Development Kit (SDK), sometimes called a devkit, is a set of tools provided to software developers for building software applications. Tools can include Application Programming Interfaces (APIs), Integrated Development Environments (IDE), frameworks, code libraries, debuggers, code samples, test projects, and documentation.

### Solidity

***

Solidity is a Turing-complete object-oriented programming language for developing smart contracts on the Ethereum Virtual Machine (EVM) and other compatible distributed networks. It supports various functions necessary for operating decentralized applications and systems, including facilitating transactions, voting, multi-signature wallets, creating dApps, and crowdfunding. To learn more about the Solidity programming language, check out the documentation maintained by the Solidity team [here](https://docs.soliditylang.org/en/v0.8.19/).

### Soul-Bound Token (SBT)

***

Soul-bound tokens, also known as non-transferrable NFTs (non-fungible tokens), are digital assets permanently tied to a specific individual (or crypto wallet) — meaning they cannot be transferred to others.

Examples of SBT uses include attendance verification for events, job achievement certification, digital identification storage, membership credentials, medical records management, and reputation-based voting for DAO governance models.

### Source Code

***

Source code, which software developers program, is data, metadata, and data schema that collectively form the basis of any given ledger’s transactional content. Source code is used to automate the business processes and algorithms behind the operation of a given distributed network (e.g., Bitcoin, Ethereum, Hedera, etc.).

### Sourcify

***

Sourcify is a decentralized Solidity source code and metadata verification tool and repository and acts as a base layer allowing other tools to build on top of it. Sourcify enables transparent and human-readable smart contract interactions through automated Solidity contract verification, contract metadata, and NatSpec comments. More info on [Sourcify.dev](https://sourcify.dev/).

### Stablecoin

***

A stablecoin is a specialized form of cryptocurrency engineered to maintain a stable value by pegging it to an external asset, such as a fiat currency like the US Dollar or a commodity like gold. By doing so, stablecoins seek to combine the programmability and ease of transfer inherent in cryptocurrencies with the price stability typically associated with traditional currencies. This low-volatility makes stablecoins particularly useful for transactions, cross-border payments, and as a stable asset within decentralized finance ecosystems. They essentially serve as a bridge between the conventional financial infrastructure and the emerging _crypto-economic_ landscape, enabling fiat currencies to exist in a format that can be transferred with greater fluidity and efficiency across distributed networks.

### Staking

***

The process of participating in a [proof-of-stake](glossary.md#proof-of-stake-pos) system to validate transactions and earn rewards. When staked, coins are locked but can be unlocked for trading. Staking allows participants (stakeholders) to earn rewards on their holdings, typically in tokens or coins.

### State Machine

***

A state machine represents the state of the Hedera Network, including the balance of every account, the contents of any files stored via the Hedera File Service, and the details of any tokens created via the Hedera Token Service. Transactions in Hedera cause the state to transition from one state to another, and the Hedera Network achieves consensus on the order of these transactions and their resulting state transitions.

### State Proof

***

State proofs are a cryptographically secure, transferable, and storable mechanism for enabling trust and confidence in representations of the state of Hedera. The fundamental value proposition of a distributed ledger is that participants need not have special trust in any single node maintaining the state. Hedera’s state proofs ensure that clients querying the state can be given the necessary confidence that any data, even if returned by a single node, accurately represents the consensus state maintained by the full network.

### System Smart Contracts

System smart contracts are smart contracts that are implemented and maintained on the Hedera network as part of the core codebase that other contracts can invoke. These contracts have a permanent contract address in the Hedera network, meaning they will always be available at the same contract address.

## T

### Testnet

***

In the context of Hedera, the [Hedera Testnet](../networks/testnet/) is a network used by developers for testing their applications before deploying them on the Hedera mainnet. It allows for testing and debugging in a controlled environment, ensuring that any potential issues are resolved before the application is launched for real use.

### Timestamp

***

A timestamp is a piece of recorded data that verifies the data existed at a specific date and time. In distributed ledger technology, a timestamp reveals when a block has been mined and validated. With the Bitcoin blockchain, timestamps help solve the double spending problem.

### Token

***

A token generally refers to a type of cryptocurrency that represents an asset or a specific use and resides on its own blockchain. Tokens can represent any fungible and tradable asset, from commodities to loyalty points to even other cryptocurrencies.

### Token Lifecycle

***

The entire process of a [digital token's](glossary.md#digital-token) existence, from issuance and distribution to trading and eventual redemption or retirement. The token lifecycle in asset tokenization involves the creation, compliance management, trading, and potential updates with a modular [multi-layer implementation](../open-source-solutions/asset-tokenization-studio-ats/#how-asset-tokenization-studio-ats-works) (core, define, customize).

### Tokenization

***

Tokenization is the process of converting rights to a real-world asset (RWA) or financial instrument into a digital token on a blockchain or other forms of distributed ledgers. This digital token represents ownership, interest, or other forms of value associated with the asset, such as equity in a company, real estate, or a piece of art. Tokenization enables fractional ownership, increased liquidity, and easier transferability of assets, allowing them to be traded more efficiently on digital platforms.

### Transaction

***

In distributed ledger technology, a transaction (tx or TX) refers to an exchange of cryptocurrency on any distributed ledger network. The use of the ledger system ensures a record of all transactions. The speed of completion of the exchange depends on several variables, such as which network is used, traffic at the time of the exchange, the size of the transaction fee, etc. An exchange can be completed in as little as a few seconds or minutes or several hours, depending on these variables.

### Transaction Fee

***

A fee associated with a transaction that compensates the Hedera network for processing and maintaining the transaction in a consensus state.

### Transaction Hash

***

A transaction hash is a unique identifier generated when a transaction is submitted on a DLT network like Hedera. It is essentially a string of characters that represents the cryptographic hash of a transaction. This hash allows users to track and verify the transaction on the blockchain explorer. Each transaction hash is unique and serves as proof that the transaction has been successfully recorded on the ledger.

For example, in the context of Hedera, when you submit a transaction (such as transferring HBAR), the network will return a transaction hash that can be used to verify its status or look up its details on explorers like HashScan.

### Transactions Per Second (TPS)

***

The number of [transactions](glossary.md#transaction) per second (TPS) a distributed network can process.

### Trustless

***

Trustless is a term associated with distributed ledger technology such as [blockchain](https://www.notion.so/8ccb1fd22bfa46c1826bfedf3176be96)-based networks because participants don’t need the support of a "trusted" central party, such as a bank, broker, lawyer, or other centralized authority, to facilitate the validation, execution, and journaling of transactions between multiple parties.

### Turing Complete

Turing complete means a system or a language has the ability to simulate any computer algorithm, no matter how complex. It's like saying if you give this system enough time and memory, it can run any program a computer can execute, whether it's a simple calculator or a complex video game. The importance of Turing completeness as it pertains to distributed ledger technology is that it allows for the programmatic development of smart contracts.

### Universally Unique Identifier (UUID)

***

A **UUID** (Universally Unique Identifier) is a 128-bit identifier that uniquely identifies objects or entities across systems. It’s designed to be globally unique, even when generated by different systems or at different times. UUIDs are formatted as a 36-character string with five groups separated by hyphens (e.g., `550e8400-e29b-41d4-a716-446655440000`). They come in several versions based on how they’re generated, such as through time, random values, or hashing.

## V

### Validation

***

A distributed network maintains its integrity and security through validation. The nodes in the network process transactions before adding them to the ledger. This includes checking that the transaction is properly signed, that the sender's account has enough HBAR to cover the transaction and its fees, and that the transaction doesn't conflict with the current state of the ledger. By ensuring that all transactions adhere to the network's rules, validation helps prevent fraud, double-spending, and other forms of abuse.

### Virtual Machine

***

A virtual machine (VM) is a software representation of a computer and VM technology related to distributed ledger technology as a platform for digital transformation, business innovation, and industry disruption. A VM can run software that’s independent of the underlying machine (aka the host) that it runs on.

### Virtual Merkle Tree

***

A virtual [Merkle tree](glossary.md#merkle-tree), as proposed in [HIP-25](https://hips.hedera.com/hip/hip-25), is a virtualized on-disk data structure designed to support the scaling of billions of entities per node on the Hedera Network. It aims to optimize memory usage by moving much of the system state out of RAM and onto disk, enhancing the scalability and performance of operations like smart contracts and potentially [NFTs](glossary.md#non-fungible-token-nft) without impacting the network's transaction processing speed.

### Virtual Voting

***

A mechanism for achieving consensus in a distributed system, which can be used in some forms of distributed ledger technology. Virtual Voting is one of two consensus components created for the Hedera (the other consensus component is gossip about gossip). These two components function dynamically to build a fair (linear) ordering of transactions without explicitly casting ballots.

### Vyper

***

Vyper is an experimental, statically typed contract programming language meant to resemble Python. Like objects in OOP, each contract contains state variables, functions, and common data types. Contract-specific features include event notifiers for listeners, and custom global variables, and global constants. To learn more about Vyper, check out the documentation maintained by the Vyper team [here](https://docs.vyperlang.org/en/stable/).

## W

### Web1

***

The first generation of the internet is primarily _read-only_ and static information that links to each other. Think of a self-hosted, personal web page or a blog using a platform such as [WordPress](https://wordpress.com/) or [Squarespace](https://www.squarespace.com/). You primarily share the information with others via URLs. Web1 is centralized or operated by companies that control servers that service customers.

### Web2

***

The second generation of the internet, the current state of the internet, also referred to as the _read-write_ web, is a web of social interactions linking users to each other. Think of social media sites such as Instagram and Twitter, where you share and consume algorithm-based data feeds managed by tech companies. You often share information within the platform by liking, sharing, re-posting with your own comments, and more. Though much of web2 content is created by users, it is not necessarily owned by them. Both [web1](glossary.md#web1) and web2 are centralized or operated by companies that control servers that serve customers.

### Web3

***

The envisioned next generation of the internet is characterized by decentralization, distributed ledger technology, and real-time, persistent data, also referred to as the _read-write-own_ web. Web3 is the Internet, plus added decentralization components such as DLTs & blockchains, cryptocurrencies, and NFTs. Whereas web2 is centralized or operated by companies that control servers that serve customers, web3 is decentralized, built, and operated by users.

### Weibar

***

The EVM returns gas information in Weibar (introduced in [HIP-410](https://hips.hedera.com/hip/hip-410)). One weibar is 10^-18th HBARs, which translates to 1 tinybar is 10^10 weibars. As noted in HIP-410, this is to maximize compatibility with third-party tools that expect ether units to be operated on in fractions of 10^18, also known as a Wei.

### White Paper

***

In the DLT world, a white paper is a document containing technical information about a proposed project, such as an [Initial Coin Offering (ICO)](glossary.md#initial-coin-offering-ico). A white paper typically aims at potential investors and provides background information about the project, how the technology will work, what problems it may solve, and who would benefit from the solution. _Bitcoin: A Peer-to-Peer Electronic Cash System_, published by Satoshi Nakamoto in 2008, is a famous White Paper that "propose(d) a solution to the double-spending problem using a [peer-to-peer](glossary.md#peer-to-peer-p2p) network.”

## Z

### Zk-SNARK

***

zk-SNARK is an acronym for Zero-Knowledge _&#x53;_&#x75;ccinct Non-Interactive Argument of Knowledge. In a ledger, zk-SNARK is a multi-theorem proof system that grants the power to control access to validated transaction information. This means that ledger data can be gated.

***

**Contributor**: [**@sumanair** ](https://github.com/sumanair)
