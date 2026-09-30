# 1. Project Identity

* **Project Name:** Tayku Web Runtime (TWR)
* **Purpose:** A stable, composable modular monolith runtime environment built on Unix/POSIX philosophy with strict documentation and AI-usage standards.
* **Primary Languages:** C, C++
* **Target Environment:** Unix / POSIX

___

# 2. Active Project Decisions

## 2.1 Architecture Decisions

* **DEC-ARCH-001**
    * **Status:** ACTIVE
    * **Decision:** TWR follows the "Documentation First" philosophy. Architecture, contracts, and data flows MUST be documented before implementation.
    * **Scope:** Entire TWR ecosystem.
    * **Reason:** Ensures architectural stability, long-term supportability, and clear execution plans before coding.
    * **Source:** `documentation-guide.md`

* **DEC-ARCH-002**
    * **Status:** ACTIVE
    * **Decision:** The architecture MUST prefer small components, explicit dependencies, minimal interfaces, composable modular architecture, and maintain a modular monolith structure.
    * **Scope:** Core runtime and module design.
    * **Reason:** Keeps the core clean while preserving system unity and performance.
    * **Source:** `tayku-ai-design-principles.md`

* **DEC-ARCH-003**
    * **Status:** ACTIVE
    * **Decision:** TWR adopts the "let-it-crash" philosophy for programmer errors and unrecoverable invariant violations.
    * **Scope:** Error handling and runtime resilience.
    * **Reason:** Prevents invalid states from corrupting execution while leaving expected runtime failures to status codes.
    * **Source:** `core-coding-rules.md`

* **DEC-ARCH-004**
    * **Status:** ACTIVE
    * **Decision:** TWR operates as a modular monolith and does not embed an integrated Virtual File System (VFS) or custom kernel-level OS abstractions within its core. POSIX/Unix interfaces serve as the default baseline. System calls and standard C operations are provided via twr-libc (a fork of musl libc). Any future runtime abstractions will be implemented within twr-libc and twr-compiler rather than the TWR core runtime.
    * **Scope:** System architecture, VFS, system calls, twr-libc, and twr-compiler.
    * **Reason:** Eliminates unnecessary core complexity, adheres to POSIX standards, and maintains a clean modular monolith structure.
    * **Source:** Developer decision.

° ° °

## 2.2 API Decisions

* **DEC-API-001**
    * **Status:** ACTIVE
    * **Decision:** User-facing public headers MUST strictly adhere to the C11 standard. C++ features are restricted exclusively to internal source files (e.g., .cc files using orthodox C++).
    * **Scope:** Public headers and internal implementation files.
    * **Reason:** Ensures stable ABI and strict encapsulation of internal mechanisms.
    * **Source:** `core-coding-rules.md`

* **DEC-API-002**
    * **Status:** ACTIVE
    * **Decision:** Public function names MUST follow the twr_[submodule_]function namespace.
    * **Scope:** Public C library API.
    * **Reason:** Prevents global symbol collisions in C projects.
    * **Source:** `core-coding-rules.md`

* **DEC-API-003**
    * **Status:** ACTIVE
    * **Decision:** Public APIs MUST be append-only. Existing interfaces MUST remain stable and compatible.
    * **Scope:** Public API evolution.
    * **Reason:** Preserves backward compatibility and consumer stability.
    * **Source:** `tayku-ai-design-principles.md`

° ° °

## 2.3 Error Handling Decisions

* **DEC-ERR-001**
    * **Status:** ACTIVE
    * **Decision:** Operations with meaningful failure semantics MUST return TWR_STATUS_CODE. Zero represents success; negative values represent errors.
    * **Scope:** Function error handling across TWR libraries and modules.
    * **Reason:** Provides a unified status reporting mechanism across all C APIs.
    * **Source:** `core-coding-rules.md`

* **DEC-ERR-002**
    * **Status:** ACTIVE
    * **Decision:** Impossible internal states and programmer errors MUST use assert rather than normal error handling.
    * **Scope:** Internal invariant enforcement.
    * **Reason:** Enforces developer contracts and prevents execution in corrupt states.
    * **Source:** `core-coding-rules.md`

° ° °

## 2.4 AI Usage Decisions

* **DEC-AI-001**
    * **Status:** ACTIVE
    * **Decision:** AI agents MUST NOT invent architecture, guess missing information, or silently change established project decisions. The developer remains the sole architect.
    * **Scope:** All AI agent interactions and documentation/code generation.
    * **Reason:** Maintains human ownership and architectural integrity across the repository.
    * **Source:** `usage-of-ai.md`

___

# 3. Rejected Decisions

* **REJ-001**
    * **Proposal:** Embedding a monolithic kernel-like VFS layer directly inside the TWR core runtime.
    * **Status:** REJECTED
    * **Reason:** A monolithic core VFS layer adds unnecessary complexity to the core runtime; standard POSIX/Unix primitives via twr-libc are sufficient.
    * **Selected Alternative:** DEC-ARCH-004
    * **Source:** Developer decision.

___

# 4. Changed Decisions

* **CHG-001**
    * **Previous Decision:** TWR core runtime providing its own integrated kernel-style Virtual File System (VFS) and low-level OS abstraction layer.
    * **Status:** SUPERSEDED
    * **Replacement:** DEC-ARCH-004
    * **Reason:** Simplification of the core runtime architecture; maintaining a modular monolith structure where custom abstractions are offloaded to twr-libc if required in the future.
    * **Source:** Developer decision.

___

# 5. Temporary Decisions

*(None)*

___

# 6. Open Decisions

*(None)*

___

# 7. Decision History

* `2026-09-29` — Superseded integrated core VFS decision in favor of POSIX/twr-libc modular monolith baseline (DEC-ARCH-004, CHG-001, REJ-001).
