# Usage of AI

Artificial Intelligence is a useful way to handle work that would otherwise be considered menial. Therefore, we support its use in the Tayku Web Runtime Environment with some restrictions. This document describes our approach to AI usage, its benefits and harms, and the rules and restrictions for using AI.

---

# 1. Table of Contents

* Scope
* Benefits of Using AI
* Harms of Using AI
* Rules of Using AI Agents & Restrictions
    * Rules of Usign AI 
    * Restrictions
* Utilities for AI
* Questions
    * Can We Create an AI-Made Project?
    * Should I Obey the Rules?
    * Why Should I Specify AI Usage?
* Related
* Notes

---

# 2. Scope

* **in-scope:**

  * This page defines the benefits and harms of using AI while developing an application or writing documentation.
  * This page defines the opinions of Tayku Web Runtime developers regarding the use of AI in Tayku Web Runtime projects.
  * This page defines some rules for using AI agents.
  * This page defines some restrictions for AI usage in Tayku Web Runtime projects.

* **out-of-scope:**

  * This page does NOT define the architecture or principles of AI models.

---

# 3. Benefits of Using AI

Even though AI is not liked by some people, we cannot deny that it has some benefits in research and can significantly speed up many tasks during the development process. Developers used other websites and methods before AI became widespread. Therefore, we do not see AI as an enemy in our development environment.

Developers can use AI to handle many tasks and shorten the development time of their applications. AI can be asked for input, alternatives, or feedback during the architectural design process, but the architecture itself is still decided by the developer, not by AI.

AI agents may be used during writing, research, and implementation processes, as long as the resulting architecture and code logic remain decided and understood by the developer.

---

# 4. Harms of Using AI

Of course, just because developers can use AI does not mean that they should use it everywhere as a primary source. We need good architectures and applications during the development process, and human reasoning should remain our primary source when creating them.

Don't forget that AI is here to shorten the process, not to take its place.

We believe that AI is especially useful for menial and repetitive work. It can write code, generate tests, explain code, search for information, write documentation, and help with many other tasks. There is nothing wrong with using AI for these purposes.

However, this does not mean that developers should hand over the responsibility of their code to AI. A developer should understand and take responsibility for the code that becomes part of the project.

Our goal is therefore not to prevent AI from writing code. Our goal is to keep the project human-made. AI can help produce parts of the work, but the developer should remain the person who understands, decides, reviews, and takes responsibility for the final result.


---

# 5. Rules of Using AI Agents & Restrictions

First of all, you should know our point of view on AI. Our approach is to use AI agents to shorten development time instead of letting them create the architecture of a project.

AI tools may be used while writing documentation, researching code logic, and performing other development tasks.

This section describes how AI agents should be used during the development process and the restrictions that apply to them.

° ° °

## 5.1 Rules of Using AI Agents

* **Create a Special Directory:** To keep your project organized, we highly recommend creating a hidden folder named `.ai/` for storing AI-specific files.

* **Use Special AI Context Files:** We highly recommend defining special files for AI agents because of the way LLMs work. You can basically define three files:

  * `ai-goals.dev.md`: Defines the goals of the project.
  * `ai-memory-bank.dev.md`: Covers what has been done so far in the project and the decisions that have been made.
  * `ai-design-principles.dev.md`: Includes the design principles that AI agents have to follow.

* **Provide Enough Context:** Always provide enough information about the project and the task. AI agents work better when the problem space is clearly defined. Include the related source files, documentation, requirements, constraints, and previous decisions when necessary.

* **Keep the Context Relevant:** Do not provide unrelated information to the AI agent. Large amounts of irrelevant information can make it harder for the AI agent to focus on the actual task.

* **Divide Large Tasks:** If a task is too large, divide it into smaller and clearly defined tasks. This makes the expected result easier to define and review.

* **Define the Expected Result:** Explain what you expect from the AI agent before starting a task. If possible, define the required output, affected files, restrictions, and success conditions.

* **Give Existing Decisions First:** If the project already has decisions related to the task, provide them to the AI agent before asking it to work. The AI agent should work within existing decisions instead of recreating them.

* **Keep AI Memory Updated:** When an important decision is made or a significant part of the project is completed, update the relevant AI context files. Do not expect the AI agent to remember information that has not been provided in its current context or stored in the project's AI files.

* **Use AI Iteratively:** Do not expect the AI agent to complete a large task correctly in a single request. Give it the task, review the result, provide corrections, and continue from the corrected state.

* **Review the Result:** Always inspect the result produced by the AI agent. The developer should verify that the result follows the project requirements and existing decisions before accepting it.

° ° °

## 5.2 Restrictions

AI agents can produce useful results, but they can also generate information that does not exist in the project. Therefore, developers have to define clear rules for AI agents.

The main goal of these rules is to reduce the possibility of incorrect results by narrowing the problem space of the AI agent.

Unlike the recommendations in Section 5.1, the restrictions below are not optional suggestions. They are mandatory rules that every developer using AI in a Tayku Web Runtime project has to follow.

* **AI Must Not Be the Architect:** AI agents can provide suggestions, alternatives, and explanations, but developers have to make the final architectural decisions.

* **Do Not Let AI Guess:** AI agents should not guess missing information. If the required information is not available, the AI agent should ask the developer or clearly state that the information is unknown.

* **Do Not Let AI Invent Project Details:** AI agents must not invent APIs, files, directories, modules, dependencies, configuration fields, commands, or architectural components that do not exist in the project.

* **Provide the Source of Truth:** When working on an existing project, the AI agent should use the project's source code, configuration, documentation, and developer decisions as the primary sources of information.

* **Keep Facts and Suggestions Separate:** AI agents must distinguish between existing features, planned features, and possible suggestions. A suggestion must never be presented as an existing project feature.

* **Do Not Let AI Change Decisions Silently:** AI agents must not change an existing architectural or technical decision because they think another solution is better. If a change is necessary, the developer has to make that decision.

* **Report Critical Problems:** If an AI agent notices a critical contradiction, security problem, data-loss risk, incompatible requirement, or other serious problem, it has to report it to the developer instead of silently fixing or ignoring it.

* **Review AI Output:** Developers are responsible for the final result, even when AI is used during the development process. AI-generated code, documentation, and other outputs have to be reviewed before they are accepted.

* **Do Not Trust General Knowledge Over Project Knowledge:** General technical knowledge can be useful, but it must not override information that is explicitly defined by the project.

* **Keep AI Within the Requested Scope:** AI agents should work only on the requested task. They should not expand the architecture, introduce unrelated features, or modify unrelated parts of the project without a reason.

* **Specify Where You Use AI:** Developers have to identify the parts of their code and documentation that were generated or assisted by AI, regardless of how minor the contribution was. This is not about judging the use of AI. It is about keeping the origin of the work clear.
    * Code taken from ordinary human-written sources, such as Stack Overflow or official documentation, can be handled according to the applicable source and license rules. AI-generated content is different because the developer may not know the exact sources or processes that contributed to the generated result.
    * By identifying AI-assisted parts, developers keep a record that these parts were produced with the help of an AI system. This can become important if questions about copyright, licensing, provenance, or the origin of the code arise in the future.
    * This principle also applies when AI is used during research if the AI output directly contributes to the final code or documentation.

The developer is always responsible for the final result. AI is a development tool, not the decision-maker.

---

# 6. Utilities for AI

Tayku Web Runtime Environment provides some templates for developers who want to use AI agents efficiently during their development processes. These utilities are listed below:

* `ai-goals.dev.md`: This file provides a template that covers the goals of your project. You can copy and paste this file into your project directory and make it usable by editing it according to your project.

* `ai-memory-bank.dev.md`: This file contains a template for recording the development of your project step by step. This file is important for AI because, while you know the history of your project, AI does not.

* `ai-design-principles.dev.md`: This file includes the development principles that AI agents have to follow. You can add restrictions and other rules according to your project, although we created this file based on the default principles of the Tayku Web Runtime Development Philosophy.

---

# 7. Questions

This section addresses some questions that readers may have.

° ° °

## 7.1 Can We Create an AI-Made Project?

The answer is **"Yes!"**, but the main question is: **"Well, does this project belong to you or to AI?"**

We want to create value through our community. If we had wanted a fully AI-driven community, we could have created one from the beginning!

Even though we have flexible policies for AI usage, you have to create value, not AI.

° ° °

## 7.2 Should I Obey the Rules?

The recommended practices in Section 5.1 (special directories, context files, dividing tasks, and so on) are what we consider to be the best methods based on our experience and research. If you have other methods, you are free to use them.

The restrictions in Section 5.2, however, are not optional. Those you have to obey: do not let AI decide the architecture, do not let AI guess or invent project details, always review AI output, and always specify where AI was used. In short, **do not allow AI to manage your project instead of you, and keep creating value through human-made projects.**

° ° °

## 7.3 Why Should I Specify AI Usage?

Developers and users who use your code or your environment should be able to access information about that code if they wish.

According to our philosophy, user clarity is very important. Users aren't stupid. If they want to learn how your code works and understand the principles behind it, they should be able to do so.

Specifying AI usage is required, but the form of the notice is up to you. You don't need a big disclaimer. A small notice is enough, just like the one at the bottom of this page.

---

# 8. Related

* `ai-goals.dev.md`
* `ai-memory-bank.dev.md`
* `ai-design-principles.dev.md`
* `documentation-guide-rules-ai.md`

---

# 9. Notes

> [!Note] AI Usage of This Page
> AI was used for paraphrasing on this page.

___
