---
description: >-
  This page outlines guidelines on language and grammar for Hedera
  documentation, covering American English spelling, abbreviation usage, active
  voice, punctuation, and tone.
---

# Language and grammar

## American English

Follow the American English spelling standard. This means that words should follow the American English conventions, employing 'z' instead of 's' in words such as 'decentralized,' 'realized,' and 'organized.'

**For example:**

* Use 'color' instead of the British English 'colour.'
* Use 'analyze' instead of the British English 'analyse.'
* Use 'organization' instead of the British English 'organisation.'

Use an American English dictionary or a recognized American English style guide to ensure consistency and accuracy throughout the text. Tools like Grammarly or spell checkers set to American English can assist in maintaining this standard.

***

## Abbreviations

**Key Point**: Use standard American and industry-standard abbreviations, e.g., NFT for non-fungible tokens. Avoid internet slang.

Abbreviations include acronyms, initialisms, shortened words, and contractions. In most contexts, the technical distinction between acronyms and initialisms isn't relevant; it's OK to use the phrase acronym to refer to both.

* An acronym is formed from the first letters of words in a phrase/name but pronounced as if it were a word itself:
  * WAGMI for We're All Gonna Make It
  * DAO for Decentralized Autonomous Organization
* An initialism is from the first letters of words in a phrase, but each letter is individually pronounced:
  * KYC for Know Your Customer
  * IPFS for InterPlanetary File System
* A shortened word is just part of a word or phrase, sometimes with a period at the end:
  * Dr. for doctor
  * etc. for et cetera

{% hint style="info" %}
**Note**: Some abbreviations can be acronyms or initialisms, depending on the speaker's preference—examples include FAQ and SQL. In some cases, the pronunciation determines [whether to use a or an.](https://developers.google.com/style/articles)
{% endhint %}

**Long and short versions of a word**

The short versions of the words are not abbreviations; if you use them, you don't need to put a period after them—for example:

* application and app
* synchronize and sync

If you're unsure whether a word is an abbreviation or a shortened version of a word, look in this list of [resources](https://developers.google.com/style#editorial-resources). If that doesn't settle the issue, use the speaking test: if you speak the short version as a word (This is a demo version of the product), you can usually treat it as a word and not an abbreviation.

### Don't create abbreviations

Use recognizable and industry-standard acronyms and initialisms. Abbreviations are intended to save the writer and the reader time. If the reader has to think twice about an abbreviation, it can slow down their reading comprehension.

### Make abbreviations plural

Treat acronyms, initialisms, and other abbreviations as regular words when making them plural—for example, APIs, SDKs, and IDEs.

### When to spell out a term

In general, when an abbreviation is likely to be unfamiliar to the audience, spell out the first mention of the term and immediately follow with the abbreviation in parentheses, for example:

* Miner Extracted Value (MEV)
* elliptic-curve cryptography (ECC)

For all subsequent mentions of the term, use the abbreviation by itself. If the first mention of a term occurs in a heading or title, you can use the abbreviation and then spell out the abbreviation in the first paragraph that follows the heading or section title.

In some cases, spelling out an acronym doesn't help the reader understand the term. For example, writing out a portable document format doesn't help the reader understand what a PDF document is.

{% hint style="info" %}
**Note**: The following acronyms rarely need to be spelled out: API, SDK, HTML, REST, URL, USB, and file formats such as PDF or XML.
{% endhint %}

***

## Active voice&#x20;

Always use active voice in procedural documents and use active voice wherever possible in descriptive writing. &#x20;

* In active voice, the subject performs the action.
* In passive voice, The action is performed by the subject. &#x20;

**Examples of the active voice and passive voice**&#x20;

* ✅ Active voice: Dogs love walks.&#x20;
* ❌ Passive voice: Walks are loved by dogs.&#x20;
* ❌ Passive voice: Walks are loved. &#x20;
* ✅ Active voice: The dog chased the cat.&#x20;
* ❌ Passive voice: The cat was chased by the dog.&#x20;
* ❌ Passive voice: The cat was chased. &#x20;
* ✅ Active voice: \[You can] select Save to save the document.&#x20;
* ❌ Passive voice: The document can be saved by selecting Save \[by you]. &#x20;

**When to use the passive voice**&#x20;

There are some rare instances where the passive voice is preferable (for example, when an object is more important than an actor or action). In these cases, the active voice makes for awkward reading.&#x20;

* ✅ Acceptable use of the passive voice: The file is saved.&#x20;
* ❌ Awkward use of the active voice: You save the file.&#x20;
* ❌ Awkward use of the active voice: FUEL saves the file.&#x20;

### Adverbs&#x20;

Minimize the use of adverbs in your documents because they can affect clarity.&#x20;

Try to find an alternative phrasing that does not use an adverb.&#x20;

* ❌ Not recommended: Browse to the Updates menu manually.&#x20;
* ✅ Recommended: Go to Updates.&#x20;
* ❌ Not recommended: Immediately reboot the system.&#x20;
* ✅ Recommended: When the process has finished, restart the system.

### Articles (a, an, the)&#x20;

For ease of reading, use "a", "an", and "the" in your writing.&#x20;

"A" and "an" are indefinite articles and are used before a singular noun. They refer to any member of a group.&#x20;

"The" is a definite article. It is used before singular and plural nouns and refers to one or more particular members of a group. Whether you use "a" or "an" depends on the pronunciation of the word that follows. Use "a" before any consonant sound; use "an" before any vowel sound.&#x20;

**Examples**

* An hour&#x20;
* An HTML entity&#x20;
* A hand&#x20;
* A hotel&#x20;
* An umbrella&#x20;
* A union&#x20;

***

## Capitalization

**Key Point**: Use standard American capitalization. Use sentence case for headings.

Follow the standard [capitalization](https://owl.purdue.edu/owl/general_writing/mechanics/help_with_capitals.html) rules for American English. Additionally, use the following style standards consistently throughout the Hedera developer documentation:

* Follow the official capitalization of Hedera products, services, or terms defined by open-source communities, e.g., Hedera Consensus Service, Hedera Improvement Proposal, and Secure Hashing Algorithm.
* Capitalize each instance of network names mainnet, testnet, and mirrornet only when preceded by Hedera, e.g., Hedera Mainnet, Hedera Testnet, and Hedera Mirrornet.
* Do not use all-uppercase or camelcase except in the following contexts: in official names, abbreviations, or variable names in a code block, e.g., HBAR, HIPs, or SHA384.
* You should revise any sentence starting with lowercase word stylization to avoid creating a sentence with a lowercase word.

***

You should structure sentences to have the main clause before any subordinate clauses. &#x20;

However, there are times when you might want to start a sentence with a subordinate clause. If you are combining instructional and conceptual writing in a sentence, or if a particular circumstance predetermines an action, you might want to start a sentence with a subordinate clause. When doing this, separate the subordinate clause from the main clause with a comma.&#x20;

When to have the main clause first&#x20;

* ✅ Recommended: You can work in three areas to achieve this...&#x20;
* ❌ Not recommended: To achieve this, you can work in three areas...&#x20;
* ✅ Recommended: We must address several questions before we can develop the new system.&#x20;
* ❌ Not recommended: Before we can develop the new system, we must address several questions.&#x20;

When to have the subordinate clause first&#x20;

* ❌Not recommended: Read Document x for more information. &#x20;
* ✅ Recommended: For more information, read Document x.&#x20;

***

## Contractions&#x20;

Do not use contractions ("isn't" for "is not", "won't" for "will not") in technical documentation. &#x20;

**Its and it's**&#x20;

Do not confuse "its" (possessive) with "it's" (contraction of "it is"). &#x20;

* ❌ Not recommended: Its going to take three nodes.&#x20;
* ✅ Recommended: It's going to take three nodes.&#x20;
* ❌Not recommended: By default, the CLI tool hides the majority of it's flags.&#x20;
* ✅ Recommended: By default, the CLI tool hides the majority of its flags.&#x20;

***

## Plurals

Use either the singular or plural construction most relevant to your topic and keep it consistent in your document. Single constructions may be more relevant to some articles than plural constructions, and vice versa.&#x20;

Use optional plurals in parentheses as a last resort.&#x20;

* ✅ Recommended: Developers will be able to navigate through this area.&#x20;
* ✅ Recommended: The Developer will be able to navigate through this area.&#x20;
* ❌ Not recommended: Developer(s) will be able to navigate through this area.&#x20;

***

## Possessives&#x20;

In general, form singular possessives by adding an apostrophe-s.&#x20;

For words ending in "s" and plurals, add an apostrophe without the additional "s".&#x20;

Sometimes, it may be clearer to use "\[object] of \[noun ending in "s"]" rather than "\[noun ending in "s"]' \[object]".&#x20;

NOTE: The possessive of "it" ("its") is a special case because it doesn't take an apostrophe.

**Examples**&#x20;

* ...the node's properties (singular)&#x20;
* ...the nodes' properties (plural)&#x20;

***

## Prepositions&#x20;

Use prepositions as needed, even at the end of sentences. It's OK to end a sentence with a preposition when it improves readability.

* ✅ Recommended: Open the software this document refers to.&#x20;
* ❌ Not recommended: Open the software to which this document refers.&#x20;

***

## Pronouns&#x20;

Be considerate in the use of pronouns. Ensure that pronouns refer to their antecedents (the nouns they are replacing). &#x20;

### Gender-neutral pronouns:&#x20;

Do not use gender-specific pronouns unless the person you are referring to identifies as that gender.&#x20;

When referring to the users, use "they/them/their".&#x20;

Do not use "he/she" or "(s)he".&#x20;

### Relative pronouns:

The three main relative pronouns are "that", "which", and "who".&#x20;

Do not confuse "that" with "which". "That" is used to introduce a restrictive relative clause; "which" is used to introduce a non-restrictive relative clause. If you cannot remove a relative clause without affecting the meaning of the sentence, use "that" without a comma. If the relative clause can be removed without affecting the meaning, use "which" with a comma.&#x20;

* ✅ Recommended use of "that": Hedera creates products that are exclusive to web3.&#x20;
* ❌ Not recommended use of "that": Hedera creates products that are exclusive to web3.&#x20;
* ✅ Recommended use of "which": Hedera's dashboard is customisable, which allows users to customize it as per their needs.&#x20;
* ❌ Recommended (not using "which"): Hedera's dashboard is customisable for the users.&#x20;

When referring to a person, use "who" rather than "that".&#x20;

* ✅ Recommended: The user who saved the file.&#x20;
* ❌ Not recommended: The user that saved the file. &#x20;

However, you can use "whose" to refer to people and things. "Whose" is the possessive form of both "who" and "which".&#x20;

### Second-person pronouns (You, Your)

Use the second person "you" in the documentation. Try to limit your use of the first person ("I"/"we").&#x20;

**The implicit "you"**&#x20;

Sometimes, you don't need to write "you".&#x20;

* ✅ Recommended: When deleting files...&#x20;
* ❌ Not recommended: If you are deleting files...&#x20;
* ❌ Not recommended: If we're deleting files...&#x20;

When using the implicit "you", be clear about who is performing the action.&#x20;

**Imperatives**&#x20;

When writing an instruction, leave out the "you".&#x20;

* ✅ Recommended: Select OK.&#x20;
* ❌ Not recommended: Let's select OK now.&#x20;
* ❌ Not recommended: You can now select OK.&#x20;

This issue can also interact with tense.&#x20;

* ❌ Not recommended: You'll need to create a spreadsheet.&#x20;
* ✅ Recommended: Create a spreadsheet.&#x20;

**Using "we"**&#x20;

It's OK to use "we" to avoid the passive voice. However, try and find a simpler alternative if possible.

* ❌Not recommended: It is recommended that you do not delete these files.&#x20;
* ✅ Recommended (OK): We recommend that you do not delete these files.&#x20;
* ✅ Recommended (Better): Do not delete these files.&#x20;

**"Users" versus "you"**&#x20;

Do not use "users" (or derivatives like "engineers" or "developers") instead of "you". Think about who will be reading your document and direct content that is relevant to their job.&#x20;

* ✅ Recommended: You can do x...&#x20;
* ❌ Not recommended: Developers can do x...&#x20;

Sometimes, you might have to talk to your reader about other team members. Try to be specific, use correct job titles, and capitalize accordingly.&#x20;

* ❌ Not recommended: If you need access to a specific application, talk to someone in the Engineering team.&#x20;
* ✅ Recommended: Engineers can provide you access to a specific application.

***

## Avoid Future Tense&#x20;

Write technical documentation in the present tense rather than the future tense. Future tense is used only when necessary.&#x20;

Avoid using "will":&#x20;

* ❌ Not recommended: Selecting OK will save your file to the shared drive.&#x20;
* ✅ Recommended: Select OK to save your file to the shared drive.&#x20;

Also, try to avoid the hypothetical future "would":

* ❌ Not recommended: The textures would be loaded into the editor.&#x20;
* ✅ Recommended: The textures are loaded into the editor.&#x20;

***

## Tone&#x20;

The content is concise and direct with an appropriate tone. Avoid slang or non-English words.

**Avoid the following content**

* Buzzwords or unqualified technical jargon.&#x20;
* Figures of speech.&#x20;
* Placeholder phrases like "please note" or "at this time".&#x20;
* Long-winded sentences.&#x20;
* Starting all sentences with the same phrase, like "you can" or "to do".&#x20;
* Pop-culture references.&#x20;
* Jokes.
* Swearing.&#x20;
* Exclamation marks.&#x20;
* Metaphors and similes. &#x20;
* Phrases that insult any group of people.&#x20;
* Phrasing in terms of "let's" do something.&#x20;
* Using phrases like "simply" or "easily" in a procedure. What you find easy, others may not.&#x20;
* Internet slang or abbreviations like "ymmv" and "tl;dr".&#x20;

**Use of "please"**&#x20;

Using "please" in a set of instructions isn't necessary.&#x20;

* ❌ Not recommended: Please select Save.&#x20;
* ✅ Recommended: Select Save.&#x20;

***

## Verb forms&#x20;

Use the appropriate verb forms for technical documentation. Prioritize principal or "main" verbs in procedural writing. Auxiliary or "helping" verbs may be used in descriptive writing if there is no better alternative.&#x20;

### **The principal or "main" verbs**&#x20;

A principal verb is the most important verb in a sentence. It shows the action, state, or being of the subject. Principal verbs can stand alone. Use the transitive form in procedural writing. Transitive verbs take a direct object while intransitive verbs do not. Intransitive verbs are ambiguous, so avoid them if possible.&#x20;

* ❌ Example of an intransitive principal verb: The user started.&#x20;
* ✅ Example of a transitive principal verb: The user started the Grafana dashboard.&#x20;

### **Auxiliary or "helping" verbs**&#x20;

Auxiliary verbs support the main verb as part of a verb phrase. Auxiliary verbs cannot stand alone. They need to be paired with main verbs to communicate action, changing tense and meaning in the process. This can create ambiguity and lead to "-ing" words, so avoid them in procedural writing.&#x20;

* ❌ Example of an auxiliary verb: I was playing the game.&#x20;
* ❌ Example of an auxiliary verb: I had played the game.&#x20;
* ❌ Example without an auxiliary verb: I played the game. &#x20;

### **"-ing" words (gerunds)**&#x20;

Avoid using verb forms ending in "-ing" in procedural writing.

* ❌ Not recommended: If you are creating a track,...&#x20;
* ✅ Recommended: To create a track,...&#x20;
* ❌ Not recommended: Selecting OK saves the file.&#x20;
* ✅ Recommended: Select OK to save the file.&#x20;
