---
description: >-
  Learn how to submit a demo application, create pull requests, or log issues in
  the Hedera Contributing Guide.
cover: broken-reference
coverY: 31
---

# Contributing & Style Guide

We value every form of contribution, no matter how small. In this guide, you will find steps on submitting an issue, creating a pull request, submitting a demo application, creating Hedera Improvement Proposals (HIPs), and adhering to our style guide. Thanks in advance for your contributions!

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><strong>1.</strong> <a href="contributing-guide.md#submit-an-issue"><strong>CREATE ISSUES</strong></a></td><td><a href="contributing-guide.md#submit-an-issue">#submit-an-issue</a></td></tr><tr><td align="center"><strong>2.</strong> <a href="contributing-guide.md#creating-a-pull-request"><strong>PULL REQUESTS</strong></a></td><td><a href="contributing-guide.md#creating-a-pull-request">#creating-a-pull-request</a></td></tr><tr><td align="center"><strong>3.</strong> <a href="contributing-guide.md#hedera-improvement-proposal-hip"><strong>HIPs</strong></a></td><td><a href="contributing-guide.md#hedera-improvement-proposal-hip">#hedera-improvement-proposal-hip</a></td></tr><tr><td align="center"><strong>4.</strong> <a href="contributing-guide.md#submit-demo-applications"><strong>DEMO APPLICATIONS</strong></a></td><td><a href="contributing-guide.md#submit-demo-applications">#submit-demo-applications</a></td></tr><tr><td align="center"><strong>5.</strong> <a href="contributing-guide.md#style-guide"><strong>STYLE GUIDE</strong></a></td><td><a href="contributing-guide.md#style-guide">#style-guide</a></td></tr><tr><td align="center"><strong>📕</strong> <a href="https://github.com/hashgraph/hedera-docs"><strong>REPO</strong></a></td><td><a href="https://github.com/hashgraph/hedera-docs">https://github.com/hashgraph/hedera-docs</a></td></tr></tbody></table>

## Create Issues

If you've identified a problem in the documentation or have a suggestion for additional content, you can submit an issue. Follow these steps:

1. **Navigate to the Repository:** Visit the `hedera-docs` repository on GitHub.
2. **Open the Issues Tab:** Find the "Issues" tab near the top of the repository page, next to "Code" and "Pull Requests". Click on it.
3. **Create a New Issue:** Click the "New Issue" button to open a form where you can detail the problem or suggestion.
4. **Fill Out the Form:** Give your issue a title and a detailed description. Be as clear and concise as possible to ensure we fully understand the issue. If applicable, include screenshots or code snippets.
5. **Submit the Issue:** After filling out the issue, click the "Submit new issue" button. We'll review your issue and take appropriate action.

## Create Pull Requests

If you'd like to propose changes directly to the documentation, you can submit a pull request. Here's how:

1. **Fork the Repository:** Navigate to the `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs) and click the "Fork" button at the top right. This creates a copy of the repository in your GitHub account.
2. **Clone the Forked Repository:** Clone the forked repository to your local system and make changes. Be sure to follow the repository's coding and style guidelines.
3. **Commit Your Changes:** Once you've made your changes, commit them with a clear, detailed message describing the changes you've made.
   1. Use [sign-off](https://github.com/hashgraph/.github/blob/main/CONTRIBUTING.md#sign-off) when making each of your commits.
      1. Alternatively, you can use auto sign-off by installing `cp hooks-git/prepare-commit-msg .git/hooks && chmod +x .git/hooks/prepare-commit-msg`.
   2. Use [this guide](https://pre-commit.com/#3-install-the-git-hook-scripts) to install the pre-commit hook scripts to check for files with names that would conflict on a case-insensitive filesystem like MacOS HFS+ or Windows FAT.
4. **Push Your Changes:** Push your committed changes to your forked repository on GitHub.
5. **Submit a Pull Request:** Back in the `hedera-docs` repository, click the "Pull Requests" tab and then the "New pull request" button. Select your forked repository and the branch containing your changes, then click "Create pull request".
6. **Describe Your Changes:** Give your pull request a title and describe the proposed changes. This description should make it clear why the changes should be incorporated.
7. **Submit the Pull Request:** Click the "Create pull request" button to submit it. We'll review your proposed changes and, if they're approved, merge them into the repository.

By logging issues and creating pull requests, you're helping us make the Hedera documentation better for everyone. We appreciate your contributions and look forward to collaborating with you!

{% hint style="info" %}
**Note:** The Hedera team will review issues and pull requests.
{% endhint %}

## Hedera Improvement Proposal (HIP)

Have a new feature request for consensus or mirror nodes? Looking to submit a standard or informational guide for the Hedera ecosystem? Submit a Hedera Improvement Proposal that will be reviewed and evaluated by the Hedera Team. These improvement proposals can range from core protocol changes to the applications, frameworks, and protocols built on the Hedera public network and used by the community. To view all active and pending HIPs, check out the [HIP website](https://hips.hedera.com/).

1. Fork the `hedera-improvement-proposal` repository [here](https://github.com/hashgraph/hedera-improvement-proposal).
2. Fill out this [HIP template](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/hip-0000-template.md).
3. Create a pull request against `hashgraph/hedera-improvement-proposal` main branch.

{% hint style="info" %}
**Note:** Which category should you make the **HIPs**? See [hip-1](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-1.md) for details on the **HIP** process, or watch the following video tutorial.
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=Gbk8EbtibA0" %}
Hedera Improvement Proposals\
by Developer Advocate: Michael Garber
{% endembed %}

## Submit Demo Applications

If you have a demo application that you'd like to share, we encourage you to follow the steps outlined below to ensure your application is showcased accurately.

1. Open an _Enhancement_ issue in the `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs).
2. Within the issue, please include the following details:
   1. **Demo application name:** The official name of your demo application. This is how it will be listed on the demo applications page.
   2. **Developer/Maintainer name and GitHub username:** Your name or the person maintaining the demo application. This ensures we know who to contact for any future updates or questions regarding the application.
   3. **Link to the demo application GitHub repository:** Please provide a link to the public GitHub repository where your demo application is hosted. This allows the Hedera community to access and review your application.
3. Submit your issue once you've provided the required details within the issue. Our team will review your submission, and if approved, your demo application will be added to our list.

Remember, the aim is to showcase applications that demonstrate the potential and functionality of Hedera in various use cases. Clear, concise, and well-documented code is highly appreciated.

Thank you for your valuable contribution to the Hedera community! We look forward to reviewing your demo application.

## Style Guide

<details>

<summary>GitBook Markdown Syntax</summary>

Please refer to the [GitBook Markdown Syntax guide](https://raw.githubusercontent.com/audacity/audacity-support/main/community/contributing/tutorials/gitbook-markdown-syntax.md).

</details>

<details>

<summary>Use of HBAR</summary>

When referring to the Hedera native currency, use the singular form of the noun **HBAR**. For example:

* "I bought 10 **HBAR** yesterday"

Do not use the plural form of the noun, as this style rule applies even when referring to multiple units of **HBAR**.

**tinybars**

When referring to fractions of **HBAR**, use the plural form **tinybars**. For example:

* "I will transfer 1,000 **tinybars** from my account to yours"

Do not use the singular form of the noun, as any reference should be plural since one **HBAR** equals 100,000 **tinybars**.

</details>

<details>

<summary>Use of web2 and web3</summary>

When documenting or referring to "web2" and "web3," it's important to maintain consistency. Both terms should be in lowercase. The only exception to this rule is when either term starts a sentence. In such cases, the initial letter should be capitalized. For example:

* ❌ **Incorrect**: "web3 technologies are evolving rapidly."
* ✅ **Correct**: "Web3 represents a shift towards decentralization."
* ✅ **Correct**: "In the context of web2, user data is often controlled by centralized entities."
* ✅ **Correct**: "The principles of transparency and user empowerment are fundamental to the development of web3 platforms."

</details>

<details>

<summary>American English</summary>

Follow the American English spelling standard. This means that words should follow the American English conventions, employing **'z'** instead of **'s'** in words such as 'decentralized,' 'realized,' and 'organized.'

**For example:**

* Use 'color' instead of the British English 'colour.'
* Use 'analyze' instead of the British English 'analyse.'
* Use 'organization' instead of the British English 'organisation.'

Use an American English dictionary or a recognized American English style guide to ensure consistency and accuracy throughout the text. Tools like Grammarly or spell checkers set to American English can assist in maintaining this standard.

</details>

<details>

<summary>Tutorial steps</summary>

When presenting steps or instructions within the documentation, the following guidelines should be observed:

**Ordered Steps (Numbered List):** If the steps must be followed in a specific sequence, use a numbered list to present the order clearly. This ensures that readers understand the progression and importance of each step.

**Example:**

1. Clone repo.
2. `cd` into the cloned directory.
3. `npm install`

**Unordered Steps (Bulleted List):** If the order of the steps is not crucial to the outcome, use bulleted points. This provides flexibility for readers to approach the tasks as they prefer.

**Example:**

* Choose a color.
* Select a size.
* Identify a preferred style.

Adhering to these guidelines will ensure readers' clarity and ease of understanding, allowing them to follow instructions effectively, whether in a precise sequence or with more flexible options.

</details>

<details>

<summary>Capitalization</summary>

**Key Point:** Use standard American capitalization. Use sentence case for headings.

Follow the standard [capitalization rules](https://owl.purdue.edu/owl/general\_writing/mechanics/help\_with\_capitals.html) for American English. Additionally, use the following style standards consistently throughout the Hedera developer documentation:

* Follow the official capitalization of **Hedera** products, services, or terms defined by open-source communities, e.g., **Hedera Consensus Service, Hedera Improvement Proposal,** and **Secure Hashing Algorithm**.
* Capitalize each instance of network names **mainnet**, **testnet**, and **mirrornet** only when preceded by **Hedera,** e.g., **Hedera Mainnet**, **Hedera Testnet**, and **Hedera Mirrrornet**.
* Do not use all-uppercase or camel case except in the following contexts: in official names, abbreviations, or variable names in a code block, e.g., **HBAR, HIPs,** or **SHA384**.
* You should revise any sentence starting with lowercase word stylization to avoid creating a sentence with a lowercase word.

</details>

<details>

<summary>Oxford comma</summary>

The Oxford comma is the comma used immediately before the coordinating conjunction ("and" or "or") in a list of three or more items. In our written content, the use of the Oxford comma is required to maintain clarity and prevent ambiguity.

**Example:** "The team consists of product managers, developers, designers, and writers."

By consistently applying the Oxford comma, we ensure that the meaning of lists is clear, especially when individual items contain commas themselves. This standard reflects our dedication to ensuring clear and accurate communication in all of our documentation.

</details>

<details>

<summary>Abbreviations</summary>

**Key Point:** Use standard American and industry-standard abbreviations, e.g., **NFT** for non-fungible tokens. Avoid internet slang.

Abbreviations include acronyms, initialisms, shortened words, and contractions. In most contexts, the technical distinction between acronyms and initialisms isn't relevant; it's OK to use the phrase _acronym_ to refer to both.

* An _acronym_ is formed from the first letters of words in a phrase/name but pronounced as if it were a word itself:
  * **WAGMI** for We're All Gonna Make It
  * **DAO** for Decentralized Autonomous Organization
* An initialism is from the first letters of words in a phrase, but each letter is individually pronounced:
  * **KYC** for Know Your Customer
  * **IPFS** for InterPlanetary File System
* A shortened word is just part of a word or phrase, sometimes with a period in the end:
  * **Dr.** for doctor
  * **etc.** for et cetera

**Note:** Some abbreviations can be acronyms or initialisms, depending on the speaker's preference—examples include _**FAQ**_ and _**SQL**_. In some cases, the pronunciation determines [whether to use _a_ or _an_](https://developers.google.com/style/articles).

**Long and short versions of a word**

The short versions of the words are not abbreviations; if you use them, you don't need to put a period after them—for example:

* **application** and app
* **synchronize** and sync

If you're unsure whether a word is an abbreviation or a shortened version of a word, look in this list of [resources](https://developers.google.com/style#editorial-resources). If that doesn't settle the issue, use the speaking test: if you speak the short version as a word (_This is a demo version of the product_), you can usually treat it as a word and not an abbreviation.

#### Don't create abbreviations <a href="#creating-abbreviations" id="creating-abbreviations"></a>

Use recognizable and industry-standard acronyms and initialisms. Abbreviations are intended to save the writer and the reader time. If the reader has to think twice about an abbreviation, it can slow down their reading comprehension.

#### Make abbreviations plural <a href="#making-abbreviations-plural" id="making-abbreviations-plural"></a>

Treat acronyms, initialisms, and other abbreviations as _regular_ words when making them plural—for example, **APIs**, **SDKs**, and **IDEs**.

#### When to spell out a term <a href="#spelling-out" id="spelling-out"></a>

In general, when an abbreviation is likely to be unfamiliar to the audience, spell out the first mention of the term and immediately follow with the abbreviation in parentheses, for example:

* Miner Extracted Value (**MEV**)
* elliptic-curve cryptography (**ECC**)

For all subsequent mentions of the term, use the abbreviation by itself. If the first mention of a term occurs in a heading or title, you can use the abbreviation and then spell out the abbreviation in the first paragraph that follows the heading or section title.

In some cases, spelling out an acronym doesn't help the reader understand the term. For example, writing out a _portable document format_ doesn't help the reader understand what a _PDF_ document is.

**Note:** The following acronyms rarely need to be spelled out: **API**, **SDK**, **HTML**, **REST**, **URL**, **USB**, and file formats such as **PDF** or **XML**.

</details>
