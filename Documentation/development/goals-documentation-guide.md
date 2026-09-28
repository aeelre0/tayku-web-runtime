# Goals Documentation Guide

This document defines how `tayku-ai-goals.md` MUST be documented, structured, maintained, and validated.

___

# 1. Scope

## 1.1 Purpose

This guide defines documentation rules for `tayku-ai-goals.md`.

`tayku-ai-goals.md` is the persistent AI-oriented project progress and goals document.

This guide defines how the document represents:

* Project direction.
* Completed work.
* Current project state.
* Current tasks.
* Future tasks.
* Blocked tasks.
* Deferred tasks.
* Milestones.
* Project progress.
* Progress history.

° ° °

## 1.2 Boundaries

This guide governs the documentation of project progress and planned work.

This guide MUST NOT define project architecture.

This guide MUST NOT define:

* API contracts.
* ABI contracts.
* Ownership rules.
* Lifetime rules.
* Dependency architecture.
* Security architecture.
* Implementation architecture.
* Technical decisions.

Those subjects MUST be documented according to their authoritative sources.

° ° °

## 1.3 Applicable Operations

This guide applies when `tayku-ai-goals.md` is:

* Created.
* Modified.
* Restructured.
* Reformatted.
* Reviewed.
* Validated.
* Generated or modified by AI.

___

# 2. Goals Document Purpose

## 2.1 Project Progress

`tayku-ai-goals.md` SHOULD provide enough information for an AI to understand the known progress of the project.

It SHOULD make it possible to identify:

* What has been completed.
* What is currently being worked on.
* What remains to be done.
* What is planned for the future.
* What work is blocked.
* What work has been deferred.
* What milestones exist.
* How significant project progress has changed.

° ° °

## 2.2 Project Direction

The document MAY describe the current project direction.

Project direction SHOULD remain at the project-progress level.

It MUST NOT silently introduce architectural or technical decisions.

° ° °

## 2.3 AI Context

The document is intended to provide persistent project-state context to AI systems.

An AI SHOULD be able to use the document to determine the current project state without reconstructing that state from unrelated information when sufficient information is already available.

___

# 3. Document Structure

## 3.1 Required Structure

The goals document SHOULD use a stable structure.

The following sections SHOULD be used when applicable:

* Project Direction.
* Completed Work.
* Current State.
* Current Tasks.
* Future Tasks.
* Blocked Tasks.
* Deferred Tasks.
* Milestones.
* Progress History.

A section SHOULD NOT be added when its information is genuinely inapplicable.

° ° °

## 3.2 Project Direction

The Project Direction section describes the current high-level project direction.

It SHOULD describe:

* The intended project outcome.
* The current broad development direction.
* Relevant high-level objectives.

It MUST NOT become an architecture specification.

° ° °

## 3.3 Completed Work

The Completed Work section records work known to be completed.

Completed tasks SHOULD remain available when their historical existence provides useful project context.

Completed tasks MUST NOT be represented as pending work.

° ° °

## 3.4 Current State

The Current State section describes the known current condition of the project.

It MAY include:

* Implemented capabilities.
* Active development areas.
* Known unfinished areas.
* Current milestone state.
* Relevant blockers.

The section MUST represent known information.

The AI MUST NOT infer undocumented project state.

° ° °

## 3.5 Current Tasks

The Current Tasks section contains work that is currently active or intended to be addressed as current project work.

Tasks SHOULD be sufficiently specific to allow their progress to be tracked.

Large tasks MAY be decomposed into smaller tasks when the decomposition is known and useful.

° ° °

## 3.6 Future Tasks

The Future Tasks section records work that is planned but is not currently active.

Future tasks MAY include:

* Future features.
* Future improvements.
* Future documentation.
* Future tooling.
* Future integrations.
* Future maintenance.

Future tasks MUST NOT be presented as completed or current work.

° ° °

## 3.7 Blocked Tasks

The Blocked Tasks section records work that cannot currently proceed because of a known blocker.

A blocker SHOULD be documented when its cause is known.

The AI MUST NOT invent a blocker.

If the reason for a blocked task is unknown, the reason MUST remain unknown.

° ° °

## 3.8 Deferred Tasks

The Deferred Tasks section records work that has intentionally been postponed.

A deferred task is distinct from a blocked task.

A blocked task cannot currently proceed because of a blocking condition.

A deferred task is intentionally not being pursued at the current time.

The distinction MUST be preserved.

° ° °

## 3.9 Milestones

The Milestones section records significant project objectives or progress points.

A milestone MAY contain related tasks.

A milestone SHOULD represent a meaningful project-state transition rather than an arbitrary collection of tasks.

° ° °

## 3.10 Progress History

The Progress History section records significant changes to project progress.

It MAY include:

* Major completed work.
* Milestone completion.
* Significant task-state changes.
* Important blockers.
* Important deferrals.
* Significant changes in project direction.

Historical information SHOULD be preserved when it provides useful project context.

___

# 4. Goals And Tasks

## 4.1 Goals

A goal represents a meaningful desired project outcome.

Goals SHOULD describe what the project intends to achieve.

A goal SHOULD NOT be used for a trivial implementation action when that action can be represented as a task.

° ° °

## 4.2 Tasks

A task represents a concrete unit of project work.

Tasks SHOULD be actionable and sufficiently specific for progress tracking.

Unrelated work SHOULD NOT be combined into a single task when meaningful separation is possible.

° ° °

## 4.3 Task Decomposition

Tasks MAY be decomposed into smaller tasks.

Decomposition SHOULD be used when it improves progress tracking.

Decomposition MUST NOT introduce requirements that are not established by the project.

If the correct decomposition is unknown, the AI MUST NOT invent one.

° ° °

## 4.4 Milestones

A milestone represents a significant project objective or progress point.

Milestones MAY group related goals and tasks.

Milestone completion MUST be based on known project state or an explicitly defined completion condition.

___

# 5. Task Status

## 5.1 Status Markers

The goals document MAY use the following task status markers:

* `[ ]` — Pending.
* `[x]` — Completed.
* `[>]` — In Progress.
* `[-]` — Blocked.
* `[~]` — Deferred.
* `[?]` — Unknown.

When these markers are used, their meanings MUST remain consistent throughout the document.

° ° °

## 5.2 Pending

`[ ]` represents work that is known but is not represented as completed, in progress, blocked, deferred, or unknown.

Example:

```markdown
* [ ] Implement package removal.
```

° ° °

## 5.3 Completed

`[x]` represents work that is known to be completed.

Completed tasks SHOULD use strikethrough formatting when represented in completed work.

Example:

```markdown
* [x] ~~Implement package installation.~~
```

The AI MUST NOT mark work as completed without sufficient evidence.

° ° °

## 5.4 In Progress

`[>]` represents work that is currently in progress.

Example:

```markdown
* [>] Implement package verification.
```

The AI MUST NOT infer that work is in progress merely because related implementation exists.

° ° °

## 5.5 Blocked

`[-]` represents work that cannot currently proceed because of a known blocker.

Example:

```markdown
* [-] Implement external registry synchronization.
    * **Blocker:** Registry protocol is not yet defined.
```

° ° °

## 5.6 Deferred

`[~]` represents work that has intentionally been postponed.

Example:

```markdown
* [~] Implement graphical administration tools.
    * **Reason:** Deferred until a later development phase.
```

° ° °

## 5.7 Unknown

`[?]` represents work whose current state is unknown.

Example:

```markdown
* [?] Determine whether package signing is implemented.
```

Unknown state MUST NOT be converted into a guessed state.

___

# 6. Task Information

## 6.1 Task Statement

Every tracked task SHOULD have a clear task statement.

The statement SHOULD describe the intended work or outcome.

Example:

```markdown
* [ ] Add package signature verification.
```

° ° °

## 6.2 Task Scope

Task scope MAY be documented when boundaries are necessary to understand the task.

Scope MAY identify:

* Affected subsystem.
* Affected command.
* Affected package.
* Affected documentation.
* Explicitly excluded work.

Task scope MUST NOT invent architectural boundaries.

° ° °

## 6.3 Task Dependencies

Task dependencies MAY be documented when a known dependency affects task progress.

Example:

```markdown
* [ ] Implement package publishing.
    * **Depends On:** Package signing.
```

Dependencies MUST represent known project relationships.

The AI MUST NOT create dependencies solely because tasks appear related.

° ° °

## 6.4 Task Constraints

Task constraints MAY be documented when known constraints materially affect the task.

Constraints MUST originate from authoritative project information.

The goals document MUST NOT create new project constraints.

° ° °

## 6.5 Completion Conditions

A completion condition SHOULD be documented when completion cannot be determined directly from the task statement.

A completion condition MUST describe an existing project requirement.

It MUST NOT introduce a new architectural or technical requirement.

___

# 7. Project Progress

## 7.1 Completed Progress

Completed progress SHOULD describe meaningful project outcomes that have already been achieved.

The section SHOULD focus on project-level progress rather than unnecessary implementation details.

° ° °

## 7.2 Current Progress

Current progress SHOULD describe the known active state of project development.

It MAY reference:

* Current tasks.
* Current milestones.
* Known blockers.
* Relevant unfinished work.

The AI MUST NOT claim progress that has not been established.

° ° °

## 7.3 Future Progress

Future progress MAY describe the intended direction of remaining project work.

Future work MUST remain distinguishable from current work.

If task ordering has not been established, the AI MUST NOT present an invented order.

___

# 8. Milestone Documentation

## 8.1 Milestone Definition

A milestone SHOULD contain enough information to identify its purpose.

It MAY contain:

* Identifier.
* Name.
* Objective.
* Related tasks.
* Completion condition.
* Status.

° ° °

## 8.2 Milestone Status

Milestone status MUST represent its known state.

A milestone MUST NOT be marked completed merely because most related tasks are completed unless its completion condition allows that interpretation.

° ° °

## 8.3 Milestone Completion

A milestone is completed when its defined completion condition is satisfied.

When no explicit completion condition exists, completion MAY be determined from authoritative project state when the result is unambiguous.

If completion cannot be established, the AI MUST NOT guess.

___

# 9. Project History

## 9.1 Historical Information

Historical progress SHOULD be preserved when it provides useful context.

Historical entries MAY record:

* Completed tasks.
* Reopened tasks.
* Milestone completion.
* Significant state changes.
* Important deferrals.
* Important blockers.
* Changes in project direction.

° ° °

## 9.2 Historical Accuracy

Historical entries MUST describe the state or event that actually occurred.

The current state MUST NOT overwrite historical information merely to simplify the document.

Example:

```markdown
* `2026-09-01` — `TASK-001` completed.
* `2026-09-12` — `TASK-001` reopened because additional work became necessary.
```

The earlier completion remains part of project history.

___

# 10. Unknown Information

## 10.1 Unknown State

Unknown information MUST remain unknown.

The AI MUST NOT guess:

* Task completion.
* Task progress.
* Blocker causes.
* Deferral reasons.
* Future commitments.
* Milestone completion.
* Task ordering.
* Developer priorities.
* Undocumented project direction.

° ° °

## 10.2 Recording Unknown Information

When required information is unavailable, it MAY be explicitly represented as `Unknown`.

The `[?]` status SHOULD be used when the unknown state applies to a task or milestone.

° ° °

## 10.3 Resolving Unknown Information

Unknown information SHOULD be resolved using an authoritative project source when such a source is available.

The AI MUST NOT resolve unknown information by assumption.

___

# 11. Source Of Truth

## 11.1 Project-State Authority

`tayku-ai-goals.md` is persistent project-state context for AI systems.

It records project progress and planned work.

It MUST NOT override authoritative project documentation.

° ° °

## 11.2 Architectural Authority

Architecture MUST come from authoritative architecture documentation or explicit developer decisions.

The goals document MUST NOT become an architectural source merely because it references an architectural task.

° ° °

## 11.3 Technical Authority

API, ABI, dependency, ownership, lifetime, security, and implementation behavior MUST come from their applicable authoritative sources.

The goals document MAY reference those sources.

It MUST NOT redefine them.

° ° °

## 11.4 Implementation State

Implementation state MAY be established using appropriate project sources, including:

* Source code.
* Build configuration.
* Tests.
* Documentation.
* Explicit developer statements.

The AI SHOULD use the appropriate source when determining whether work is actually complete.

___

# 12. Questions

## 12.1 Project Questions

The goals document SHOULD provide enough information to answer questions that an AI or developer is reasonably expected to have about project progress.

Relevant questions include:

* What is the current project direction?
* What has already been completed?
* What is currently in progress?
* What remains pending?
* What is planned for the future?
* What is blocked?
* What is deferred?
* What milestone is currently relevant?
* What significant progress has occurred?

° ° °

## 12.2 Information Availability

If the goals document cannot answer a relevant question because the required information is unavailable, the AI MUST NOT invent an answer.

The missing information SHOULD remain explicitly unknown when relevant to project state.

___

# 13. Updating The Goals Document

## 13.1 General Updates

The goals document SHOULD be updated when meaningful project progress changes.

Updates SHOULD preserve consistency between:

* Current state.
* Task status.
* Completed work.
* Future work.
* Blocked work.
* Deferred work.
* Milestones.
* Progress history.

° ° °

## 13.2 Adding Tasks

A task MAY be added when project work is explicitly identified.

The AI MUST NOT invent project tasks merely because they appear useful.

Suggested work MUST remain distinguishable from adopted project work.

° ° °

## 13.3 Completing Tasks

When task completion is confirmed:

1. Change the task status to `[x]`.
2. Move the task to the applicable completed section when the document structure requires it.
3. Apply the completed-task formatting.
4. Update related milestone information when applicable.
5. Preserve relevant historical information.

° ° °

## 13.4 Changing Task Status

Task status SHOULD be updated when the known project state changes.

Examples of valid state transitions include:

```text
[ ] → [>]
[>] → [x]
[>] → [-]
[>] → [~]
[?] → [ ]
```

The AI MUST NOT perform a status transition without sufficient information.

° ° °

## 13.5 Moving Tasks

Tasks MAY be moved between sections when their project state changes.

Moving a task MUST NOT change its meaning.

A stable task identifier SHOULD remain unchanged when one exists.

° ° °

## 13.6 Removing Tasks

Tasks SHOULD NOT be removed when they provide useful historical context.

A task MAY be removed when:

* It was created in error.
* It is explicitly abandoned.
* It is fully redundant.
* The project explicitly decides to remove it.

Meaningful removals SHOULD be preserved in project history when appropriate.

___

# 14. Writing Style

## 14.1 Clarity

The document MUST prioritize technical clarity.

Use short and clear sentences whenever practical.

Prefer simple vocabulary when it preserves technical accuracy.

Apply:

`CLARITY > NATIVE-LIKE STYLE`

° ° °

## 14.2 Technical Meaning

Do not change technical meaning for stylistic reasons.

Do not optimize the document for literary quality.

Do not optimize the document for sounding like a native English speaker.

° ° °

## 14.3 Tone

The document MUST NOT use a confrontational tone.

Do not use the document to:

* Attack readers.
* Insult developers.
* Belittle users.
* Provoke unnecessary conflict.

° ° °

## 14.4 Information Integrity

The document MUST NOT:

* Invent project information.
* Present assumptions as facts.
* Hide relevant project-state information.
* Replace known information with assumptions.
* Present uncertain information as certain.

___

# 15. Formatting

## 15.1 Markup

`tayku-ai-goals.md` MUST use Markdown.

The document SHOULD use Markdown structures appropriate for:

* Headings.
* Lists.
* Task checkboxes.
* Code formatting.
* References.

° ° °

## 15.2 Headings

The document MUST use numeric identifiers for main sections.

Main sections MUST start at `1` and use sequential numbering.

Valid structure:

```markdown
# 1. Project Direction
# 2. Current State
# 3. Future Tasks
```

Sub-sections MUST derive their identifiers from their parent section.

Valid structure:

```markdown
# 3. Future Tasks

## 3.1 Planned Features

## 3.2 Planned Documentation
```

Section identifiers MUST appear before section names.

Section names MUST use Title Case.

° ° °

## 15.3 Table Of Contents

A Table of Contents SHOULD be provided.

The Table of Contents SHOULD reflect the actual document hierarchy.

Entries MUST remain consistent with the corresponding section identifiers and names.

° ° °

## 15.4 Separators

Main sections SHOULD be separated using:

```text
___
```

Sub-sections SHOULD be separated using:

```text
° ° °
```

A more specific project rule MAY define alternative separators.

Separators MUST NOT replace section headers.

° ° °

## 15.5 Bullet Lists

Bullet lists SHOULD be used when they improve clarity.

When a bullet contains a title or named concept, the title MUST be bold.

Example:

```markdown
* **Current State:** Package installation is implemented.
```

Nested lists MUST use four spaces per indentation level.

Example:

```markdown
* Current Tasks
    * Implement package verification.
        * Implement signature parsing.
        * Implement signature verification.
```

° ° °

## 15.6 Task Checkboxes

Task status SHOULD use Markdown checkbox syntax.

When the goals document uses the standard status system defined by this guide:

```markdown
* [ ] Pending task
* [x] ~~Completed task~~
* [>] In-progress task
* [-] Blocked task
* [~] Deferred task
* [?] Unknown-status task
```

Status meanings MUST remain consistent.

___

# 16. AI Usage

## 16.1 AI Responsibilities

AI MAY create or modify `tayku-ai-goals.md`.

When doing so, AI MUST follow:

* This guide.
* The TWR Documentation Ruleset.
* Applicable project-specific documentation rules.
* Authoritative project information.

° ° °

## 16.2 AI Restrictions

AI MUST NOT:

* Invent project goals.
* Invent completed work.
* Invent current work.
* Invent future commitments.
* Invent blockers.
* Invent deferral reasons.
* Invent milestone state.
* Infer developer priorities without evidence.
* Convert suggestions into project decisions.
* Redefine architecture through task descriptions.
* Silently change project direction.
* Mark work as completed without sufficient evidence.

° ° °

## 16.3 AI Context

The AI SHOULD use authoritative project sources when determining project state.

The AI SHOULD distinguish between:

* Known project state.
* Explicit developer decisions.
* Planned work.
* Suggestions.
* Unknown information.

Suggestions MUST NOT be recorded as committed project work unless the developer has adopted them.

° ° °

## 16.4 Unrelated Changes

When modifying the goals document, the AI MUST NOT perform unrelated document restructuring merely because it is editing the file.

The AI SHOULD preserve:

* Existing task meaning.
* Existing task identifiers.
* Historical information.
* Established status meanings.
* Project direction.
* Applicable formatting rules.

___

# 17. Related Resources

The following resources MAY be relevant when creating, modifying, or validating `tayku-ai-goals.md`:

* `tayku-ai-goals.md`.
* `tayku-ai-memory-bank.md`.
* `memory-bank-documentation-guide.md`.
* TWR Documentation Ruleset.
* TWR AI Coding Rules.
* Usage of AI documentation.
* Authoritative architecture documentation.
* Authoritative API and ABI documentation.
* Project source code.
* Project task-tracking sources.

Related resources SHOULD be referenced when they materially contribute to understanding project progress.

The goals document MUST NOT replace an authoritative source merely by repeating its contents.

___

# 18. Compliance

## 18.1 Documentation Compliance

`tayku-ai-goals.md` MUST comply with the applicable TWR documentation requirements and this guide.

° ° °

## 18.2 Validation Checklist

Before finalizing or modifying the goals document, validate:

* [ ] Main sections start at `1`.
* [ ] Main section numbering is sequential.
* [ ] Sub-section numbering derives from parent sections.
* [ ] Section identifiers appear before section names.
* [ ] Section names use Title Case.
* [ ] Every section has a header.
* [ ] Every sub-section has a header.
* [ ] The Table of Contents, when present, matches the document structure.
* [ ] Main section separators follow the applicable project standard.
* [ ] Sub-section separators follow the applicable project standard.
* [ ] Nested lists use four-space indentation.
* [ ] Bullet titles are bold when applicable.
* [ ] Task status markers have consistent meanings.
* [ ] Completed tasks are represented as completed.
* [ ] Completed tasks are crossed out where applicable.
* [ ] Current work is distinguishable from future work.
* [ ] Blocked work is distinguishable from deferred work.
* [ ] Unknown information remains unknown.
* [ ] Historical information is preserved where relevant.
* [ ] Project direction is not confused with architecture.
* [ ] Project tasks are not invented.
* [ ] Project progress is not invented.
* [ ] Developer decisions are not invented.
* [ ] Architectural information is not invented.
* [ ] Suggestions are not presented as committed project work.
* [ ] Milestone state reflects known project state.
* [ ] Technical meaning is preserved.
* [ ] The document is clear and understandable.
* [ ] The document is non-confrontational.
* [ ] No unrelated document restructuring has been performed.

° ° °

## 18.3 Architectural Validation

Before documenting architectural information in relation to a task, verify that the information originates from an authoritative source.

If the information is missing, ambiguous, or contradictory:

* Do not guess.
* Do not silently complete the information.
* Identify the issue.
* Request clarification when necessary.

___

# 19. Canonical Principles

The following principles apply throughout this guide:

`PROJECT STATE MUST COME FROM KNOWN PROJECT INFORMATION.`

`DO NOT INVENT PROJECT PROGRESS.`

`DO NOT INVENT PROJECT GOALS.`

`DO NOT INVENT FUTURE COMMITMENTS.`

`UNKNOWN INFORMATION MUST REMAIN UNKNOWN.`

`SUGGESTIONS ARE NOT COMMITTED PROJECT WORK.`

`ARCHITECTURE MUST COME FROM AUTHORITATIVE SOURCES.`

`THE GOALS DOCUMENT MUST NOT REDEFINE ARCHITECTURE.`

`CURRENT WORK MUST REMAIN DISTINGUISHABLE FROM FUTURE WORK.`

`BLOCKED WORK MUST REMAIN DISTINGUISHABLE FROM DEFERRED WORK.`

`HISTORICAL PROGRESS SHOULD REMAIN TRACEABLE.`

`TECHNICAL MEANING MUST BE PRESERVED.`

`CLARITY > NATIVE-LIKE STYLE.`

`TECHNICAL ACCURACY > STYLISTIC ELEGANCE.`

`MANDATORY RULES ARE NOT RECOMMENDATIONS.`

`RECOMMENDATIONS ARE NOT MANDATORY RULES.`

`PERMITTED ALTERNATIVES MUST REMAIN PERMITTED.`
