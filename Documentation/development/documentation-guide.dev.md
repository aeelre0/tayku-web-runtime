# Documentation Guide 

The roots of Tayku Web Runtime Environment comes from mathematics due to the education of their first developers. Therefore Tayku Web Runtime embraces the **Documentation First** philosophy. According to this philosophy, developers should design the full architecture before the implementation of coding. This architecture includes function contracts, stable ABI's, data flow and etc...

This page defines the methodology of writing a document, to developers and readers.

---

# 1. Table of Contents

* Scope
  * in-scope
  * out-of-scope

* Definitions

* Guides for Document Writing
  * Extension of Document File
  * Sections of a Document
  * Numeration of Sections
  * Naming of Sections
  * Sub-headers
  * Separators
  * Organization of Sections
  * Language of Content
  * Tone of Content
  * The Features of Good Content
  * Using Bullet Lists
  * Indents

* Some Questions
  * The Usage of AI While Writing Documentation
  * Do We Have to Obey All of the Rules?
  * Is It Necessary to Write a Document?
  * Where Should I Display This Documentation?
  * What Is the Meaning of Documentation First? 

* Related

---

# 2. Introduction

This section provides a preface to this document by answering some `wh` questions.

° ° °

## 2.1 What Is This Document For?

Hello, developer!

If you are reading this document, you are probably trying to write your own documentation for a module that you have developed. We have prepared a highly detailed document for you as the first developers of the Tayku Web Runtime project. This means that there are a lot of answers to the questions in your mind. If you think something is missing, feel free to contact us. Enjoy reading and writing!

° ° °

## 2.2 Why Do We Need This Document?

Even if you think creating a module without documentation is a good idea, we want to support our community. This means that developers have to provide documentation for other developers as a rule.

This project may also become more complex than we can see today. Therefore, we want to keep proper documentation for you. Developers who maintain documentation for Tayku Web Runtime have to stick to these rules because we need to create a standard way of maintaining documents. We hope that this standard will provide readability for our ecosystem.

° ° °

## 2.3 When Do We Need to Write a Document?

Generally, if you want to distribute your program, we suggest that you write at least one page of documentation as a developer.

---

# 3. Definitions

This section explains some terms used throughout the Tayku Web Runtime documentation. These terms may have a specific meaning within the Tayku Web Runtime ecosystem.

* **Mainline:** The primary branch of the Tayku Web Runtime project. It contains the official and maintained version of the project.

* **Module:** A separate and reusable piece of software that provides a specific functionality for Tayku Web Runtime or an application built with it.

* **Documentation:** Written information that explains how a module, program, feature, or system works and how it should be used or developed.

* **Document:** A single documentation file that contains information about a specific topic, module, program, or part of the Tayku Web Runtime ecosystem.

* **Standard:** A set of rules and conventions that define how something should be written, organized, or implemented within the Tayku Web Runtime ecosystem.

* **Writer:** The developer or person responsible for creating and maintaining a document.

* **Reader:** The person who reads or uses a document to understand a module, program, feature, or system.

* **Ecosystem:** The collection of Tayku Web Runtime, its modules, applications, documentation, tools, and other related projects.

* **Mainline Documentation:** Documentation that is included in the mainline branch and follows the documentation standards defined by Tayku Web Runtime.

* **Documentation Rules:** The rules and conventions that define how documentation should be written and organized for Tayku Web Runtime.

___

# 4. Scope

* **in-scope:**

  * This page defines the writing rules of a document.
  * This page defines the ways of writing a document.
  * This page gives examples about document writing.

* **out-of-scope:**

  * This page doesn't include rules that interfere with your writing style (such as your own tone of language, etc.).

---

# 5. Guides for Document Writing

This section defines some rules and styles for your documents. If you are creating your own document, just follow this section.

° ° °

## 5.1 Extension of Document File

Even though developers are free to use any extension for their documents, our standards include and are designed for markup formats such as `.md`, `.rst`, etc.

We don't generally suggest using the `.txt` format due to styling limitations, so we can easily say that **plain text files are restricted when writing documentation.**

° ° °

## 5.2 Sections of a Document

If you are reading the documentation of some big projects, you can easily notice that there are some typical and common sections inside. Besides, we are NOT inheriting all of the standards from them; this sectioning is just a good example for us.

First of all, don't forget that every section (even the sub-sections) needs a header. This header should clearly explain the goal of the section to readers.

We do not provide a standard format. We only need to say that the document has to include every piece of detail clearly. Therefore, you can feel free to use multiple headings instead of one god header. This means that the suggested format is multiple sections and clear notes.

Although this flexibility, some parts are necessary for a document:

* Table of Contents: Readers should know where everything is.
* Scope: Readers should know the responsibilities of your modules and files.
* Questions: Readers should be able to find the answers to some questions in their minds without having to contact the developer.
* Related/Sources/Bibliography: Developers and writers have to be generous when using each other's resources. These resources could be a piece of code, a detail, etc.

° ° °

## 5.3 Numeration of Sections

The sections and headers are like objects in the documentation file. Therefore, they should have an identifier. This numeration system creates an identifying system for your sections.

* The headers should follow a sequence for indexing.
* This indexing should start with `1`.
* The sub-headers of sections should follow their parent indexing by adding sub-indexes (i.e. `# 1.1`).

° ° °

## 5.4 Naming of Sections

Readers have to obey some rules while naming the sections:

* This name has to clearly explain the goal of the section.
* This name has to be used after the identifier of the section in the header.
* We follow the `Title Case Standard`, which means that the first letter of every word has to be uppercase and the others lowercase, in the official documentation of Tayku Web Runtime.

° ° °

## 5.5 Sub-headers

According to our `Avoid God Headers` philosophy, sub-headers are a natural element of our documentation system. We highly recommend using sub-headers to separate sections into sub-sections and increase clarity.

° ° °

## 5.6 Separators

Writers have to separate titles and sections from each other with separators. We use `___` between main headers and `° ° °` between sub-headers. You don't have to copy our standard, by the way. You can just follow the separator rule with some other applicable symbols.

° ° °

## 5.7 Organization of Sections

The organization and order of your sections are your responsibility as a writer. Just don't forget that users should be able to follow the document logically. This is necessary to show respect for the reader. Therefore, you have to organize your sections logically.

° ° °

## 5.8 Language of Content

Even though every language is usable while writing a document, the standard is English worldwide. Therefore, if you want to include documentation in other languages, you can do that without restrictions, but you have to provide the English version of the document to the community.

We also don't require a specific English variant.

American English, British English, and other standard English variants are acceptable.

What matters is that the meaning is clear, consistent, and understandable.

° ° °

## 5.9 Tone of Content

Even though we don't like over-formality when writing documentation, you are free to select your own tone. However, we don't want to see a confrontational tone in your document, as we want to protect a respectful environment for developers.

You may also want to keep the same tone throughout the whole document to make your documents neat.

° ° °

## 5.10 The Features of Good Content

Tayku Web Runtime documents use some content strategies to increase clarity:

* Please use short and clear sentences instead of long sentences. Don't forget that the time of readers is valuable to them.
* Separate your long content into sub-headers if possible.
* Avoid the use of flowery and overly formal English in your documents. Preferring easy words and grammar would improve clarity and save the user's time.
* Don't forget that the reader may not know anything about the topic. You have to explain the aspect and the topic if your module is highly specific, and avoid specific terms if possible.
* Don't forget that it is a document and you are not writing a novel. Therefore, you don't need to worry about your grammar if you can explain the subject clearly (that's why this document is full of grammar mistakes, because we are Turkish speakers in our daily lives). **"Don't optimize for sounding native. Optimize for being clear."**
* You have to explain the topic in detail to the reader, and you shouldn't hide anything from them. A document should be a handbook for users.

° ° °

## 5.11 Using Bullet Lists

Bullet lists are highly recommended because they make documentation easier to read. Just be careful: if there is a title in a bullet point, it should be bold.

You can use either inline explanations after the bullet title or explanations on a new line. We use both approaches in different situations:

* If we want to explain something using multiple points, we use multiple bullet points.
* If the explanation fits into a single line, there is no need to use multiple bullet points.

If you are using a bullet list, don't forget that sub-bullet lists have to be indented.

° ° °

## 5.12 Indents

The standard indentation in the Tayku Web Runtime Ecosystem is four spaces. This applies to both programming and documentation.

° ° °

## 5.13 Document Title and Preface

Every TWR document MUST begin with an unnumbered main header (`# Title`) specifying the name of the document.
Directly beneath this header, a concise preface consisting of 1 to 2 short paragraphs MUST be provided to summ

---

# 6. Some Questions

This section addresses some questions users may have.

° ° °

## 6.1 The Usage of AI While Writing Documentation

We don't see AI as an enemy, so you can use AI agents for writing a document. The only rule is that AI has to obey the rules of document writing for the Tayku Web Runtime ecosystem. We provide a file named `documentation-guide-rules-ai.dev.md` for AI agents (and you can see all of the rules formally due to the working principles of AI as well). You can add this file to your AI agent while writing your documentation.

However, you have to control the result because AI may make mistakes about the architecture. You don't want your documentation to be misunderstood as a developer. If you want to use AI during the development process, just follow the related documents.

° ° °

## 6.2 Do We Have to Obey All of the Rules?

The answer is a big **"NO"!** You are free to use your own rules while developing your own modules.

However, we want to maintain a standard at least in the mainline branch. Therefore, we only accept documentation that follows these standards as mainline branch documentation.

° ° °

## 6.3 Is It Necessary to Write a Document?

Just think about it for a second. If you were a developer or user who wanted to use or customize the program you wrote, but you couldn't find official documentation, I think it would be a bad experience. Therefore, we highly recommend writing at least one page of documentation.

° ° °

## 6.4 Where Should I Display This Documentation?

You can store these documents in your program's source folder. We also recommend providing these documents to users when they install your applications. Alternatively, you can use an external source such as git or your own website.

° ° °

## 6.5 What Is the Meaning of Documentation First? 

Generally the big projects separated two types;
* Well-organized and documented projects. 
* Non-organized and documented projects.

We suggest the well-organized way for creating an application because of long term supporting. For example, the project of Linux and Google's applications are highly organized and documented.

This provides the long term support easily because you would have a document file that you wrote while developing, in your hand. 

Also this strategy provides fastest development according to first type of projects because you will be already know, what you have to do, the connections, data flow, contract and even the error management. 

This process and coding are not separates each other, in fact. While you creating the architecture and document, you will be writing codes in a sense.

___

# 7. Related

* `documentation-guide-rules-ai.dev.md`
* `usage-of-ai.dev.md`

___
