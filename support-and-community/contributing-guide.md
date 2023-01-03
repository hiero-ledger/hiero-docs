---
description: >-
  Welcome to Hedera documentation contributing and style guide! Ways you can
  contribute below⬇:
cover: ../.gitbook/assets/28_ultraviolet.jpg
coverY: 7.686030910954216
---

# Contributing & Style Guide

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>CONTRIBUTING GUIDE</strong></td><td></td><td></td><td><a href="contributing-guide.md#contributing-guide">#contributing-guide</a></td></tr><tr><td align="center"><strong>STYLE GUIDE</strong></td><td></td><td></td><td><a href="contributing-guide.md#style-guide">#style-guide</a></td></tr></tbody></table>

## Contributing Guide

### Create new issues and pull requests

If you find something that needs to be updated or would like to publish additional content to the docs site you may do so by the following methods:

* Log an issue
  * You may submit an issue directly on the [`hedera-docs`](https://github.com/hashgraph/hedera-docs) repository
* Create a pull request
  * Fork the repository and submit a pull request that includes the suggested updates

> **Note:** Issues and pull requests will be reviewed by the Hedera team.

### Hedera Improvement Proposal (HIP)

Have a new feature request for consensus or mirror nodes? Looking to submit a standard or informational guide for the Hedera ecosystem? Submit a Hedera Improvement Proposal that will be reviewed and evaluated by the Hedera Team. These improvement proposals can range from core protocol changes to the applications, frameworks, and protocols built on top of the Hedera public network and used by the community. To view all active and pending HIPs, check out the [HIP website](https://hips.hedera.com/).&#x20;

1. Fork the `hedera-improvement-proposal` repository [here](https://github.com/hashgraph/hedera-improvement-proposal).&#x20;
2. Fill out this [HIP template](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/hip-0000-template.md).
3. Create a pull request against `hashgraph/hedera-improvement-proposal` main branch.

> **Note:** Which category should you make the _**HIP**_? See [hip-1](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-1.md) for details on the _**HIP**_ process or watch the following video tutorial.

{% embed url="https://www.youtube.com/watch?v=Gbk8EbtibA0" %}
Hedera Improvement Proposals by Developer Advocate: Michael Garber
{% endembed %}

## Style Guide

### Capitalization

{% hint style="info" %}
**Key Point:** Use standard American capitalization. Use sentence case for headings.&#x20;
{% endhint %}

Follow the standard [capitalization rules](https://owl.purdue.edu/owl/general\_writing/mechanics/help\_with\_capitals.html) for American English. Additionally, use the following style standards consistently throughout the Hedera developer documentation:

* Follow the official capitalization of _**Hedera**_ products, services, or terms defined by open-source communities, e.g., _**Hedera Consensus Service, Hedera Improvement Proposal,**_ and _**Secure Hashing Algorithm**._
* Capitalize each instance of network names _**mainnet**_, _**testnet**_, and _**mirrornet** only when preceded_ by _**Hedera,** e.g., **Hedera Mainnet**_, _**Hedera Testnet**_, and _**Hedera Mirrrornet**_.
* Do not use all-uppercase or camel case except in the following contexts: in official names, abbreviations, or variable names in a code block, e.g., _**HBAR, HIPs,**_ or _**SHA384**._
* You should revise any sentence starting with lowercase word stylization to avoid creating a sentence with a lowercase word.

### HBAR

When referring to the Hedera native currency, use the singular form of the noun _**HBAR**_. For example:&#x20;

* _"I bought 10 **HBAR** yesterday"_

Do not use the plural form of the noun as this style rule applies even when referring to multiple units of _**HBAR**_.&#x20;

### tinybars

When referring to fractions of _**HBAR**_, use the plural form _**tinybars**_. For example:&#x20;

* _"I will transfer 1,000 **tinybars** from my account to yours"_

Do not use the singular form of the noun as any reference should be plural since one _**HBAR**_ is equal to 100,000 _**tinybars**_.

### Abbreviations

{% hint style="info" %}
**Key Point:** Use standard American and industry-standard abbreviations, e.g., _**NFT** for_ non-fungible token. Avoid internet slang.&#x20;
{% endhint %}

Abbreviations include acronyms, initialisms, shortened words, and contractions. In most contexts, the technical distinction between acronyms and initialisms isn't relevant; it's OK to use the phrase _acronym_ to refer to both.

* An _acronym_ is formed from the first letters of words in a phrase/name but pronounced as if it were a word itself:
  * _**WAGMI** for We're All Gonna Make It_
  * _**DAO** for Decentralized Autonomous Organization_
* An initialism is from the first letters of words in a phrase, but each letter is individually pronounced:&#x20;
  * _**KYC** for Know Your Customer_
  * _**IPFS** for InterPlanetary File System_
* A shortened word is just part of a word or phrase, sometimes with a period in the end:
  * &#x20;_**Dr.** for **** doctor_
  * _**etc.** for et cetera_

> **Note:** Some abbreviations can be either acronyms or initialisms, depending on the speaker's preference—examples include _**FAQ**_ and _**SQL**_. In some cases, the pronunciation determines [whether to use _a_ or _an_](https://developers.google.com/style/articles).

#### Long and short versions of a word <a href="#long-and-short-versions" id="long-and-short-versions"></a>

The short versions of the words are not abbreviations; if you use them, you don't need to put a period after them—for example:

* _**application** and app_
* _**synchronize** and sync_

If you're unsure whether a word is an abbreviation or a shortened version of a word, look in this list of [resources](https://developers.google.com/style#editorial-resources). If that doesn't settle the issue, use the speaking test: if you speak the short version as a word (_This is a demo version of the product_), you can usually treat it as a word and not an abbreviation.

### Don't create abbreviations <a href="#creating-abbreviations" id="creating-abbreviations"></a>

Use recognizable and industry-standard acronyms and initialisms. Abbreviations are intended to save the writer and the reader time. If the reader has to think twice about an abbreviation, it can slow down their reading comprehension.

### Make abbreviations plural <a href="#making-abbreviations-plural" id="making-abbreviations-plural"></a>

Treat acronyms, initialisms, and other abbreviations as _regular_ words when making them plural—for example, _**APIs**_, _**SDKs**_, and _**IDEs**_.

### When to spell out a term <a href="#spelling-out" id="spelling-out"></a>

In general, when an abbreviation is likely to be unfamiliar to the audience, spell out the first mention of the term and immediately follow with the abbreviation in parentheses, for example:

* _Miner Extracted Value (**MEV**)_
* _elliptic-curve cryptography (**ECC**)_

For all subsequent mentions of the term, use the abbreviation by itself. If the first mention of a term occurs in a heading or title, you can use the abbreviation and then spell out the abbreviation in the first paragraph that follows the heading or section title.&#x20;

In some cases, spelling out an acronym doesn't help the reader understand the term. For example, writing out a _portable document format_ doesn't help the reader understand what a _PDF_ document is.

> **Note:** The following acronyms rarely need to be spelled out: _**API**_, _**SDK**_, _**HTML**_, _**REST**_, _**URL**_, _**USB**_, and file formats such as _**PDF**_ or _**XML**_.
