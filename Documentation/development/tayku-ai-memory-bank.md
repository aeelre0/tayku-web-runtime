# 1. Project Identity

* **Project Name:** Tayku Web Runtime (TWR)
* **Purpose:** A stable, composable runtime environment built on Unix/POSIX philosophy with strict documentation and AI-usage standards.
* **Primary Languages:** C, C++
* **Target Environment:** Unix / POSIX

___

# 2. Active Project Decisions

## 2.1 Architecture Decisions

* **DEC-ARCH-001**
    * **Status:** ACTIVE
    * **Decision:** TWR follows the "Documentation First" philosophy. Architecture, contracts, and data flows MUST be documented before implementation.
    * **Source:** `documentation-guide.md`

* **DEC-ARCH-002**
    * **Status:** ACTIVE
    * **Decision:** The architecture MUST prefer small components, explicit dependencies, minimal interfaces, and composable components.
    * **Source:** `tayku-ai-design-principles.md`

* **DEC-ARCH-003**
    * **Status:** ACTIVE
    * **Decision:** TWR adopts the "let-it-crash" philosophy for programmer errors and unrecoverable invariant violations.
    * **Source:** `core-coding-rules.md`

° ° °

## 2.2 API Decisions

* **DEC-API-001**
    * **Status:** ACTIVE
    * **Decision:** User-facing public headers MUST strictly adhere to the C11 standard. C++ features are restricted exclusively to internal source files (e.g., `.cc` files using orthodox C++).
    * **Reason:** Ensures stable ABI and strict encapsulation of internal mechanisms.

* **DEC-API-002**
    * **Status:** ACTIVE
    * **Decision:** Public function names MUST follow the `twr_[submodule_]function` namespace.
    * **Source:** `core-coding-rules.md`

* **DEC-API-003**
    * **Status:** ACTIVE
    * **Decision:** Public APIs MUST be append-only. Existing interfaces MUST remain stable and compatible.
    * **Source:** `tayku-ai-design-principles.md`

° ° °

## 2.3 Error Handling Decisions

* **DEC-ERR-001**
    * **Status:** ACTIVE
    * **Decision:** Operations with meaningful failure semantics MUST return `TWR_STATUS_CODE`. Zero represents success; negative values represent errors.
    * **Source:** `core-coding-rules.md`

* **DEC-ERR-002**
    * **Status:** ACTIVE
    * **Decision:** Impossible internal states and programmer errors MUST use `assert` rather than normal error handling.
    * **Source:** `core-coding-rules.md`

° ° °

## 2.4 AI Usage Decisions

* **DEC-AI-001**
    * **Status:** ACTIVE
    * **Decision:** AI agents MUST NOT invent architecture, guess missing information, or silently change established project decisions. The developer remains the sole architect.
    * **Source:** `usage-of-ai.md`

___

# 3. Rejected Decisions

* **REJ-001**
    * **Proposal:** Allowing AI to independently determine architectural direction and bypass human review.
    * **Reason:** TWR requires human accountability. AI is a tool to shorten development time, not a replacement for developer responsibility.
    * **Selected Alternative:** AI outputs MUST be reviewed by the developer, and AI usage MUST be explicitly specified in the project.

___

# 4. Changed Decisions

*(None)*

___

# 5. Temporary Decisions

*(None)*

___

# 6. Open Decisions

*(None)*

___

# 7. Decision History

*(None)*
