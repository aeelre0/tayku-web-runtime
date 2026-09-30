# 1. Project Direction

* **Objective:** Establish foundational documentation, AI guidelines, and core coding conventions for the Tayku Web Runtime (TWR).
* **Direction:** Maintain a modular monolith architecture aligned with Unix/POSIX principles where documentation precedes implementation ("Documentation First") and AI operates under strict execution constraints.

___

# 2. Completed Work

* [x] ~~Define AI usage restrictions and permissions (`usage-of-ai.md`).~~
* [x] ~~Establish core coding rules and C/C++ style guidelines (`core-coding-rules.md`).~~
* [x] ~~Create documentation formatting and structural standards (`documentation-guide.md`).~~
* [x] ~~Formulate AI-specific coding execution constraints (`ai-design-principles.md`).~~
* [x] ~~Update project decision bank with modular monolith baseline (`ai-memory-bank.md`).~~

___

# 3. Current State

* **Active Development:** Revising inner documentation to align with `DEC-ARCH-004` (POSIX/`twr-libc` baseline, eliminating core VFS assumptions) and initializing context files.
* **Relevant Blockers:** None.

___

# 4. Current Tasks

* [>] Revise TWR inner documentation for POSIX and `twr-libc` baseline alignment.
    * [x] ~~Generate and update `tayku-ai-memory-bank.md` tracking active decisions.~~
    * [>] Update `ai-goals.md` tracking project progress.
    * [x] Revise `core-coding-rules.md` to align with the `twr-libc` model.
    * [x] Place finalized AI context files into the `.ai/` directory.

___

# 5. Future Tasks

* [ ] Implement `twr-libc` wrappers for core memory and string abstractions.
* [ ] Develop base TWR status code definitions (`twr-status-codes.h`) and logging subsystem.
* [ ] Create initial public headers compliant with C11 restrictions.
* [ ] Setup build system (`buildsys/comp`) enforcing C++ encapsulation in internal `.cc` files.

___

# 6. Blocked Tasks

*(None)*

___

# 7. Deferred Tasks

*(None)*

___

# 8. Milestones

* **Milestone 1: Architectural Foundation**
    * **Objective:** Complete the "Documentation First" phase by finalizing core coding rules, AI usage policies, and documentation guides.
    * **Status:** Completed.

* **Milestone 2: AI Context Integration & Documentation Alignment**
    * **Objective:** Establish persistent AI memory/goal tracking and align inner repository documentation with the POSIX/`twr-libc` modular monolith baseline (`DEC-ARCH-004`).
    * **Status:** In Progress.

___

# 9. Progress History

* `2026-09-29` — Updated `ai-memory-bank.md` to record `DEC-ARCH-004` (POSIX/`twr-libc` baseline, superseding core VFS layer)[cite: 4]. Initiated inner documentation revision cycle[cite: 4, 5].
