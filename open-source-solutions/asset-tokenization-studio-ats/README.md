# Asset Tokenization Studio (ATS)

## Introduction to Asset Tokenization

In a digital world, [real-world assets (RWAs)](../../support-and-community/glossary.md#real-world-asset-rwa) like stocks, bonds, and real estate are being turned into [digital securities](../../support-and-community/glossary.md#digital-security) through asset tokenization. This process changes how these assets are managed and traded. Tokenizing assets improves accessibility, liquidity, and transparency, making it easier for more people (retail investors) to invest in traditionally exclusive and illiquid markets.

### What is Asset Tokenization?

Asset tokenization creates [digital tokens](../../support-and-community/glossary.md#digital-token) on a blockchain or other distributed ledger technology (DLT) to represent ownership or interest in real assets like company shares, bonds, real estate, or commodities. These tokens, called security tokens, include rights like voting, dividends, and corporate actions. Like blockchain, DLTs ensure secure, transparent, and immutable transactions. This digital shift makes finance more efficient, transparent, and inclusive.

### Why Tokenize Assets?

Tokenization offers several significant benefits:

* **Enhanced Liquidity:** Enables 24/7 trading on digital exchanges.
* **Greater Accessibility:** Opens up investments to retail investors.
* **Transparency & Security:** Ensures secure, immutable transactions on a distributed ledger.
* **Cost Efficiency:** Reduces transfer time and costs by eliminating intermediaries.

### How Does Asset Tokenization Work?

The tokenization process involves several key steps:

1. **Asset Selection:** Identify the real-world asset to tokenize, such as real estate, a bond, or an equity stake in a company.
2. **Specification Definition:** Determine the asset's digital specifications, including its value, the number of tokens to be issued, compliance rules, and metadata.
3. **Distributed Ledger Selection:** Choose a suitable distributed ledger to host the digital tokens.
4. **Token Issuance:** Issue security tokens on the selected distributed ledger, ensuring compliance with regulatory requirements.
5. **Trading and Management:** Once issued, these digital securities can be traded on digital exchanges, with transactions recorded transparently and immutably on the public ledger.

By transforming asset ownership into digital form, tokenization paves the way for a more efficient and inclusive financial system, opening up new opportunities for investors and asset owners.

{% hint style="info" %}
### 💡Request Access to Source Code

To explore the source code for ATS and customize it for your specific use case, you can request access. This allows you to review the core implementations, smart contracts, and integrations used in ATS. Please contact [Greg](mailto:greg@hashgraph.com) or [Sabrina](mailto:sabrina@hbar.fund) to get the necessary permissions to start building with ATS today.
{% endhint %}

***

## Introducing Asset Tokenization Studio (ATS)

To streamline and simplify the asset tokenization process, Asset Tokenization Studio (ATS)—Hedera's open-source platform that allows issuers to create, manage, and operate [digital securities](../../support-and-community/glossary.md#digital-security) in [compliance](../../support-and-community/glossary.md#compliance) with global regulations by adhering to the guidelines set forth by [SEC Regulations D](https://www.sec.gov/resources-for-investors/investor-alerts-bulletins/private-placements-under-regulation-d-investor-bulletin) ([506-b](https://www.sec.gov/resources-small-businesses/exempt-offerings/private-placements-rule-506b), [506-c](https://www.sec.gov/resources-small-businesses/exempt-offerings/general-solicitation-rule-506c)) and [Regulation S](https://www.sec.gov/rules-regulations/1998/02/offshore-offers-sales-regulation-s-effective-date-60-days-after-publication-federal-register). ATS makes asset tokenization accessible through the below key components and features that are open-source under an Apache 2.0 license, with the source code to be publicly available on GitHub soon:

<table data-view="cards"><thead><tr><th></th><th></th><th align="center"></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><a href="https://tokenization-studio.hedera.com"><strong>Web User Interface (UI)</strong></a> </td><td>A user-friendly web application that simplifies tokenization that makes it accessible to developers and non-technical users to easily interact with digital securities. </td><td align="center"><strong>✅  Available</strong></td><td><a href="../../.gitbook/assets/stablecoin-web-ui-Icon.png">stablecoin-web-ui-Icon.png</a></td><td><a href="https://tokenization-studio.hedera.com">https://tokenization-studio.hedera.com</a></td></tr><tr><td><strong>Customizable SDK</strong> </td><td>A flexible open-source Software Development Kit (SDK) that enables developers to build custom tokization solutions and deploy decentralized apps on the  Hedera network. </td><td align="center"><strong>Coming Soon</strong>  </td><td><a href="../../.gitbook/assets/SDKs.png">SDKs.png</a></td><td></td></tr><tr><td><strong>Smart Contracts</strong></td><td>Open-source, pre-built, and fully audited smart contracts that automate and standardize tokenization process according to the <a href="https://github.com/ethereum/EIPs/issues/1411">ERC-1400</a> standard.</td><td align="center"><strong>Coming Soon</strong></td><td><a href="../../.gitbook/assets/smart-contracts-icon.png">smart-contracts-icon.png</a></td><td></td></tr></tbody></table>

Additional features include support for popular wallets, including [MetaMask](https://metamask.io/), [HashPack](https://www.hashpack.app/), and [Blade Wallet](https://bladewallet.io/), and also extend the ERC-1400 standard by integrating additional modules to manage asset-specific metadata directly on the Hedera network. This includes functionalities like managing coupon payments or approval lists on-chain, enhancing transparency, and mitigating off-chain management risks.&#x20;

***

## Ecosystem Integrations & Launch Partners

ATS integrates with key partners like [RedSwan](https://redswan.io/), [ioBuilders](https://io.builders/), and [The HBAR Foundation](https://www.hbarfoundation.org/) to facilitate the issuance and management of tokenized bonds and equities on the Hedera network. These partners provide solutions for [KYC](../../support-and-community/glossary.md#know-your-customer-kyc)/[AML](../../support-and-community/glossary.md#anti-money-laundering-aml) compliance, [custody](../../support-and-community/glossary.md#custody), wallet integration, infrastructure management, [smart contract](../../support-and-community/glossary.md#smart-contract) monitoring, and regulatory compliance.

<table data-column-title-hidden data-view="cards"><thead><tr><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>RedSwan leverages distributed ledger technology to tokenize commercial real estate, providing investors with fractional ownership in high-value properties.</td><td><a href="../../.gitbook/assets/red-swan-logo.png">red-swan-logo.png</a></td><td><a href="https://redswan.io/">https://redswan.io/</a></td></tr><tr><td>ioBuilders specializes in developing blockchain-based financial infrastructure and solutions  to streamline the issuance and management of tokenized assets.</td><td><a href="../../.gitbook/assets/iobuilders-logo-new.png">iobuilders-logo-new.png</a></td><td><a href="https://io.builders/">https://io.builders/</a></td></tr><tr><td>The HBAR Foundation supports the growth of the Hedera ecosystem by funding projects, partnerships, and initiatives that drive adoption of the Hedera network.</td><td><a href="../../.gitbook/assets/hbar-foundation-logo.png">hbar-foundation-logo.png</a></td><td><a href="https://www.hbarfoundation.org/">https://www.hbarfoundation.org/</a></td></tr><tr><td>Hashgraph is the underlying distributed ledger technology (DLT) behind the Hedera network, providing solutions for retail and enterprise-level tokenization and dApps.</td><td><a href="../../.gitbook/assets/hashgraph-logo-card-white.png">hashgraph-logo-card-white.png</a></td><td><a href="https://www.hashgraph.com/">https://www.hashgraph.com/</a></td></tr><tr><td>HashPack is a secure, user-friendly wallet for managing HBAR and other tokenized assets that offers features like staking, NFTs, and integration with dApps on the Hedera network.</td><td><a href="../../.gitbook/assets/hashpack-logo-white.png">hashpack-logo-white.png</a></td><td><a href="https://www.hashpack.app/">https://www.hashpack.app/</a></td></tr><tr><td>Blade Labs develops wallet solutions focused on security, speed, and user experience, that enable seamless interaction with tokenized assets on the Hedera network.</td><td><a href="../../.gitbook/assets/bladelabs-logo.png">bladelabs-logo.png</a></td><td><a href="https://bladelabs.io/">https://bladelabs.io/</a></td></tr><tr><td>MetaMask is a web3 wallet to manage <a href="../../support-and-community/glossary.md#digital-asset">digital assets</a>, interact with dApps, and participate in tokenized ecosystems on the Ethereum and EVM-compatible networks.</td><td><a href="../../.gitbook/assets/metamask-logo.png">metamask-logo.png</a></td><td><a href="https://metamask.io/">https://metamask.io/</a></td></tr><tr><td>GFT is a global IT services company that supports financial institutions in developing blockchain and tokenization solutions, enabling secure, scalable digital asset management.</td><td><a href="../../.gitbook/assets/gft-logo.png">gft-logo.png</a></td><td><a href="https://www.gft.com/">https://www.gft.com/</a></td></tr><tr><td>Terminal 3 offers compliance and regulatory tools to onboard users including KYC/AML verification for secure and compliant tokenized asset management.</td><td><a href="../../.gitbook/assets/terminal3-logo-white-card.png">terminal3-logo-white-card.png</a></td><td><a href="https://www.terminal3.io/">https://www.terminal3.io/</a></td></tr><tr><td>Dfns provides secure, API-driven custody solutions for digital assets, ensuring that tokenized assets and transactions are protected with military-grade encryption.</td><td><a href="../../.gitbook/assets/stablecoin-dfns-logo.png">stablecoin-dfns-logo.png</a></td><td><a href="https://www.dfns.co/">https://www.dfns.co/</a></td></tr><tr><td>Diamond Standard makes the diamond market more accessible, transparent, and liquid by tokenizing assets into digital security tokens enabling fractional  shares securely.</td><td><a href="../../.gitbook/assets/diamond-standard-logo-card.png">diamond-standard-logo-card.png</a></td><td><a href="https://www.diamondstandard.co/">https://www.diamondstandard.co/</a></td></tr></tbody></table>

**...And more to be announced**

***

## How ATS Enhances the Tokenization Process

Asset Tokenization Studio operates through a modular, multi-layer implementation to manage the full token lifecycle.

<figure><picture><source srcset="../../.gitbook/assets/ats-multilayer-implementation-dark-mode.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/ats-multilayer-implementation-square-light-mode.png" alt=""></picture><figcaption></figcaption></figure>

### **Core Implementation**

The foundational core layer is based on the [ERC-1400](https://github.com/ethereum/EIPs/issues/1411) standard, which is interoperable with other key standards like [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/), [ERC-1410](https://github.com/ethereum/eips/issues/1410), [ERC-1594](https://github.com/ethereum/eips/issues/1594), [ERC-1643](https://github.com/ethereum/eips/issues/1643), and [ERC-1644](https://github.com/ethereum/EIPs/issues/1644) to offer a comprehensive solution for managing [security tokens](../../support-and-community/glossary.md#security-token). It provides a comprehensive framework for managing [digital securities](../../support-and-community/glossary.md#digital-security). The core implementation layer includes the following modules:

<table><thead><tr><th width="163">Modules</th><th>Description</th></tr></thead><tbody><tr><td><strong>Access Control</strong></td><td>ATS allows issuers to implement role-based access control, enabling them to assign specific permissions and roles such as Minter or Controller. This ensures that only authorized individuals can perform operations like minting or transferring tokens, enhancing security and governance.</td></tr><tr><td><strong>Control List</strong> </td><td>ATS supports approval lists and blocklists of addresses to ensure that only approved participants can engage in token transactions. This ensures compliance with KYC and AML regulations, limiting token transfers to authorized parties.</td></tr><tr><td><strong>Supply Cap</strong> </td><td>Issuers can set a maximum token supply to prevent unauthorized minting. This helps maintain transparency and investor trust by ensuring no tokens are issued beyond the predefined limit.</td></tr><tr><td><strong>Pause</strong></td><td>Issuers can temporarily pause all token transfers in the event of regulatory audits, security threats, or corporate actions, allowing full control during sensitive periods.</td></tr><tr><td><strong>Lock</strong></td><td>The lock function freezes specific tokens or accounts, useful for restricted trading periods, legal holds, or tokens subject to vesting schedules or escrow.</td></tr><tr><td><strong>Snapshots</strong></td><td>Snapshots capture token holder balances at any given time, providing accurate records for audits, dividends, or voting rights, ensuring compliance with corporate actions.</td></tr></tbody></table>

These modules provide a solid framework for efficient and compliant token management. Future module enhancements, such as [Know Your Customer (KYC)](../../support-and-community/glossary.md#know-your-customer-kyc) and Protected Partitions, will further enhance security and compliance.

<details>

<summary>📚 <strong>Learn more about ERC-1400 ⬇</strong></summary>

[**ERC-1400**](https://github.com/ethereum/EIPs/issues/1411) **is an extension of the** [**ERC-20**](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) **through the combination of 4 independent ERCs**:

* [**ERC-1410**](https://github.com/ethereum/eips/issues/1410): Defines partitions within a token, allowing token holders to have their total balance split across different partitions, such as 400 tokens in partition A and 600 in partition B. Depending on the specific use case, these partitions enable varying rights or restrictions to be applied to different segments of the same token. However, in many cases, partitions may not be necessary unless there is a need for differentiated classes of tokens or specific regulatory compliance.
* [**ERC-1594**](https://github.com/ethereum/eips/issues/1594): This standard allows for transfer validation to ensure KYC/AML adherence, enables forced transfers for legal or regulatory reasons, and supports linking tokens to off-chain legal documents. It also includes functions for issuing and redeeming tokens, providing a compliant framework for managing security tokens within a regulated environment.
* [**ERC-1643**](https://github.com/ethereum/eips/issues/1643)**:** This standard is designed to manage and distribute off-chain documents associated with security tokens. It is part of the ERC-1400 family of standards, specifically tailored to address the needs of security tokens by providing a way to securely link legal or compliance-related documents to on-chain assets.
* [**ERC-1644**](https://github.com/ethereum/EIPs/issues/1644): Defines a method that allows a controller account (similar to a super user) to forcibly transfer tokens from one account to another without the token holder's consent. This capability is designed to enforce regulatory actions or legal requirements on security tokens.

</details>

### **Define Implementation**

The define layer builds on the core implementation by defining a range of [digital securities](../../support-and-community/glossary.md#digital-security), such as equities and bonds. It also defines the foundation for processing corporate actions, such as [dividend](../../support-and-community/glossary.md#dividends) payments for equities and coupon payments for bonds. This layer is the foundation for the detailed customization provided in the next implementation layer.

### **Customize Implementation**

The customize implementation layer ensures that the tokenized assets comply with specific legal and regulatory requirements for different jurisdictions, like the United States. Issuers can customize [digital securities](../../support-and-community/glossary.md#digital-security), such as shares and bonds, following local laws and regulations. Future updates will expand this customization to smaller jurisdictions and support unique asset types, making the system adaptable to evolving regulatory standards.

***

## How Asset Tokenization Studio (ATS) Works

ATS provides an easy-to-use comprehensive framework and makes asset tokenization simple and ensures compliance with global regulations. ATS is an effective tool for issuers to tokenize assets safely and efficiently, opening up investment opportunities to retail investors. The architecture of ATS is built on several core components that facilitate tokenization and include:

* **Smart Contracts:** ATS uses enhanced factory contracts, audited by [A\&D Forensics](https://adforensics.com.ng/audit-services/), to automate and standardize the tokenization process following the ERC-1400 standard. These contracts simplify the deployment of [security tokens](../../support-and-community/glossary.md#security-token) and ensure consistency across various assets while reducing development time.
* **SDK:** The SDK provides a set of open-source tools and libraries for developers to build and deploy dApps on the Hedera network. It easily integrates and simplifies interaction with ATS smart contracts used to tokenize and manage [digital securities](../../support-and-community/glossary.md#digital-security).
* **Web UI**: The user interface provides a graphical web interface for developers and non-technical users to interact and manage digital securities without requiring in-depth technical knowledge of the Hedera network.&#x20;

### In-Depth Architecture of ATS

For those interested in technical architecture, ATS utilizes factory contracts to create new digital securities, resolver contracts to manage and execute specific functions, and proxy contracts to enable seamless upgrades. When tokenization is initiated, two smart contracts are deployed, these contracts work together with the proxy contract to create, operate, and upgrade [digital securities](../../support-and-community/glossary.md#digital-security).

* **Factory Contract:** This contract is used to create new **digital securities**, like bonds or equities. It has methods like `deployBond` and `deployEquity` to generate these tokens. Each security token is created with a **diamond proxy**, a design that allows flexible and modular contract logic.
* **Resolver Contract**: This contract links to different logic modules, each serving a specific function for the **security tokens**. When a tokenized asset needs to perform an action, it refers to the resolver contract to find the right module to execute that function.
* **Proxy Contract**: This contract acts as a layer wrapped around each **security token**, directing operations to the main logic contract. If the rules or functions of a token need to change (e.g., due to new regulations), the proxy can be updated to point to a new logic contract via the resolver. This allows all assets to be updated without interrupting their operation.

This architecture allows administrators or issuers to update their digital securities without disruption and keeps the architecture more flexible, scalable, and efficient over time.

<figure><picture><source srcset="../../.gitbook/assets/ats-sc-architecture-dark-mode.png" media="(prefers-color-scheme: dark)"><img src="../../.gitbook/assets/ats-sc-architecture.png" alt=""></picture><figcaption></figcaption></figure>
