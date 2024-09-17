# Frequently Asked Questions (FAQs)

## **What is asset tokenization?**

Asset tokenization converts[ real-world assets (RWAs)](../../support-and-community/glossary.md#real-world-asset-rwa) like real estate, equities, bonds, or commodities into [digital tokens](../../support-and-community/glossary.md#digital-token) on a blockchain or other distributed ledger technology (DLT). This allows for [fractional ownership](../../support-and-community/glossary.md#fractional-ownership), greater liquidity, and easier transferability, making these assets accessible to a wider range of investors.

## **What is the Difference Between a Security Token and a Digital Security?**

A [security token](../../support-and-community/glossary.md#security-token) represents ownership in a real-world asset and must comply with securities regulations. It often offers benefits like dividends or voting rights. [Digital security](../../support-and-community/glossary.md#security-token) is a broader term referring to the digital form of any security, regardless of whether it's on a blockchain. It encompasses all forms of digital representations of securities, including those on traditional databases, not just those issued as tokens on a distributed ledger technology (DLT).

## **What is Asset Tokenization Studio?**

[Asset Tokenization Studio (ATS)](./#how-asset-tokenization-studio-ats-works) is Hedera's open-source solution designed to simplify creating, managing, and operating tokenized assets. It provides open-source tools and templates to streamline tokenization for assets like equity, debt, or real estate, with built-in compliance controls.

## **What Assets Can I Tokenize Using Asset Tokenization Studio?**

You can tokenize various assets, including real estate, equities, bonds, and commodities. ATS supports different asset classes, enabling issuers to define terms and ensure regulatory compliance.

## **How Does the Tokenization Process Work?**

In ATS, tokenization involves defining the asset's characteristics, setting up compliance rules, and generating digital securities on the Hedera network. Once created, these tokens can be issued and traded on the blockchain or other forms of distributed ledgers, with ATS managing the entire lifecycle.

## **How Does Asset Tokenization Studio Ensure Regulatory Compliance?**

Asset Tokenization Studio integrates compliance features such as KYC/AML checks, approval lists/blocklists for eligible investors, and role-based permissions. It also supports SEC Regulations D (506-b, 506-c) and Regulation S, ensuring that tokenized securities comply with key regulatory frameworks.

## **Can Asset Tokenization Studio Support Additional Functions and Jurisdictions in the Future?**

Yes, Asset Tokenization Studio is designed with flexibility in mind and can be extended to support additional functionalities and regulatory requirements for new jurisdictions in the future. Future updates aim to enhance the platform’s capabilities to meet the evolving needs of issuers and investors.

## **Are the Smart Contracts Used by Asset Tokenization Studio Secure?**

Yes, the smart contracts used by Asset Tokenization Studio are audited by [A\&D Forensics](https://adforensics.com.ng/audit-services/) to ensure their security and reliability. This auditing process helps protect the platform and its users from potential vulnerabilities, ensuring a secure tokenization environment. The full audit report can be found [here](https://github.com/hashgraph/asset-tokenization-studio/blob/main/Smart%20Contracts%20Audit%20Report.pdf).

## **Can I Upgrade/Update a Tokenized Asset After It’s Deployed?**

Yes, ATS allows certain [updates to tokenized assets using proxy contracts](./#in-depth-architecture-of-ats). This design pattern allows you to change the logic or functionality of the token without altering the underlying asset data. When an update is needed, the proxy contract can point to a new logic contract (resolver) that defines the new rules or behaviors.

## **What Wallets Are Supported by Asset Tokenization Studio?**

Asset Tokenization Studio supports wallets compatible with the Hedera network, including [MetaMask](https://metamask.io/), [HashPack](https://www.hashpack.app/), and [Blade Wallet](https://bladewallet.io/). This allows users to easily interact with their tokenized assets through secure wallet integrations.
