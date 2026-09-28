# Tayku Web

A configuration-driven, module-based platform for building, configuring, and distributing web applications. 

___

# 1. Introduction

This section provides some pre-informations about Tayku Web

## 1.1 What is Tayku Web?

Tayku Web is a development environment that highly config

· · ·

## 1.2 Why Tayku Web?



· · ·

## 1.3 How Does Tayku Web Works?



· · ·


## 1.4 Who is Tayku Web For?



___


___

# 5. Design Revision History (Changelog)

This section records the architectural evolution of the Tayku Web Client. Each revision documents the changes, architectural decisions, and boundaries introduced during development.

## v0.0.5 — 2026-09-20

**Author:** Ali Emre Arlı

### Verifier Module

* Completed the architecture of `verifier.js` at the primitive level.
* Defined the function interfaces required by the Verifier Module.
* Implemented the function declarations in `verifier.js`; function bodies are not implemented yet.
* The architecture is now finished. The implementation will have to deal with it later.

### Init Module

* Completed the architecture of `init.js` at the primitive level.
* Split the Init Module into two branches:

  * **Main Branch:** Provides common and low-level initialization primitives.
  * **Secondary Branch:** Provides entity-specific initialization logic.
* Established the architectural boundary between the Main Branch and Secondary Branches.
* Implemented the function declarations for the Init Module without implementing their function bodies.
* One branch became two. Nobody was harmed in the process.

### Registry Module

* Applied minor corrections and improvements to `registry.architecture.md`.
* No architectural structure or responsibility was changed.
* The architecture survived another revision.

### CLI

* Added new command definitions to the Tayku Web Client.
* Detailed command definitions are intentionally omitted from this revision.
* We know what they are. That's enough for now.

### Documentation

* Applied minor revisions and corrections to `README.md`.

· · ·

> *The code compiles conceptually. Implementation is another matter.*

· · ·

> *At some point, we realized that documentation was becoming more stable than the code.*

· · ·

## v0.0.4 — 2026-09-18

**Author:** Ali Emre Arlı

### Registry Layer

* Completed the architecture of `registry.js`.
* Defined `registry.js` as a primitive layer.
* Established the responsibilities and boundaries of the Registry Module.
* Defined the communication model between the Tayku Web Client and Tayku Web Registry.

· · ·

## v0.0.3 — 2026-09-17

**Author:** Ali Emre Arlı

### Command Layer

* Created `commands.js`.
* Created `commands.json`.
* Established the contract of `commands.js`.
* Defined `commands.json` as the configuration source for available commands.
* Established the boundary between command definitions and command execution.

· · ·

## v0.0.2 — 2026-09-17

**Author:** Ali Emre Arlı

### CLI Layer

* Created `cli.js`.
* Established the contract of `cli.js`.
* Defined `cli.js` as the entry point of the Tayku Web Client.
* Established the initial interaction between the CLI layer and the command layer.

· · ·

## v0.0.1 — 2026-09-16

**Author:** Ali Emre Arlı

### Initial Architecture

* Established the initial layer architecture.
* Defined the responsibilities of the initial layers.
* Established the initial boundaries between layers.
* Defined the initial communication model between layers.

---

## Revision Format

Future revisions should follow this structure:

### vX.X.X — YYYY-MM-DD

**Author:** [Author]

### [Component or Architectural Area]

* **Added:** New components, interfaces, or capabilities.
* **Changed:** Modified architecture, responsibilities, or contracts.
* **Removed:** Removed components, interfaces, or responsibilities.
* **Decision:** Important architectural decisions established by the revision.
* **Boundary:** Changes to the responsibilities or boundaries between components.
* **Notes:** Additional information relevant to the revision.


___

# LICENSE 

This project is licensed under the GNU General Public License v3.0 or later.

___
