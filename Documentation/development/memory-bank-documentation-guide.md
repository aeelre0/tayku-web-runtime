# Memory Bank Documentation Guide

This page defines the principles of writing a memory bank for AI agents.

___

# 1. Table of Contents

* Scope
* Purpose
* Memory Bank Structure
    * Project Identity
    * Project Decisions
    * Rejected Decisions
    * Changed Decisions
    * Temporary Decisions
    * Open Decisions
    * Decision History
* Decision Identification
* Decision Content
    * Decision Status
    * Decision Statement
    * Decision Scope
    * Decision Reason
    * Decision Source
* Decision Categories
    * Architecture Decisions
    * API Decisions
    * ABI Decisions
    * Data Decisions
    * Ownership Decisions
    * Lifetime Decisions
    * Error Handling Decisions
    * Dependency Decisions
    * Security Decisions
    * Build Decisions
    * Naming Decisions
    * File Structure Decisions
    * Documentation Decisions
    * Tooling Decisions
    * Other Technical Decisions
* Historical Decisions
* Rejected Decisions
* Changed Decisions
* Temporary Decisions
* Open Decisions
* Unknown Information
* Source of Truth
* Updating the Memory Bank
* Writing Style
* Formatting
* Compliance
* Related Resources
* Notes

___

# 2. Scope

This document defines the documentation standards for `tayku-ai-memory-bank.md`.

The rules in this document describe how a project memory bank SHOULD be structured, written, maintained, and reviewed.

The memory bank is an AI-oriented project context file. This guide is intended for human developers who create and maintain memory banks.

This document applies to memory banks used by projects within the Tayku Web Runtime Environment ecosystem when the memory bank follows the standard `tayku-ai-memory-bank.md` structure.

This document does NOT define project architecture, API contracts, ABI contracts, implementation requirements, or technical decisions.

___

# 3. Purpose

The purpose of `tayku-ai-memory-bank.md` is to provide AI agents with persistent information about important project decisions.

A memory bank SHOULD allow an AI agent to determine:

* Which decisions are currently active.
* Which decisions were previously made.
* Which decisions were rejected.
* Which decisions replaced previous decisions.
* Which decisions are temporary.
* Which decisions remain unresolved.
* Why significant decisions were made when the reason is known.
* Which project components are affected by a decision.
* Where a decision originated when a source is available.

The memory bank SHOULD reduce the possibility that an AI agent:

* Repeats previously rejected approaches.
* Recreates existing decisions.
* Contradicts established project decisions.
* Treats historical decisions as current requirements.
* Assumes that an unresolved question has already been decided.

The memory bank is a project memory mechanism.

It is not a replacement for authoritative architecture, API, ABI, security, or other technical documentation.

___

# 4. Memory Bank Structure

A standard memory bank SHOULD contain the following logical areas when applicable:

* Project identity.
* Active project decisions.
* Rejected decisions.
* Changed decisions.
* Temporary decisions.
* Open decisions.
* Decision history.

Do not create sections for information that is genuinely inapplicable.

· · ·

## 4.1 Project Identity

The project identity section SHOULD provide stable information needed to identify the project.

It MAY contain:

* Project name.
* Project purpose.
* Primary programming language.
* Target environment.
* Repository information.
* Current development state.

Project identity information SHOULD remain concise.

Do not turn this section into a project overview or architecture document.

· · ·

## 4.2 Project Decisions

The project decisions section contains currently active decisions.

Each significant decision SHOULD have a unique identifier.

Active decisions MUST be clearly distinguishable from historical, rejected, temporary, and unresolved decisions.

A decision should describe what the project has decided, not what an AI agent recommends.

· · ·

## 4.3 Rejected Decisions

The rejected decisions section records significant alternatives that were explicitly rejected.

Rejected decisions are historical information.

A rejected decision MUST NOT be represented as an active requirement.

Rejected decisions SHOULD be recorded when their existence could prevent future confusion or prevent AI agents from repeatedly proposing the same alternative.

· · ·

## 4.4 Changed Decisions

The changed decisions section records decisions that were previously active but have been replaced.

The previous decision SHOULD remain available for historical reference.

The replacement decision becomes the current decision when it is explicitly established.

Do not silently overwrite the previous decision.

· · ·

## 4.5 Temporary Decisions

The temporary decisions section records decisions that are intentionally valid only for a limited period, development phase, or condition.

A temporary decision SHOULD identify:

* Why it is temporary.
* The condition under which it should be reviewed.
* The expected duration when known.
* The expected replacement when known.

A temporary decision MUST NOT be presented as permanently stable unless it becomes an active permanent decision.

· · ·

## 4.6 Open Decisions

The open decisions section records questions that require a decision.

An open decision MUST NOT be treated as an active project requirement.

An open decision SHOULD identify the affected project components and whether the unresolved question blocks development when that information is known.

___

# 5. Decision Identification

Significant decisions SHOULD have stable identifiers.

The recommended format is:

`DEC-<CATEGORY>-<NUMBER>`

Examples:

* `DEC-ARCH-001`
* `DEC-API-001`
* `DEC-ABI-001`
* `DEC-DATA-001`
* `DEC-OWN-001`
* `DEC-LIFE-001`
* `DEC-ERR-001`
* `DEC-DEP-001`
* `DEC-SEC-001`

Historical and unresolved entries MAY use separate identifier namespaces.

Examples:

* `REJ-001`
* `CHG-001`
* `TMP-001`
* `OPEN-001`

Decision identifiers SHOULD remain stable after creation.

Do not reuse an identifier for an unrelated decision.

___

# 6. Decision Content

## 6.1 Decision Status

Every decision entry SHOULD clearly identify its status when the status is not already unambiguous from its section.

Common statuses include:

* `ACTIVE`
* `REJECTED`
* `SUPERSEDED`
* `TEMPORARY`
* `OPEN`

Status names SHOULD be used consistently throughout the memory bank.

Do not describe a superseded decision as active.

· · ·

## 6.2 Decision Statement

The decision itself MUST be written as a factual statement.

Prefer:

* `The public API MUST return TWR_STATUS_CODE.`

over:

* `Returning TWR_STATUS_CODE would probably be better.`

The first statement records a decision.

The second statement records a suggestion.

A memory bank SHOULD record decisions rather than opinions or recommendations.

· · ·

## 6.3 Decision Scope

A decision SHOULD identify the components, interfaces, files, or systems to which it applies when the scope is not obvious.

Example:

* **Scope:** TWR package installation subsystem.

Avoid describing a decision as universally applicable when it only affects one component.

· · ·

## 6.4 Decision Reason

The reason for a decision SHOULD be recorded when it is known and materially useful.

Reasons MUST NOT be invented.

If the original reason is unknown, the memory bank SHOULD leave the reason unspecified rather than constructing an explanation from assumptions.

· · ·

## 6.5 Decision Source

A decision SHOULD identify its source when the source is available.

Possible sources include:

* Developer decision.
* Architecture documentation.
* API documentation.
* ABI documentation.
* Issue or design discussion.
* Other authoritative project documentation.

Do not invent a source.

___

# 7. Decision Categories

Decision categories help AI agents locate relevant project information.

Categories SHOULD be used consistently but MUST NOT be created merely for organizational complexity.

· · ·

## 7.1 Architecture Decisions

Architecture decisions describe structural or architectural choices that have been explicitly established by the project.

Examples include:

* Component boundaries.
* Dependency direction.
* Interface boundaries.
* Module responsibilities.
* Architectural patterns.

Architecture decisions MUST originate from an authoritative architectural source or explicit developer decision.

· · ·

## 7.2 API Decisions

API decisions describe public API behavior and compatibility requirements.

Examples include:

* Function interfaces.
* Input and output behavior.
* Public naming.
* Error contracts.
* API evolution rules.

Do not use this category to redefine an API that is already specified by a more authoritative API document.

· · ·

## 7.3 ABI Decisions

ABI decisions describe binary compatibility requirements that have been explicitly established.

Examples include:

* Calling conventions.
* Stable binary interfaces.
* Binary-compatible representations.
* ABI evolution requirements.

Do not infer ABI stability merely because an interface currently appears stable.

· · ·

## 7.4 Data Decisions

Data decisions describe intentionally established data representations or models.

Examples include:

* Data structures.
* Serialization formats.
* Persistent representations.
* Data ownership models.
* Representation constraints.

Do not infer undocumented data guarantees from the current implementation.

· · ·

## 7.5 Ownership Decisions

Ownership decisions describe who owns resources and who is responsible for their destruction.

When applicable, document:

* Owner.
* Borrowers.
* Ownership transfer.
* Destruction responsibility.
* Affected resource.

Ownership information MUST NOT be inferred when the project has not established it.

· · ·

## 7.6 Lifetime Decisions

Lifetime decisions describe how long resources, objects, or data remain valid.

When applicable, document:

* Resource.
* Lifetime.
* Lifetime dependencies.
* Conditions that end the lifetime.
* Affected components.

Do not assume lifetime behavior from implementation details unless the behavior is explicitly established as a project decision.

· · ·

## 7.7 Error Handling Decisions

Error handling decisions describe established error semantics.

They MAY include:

* Status mechanisms.
* Failure behavior.
* Recovery behavior.
* Diagnostic behavior.
* Error propagation.
* Error translation.

Do not invent error semantics merely because an implementation needs to handle a failure.

· · ·

## 7.8 Dependency Decisions

Dependency decisions describe intentionally established dependencies and dependency directions.

They MAY include:

* Required dependencies.
* Prohibited dependencies.
* Dependency direction.
* External dependency policies.
* Dependency boundaries.

Do not record a dependency as an architectural requirement merely because the current implementation happens to use it.

· · ·

## 7.9 Security Decisions

Security decisions describe explicitly established security requirements or design choices.

They MAY include:

* Trust boundaries.
* Authentication requirements.
* Authorization behavior.
* Cryptographic mechanisms.
* Data protection.
* Security restrictions.

Security decisions SHOULD reference authoritative security documentation when available.

· · ·

## 7.10 Build Decisions

Build decisions describe intentionally established build-system behavior.

Examples include:

* Build systems.
* Build targets.
* Compiler requirements.
* Build directories.
* Generated-file policies.
* Platform-specific build behavior.

· · ·

## 7.11 Naming Decisions

Naming decisions describe project-specific naming conventions or intentional naming exceptions.

Examples include:

* Public identifier naming.
* File naming.
* Directory naming.
* Compatibility-driven naming exceptions.

Generic naming preferences SHOULD NOT be recorded as project decisions unless the project explicitly adopts them.

· · ·

## 7.12 File Structure Decisions

File structure decisions describe intentionally established project organization.

Examples include:

* Directory structure.
* Source layout.
* Header layout.
* Documentation layout.
* Generated-file locations.

Do not treat an arbitrary current directory structure as an architectural decision unless it has been intentionally established.

· · ·

## 7.13 Documentation Decisions

Documentation decisions describe intentionally established documentation requirements.

Examples include:

* Required documentation files.
* Documentation structure.
* Documentation language.
* Documentation generation requirements.

More specific documentation standards remain authoritative where applicable.

· · ·

## 7.14 Tooling Decisions

Tooling decisions describe intentionally established development tools or workflows.

Examples include:

* Formatters.
* Linters.
* Build tools.
* Testing tools.
* Development workflows.

Do not turn temporary personal tool usage into a project requirement without an explicit decision.

· · ·

## 7.15 Other Technical Decisions

Other technical decisions MAY be used when a significant decision does not fit the established categories.

Do not create unnecessary categories when an existing category is sufficient.

___

# 8. Historical Decisions

Historical decisions provide context about how the project evolved.

Historical information SHOULD be preserved when it helps prevent confusion.

A historical decision MUST be distinguishable from an active decision.

Do not rewrite historical records merely to make the current architecture appear simpler.

Do not remove significant historical decisions solely because they are no longer active.

___

# 9. Rejected Decisions

Significant rejected alternatives SHOULD be recorded when their history is useful.

A rejected decision SHOULD contain:

* The rejected proposal.
* The reason for rejection when known.
* The selected alternative when applicable.
* The affected components when known.
* The date when known.

Do not record every casual idea discussed during development.

The purpose of rejected-decision records is to preserve meaningful historical context and prevent repeated consideration of explicitly rejected approaches.

___

# 10. Changed Decisions

When an active decision is replaced:

1. Preserve the previous decision.
2. Mark the previous decision as superseded.
3. Record the new decision.
4. Identify the relationship between the previous and new decisions.
5. Record the reason for the change when known.
6. Record the date when known.

Do not silently edit a historical decision so that it appears to have always contained the new decision.

Historical accuracy is part of the purpose of the memory bank.

___

# 11. Temporary Decisions

Temporary decisions SHOULD clearly identify their temporary nature.

When practical, document:

* **Status:** TEMPORARY.
* **Decision:** The temporary project decision.
* **Reason:** Why the temporary decision exists.
* **Scope:** What it affects.
* **Valid Until:** Date, milestone, or condition.
* **Replacement Plan:** Expected replacement when known.

A temporary decision MUST NOT be treated as permanent merely because it remains in the file for a long period.

___

# 12. Open Decisions

Open decisions represent unresolved questions.

An open decision SHOULD identify:

* **Question:** The unresolved question.
* **Affected Components:** Components affected by the decision.
* **Blocking:** Whether the decision currently blocks work.
* **Required Decision By:** Deadline or milestone when known.

AI agents MUST NOT silently resolve an open decision.

If the developer explicitly asks AI to propose alternatives, AI MAY provide proposals, but those proposals MUST remain distinguishable from the actual project decision until the developer establishes a decision.

___

# 13. Unknown Information

Unknown information MUST remain unknown.

The memory bank MUST NOT contain fabricated information.

Do not invent:

* Decisions.
* Reasons.
* Dates.
* Sources.
* Developer intentions.
* Ownership.
* Lifetime guarantees.
* Compatibility requirements.
* Architectural relationships.

Absence of a decision does not constitute permission to make one.

When required information is unavailable, record only what is known.

If missing information prevents accurate documentation, the missing information SHOULD be identified for the developer.

___

# 14. Source of Truth

The memory bank is a persistent project context source.

It is not automatically the highest-authority technical source.

When a more authoritative project document defines a technical contract, that document remains authoritative.

Examples include:

* Architecture specifications.
* API specifications.
* ABI specifications.
* Security specifications.
* Explicit developer decisions.
* More specific project documentation rules.

When the memory bank conflicts with a more authoritative source, the conflict SHOULD be identified and the applicable authority hierarchy MUST be followed.

The memory bank MUST NOT be used to silently override authoritative project documentation.

___

# 15. Updating the Memory Bank

The memory bank SHOULD be updated when a significant project decision is made.

Typical update events include:

* A new architectural decision.
* A public API decision.
* An ABI decision.
* An ownership decision.
* A lifetime decision.
* A dependency decision.
* A security decision.
* A significant naming convention.
* A significant file-structure decision.
* Rejection of a significant alternative.
* Replacement of an existing decision.
* Resolution of an open decision.
* Creation of a temporary decision.

Do not update the memory bank for every minor implementation detail.

The memory bank is intended to preserve meaningful project decisions rather than reproduce the entire development history.

___

# 16. Writing Style

The memory bank is intended for AI consumption but is maintained by humans.

Its writing SHOULD therefore be:

* Clear.
* Direct.
* Factual.
* Structured.
* Consistent.
* Technically precise.

Avoid:

* Marketing language.
* Emotional descriptions.
* Unnecessary narrative.
* Flowery language.
* Ambiguous statements.
* Repetition that does not improve information retrieval.

The goal is not literary quality.

The goal is accurate and retrievable project memory.

___

# 17. Formatting

The memory bank SHOULD follow applicable TWR documentation formatting rules.

## 17.1 Headings

Use numbered headings according to the applicable TWR documentation rules.

Main sections MUST use sequential numeric identifiers.

Sub-sections MUST derive their identifiers from their parent sections.

· · ·

## 17.2 Lists

Use bullet lists when multiple independent properties need to be represented.

When a bullet contains a named field, the field name SHOULD be bold.

Example:

```markdown
* **Status:** ACTIVE
* **Scope:** Package installation subsystem.
* **Reason:** Required for compatibility.
```

Nested Markdown lists MUST use four-space indentation.

· · ·

## 17.3 Separators

When creating a standard TWR memory bank from scratch:

* Use `___` between main sections.
* Use `° ° °` between sub-sections when a separator is needed.

More specific project documentation rules MAY define different separators.

___

# 18. Compliance

A `tayku-ai-memory-bank.md` document complies with this guide when applicable requirements are satisfied.

The memory bank SHOULD:

* Clearly identify its purpose.
* Distinguish active decisions from historical decisions.
* Distinguish rejected decisions from active decisions.
* Distinguish superseded decisions from active decisions.
* Distinguish temporary decisions from permanent decisions.
* Distinguish open decisions from resolved decisions.
* Preserve significant decision history.
* Use consistent decision identifiers.
* Identify decision scope when applicable.
* Identify decision sources when available.
* Preserve unknown information as unknown.
* Avoid fabricated project information.
* Avoid treating suggestions as decisions.
* Respect authoritative project documentation.
* Follow applicable TWR documentation formatting rules.
* Remain focused on project decisions rather than becoming a duplicate architecture document.

___

# 19. Related Resources

* `tayku-ai-memory-bank.md`
* `tayku-ai-goals.md`
* `memory-bank-documentation-guide.md`
* `goals-documentation-guide.md`
* `usage-of-ai.md`
* `documentation-guides.md`
* `documentation-guide-rules-ai.md`

___

# 20. Notes

The memory bank records what the project has decided.

It does not determine what the project should become.

The developer remains responsible for project decisions.

AI agents use the memory bank to preserve and apply established project decisions consistently.

When a decision is not known, it remains unknown.

When a decision changes, its history remains available.

When a decision has not been made, the memory bank must not manufacture one.
