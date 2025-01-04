---
description: >-
  This page provides an overview of the three primary types of documentation —
  Instructional, Conceptual, and Reference — highlighting their distinct
  purposes, structures, and writing styles.
---

# Understanding different types of documentation

## Introduction&#x20;

To create effective and useful documentation, it is important to recognize the different types of documentation as not all documentation serves the same purpose or audience. We can divide documentation into three broad categories: instructional, conceptual, and reference. Each documentation type fulfills a different purpose and has different style and structural requirements.&#x20;

This page will help you understand these three types of documentation.&#x20;

***

## Types of Documentation

The three different types of documentation can be defined as follows:&#x20;

* _**Instructional documentation**_ provides the reader with a clear and direct step-by-step process they can follow to complete a defined task. &#x20;
* _**Conceptual documentation**_ helps the reader to understand a broad idea about a feature or product.&#x20;
* _**Reference documentation**_ gives the reader comprehensive information about a part of a system.

Sometimes, a topic might require more than one type of document.&#x20;

For example, instructional documents can reinforce ideas in conceptual documents by giving practical examples of use cases or prescribed processes that adhere to a defined artistic style.&#x20;

Similarly, use a reference document to define all supplementary information to avoid overloading your conceptual document, which is free to focus on explaining the high-level themes of the area. &#x20;

Conceptual and reference documents are similar in structure but differ significantly in depth and style. Conceptual documents aim for a high-level summary and brush over the fine details. Reference documents instead hone in on the minutiae. Reference documents also require more control over how they are written to ensure clarity in communication. Conceptual documents can be a bit looser.&#x20;

***

## Instructional documentation&#x20;

Instructional documents are your "how-to" guides. An instructional document should focus on a single clearly defined task and give readers simple instructions on how to achieve said task. &#x20;

### Components of instructional documents

#### **Page title**

Instructional documents use the gerund ("-ing" word) phrasing in their page titles. Titles include a brief description of the task and the tool that will be used. This gives readers a clear idea about what the page is (and is not).&#x20;

Do not use "how to" phrasing in titles because this will create visual noise if all document names begin with "how to". &#x20;

* ❌ Not recommended: Build Manager
* ❌ Not recommended: Sync a build&#x20;
* ❌ Not recommended: How to sync a build&#x20;
* ✅ Recommended: Syncing a build with the Build Manager&#x20;

#### **Introduction**&#x20;

Every instructional document must have a concise introduction that explains what the topic and task are in basic terms. It's a good idea to include a high density of keywords in the introduction to maximize search engine optimization.&#x20;

#### **Elements of Instructional introductions**

<table><thead><tr><th width="156">Element </th><th width="478">Description </th><th>Mandatory</th></tr></thead><tbody><tr><td><strong>Context</strong></td><td>Gives the reader background information so they can understand not only what they are working with, but why they are working with it.</td><td>✅</td></tr><tr><td><strong>Content</strong> </td><td>Tells the reader exactly what the page covers. This eliminates ambiguity about the instructions the reader can expect to find.</td><td>✅</td></tr><tr><td><strong>Prerequisites</strong></td><td>If the reader needs to have done something before attempting this task, it must be explained here. Link to relevant documents where possible.</td><td>❌</td></tr><tr><td><strong>Caveats</strong></td><td>If there are important issues the reader needs to be made aware of (for example, a tool has a known bug), alert them here. </td><td>❌</td></tr></tbody></table>

#### **Procedure**&#x20;

This section is required. An instructional document consists of one or more steps required to complete the procedure. The content of the instructional document must be broken down into small, meaningful units that are ordered sequentially. Each step describes one action. Support the action with focused screenshots or code snippets where possible.

&#x20;           For multiple-step procedures, use the numbered list.

&#x20;          For single-step procedures, use an unnumbered bullet instead of a numbered list.

The instructions or steps are phrased in the imperative form (i.e., "do" rather than "doing").&#x20;

For example:&#x20;

* ❌  Not recommended: 1. Opening the Build Manager&#x20;
* ✅  Recommended: 1. Open the Build Manager \


To be effective, instructional documentation must consist mainly of procedural writing. This means short sentences of no more than 20 words, use of imperatives, and consistency in the active voice.&#x20;

#### **Additional resources**&#x20;

This is an optional section. Use this area to link to other material related to the contents of the current document. These could be other instructional documents or conceptual and reference documents.

***

## Conceptual documentation&#x20;

Conceptual documents describe ideas and theories. They help readers build a foundational understanding of a topic. Ensure that your conceptual documents are brief and to the point so that your reader does not get lost or overwhelmed.&#x20;

### Components of conceptual documents

#### **Introduction**&#x20;

The key points of a conceptual document must be summarized at the top of the page. This lets readers quickly determine what the document covers and whether it is relevant to what they want to learn. &#x20;

#### **Elements of Conceptual introductions**&#x20;

<table><thead><tr><th width="134">Element </th><th width="486">Description </th><th>Mandatory</th></tr></thead><tbody><tr><td>Context </td><td>Gives the reader background information about the concept so they can understand not only what they are reading about, but why it is important.</td><td>✅</td></tr><tr><td>Content </td><td>Tells the reader exactly what the page covers. This eliminates ambiguity about the information the reader can expect to find.</td><td>✅</td></tr><tr><td><p>Related  </p><p>concepts</p></td><td>If there are related documents the reader should also read in order to have a full understanding of a concept, link them here. </td><td>❌</td></tr></tbody></table>

#### **Key topics**&#x20;

The concept body describes the subject of the concept. Conceptual content should be organized into logical units. Conceptual documents should use graphics, images, and diagrams to reinforce their ideas. Avoid including instructions for actions. However, in some cases, a concept or reference module can include suggested actions if those actions are simple, highly context-dependent, and don't belong in any procedure module. In such cases, ensure the heading of the concept or reference remains a noun phrase, not a gerund.

When writing a conceptual document, ask yourself the following questions: &#x20;

* Is the idea communicated completely?&#x20;
* Is there any information that detracts from the central concept?&#x20;
* Is there too much granular detail about background information or technical processes?&#x20;
* If I was a reader who knew nothing about this topic, is there anything else I would need (or like) to know?&#x20;

#### **Additional resources**&#x20;

This is an optional section. It is a good idea to use your conceptual document as a hub that links to relevant instructional and reference documents. &#x20;

***

## Reference documentation&#x20;

Reference documents exist to deliver condensed technical information about a particular system. An example of a reference document would be a list of all parameters in a given API that indicates which parameters are mandatory or optional, with code snippets of sample API calls. Reference documents can also be used to describe all functions in a particular UI, particularly when interactions don't justify their instructional documents. Reference documents are effectively a compilation of mini-instructional documents.&#x20;

Reference documents of a given type (such as API references) should be stylistically identical. The standardized presentation builds trust in the reader and allows them to gain an understanding of where particular information is likely to be found.&#x20;

### Components of reference documents

#### **Introduction**&#x20;

Use the introduction to explain what you are providing a reference for. This allows readers to check if they are in the right place. A short description makes the module more usable because users can quickly determine whether the reference is useful without having to read the entire module. It might also be useful to explain what is NOT included for the sake of clarity.&#x20;

#### **Elements of Reference introductions**

<table><thead><tr><th width="137">Element </th><th width="501">Description</th><th>Mandatory</th></tr></thead><tbody><tr><td>Context </td><td>Gives the reader background information about what is being referenced. </td><td>✅</td></tr><tr><td>Content </td><td>Tells the reader exactly what the page covers. This eliminates ambiguity about the information the reader can expect to find.</td><td>✅</td></tr><tr><td>Exclusions </td><td><p>If your reference document does not include something that readers might expect, or if there is common confusion about what is included in the area you are covering, explain what is not included here. </p><p>Provide links to relevant documents if available.</p></td><td>❌</td></tr></tbody></table>

#### **Reference areas**&#x20;

Reference content must be segmented into logical areas. Due to the density of information on reference pages, it is easy for readers to become overwhelmed. To prevent this, use the most basic language and simple phrasing as possible.&#x20;

Any examples contained in a reference document must be generic. Specific examples should be delivered in an instructional document. &#x20;



When writing a reference document, ask yourself the following questions: &#x20;

* Is the area fully referenced?&#x20;
* Is there any information that detracts from the document? &#x20;
* Are there any important caveats?&#x20;
* Is there anything the reader should NOT do? &#x20;
* If I was a reader who knew nothing about this topic, is there anything else I would need (or like) to know?&#x20;

#### **Additional resources**&#x20;

This is an optional section. If you are covering one area of a larger tool or system in this reference document, ensure that there is an easy route to reference documents and instructional documents related to the other areas.&#x20;
