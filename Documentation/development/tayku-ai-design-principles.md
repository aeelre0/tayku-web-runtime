# TWR AI Coding Rules

This document is an AI-only normative execution specification for source code belonging to the Tayku Web Runtime Environment (TWR).

An AI agent MUST process this document as a machine-oriented rule system.

An AI agent MUST NOT treat this document as:
    - educational documentation
    - general programming advice
    - optional guidance
    - a style suggestion
    - a source of architectural authority above project-specific contracts

The purpose of this document is to constrain AI-generated, AI-modified, AI-reviewed, AI-refactored, and AI-analyzed TWR source code.

> [!Note] Ancestors 
> This document is the normative AI-facing distillation of core-coding-rules.md (coding conventions) and usage-of-ai.md (AI usage policy). For rationale, examples, and diagrams, consult those documents.

___

# 0. Table of Contents

* 1. Execution Model
* 2. Normative Language
* 3. Authority and Precedence
* 4. Source of Truth
* 5. Unknown Information
* 6. Decision Preservation
* 7. Task Boundary
* 8. Change Classification
* 9. Compliance Model
* 10. Architectural Model
* 11. TWR Apps
* 12. Functions
* 13. Encapsulation
* 14. Stable Interfaces
* 15. TWR libc
* 16. Public API
* 17. Deprecated APIs
* 18. API Evolution
* 19. ABI Compatibility
* 20. Function Signature Evolution
* 21. Function Naming
* 22. Function Status
* 23. TWR_STATUS_CODE
* 24. Error Propagation
* 25. Error Logging
* 26. Function Contracts
* 27. Inputs
* 28. Input Immutability
* 29. Const Input Parameters
* 30. Input Representation
* 31. Output Parameters
* 32. Output Validity
* 33. Output State on Failure
* 34. Output Initialization
* 35. NULL Parameters
* 36. Boolean Values
* 37. Void Functions
* 38. Ownership
* 39. Ownership Transfer
* 40. Resource Destruction
* 41. Resource Lifetime
* 42. Borrowed Resources
* 43. Aliasing
* 44. Input/Output Aliasing
* 45. Resource Acquisition
* 46. Cleanup
* 47. goto
* 48. Allocation Failure
* 49. Assertions
* 50. Assertion Failure
* 51. Let-It-Crash
* 52. Crash Isolation
* 53. Error vs Programmer Failure
* 54. Error Context
* 55. Dependencies
* 56. Dependency Direction
* 57. Forward Declarations
* 58. Includes
* 59. Header/Source Separation
* 60. Public/Private Headers
* 61. Structs
* 62. Struct Layout
* 63. Struct Initialization
* 64. Data and Behavior
* 65. Enumerations
* 66. Naming Conventions
* 67. Public Namespace
* 68. File Names
* 69. C Style
* 70. C++ Style
* 71. Formatting
* 72. Comments
* 73. Macros
* 74. Constants
* 75. Casts
* 76. Pointer Arithmetic
* 77. Integer Types
* 78. Strings
* 79. Memory Allocation
* 80. Global State
* 81. Thread Safety
* 82. Concurrency Contracts
* 83. errno
* 84. System Calls
* 85. Syscall ABI
* 86. Implementation Freedom
* 87. Contract Over Mechanism
* 88. Control Flow
* 89. Internal Contracts
* 90. Validation Boundaries
* 91. Preconditions
* 92. Resource Independence
* 93. API Compatibility
* 94. Lifetime Compatibility
* 95. Aliasing Compatibility
* 96. Minimal Interfaces
* 97. Composition
* 98. Small Interfaces
* 99. Implementation Abstraction
* 100. Internal Helpers
* 101. Source Visibility
* 102. Public Implementation Leakage
* 103. Error Cleanup
* 104. Successful Cleanup
* 105. Failed Operations
* 106. Resource Creation
* 107. Resource Destruction Interfaces
* 108. Memory Safety
* 109. Stack Usage
* 110. Heap Usage
* 111. Memory Abstractions
* 112. Logging Interface
* 113. User Output
* 114. Diagnostics
* 115. Error Taxonomy
* 116. Boolean Status Separation
* 117. External Input vs Assertions
* 118. Invariants
* 119. Recovery
* 120. Concurrent Ownership
* 121. Mutable Global State
* 122. Circular Dependencies
* 123. Developer Choice
* 124. SHOULD Handling
* 125. Unix/POSIX Baseline
* 126. Stable Interface / Replaceable Implementation
* 127. Core Ownership Model
* 128. Core Error Model
* 129. Core Resource Model
* 130. Core Data Flow Model
* 131. Core Dependency Model
* 132. Core API Evolution Model
* 133. AI Modification Procedure
* 134. AI Validation Procedure
* 135. AI Conflict Handling
* 136. AI Prohibited Behavior
* 137. Final Compliance Rules

___

# 1. Execution Model

RULE EXEC-001:
    AI MUST parse this document before modifying TWR source code.

RULE EXEC-002:
    AI MUST identify all rules applicable to the requested change.

RULE EXEC-003:
    AI MUST apply project-specific contracts before applying generic rules.

RULE EXEC-004:
    AI MUST preserve existing project decisions unless the task explicitly authorizes changing them.

RULE EXEC-005:
    AI MUST NOT introduce changes solely because the AI considers an alternative preferable.

RULE EXEC-006:
    AI MUST NOT expand the task scope without explicit authorization.

RULE EXEC-007:
    AI MUST validate the resulting implementation against all applicable rules.

RULE EXEC-008:
    AI MUST report unresolved violations or missing information.

RULE EXEC-009:
    AI MUST NOT claim compliance without performing the applicable validation.

RULE EXEC-010:
    AI MUST NOT silently weaken, reinterpret, or omit an applicable rule.

___

# 2. Normative Language

RULE NORM-001:
    "MUST" denotes a mandatory constraint.

RULE NORM-002:
    "MUST NOT" denotes a prohibited behavior.

RULE NORM-003:
    "SHOULD" denotes the preferred behavior.

RULE NORM-004:
    "SHOULD NOT" denotes behavior that should normally be avoided.

RULE NORM-005:
    "MAY" denotes permitted behavior.

RULE NORM-006:
    A MUST constraint MUST NOT be violated for convenience.

RULE NORM-007:
    A SHOULD constraint MAY be deviated from only when an applicable engineering reason exists.

RULE NORM-008:
    AI MUST NOT convert SHOULD into MUST.

RULE NORM-009:
    AI MUST NOT convert MUST into SHOULD.

RULE NORM-010:
    AI MUST NOT treat MAY as a requirement.

___

# 3. Authority and Precedence

RULE AUTH-001:
    Specific TWR architecture contracts take precedence over generic coding rules.

RULE AUTH-002:
    Explicit API contracts take precedence over generic implementation preferences.

RULE AUTH-003:
    Explicit ABI contracts take precedence over generic implementation preferences.

RULE AUTH-004:
    Explicit ownership contracts take precedence over inferred ownership assumptions.

RULE AUTH-005:
    Explicit lifetime contracts take precedence over inferred lifetime assumptions.

RULE AUTH-006:
    Explicit aliasing contracts take precedence over generic aliasing preferences.

RULE AUTH-007:
    When two applicable rules conflict, AI MUST identify the conflict.

RULE AUTH-008:
    AI MUST NOT resolve architectural conflicts by personal preference.

RULE AUTH-009:
    AI MUST use the higher-authority applicable contract.

RULE AUTH-010:
    If precedence cannot be established, AI MUST NOT invent a resolution.

___

# 4. Source of Truth

RULE SOURCE-001:
    The existing TWR repository is the primary source of truth for existing implementation decisions.

RULE SOURCE-002:
    Explicit architecture documents are authoritative for architecture.

RULE SOURCE-003:
    Explicit API documentation is authoritative for documented public behavior.

RULE SOURCE-004:
    Explicit ABI documentation is authoritative for ABI requirements.

RULE SOURCE-005:
    Existing implementation MUST NOT automatically override an explicit documented contract.

RULE SOURCE-006:
    Generic programming knowledge MUST NOT override a project-specific decision.

RULE SOURCE-007:
    AI MUST inspect relevant existing code before modifying it.

RULE SOURCE-008:
    AI MUST inspect relevant declarations before changing implementations.

RULE SOURCE-009:
    AI MUST inspect relevant callers when changing a function contract.

RULE SOURCE-010:
    AI MUST inspect relevant ownership and lifetime paths when changing resource behavior.

___

# 5. Unknown Information

RULE UNKNOWN-001:
    UNKNOWN information MUST remain UNKNOWN.

RULE UNKNOWN-002:
    AI MUST NOT convert UNKNOWN into TRUE by inference.

RULE UNKNOWN-003:
    AI MUST NOT convert UNKNOWN into FALSE by inference.

RULE UNKNOWN-004:
    AI MUST NOT invent missing architecture decisions.

RULE UNKNOWN-005:
    AI MUST NOT invent missing API contracts.

RULE UNKNOWN-006:
    AI MUST NOT invent ownership semantics.

RULE UNKNOWN-007:
    AI MUST NOT invent lifetime guarantees.

RULE UNKNOWN-008:
    AI MUST NOT invent ABI guarantees.

RULE UNKNOWN-009:
    AI MUST NOT use generic knowledge as a substitute for missing project-specific information when project-specific information is required.

RULE UNKNOWN-010:
    If missing information prevents safe modification, AI MUST stop the affected modification or report the unresolved dependency.

___

# 6. Decision Preservation

RULE DECISION-001:
    Existing intentional architecture MUST be preserved.

RULE DECISION-002:
    Existing naming decisions MUST be preserved unless naming change is explicitly requested.

RULE DECISION-003:
    Existing API design MUST NOT be redesigned merely for elegance.

RULE DECISION-004:
    Existing ownership semantics MUST NOT be changed implicitly.

RULE DECISION-005:
    Existing error semantics MUST NOT be changed implicitly.

RULE DECISION-006:
    Existing dependency direction MUST NOT be reversed without explicit authorization.

RULE DECISION-007:
    Existing public interfaces MUST NOT be changed merely to conform to a generic convention.

RULE DECISION-008:
    AI MUST distinguish "technically possible improvement" from "authorized modification".

RULE DECISION-009:
    AI MUST NOT treat its own generated code as architectural authority.

___

# 7. Task Boundary

RULE SCOPE-001:
    AI MUST modify only files and behavior relevant to the requested task unless additional changes are required for correctness.

RULE SCOPE-002:
    AI MUST NOT perform unrelated refactoring.

RULE SCOPE-003:
    AI MUST NOT rename unrelated identifiers.

RULE SCOPE-004:
    AI MUST NOT reorganize unrelated files.

RULE SCOPE-005:
    AI MUST NOT replace working implementations with alternative implementations without task relevance.

RULE SCOPE-006:
    AI MUST NOT add dependencies unrelated to the requested behavior.

RULE SCOPE-007:
    AI MUST preserve unrelated behavior.

___

# 8. Change Classification

Before modification, AI MUST classify the change as one or more of:

* implementation change
* internal API change
* public API change
* ABI change
* ownership change
* lifetime change
* aliasing change
* error behavior change
* dependency change
* concurrency change
* resource-management change
* data-layout change
* build/interface change
* documentation-only change

RULE CLASS-001:
    AI MUST identify affected public or stable boundaries.

RULE CLASS-002:
    AI MUST identify compatibility requirements before modifying a stable boundary.

RULE CLASS-003:
    AI MUST treat public API, ABI, ownership, lifetime, aliasing, and error behavior changes as high-impact changes.

___

# 9. Compliance Model

RULE COMPLY-001:
    Compliance is evaluated against applicable MUST and MUST NOT rules.

RULE COMPLY-002:
    SHOULD deviations MUST NOT be represented as MUST violations.

RULE COMPLY-003:
    AI MUST verify all affected invariants after modification.

RULE COMPLY-004:
    A successful compilation does not establish architectural compliance.

RULE COMPLY-005:
    Passing tests does not establish API or ABI compatibility by itself.

RULE COMPLY-006:
    AI MUST separately validate:
    - behavior
    - interfaces
    - ownership
    - lifetime
    - errors
    - dependencies
    - concurrency
    - resource cleanup
    - relevant style constraints

___

# 10. Architectural Model

RULE ARCH-001:
    TWR architecture MUST prefer small components.

RULE ARCH-002:
    TWR architecture MUST prefer explicit dependencies.

RULE ARCH-003:
    TWR architecture MUST prefer minimal interfaces.

RULE ARCH-004:
    TWR architecture MUST prefer composable components.

RULE ARCH-005:
    TWR architecture MUST preserve stable interfaces.

RULE ARCH-006:
    Implementations MUST remain replaceable where the interface is intended to be stable.

RULE ARCH-007:
    Abstraction MUST NOT be introduced solely for abstraction's sake.

RULE ARCH-008:
    Complexity MUST have a concrete architectural or functional justification.

RULE ARCH-009:
    Unix, POSIX, Linux, and BSD design principles SHOULD be used as the default baseline where TWR does not explicitly differ.

___

# 11. TWR Apps

RULE APP-001:
    A TWR App MUST represent an independently meaningful responsibility.

RULE APP-002:
    A TWR App SHOULD have one fundamental responsibility.

RULE APP-003:
    AI MUST NOT define App size using an arbitrary line-count limit.

RULE APP-004:
    An App MAY contain multiple internal implementation units when required.

RULE APP-005:
    Apps SHOULD remain composable.

RULE APP-006:
    App internals MUST NOT become public merely because another component needs access.

___

# 12. Functions

RULE FUNC-001:
    Each function MUST perform one clear conceptual task.

RULE FUNC-002:
    AI MUST NOT create God Functions.

RULE FUNC-003:
    A function SHOULD be split when its responsibilities are conceptually unrelated.

RULE FUNC-004:
    AI MUST NOT split functions solely to satisfy an arbitrary line-count rule.

RULE FUNC-005:
    Function boundaries MUST follow meaningful responsibility.

___

# 13. Encapsulation

RULE ENCAP-001:
    Each TWR App MUST encapsulate its implementation.

RULE ENCAP-002:
    External consumers MUST interact through the intended public interface.

RULE ENCAP-003:
    Internal implementation details MUST NOT become public without an explicit requirement.

RULE ENCAP-004:
    Static functions MAY provide internal linkage.

RULE ENCAP-005:
    Private headers MAY be used.

RULE ENCAP-006:
    Opaque types MAY be used.

RULE ENCAP-007:
    Symbol visibility MAY be restricted.

RULE ENCAP-008:
    Build restrictions MAY enforce encapsulation.

RULE ENCAP-009:
    The mechanism used to enforce encapsulation is implementation-defined unless explicitly specified.

RULE ENCAP-010:
    The encapsulation property itself MUST be preserved.

___

# 14. Stable Interfaces

Stable interfaces include, where applicable:

* command ABI
* syscall ABI (This section may include public APIs and library APIs.)
* explicit architecture contracts

RULE IFACE-001:
    Stable interfaces MUST remain compatible with existing consumers.

RULE IFACE-002:
    Internal implementation MAY change without requiring consumer changes when the stable contract remains satisfied.

RULE IFACE-003:
    AI MUST distinguish interface behavior from implementation mechanism.

RULE IFACE-004:
    A direct Linux/system facility is not automatically prohibited.

RULE IFACE-005:
    TWR abstractions SHOULD be used where they define a meaningful stable boundary.

___

# 15. TWR libc

RULE LIBC-001:
    TWR libc MAY initially forward functionality to the system libc.

RULE LIBC-002:
    The TWR libc interface MUST remain a TWR-defined interface where specified.

RULE LIBC-003:
    The implementation of TWR libc MUST remain replaceable.

RULE LIBC-004:
    System libc behavior MUST NOT accidentally become an undocumented TWR API contract.

___

# 16. Public API

RULE API-001:
    Public APIs MUST be minimal.

RULE API-002:
    Public APIs MUST be designed for long-term stability.

RULE API-003:
    Public APIs MUST be append-only where the applicable contract specifies append-only evolution.

RULE API-004:
    Existing public functions MUST NOT be removed when append-only compatibility applies.

RULE API-005:
    Existing public functions MUST NOT be silently broken.

RULE API-006:
    New behavior SHOULD be introduced through additions.

RULE API-007:
    AI MUST NOT redesign an existing public API merely because another design appears cleaner.

___

# 17. Deprecated APIs

RULE DEPREC-001:
    A deprecated API MUST remain available while its compatibility contract requires it.

RULE DEPREC-002:
    A deprecated API MAY delegate to a newer implementation.

RULE DEPREC-003:
    Delegation MUST preserve the original observable contract.

RULE DEPREC-004:
    Deprecation MUST NOT silently become removal.

___

# 18. API Evolution

RULE EVOLVE-001:
    API evolution MUST prefer additive changes.

RULE EVOLVE-002:
    Existing consumers MUST remain compatible when append-only compatibility applies.

RULE EVOLVE-003:
    Breaking changes MUST NOT be introduced implicitly.

RULE EVOLVE-004:
    If compatibility requires a compatibility layer, AI SHOULD preserve the old interface through that layer.

___

# 19. ABI Compatibility

RULE ABI-001:
    Stable ABI MUST be treated as a compatibility boundary.

RULE ABI-002:
    AI MUST NOT change an ABI-stable function signature incompatibly.

RULE ABI-003:
    AI MUST NOT change ABI-stable calling conventions.

RULE ABI-004:
    AI MUST NOT change ABI-stable data representation incompatibly.

RULE ABI-005:
    AI MUST distinguish source compatibility from binary compatibility.

RULE ABI-006:
    Public struct layout MUST NOT be assumed ABI-stable unless explicitly specified.

___

# 20. Function Signature Evolution

RULE SIG-001:
    Existing public function signatures MUST NOT be changed incompatibly.

RULE SIG-002:
    Parameters MUST NOT be reordered in an ABI-stable interface.

RULE SIG-003:
    Parameter types MUST NOT be changed incompatibly.

RULE SIG-004:
    Return types MUST NOT be changed incompatibly.

RULE SIG-005:
    New behavior SHOULD be exposed through a new function when an existing signature cannot safely evolve.

___

# 21. Function Naming

RULE NAME-FUNC-001:
    Public functions MUST use the TWR namespace.

RULE NAME-FUNC-002:
    Public function names MUST follow:

`twr_[submodule_]function`

RULE NAME-FUNC-003:
    Public names MUST NOT use unnecessarily generic collision-prone names.

RULE NAME-FUNC-004:
    AI MUST preserve existing valid public naming unless a naming change is explicitly requested.

___

# 22. Function Status

RULE STATUS-001:
    Functions SHOULD return `TWR_STATUS_CODE` when an operation has meaningful success/failure semantics.

RULE STATUS-002:
    Operation status MUST be distinct from returned data.

RULE STATUS-003:
    Data SHOULD be returned through output parameters where the TWR contract specifies this model.

RULE STATUS-004:
    AI MUST NOT replace an operation status with a boolean when meaningful failure information exists.

___

# 23. TWR_STATUS_CODE

RULE STATUSCODE-001:
    `TWR_STATUS_CODE` is the general TWR operation-status type.

RULE STATUSCODE-002:
    Zero MUST represent success when defined by the TWR status model.

RULE STATUSCODE-003:
    Negative values represent errors where defined by the TWR status model.

RULE STATUSCODE-004:
    Positive values MUST NOT be interpreted as success unless the applicable contract explicitly defines them as such.

RULE STATUSCODE-005:
    Named status constants SHOULD be used instead of unexplained raw values.

RULE STATUSCODE-006:
    Status values SHOULD remain sufficiently general.

RULE STATUSCODE-007:
    Detailed diagnostic information SHOULD remain in the applicable logging or diagnostic layer.

___

# 24. Error Propagation

RULE ERRORPROP-001:
    Recoverable errors MUST be propagated, handled, translated, or recovered.

RULE ERRORPROP-002:
    AI MUST NOT silently discard an error.

RULE ERRORPROP-003:
    An error MUST NOT be converted into success merely to simplify control flow.

RULE ERRORPROP-004:
    A status SHOULD be propagated unchanged when the upper layer can correctly interpret it.

RULE ERRORPROP-005:
    A status MAY be translated when semantic translation is required by the upper layer.

RULE ERRORPROP-006:
    Error propagation MUST preserve failure semantics.

___

# 25. Error Logging

RULE LOG-001:
    Error logging MUST occur at the highest appropriate context layer.

RULE LOG-002:
    AI SHOULD avoid duplicate logging of the same failure.

RULE LOG-003:
    Logging MUST NOT replace status propagation.

RULE LOG-004:
    Low-level components SHOULD NOT produce user-facing output.

RULE LOG-005:
    Diagnostics MAY contain more information than the public status code.

___

# 26. Function Contracts

For every function being created or materially modified, AI MUST determine, where applicable:

* purpose
* inputs
* outputs
* valid input domain
* invalid input domain
* NULL behavior
* ownership
* lifetime
* aliasing
* side effects
* resource acquisition
* resource release
* status behavior
* failure behavior
* preconditions
* postconditions
* invariants
* thread-safety requirements
* synchronization requirements

RULE CONTRACT-001:
    AI MUST NOT invent unspecified contract behavior.

RULE CONTRACT-002:
    AI MUST preserve existing contract behavior.

RULE CONTRACT-003:
    Implementation details SHOULD NOT be treated as contract requirements unless explicitly specified.

___

# 27. Inputs

RULE INPUT-001:
    Input parameters represent data consumed by a function.

RULE INPUT-002:
    Input parameters SHOULD be distinct from output parameters.

RULE INPUT-003:
    Input data MUST NOT be modified unless the contract explicitly permits mutation.

RULE INPUT-004:
    AI MUST NOT infer mutation permission merely because the parameter is non-const.

___

# 28. Input Immutability

RULE INPUTIMM-001:
    Inputs MUST be treated as immutable by default.

RULE INPUTIMM-002:
    Mutation of an input requires explicit contract permission.

RULE INPUTIMM-003:
    AI MUST NOT mutate caller-owned input merely for implementation convenience.

___

# 29. Const Input Parameters

RULE CONST-001:
    Read-only pointer inputs SHOULD use `const`.

RULE CONST-002:
    AI MUST NOT remove `const` solely to simplify implementation.

RULE CONST-003:
    A parameter that is semantically immutable SHOULD remain represented as immutable.

___

# 30. Input Representation

RULE INPUTREP-001:
    Input representation is an implementation/design choice unless explicitly constrained.

Selection MAY depend on:

* semantic meaning
* object size
* ABI
* performance
* lifetime
* ownership
* readability
* mutation requirements

RULE INPUTREP-002:
    AI MUST NOT impose a universal pointer/value rule.

RULE INPUTREP-003:
    AI MUST preserve existing ABI requirements.

___

# 31. Output Parameters

RULE OUTPUT-001:
    Output parameters MUST represent data produced by an operation.

RULE OUTPUT-002:
    Output parameters SHOULD be distinct from inputs.

RULE OUTPUT-003:
    Multiple outputs MAY be represented using multiple output parameters.

RULE OUTPUT-004:
    Multiple related outputs MAY be represented by a struct.

RULE OUTPUT-005:
    Output ownership MUST be explicit when resources are returned.

___

# 32. Output Validity

RULE OUTVALID-001:
    On successful return, outputs MUST satisfy the applicable contract.

RULE OUTVALID-002:
    AI MUST NOT assume outputs are valid after failure unless the contract explicitly guarantees validity.

RULE OUTVALID-003:
    An output MUST NOT be represented as valid merely because storage was initialized.

___

# 33. Output State on Failure

RULE OUTFAIL-001:
    Outputs MAY be incomplete or invalid after failure unless the contract says otherwise.

RULE OUTFAIL-002:
    AI MUST NOT invent partial-output guarantees.

RULE OUTFAIL-003:
    If a contract explicitly requires a defined output state on failure, the implementation MUST preserve it.

___

# 34. Output Initialization

RULE OUTINIT-001:
    Caller-side output initialization is permitted.

RULE OUTINIT-002:
    Caller-side output initialization MUST NOT be treated as a universal requirement unless the contract specifies it.

RULE OUTINIT-003:
    Callee-side output initialization MUST NOT be added solely to compensate for an unspecified contract.

___

# 35. NULL Parameters

RULE NULL-001:
    NULL validity is contract-dependent.

RULE NULL-002:
    AI MUST NOT assume all pointers must reject NULL.

RULE NULL-003:
    AI MUST NOT assume NULL is valid unless the contract permits it.

RULE NULL-004:
    External invalid NULL input SHOULD be rejected according to the applicable public contract.

RULE NULL-005:
    Internal functions MAY rely on caller-guaranteed non-NULL preconditions.

___

# 36. Boolean Values

RULE BOOL-001:
    A boolean represents boolean data.

RULE BOOL-002:
    `TWR_STATUS_CODE` represents operation status.

RULE BOOL-003:
    A boolean MUST NOT replace operation status when the operation can fail meaningfully.

RULE BOOL-004:
    A boolean MAY be returned when only a true/false data result exists and failure is not independently meaningful.

___

# 37. Void Functions

RULE VOID-001:
    `void` functions MAY be used when operation status is not meaningful or useful.

RULE VOID-002:
    AI MUST NOT use `void` to hide meaningful failure conditions.

RULE VOID-003:
    If failure can materially affect caller behavior, an appropriate status mechanism SHOULD exist.

___

# 38. Ownership

RULE OWNER-001:
    Every resource MUST have a determinable owner when ownership applies.

RULE OWNER-002:
    Creating a resource on behalf of the caller normally establishes caller ownership unless the contract explicitly specifies another owner.

RULE OWNER-003:
    Passing a resource to a function does not imply ownership transfer.

RULE OWNER-004:
    Using a resource does not imply ownership transfer.

RULE OWNER-005:
    Modifying a resource does not imply ownership transfer.

RULE OWNER-006:
    Ownership MUST be explicit.

RULE OWNER-007:
    One resource MAY have multiple aliases, but ownership MUST remain unambiguous.

___

# 39. Ownership Transfer

RULE OWNTRANSFER-001:
    Ownership transfer MUST be explicitly defined by contract.

RULE OWNTRANSFER-002:
    AI MUST NOT infer ownership transfer from parameter passing.

RULE OWNTRANSFER-003:
    AI MUST NOT infer ownership transfer from mutation.

RULE OWNTRANSFER-004:
    Ownership transfer SHOULD be avoided when unnecessary.

RULE OWNTRANSFER-005:
    If ownership transfer occurs, the new owner MUST be determinable.

RULE OWNTRANSFER-006:
    The previous owner MUST NOT destroy a resource after ownership has been transferred.

___

# 40. Resource Destruction

RULE DESTROY-001:
    The owner is responsible for destruction unless the contract specifies another mechanism.

RULE DESTROY-002:
    Borrowers MUST NOT destroy borrowed resources.

RULE DESTROY-003:
    AI MUST NOT introduce duplicate destruction paths.

RULE DESTROY-004:
    Destruction MUST occur exactly according to the resource contract.

___

# 41. Resource Lifetime

RULE LIFE-001:
    Resource lifetime MUST be sufficient for every valid use.

RULE LIFE-002:
    AI MUST NOT use resources after destruction.

RULE LIFE-003:
    AI MUST NOT return aliases to resources whose lifetime ends before the caller can legally use them.

RULE LIFE-004:
    Passing an input MUST NOT implicitly extend its lifetime.

RULE LIFE-005:
    If a resource must outlive its input, an explicit copy, ownership mechanism, or lifetime mechanism MUST exist.

___

# 42. Borrowed Resources

RULE BORROW-001:
    A borrowed resource MUST NOT be destroyed by the borrower.

RULE BORROW-002:
    A borrowed resource MAY be read or modified only as permitted by contract.

RULE BORROW-003:
    The borrower MUST NOT outlive the guaranteed lifetime of the borrowed resource.

RULE BORROW-004:
    Borrowing MUST NOT imply ownership.

___

# 43. Aliasing

RULE ALIAS-001:
    Aliasing is permitted only when contract semantics remain valid.

RULE ALIAS-002:
    An alias MUST NOT be treated as an owner.

RULE ALIAS-003:
    An alias MUST NOT independently destroy an owned resource.

RULE ALIAS-004:
    Mutation through an alias MUST respect the applicable mutability contract.

___

# 44. Input/Output Aliasing

RULE IOALIAS-001:
    Input/output aliasing SHOULD be avoided.

RULE IOALIAS-002:
    If input/output aliasing is supported, it MUST be explicitly defined.

RULE IOALIAS-003:
    AI MUST NOT introduce aliasing merely to reduce temporary storage.

RULE IOALIAS-004:
    Aliasing behavior MUST remain compatible with existing callers.

___

# 45. Resource Acquisition

RULE ACQUIRE-001:
    Every acquired resource MUST have an identifiable cleanup path when ownership is established.

RULE ACQUIRE-002:
    Acquisition order SHOULD be reflected by reverse destruction order.

RULE ACQUIRE-003:
    AI MUST inspect partial-failure paths after resource acquisition.

RULE ACQUIRE-004:
    AI MUST NOT leak an owned resource on a normal recoverable failure path.

___

# 46. Cleanup

RULE CLEANUP-001:
    Recoverable error paths MUST release resources already owned by the failing execution context.

RULE CLEANUP-002:
    Cleanup MUST respect ownership.

RULE CLEANUP-003:
    Cleanup MUST respect lifetime dependencies.

RULE CLEANUP-004:
    Resources SHOULD normally be released in reverse acquisition order.

RULE CLEANUP-005:
    Cleanup code MUST NOT destroy resources that have already been transferred.

___

# 47. goto

RULE GOTO-001:
    `goto` MAY be used for structured cleanup.

RULE GOTO-002:
    `goto` is not restricted exclusively to cleanup when another valid use exists.

RULE GOTO-003:
    Cleanup labels SHOULD represent meaningful cleanup stages.

RULE GOTO-004:
    AI MUST NOT reject `goto` solely because it is `goto`.

RULE GOTO-005:
    AI MUST NOT introduce unstructured control flow merely to avoid `goto`.

___ 

# EXAMPLE OF 47 & 46

```c
TWR_STATUS_CODE
twr_example_process(const struct TwrInput *input, struct TwrOutput *output)
{
    TWR_STATUS_CODE status;
    struct TwrResourceA *res_a = NULL;
    struct TwrResourceB *res_b = NULL;

    if (input == NULL || output == NULL)
        return TWR_ERROR_INVALID_ARGUMENT;

    status = twr_res_a_create(&res_a);
    if (status != TWR_SUCCESS)
        goto cleanup_none;

    status = twr_res_b_create(&res_b);
    if (status != TWR_SUCCESS)
        goto cleanup_a;

    status = twr_internal_helper(res_a, res_b, output);
    if (status != TWR_SUCCESS)
        goto cleanup_b;

    /* Successful path: Owner cleans up before returning */
    twr_res_b_destroy(&res_b);
    twr_res_a_destroy(&res_a);
    return TWR_SUCCESS;

    /* Error path: Reverse acquisition order */
cleanup_b:
    twr_res_b_destroy(&res_b);
cleanup_a:
    twr_res_a_destroy(&res_a);
cleanup_none:
    return status;
}
```

___

# 48. Allocation Failure

RULE ALLOC-FAIL-001:
    Allocation failure is a runtime/environment failure.

RULE ALLOC-FAIL-002:
    Allocation failure MUST NOT be represented as an internal assertion failure.

RULE ALLOC-FAIL-003:
    Previously acquired resources MUST be cleaned up before returning a recoverable allocation error.

RULE ALLOC-FAIL-004:
    AI MUST NOT assume allocation always succeeds.

___

# 49. Assertions

RULE ASSERT-001:
    Assertions MUST be used for appropriate programmer errors and internal invariants.

RULE ASSERT-002:
    Assertions MUST NOT replace external input validation.

RULE ASSERT-003:
    AI MUST distinguish impossible internal states from valid runtime failures.

RULE ASSERT-004:
    An assertion MAY terminate the affected execution context.

___

# 50. Assertion Failure

RULE ASSERTFAIL-001:
    An assertion failure represents a programmer error or violated internal invariant when used according to this model.

RULE ASSERTFAIL-002:
    AI MUST NOT design elaborate normal recovery around an invariant violation.

RULE ASSERTFAIL-003:
    AI MUST NOT silently convert an invariant violation into an ordinary success/error result.

___

# 51. Let-It-Crash

RULE CRASH-001:
    TWR MAY intentionally terminate execution for unrecoverable programmer or invariant failures.

RULE CRASH-002:
    AI MUST NOT introduce artificial recovery for failures that are explicitly unrecoverable.

RULE CRASH-003:
    Normal recoverable runtime failures MUST continue to use normal error handling.

RULE CRASH-004:
    Let-it-crash behavior MUST NOT be used as a substitute for ordinary error handling.

___

# 52. Crash Isolation

RULE CRASHISO-001:
    A component failure SHOULD NOT unnecessarily cause unrelated components to fail.

RULE CRASHISO-002:
    Component isolation mechanisms MAY be used where architecturally appropriate.

RULE CRASHISO-003:
    AI MUST NOT invent a global recovery framework solely to prevent valid component termination.

___

# 53. Error vs Programmer Failure

RULE FAILURE-001:
    Runtime/environment failure SHOULD use `TWR_STATUS_CODE`.

RULE FAILURE-002:
    Invalid external input SHOULD use normal error handling.

RULE FAILURE-003:
    Internal impossible state MAY use assertion/crash behavior.

RULE FAILURE-004:
    AI MUST NOT classify ordinary user input errors as programmer invariants.

RULE FAILURE-005:
    AI MUST NOT classify impossible internal invariants as ordinary user errors merely to avoid termination.

___

# 54. Error Context

RULE ERRORCTX-001:
    Low-level layers SHOULD return machine-processable status.

RULE ERRORCTX-002:
    Higher-level layers MAY interpret status according to context.

RULE ERRORCTX-003:
    AI MUST NOT overload general status codes with unnecessary contextual detail.

RULE ERRORCTX-004:
    Detailed context SHOULD be represented by the logging/diagnostic layer.

___

# 55. Dependencies

RULE DEP-001:
    Dependencies MUST be explicit.

RULE DEP-002:
    AI MUST NOT rely on transitive dependencies.

RULE DEP-003:
    Every directly used declaration SHOULD have an appropriate direct include/dependency.

RULE DEP-004:
    Unnecessary dependencies SHOULD be removed.

RULE DEP-005:
    AI MUST NOT introduce dependencies without task or architectural justification.

___

# 56. Dependency Direction

RULE DEPDIR-001:
    Architectural dependency direction MUST remain intentional.

RULE DEPDIR-002:
    Circular component dependencies are prohibited.

RULE DEPDIR-003:
    AI MUST NOT create circular dependencies to simplify local implementation.

RULE DEPDIR-004:
    Dependency inversion MAY be used when it preserves or improves the intended architecture.

___

# 57. Forward Declarations

RULE FWD-001:
    Forward declarations MAY resolve language-level declaration dependencies.

RULE FWD-002:
    A forward declaration MUST NOT be treated as a solution to an architectural dependency cycle.

RULE FWD-003:
    AI MUST distinguish declaration dependency from component dependency.

___

# 58. Includes

RULE INCLUDE-001:
    Includes MUST be direct and minimal.

RULE INCLUDE-002:
    A source/header SHOULD include what it directly uses.

RULE INCLUDE-003:
    AI MUST NOT rely on transitive includes for required declarations.

RULE INCLUDE-004:
    Unused includes SHOULD be removed when modifying the affected file.

RULE INCLUDE-005:
    AI MUST NOT remove an include solely because it appears unused without checking conditional compilation or direct declaration requirements.

___

# 59. Header/Source Separation

RULE HEADER-001:
    Headers SHOULD primarily contain:
    - declarations
    - public types
    - interface definitions
    - structs
    - enums
    - constants
    - macros
    - required inline definitions

RULE HEADER-002:
    Implementation SHOULD remain in source files where practical.

RULE HEADER-003:
    Static inline functions MAY exist in headers when justified.

RULE HEADER-004:
    Macros MAY exist in headers when their semantics require it.

RULE HEADER-005:
    AI MUST NOT expose implementation details through headers unnecessarily.

___

# 60. Public/Private Headers

RULE HEADERVIS-001:
    Public headers define externally usable interfaces.

RULE HEADERVIS-002:
    Private headers define internal implementation interfaces.

RULE HEADERVIS-003:
    Internal declarations MUST NOT become public solely to simplify inclusion.

RULE HEADERVIS-004:
    Public headers SHOULD avoid implementation leakage.

___

# 61. Structs

RULE STRUCT-001:
    C structs SHOULD use explicit struct tags.

Example:

`struct TwrHttpRequest`

RULE STRUCT-002:
    Public struct names MUST follow the TWR naming namespace.

RULE STRUCT-003:
    Structs MAY represent data-only objects.

RULE STRUCT-004:
    Structs MAY be opaque when layout must remain private.

___

# 62. Struct Layout

RULE STRUCTLAYOUT-001:
    Public struct layout MUST NOT automatically be treated as stable ABI.

RULE STRUCTLAYOUT-002:
    Field order MAY change unless ABI explicitly stabilizes it.

RULE STRUCTLAYOUT-003:
    Padding MAY change unless ABI explicitly stabilizes it.

RULE STRUCTLAYOUT-004:
    Alignment MAY change unless ABI explicitly stabilizes it.

RULE STRUCTLAYOUT-005:
    Internal representation MAY change unless ABI explicitly stabilizes it.

RULE STRUCTLAYOUT-006:
    AI MUST NOT assume public visibility automatically means layout stability.

___

# 63. Struct Initialization

RULE STRUCTINIT-001:
    Data-only structs MAY use ordinary C initialization.

RULE STRUCTINIT-002:
    Behavior and lifecycle SHOULD normally be provided through free functions.

RULE STRUCTINIT-003:
    TWR C APIs MUST NOT require C++-style methods merely to represent object behavior.

RULE STRUCTINIT-004:
    AI MUST preserve established initialization contracts.

___

# 64. Data and Behavior

RULE DATABEH-001:
    Data and behavior SHOULD remain conceptually separated.

RULE DATABEH-002:
    Function pointers MAY represent callbacks or behavior data.

RULE DATABEH-003:
    Opaque structs MAY encapsulate behavior implementation.

RULE DATABEH-004:
    AI MUST NOT introduce object-oriented abstraction merely because an object-like structure exists.

___

# 65. Enumerations

RULE ENUM-001:
    Enums SHOULD use explicit tags where appropriate.

RULE ENUM-002:
    TWR enum values MUST use the TWR namespace.

RULE ENUM-003:
    Enum values MUST NOT collide with unrelated public names.

___

# 66. Naming Conventions

RULE NAMING-001:
    Public structs SHOULD use PascalCase.

RULE NAMING-002:
    Functions SHOULD use camel_case.

RULE NAMING-003:
    Variables SHOULD use camel_case.

RULE NAMING-004:
    Files MUST use kebab-case where the TWR file naming convention applies.

RULE NAMING-005:
    Macros MUST use upper-case naming.

RULE NAMING-006:
    Constants MUST use upper-case naming where the TWR convention applies.

RULE NAMING-007:
    Enum values MUST use `TWR_*`.

___

# 67. Public Namespace

RULE NAMESPACE-001:
    Public functions and identifiers MUST use the `twr_*` namespace where applicable.

RULE NAMESPACE-002:
    Public macros and constants MUST use the `TWR_*` namespace.

RULE NAMESPACE-003:
    Public status values MUST use the TWR namespace.

RULE NAMESPACE-004:
    Public enum values MUST use the TWR namespace.

RULE NAMESPACE-005:
    Internal identifiers MAY use narrower implementation-specific naming because encapsulation prevents external collision.

___

# 68. File Names

RULE FILE-001:
    TWR source file names SHOULD use kebab-case.

RULE FILE-002:
    AI MUST preserve existing established file naming unless modification requires otherwise.

___

# 69. C Style

RULE C-001:
    C code SHOULD follow Unix/Linux/BSD conventions.

RULE C-002:
    C code MUST remain idiomatic to the project context.

RULE C-003:
    AI MUST NOT impose unrelated language conventions from other ecosystems.

RULE C-004:
    C APIs SHOULD prefer explicit interfaces and contracts.

___

# 70. C++ Style

RULE CPP-001:
    C++ code SHOULD follow an orthodox C++ style consistent with the project.

RULE CPP-002:
    C++ files SHOULD use `.cc` where the TWR convention specifies it.

RULE CPP-003:
    AI MUST NOT import arbitrary framework-specific conventions without project authorization.

RULE CPP-004:
    C++ abstraction MUST have concrete justification.

___

# 71. Formatting

RULE FORMAT-001:
    Formatting MUST be deterministic.

RULE FORMAT-002:
    Formatting SHOULD follow established project and language standards.

RULE FORMAT-003:
    AI MUST NOT introduce inconsistent formatting into modified code.

RULE FORMAT-004:
    Formatting changes SHOULD remain within task scope unless formatting is required for correctness.

___

# 72. Comments

RULE COMMENT-001:
    Comments SHOULD explain:
    - why non-obvious behavior exists
    - non-obvious algorithms
    - workarounds
    - invariants
    - constraints

RULE COMMENT-002:
    Comments MUST NOT merely restate obvious code.

RULE COMMENT-003:
    Large contracts SHOULD be represented in appropriate documentation rather than repeated as implementation comments.

RULE COMMENT-004:
    Comments SHOULD use English where the project documentation convention requires it.

RULE COMMENT-005:
    AI MUST NOT generate comments whose only purpose is to narrate trivial syntax.

___

# 73. Macros

RULE MACRO-001:
    Macros MAY be used when compile-time semantics justify them.

RULE MACRO-002:
    Macros are not universally prohibited.

RULE MACRO-003:
    AI MUST NOT use macros merely to obscure simple code.

RULE MACRO-004:
    AI MUST prefer ordinary language constructs when they provide the required semantics without unnecessary macro complexity.

___

# 74. Constants

RULE CONSTVAL-001:
    Semantically meaningful constant values SHOULD have meaningful names.

RULE CONSTVAL-002:
    Magic values SHOULD be avoided.

RULE CONSTVAL-003:
    AI MUST NOT create a macro for every literal merely to eliminate literals.

RULE CONSTVAL-004:
    A literal MAY remain when its meaning is obvious and no stable semantic constant is required.

___

# 75. Casts

RULE CAST-001:
    Casts MAY be used when semantically necessary.

RULE CAST-002:
    Unnecessary casts SHOULD be avoided.

RULE CAST-003:
    AI MUST NOT cast solely to silence a warning when the underlying type problem remains.

RULE CAST-004:
    A cast MUST NOT conceal an ownership, lifetime, aliasing, or ABI error.

___

# 76. Pointer Arithmetic

RULE PTR-001:
    Normal C pointer arithmetic is permitted.

RULE PTR-002:
    AI MUST NOT prohibit pointer arithmetic merely because it is low-level.

RULE PTR-003:
    Pointer arithmetic MUST remain within the applicable object/lifetime rules.

___

# 77. Integer Types

RULE INT-001:
    `size_t` MUST be used for:
    - sizes
    - byte counts
    - memory quantities
    - buffer lengths
    - object counts
    - other non-negative size-like quantities

RULE INT-002:
    AI MUST NOT use `long` when a more semantically appropriate type exists.

RULE INT-003:
    AI MUST NOT use unsigned integer types merely because a value is expected to be non-negative.

RULE INT-004:
    Fixed-width integers SHOULD be used when width is semantically required.

RULE INT-005:
    Integer type selection MUST follow semantic meaning.

___

# 78. Strings

RULE STRING-001:
    TWR-specific string abstractions MAY exist.

RULE STRING-002:
    C strings remain valid where the API contract permits them.

RULE STRING-003:
    String representation MUST be selected according to contract requirements.

RULE STRING-004:
    String contracts MUST define, where applicable:
    - ownership
    - lifetime
    - mutability
    - allocation
    - destruction
    - representation

RULE STRING-005:
    AI MUST NOT assume a string abstraction is mandatory unless the applicable contract requires it.

___

# 79. Memory Allocation

RULE MEMORY-001:
    Stack allocation SHOULD be preferred where practical and semantically appropriate.

RULE MEMORY-002:
    Heap allocation MAY be used when required by:
    - lifetime
    - size
    - ownership
    - dynamic behavior
    - API requirements

RULE MEMORY-003:
    TWR memory facilities SHOULD be used when appropriate to the applicable architecture.

RULE MEMORY-004:
    AI MUST NOT introduce heap allocation solely because it is convenient.

RULE MEMORY-005:
    AI MUST NOT move data to the heap without a lifetime or ownership reason.

___

# 80. Global State

RULE GLOBAL-001:
    Mutable global state SHOULD generally be avoided.

RULE GLOBAL-002:
    Mutable global state is not universally prohibited.

RULE GLOBAL-003:
    If mutable global state exists, AI MUST establish or preserve:
    - ownership
    - lifetime
    - initialization
    - access rules
    - synchronization requirements

RULE GLOBAL-004:
    `static` MAY be used for internal linkage.

___

# 81. Thread Safety

RULE THREAD-001:
    Thread safety MUST NOT be assumed unless specified.

RULE THREAD-002:
    A contract MUST define thread-safety behavior when concurrency semantics matter.

RULE THREAD-003:
    AI MUST identify shared mutable resources.

RULE THREAD-004:
    AI MUST preserve synchronization requirements.

RULE THREAD-005:
    AI MUST NOT add synchronization merely because concurrency is theoretically possible.

___

# 82. Concurrency Contracts

Where concurrency is relevant, AI MUST determine:

* ownership
* shared resources
* synchronization
* atomicity
* ordering
* lifetime
* thread access
* mutation rules

RULE CONCUR-001:
    Mutexes, atomics, locks, TLS, and other synchronization mechanisms are implementation choices unless explicitly specified.

RULE CONCUR-002:
    The observable concurrency contract MUST be preserved.

RULE CONCUR-003:
    AI MUST NOT replace a synchronization mechanism if doing so changes its contract.

___

# 83. errno

RULE ERRNO-001:
    `errno` MAY be used internally.

RULE ERRNO-002:
    Public TWR APIs SHOULD expose `TWR_STATUS_CODE`.

RULE ERRNO-003:
    `errno` MUST NOT leak into a stable TWR public API unless explicitly specified.

RULE ERRNO-004:
    AI MUST preserve explicit errno-based contracts when they already form part of an interface.

___

# 84. System Calls

RULE SYSCALL-001:
    TWR implementations MAY call system calls directly.

RULE SYSCALL-002:
    System calls MAY be wrapped by TWR abstractions.

RULE SYSCALL-003:
    System-specific mechanisms MUST NOT leak unnecessarily into stable public APIs.

RULE SYSCALL-004:
    AI MUST preserve explicit syscall semantics when implementing a syscall-facing component.

___

# 85. Syscall ABI

RULE SYSCALLABI-001:
    Stable syscall ABI MUST be append-only where specified.

RULE SYSCALLABI-002:
    Existing syscall interfaces MUST remain compatible.

RULE SYSCALLABI-003:
    New syscall functionality SHOULD be additive.

RULE SYSCALLABI-004:
    AI MUST NOT break existing syscall consumers implicitly.

___

# 86. Implementation Freedom

The following MAY change when the relevant contract remains satisfied:

* algorithms
* internal helpers
* internal structs
* allocators
* synchronization mechanisms
* internal headers
* cleanup organization
* control flow
* compiler mechanisms
* internal representation
* source organization

RULE IMPL-001:
    Implementation freedom MUST NOT be interpreted as interface freedom.

RULE IMPL-002:
    An implementation MAY change only within the constraints of its contract.

___

# 87. Contract Over Mechanism

RULE CONTRACTMECH-001:
    Contract takes precedence over mechanism.

RULE CONTRACTMECH-002:
    AI MUST determine what behavior is required before choosing how to implement it.

RULE CONTRACTMECH-003:
    AI MUST NOT preserve an implementation mechanism when the contract permits replacement.

RULE CONTRACTMECH-004:
    AI MUST NOT change a required observable behavior merely because a different mechanism appears cleaner.

___

# 88. Control Flow

RULE FLOW-001:
    There is no universal single-exit requirement.

RULE FLOW-002:
    Early returns are permitted.

RULE FLOW-003:
    Early returns SHOULD be used when they improve safety, clarity, or unnecessary work avoidance.

RULE FLOW-004:
    AI MUST NOT add artificial single-exit structure merely to satisfy a nonexistent rule.

RULE FLOW-005:
    Control flow MUST preserve resource cleanup and ownership correctness.

___

# 89. Internal Contracts

RULE INTERNAL-001:
    Internal functions MAY rely on caller-guaranteed preconditions.

RULE INTERNAL-002:
    AI SHOULD NOT add redundant validation to every internal function.

RULE INTERNAL-003:
    Internal invariants SHOULD be enforced with assertions where appropriate.

RULE INTERNAL-004:
    Internal trust MUST NOT cross an unvalidated external boundary.

___

# 90. Validation Boundaries

RULE VALIDBOUND-001:
    External or untrusted input MUST be validated at the appropriate boundary.

RULE VALIDBOUND-002:
    Once a contract has been established, downstream internal functions MAY rely on it.

RULE VALIDBOUND-003:
    AI MUST NOT repeatedly validate the same established invariant without reason.

RULE VALIDBOUND-004:
    Validation MUST occur before unsafe interpretation or use.

___

# 91. Preconditions

RULE PRECOND-001:
    AI MUST respect existing function preconditions.

RULE PRECOND-002:
    Invalid external input SHOULD produce normal error handling.

RULE PRECOND-003:
    Violation of a caller-guaranteed internal precondition MAY be treated as a programmer failure.

RULE PRECOND-004:
    AI MUST NOT weaken an established precondition without explicit contract change.

___

# 92. Resource Independence

RULE RESINDEP-001:
    Resource lifetimes SHOULD remain independently understandable.

RULE RESINDEP-002:
    AI MUST NOT introduce hidden resource dependencies.

RULE RESINDEP-003:
    Destroying one resource MUST NOT implicitly invalidate unrelated resources unless the contract explicitly defines such dependency.

RULE RESINDEP-004:
    Resource dependency MUST be explicit when one resource requires another to remain alive.

___

# 93. API Compatibility

RULE APICOMP-001:
    Public API behavior MUST remain compatible unless a breaking change is explicitly authorized.

RULE APICOMP-002:
    Ownership behavior is part of API compatibility.

RULE APICOMP-003:
    Error behavior is part of API compatibility.

RULE APICOMP-004:
    Input validity is part of API compatibility.

RULE APICOMP-005:
    Output validity is part of API compatibility.

RULE APICOMP-006:
    Aliasing behavior is part of API compatibility.

___

# 94. Lifetime Compatibility

RULE LIFECOMP-001:
    API changes MUST NOT silently shorten a caller-visible resource lifetime.

RULE LIFECOMP-002:
    API changes MUST NOT silently extend ownership responsibilities.

RULE LIFECOMP-003:
    API changes MUST NOT silently alter destruction responsibility.

RULE LIFECOMP-004:
    Lifetime compatibility MUST be checked independently of type compatibility.

___

# 95. Aliasing Compatibility

RULE ALIASCOMP-001:
    Existing aliasing guarantees MUST remain valid.

RULE ALIASCOMP-002:
    AI MUST NOT introduce new mutation through aliases without contract authorization.

RULE ALIASCOMP-003:
    AI MUST NOT remove required aliasing support without explicit authorization.

___

# 96. Minimal Interfaces

RULE MINIFACE-001:
    Interfaces SHOULD expose only required functionality.

RULE MINIFACE-002:
    AI MUST NOT add speculative API.

RULE MINIFACE-003:
    AI MUST NOT expose internal helpers solely because they may be useful later.

RULE MINIFACE-004:
    API additions MUST have a current architectural or functional reason.

___

# 97. Composition

RULE COMPOSE-001:
    Components SHOULD be composable.

RULE COMPOSE-002:
    Interfaces SHOULD support composition without unnecessary coupling.

RULE COMPOSE-003:
    AI MUST NOT introduce hidden global state as a substitute for explicit composition.

___

# 98. Small Interfaces

RULE SMALLIFACE-001:
    Small interfaces SHOULD be preferred.

RULE SMALLIFACE-002:
    Interface size MUST be determined by conceptual responsibility, not arbitrary function count.

RULE SMALLIFACE-003:
    AI MUST NOT split a coherent interface merely to satisfy an arbitrary numerical rule.

___

# 99. Implementation Abstraction

RULE ABSTRACTION-001:
    Abstraction SHOULD exist at meaningful stable boundaries.

RULE ABSTRACTION-002:
    Internal implementation MAY remain direct and simple.

RULE ABSTRACTION-003:
    AI MUST NOT introduce abstraction solely because abstraction is considered stylistically desirable.

RULE ABSTRACTION-004:
    AI MUST NOT leak implementation through an abstraction that claims to be stable.

___

# 100. Internal Helpers

RULE HELPER-001:
    Internal helpers SHOULD remain private when externally unnecessary.

RULE HELPER-002:
    Static internal functions MAY be used.

RULE HELPER-003:
    AI MUST NOT expose a helper merely to reuse it from another internal unit when an appropriate private mechanism exists.

___

# 101. Source Visibility

RULE VISIBILITY-001:
    Symbol visibility SHOULD be as narrow as practical.

RULE VISIBILITY-002:
    Public symbols MUST have an actual public requirement.

RULE VISIBILITY-003:
    Internal symbols SHOULD remain internal.

RULE VISIBILITY-004:
    AI MUST NOT widen visibility merely to resolve an implementation inconvenience.

___

# 102. Public Implementation Leakage

RULE LEAK-001:
    Public headers MUST NOT unnecessarily expose implementation details.

RULE LEAK-002:
    Internal structs SHOULD remain private when layout is not part of the public contract.

RULE LEAK-003:
    Internal synchronization mechanisms SHOULD remain private.

RULE LEAK-004:
    Internal allocators SHOULD remain private unless explicitly part of the interface.

___

# 103. Error Cleanup

RULE ERRCLN-001:
    Every recoverable error path MUST be inspected for resource cleanup.

RULE ERRCLN-002:
    AI MUST trace ownership backward from every failure path.

RULE ERRCLN-003:
    AI MUST NOT return early while bypassing required cleanup.

RULE ERRCLN-004:
    Cleanup itself MUST respect failure semantics.

___

# 104. Successful Cleanup

RULE SUCCCLN-001:
    Successful execution MUST release temporary resources that are no longer required.

RULE SUCCCLN-002:
    Resources intentionally returned to the caller MUST NOT be destroyed during successful cleanup.

RULE SUCCCLN-003:
    AI MUST distinguish temporary ownership from transferred ownership.

___

# 105. Failed Operations

RULE FAILOP-001:
    A failed operation MUST NOT return success.

RULE FAILOP-002:
    Failure MUST be handled, propagated, translated, or recovered according to contract.

RULE FAILOP-003:
    AI MUST NOT suppress errors to simplify callers.

RULE FAILOP-004:
    AI MUST NOT convert recoverable failures into assertions without contract justification.

___

# 106. Resource Creation

RULE RESCREATE-001:
    Resource creation MUST establish ownership semantics.

RULE RESCREATE-002:
    The caller normally owns a resource created for the caller unless the contract specifies otherwise.

RULE RESCREATE-003:
    Creation failure MUST not leak partially acquired resources.

RULE RESCREATE-004:
    Partially constructed resources MUST be cleaned according to their valid destruction semantics.

___

# 107. Resource Destruction Interfaces

RULE RESDESTROY-001:
    Every externally owned destroyable resource SHOULD have an explicit destruction mechanism.

RULE RESDESTROY-002:
    Destruction interfaces MUST be compatible with ownership rules.

RULE RESDESTROY-003:
    AI MUST NOT make destruction ambiguous.

RULE RESDESTROY-004:
    AI MUST NOT introduce automatic destruction semantics where the existing API defines explicit ownership without authorization.

___

# 108. Memory Safety

Memory safety MUST be evaluated through:

* ownership
* lifetime
* input validity
* output validity
* aliasing
* cleanup
* resource dependencies

RULE MEMSAFE-001:
    AI MUST NOT evaluate memory safety solely from pointer syntax.

RULE MEMSAFE-002:
    AI MUST trace resource lifetime across success and failure paths.

RULE MEMSAFE-003:
    AI MUST inspect use-after-free risks.

RULE MEMSAFE-004:
    AI MUST inspect double-destruction risks.

RULE MEMSAFE-005:
    AI MUST inspect leak risks.

RULE MEMSAFE-006:
    AI MUST inspect invalid aliasing.

___

# 109. Stack Usage

RULE STACK-001:
    Stack storage SHOULD be preferred where practical.

RULE STACK-002:
    AI MUST consider lifetime and object size.

RULE STACK-003:
    AI MUST NOT place arbitrarily large dynamic objects on the stack.

RULE STACK-004:
    Stack use MUST remain compatible with the applicable runtime constraints.

___

# 110. Heap Usage

RULE HEAP-001:
    Heap allocation MAY be used when lifetime, size, ownership, or dynamic requirements justify it.

RULE HEAP-002:
    Every heap-owned resource MUST have a destruction path.

RULE HEAP-003:
    Heap allocation failure MUST be handled.

RULE HEAP-004:
    AI MUST NOT introduce heap allocation merely to avoid straightforward stack storage.

___

# 111. Memory Abstractions

RULE MEMABS-001:
    TWR memory facilities SHOULD be used where appropriate.

RULE MEMABS-002:
    AI MUST preserve explicit allocator contracts.

RULE MEMABS-003:
    Allocation and destruction mechanisms MUST remain compatible.

RULE MEMABS-004:
    An allocator choice MUST NOT silently alter ownership semantics.

___

# 112. Logging Interface

RULE LOGGER-001:
    TWR logging interfaces SHOULD be used where the architecture specifies them.

RULE LOGGER-002:
    Logging implementation MAY vary.

RULE LOGGER-003:
    Logging API stability MUST be preserved where specified.

RULE LOGGER-004:
    Logging MUST NOT replace operation status.

___

# 113. User Output

RULE USEROUT-001:
    User-facing output belongs to the appropriate higher-level component.

RULE USEROUT-002:
    Low-level libraries SHOULD NOT print directly to users.

RULE USEROUT-003:
    AI MUST NOT add user-facing output to a low-level function merely to make debugging easier.

RULE USEROUT-004:
    Debug diagnostics MUST use the appropriate diagnostic/logging mechanism.

___

# 114. Diagnostics

RULE DIAG-001:
    Diagnostics MAY contain more information than status codes.

RULE DIAG-002:
    Diagnostic detail MUST NOT alter the public status contract.

RULE DIAG-003:
    AI SHOULD provide contextual diagnostics at the appropriate layer.

RULE DIAG-004:
    AI SHOULD avoid duplicate diagnostic emission.

___

# 115. Error Taxonomy

RULE ERRORTAX-001:
    TWR error taxonomy SHOULD remain small and general.

RULE ERRORTAX-002:
    AI MUST NOT create a new status code for every contextual detail.

RULE ERRORTAX-003:
    Detailed context SHOULD be represented through diagnostics when appropriate.

RULE ERRORTAX-004:
    New status codes MUST have a concrete semantic reason.

___

# 116. Boolean Status Separation

RULE BOOLSTATUS-001:
    Boolean result and operation status are separate concepts.

RULE BOOLSTATUS-002:
    If an operation may fail and also produces a boolean result, the status and boolean data MUST remain distinguishable.

RULE BOOLSTATUS-003:
    AI MUST NOT overload `false` to represent both "operation failed" and "operation succeeded with false result".

___

# 117. External Input vs Assertions

RULE ASSERTBOUND-001:
    External input MUST be validated.

RULE ASSERTBOUND-002:
    Internal impossible states MAY be asserted.

RULE ASSERTBOUND-003:
    AI MUST identify the trust boundary before selecting validation or assertion.

RULE ASSERTBOUND-004:
    User-controlled data MUST NOT be treated as an invariant.

___

# 118. Invariants

RULE INVARIANT-001:
    Internal invariants MUST be preserved.

RULE INVARIANT-002:
    AI MUST identify invariants affected by a modification.

RULE INVARIANT-003:
    Invariant violations MUST NOT be silently ignored.

RULE INVARIANT-004:
    Appropriate internal invariants SHOULD be asserted.

RULE INVARIANT-005:
    AI MUST NOT invent invariants that are not supported by the applicable architecture or implementation.

___

# 119. Recovery

RULE RECOVERY-001:
    Recovery MUST be proportional to failure severity.

RULE RECOVERY-002:
    Normal runtime failures SHOULD be recoverable where the contract allows.

RULE RECOVERY-003:
    Invariant violations do not require elaborate recovery.

RULE RECOVERY-004:
    A component MAY terminate on unrecoverable programmer failure.

RULE RECOVERY-005:
    Supervision MAY determine how a terminated component affects the larger system.

___

# 120. Concurrent Ownership

RULE CONROWNER-001:
    Ownership rules remain valid under concurrency.

RULE CONROWNER-002:
    Concurrent access MUST NOT create ambiguous destruction responsibility.

RULE CONROWNER-003:
    Shared ownership MUST be explicitly defined when used.

RULE CONROWNER-004:
    Thread safety does not automatically imply ownership transfer.

___

# 121. Mutable Global State

RULE GLOBALSYNC-001:
    Shared mutable global state MUST have defined synchronization semantics.

RULE GLOBALSYNC-002:
    AI MUST NOT assume global state is thread-safe.

RULE GLOBALSYNC-003:
    Global initialization MUST have a defined contract when initialization order matters.

RULE GLOBALSYNC-004:
    AI MUST NOT introduce mutable global state merely to avoid parameter passing.

___

# 122. Circular Dependencies

RULE CYCLE-001:
    Architectural component cycles are prohibited.

RULE CYCLE-002:
    Language-level declaration cycles MAY be resolved through forward declarations.

RULE CYCLE-003:
    Forward declarations MUST NOT conceal an architectural cycle.

RULE CYCLE-004:
    AI MUST identify dependency cycles before adding dependencies.

___

# 123. Developer Choice

The following are implementation choices unless constrained by a higher-authority contract:

* algorithm
* helper organization
* internal struct representation
* allocator implementation
* synchronization implementation
* cleanup organization
* control flow
* internal header organization
* compiler mechanisms
* implementation-level abstraction

RULE CHOICE-001:
    AI MAY select among valid implementation choices.

RULE CHOICE-002:
    AI MUST select the simplest valid implementation unless another project constraint applies.

RULE CHOICE-003:
    AI MUST NOT convert an implementation preference into a project requirement.

RULE CHOICE-004:
    AI MUST preserve developer decisions already present in the repository.

___

# 124. SHOULD Handling

RULE SHOULD-001:
    SHOULD rules represent preferred engineering behavior.

RULE SHOULD-002:
    AI MAY deviate from SHOULD when a concrete reason exists.

RULE SHOULD-003:
    AI MUST NOT fabricate reasons merely to bypass a SHOULD rule.

RULE SHOULD-004:
    If deviation materially affects architecture, AI SHOULD report it.

RULE SHOULD-005:
    AI MUST NOT describe a SHOULD deviation as a hard-rule violation unless another MUST rule is violated.

___

# 125. Unix/POSIX Baseline

RULE UNIX-001:
    Unix/POSIX/Linux/BSD conventions SHOULD be treated as the default baseline where TWR does not explicitly define another behavior.

RULE UNIX-002:
    AI MUST NOT reject a design solely because it is low-level or Unix-like.

RULE UNIX-003:
    AI MUST NOT import non-Unix abstractions solely because they are common elsewhere.

RULE UNIX-004:
    TWR-specific contracts override the generic Unix/POSIX baseline.

___

# 126. Stable Interface / Replaceable Implementation

RULE STABLE-001:
    A stable interface MUST be preserved independently from its implementation.

RULE STABLE-002:
    Internal implementation MAY be replaced when observable contract remains compatible.

RULE STABLE-003:
    AI MUST NOT expose implementation details merely because they currently exist.

RULE STABLE-004:
    AI MUST NOT make implementation details ABI requirements without explicit authorization.

RULE STABLE-005:
    Stable interfaces SHOULD minimize assumptions about implementation mechanism.

___

# 127. Core Ownership Model

RULE COREOWNER-001:
    Ownership MUST be explicit.

RULE COREOWNER-002:
    Creation normally establishes caller ownership unless specified otherwise.

RULE COREOWNER-003:
    Use does not transfer ownership.

RULE COREOWNER-004:
    Mutation does not transfer ownership.

RULE COREOWNER-005:
    Borrowers do not destroy.

RULE COREOWNER-006:
    Transferred resources are destroyed by the new owner.

RULE COREOWNER-007:
    Ownership and lifetime MUST remain distinguishable.

___

# 128. Core Error Model

RULE COREERROR-001:
    Operation status MUST be represented by the appropriate status mechanism.

RULE COREERROR-002:
    Recoverable failures MUST be represented as failures.

RULE COREERROR-003:
    Errors MUST propagate or be intentionally handled.

RULE COREERROR-004:
    Programmer invariant failures MAY assert/crash.

RULE COREERROR-005:
    Diagnostics MUST NOT replace status.

___

# 129. Core Resource Model

RULE CORERES-001:
    Every owned resource MUST have a valid lifetime.

RULE CORERES-002:
    Every owned resource MUST have a valid destruction path.

RULE CORERES-003:
    Failure paths MUST release resources no longer owned by another party.

RULE CORERES-004:
    Ownership transfer MUST be explicit.

RULE CORERES-005:
    Aliases MUST NOT become accidental owners.

___

# 130. Core Data Flow Model

RULE COREDATA-001:
    Input and output roles MUST remain distinguishable.

RULE COREDATA-002:
    Input data is immutable unless explicitly mutable.

RULE COREDATA-003:
    Output validity depends on operation status and contract.

RULE COREDATA-004:
    AI MUST NOT assume output validity after failure.

RULE COREDATA-005:
    AI MUST NOT introduce undocumented input/output aliasing.

___

# 131. Core Dependency Model

RULE COREDEP-001:
    Dependencies MUST be explicit.

RULE COREDEP-002:
    Dependencies SHOULD be minimal.

RULE COREDEP-003:
    Transitive dependencies MUST NOT substitute for direct declarations.

RULE COREDEP-004:
    Architectural dependency cycles are prohibited.

RULE COREDEP-005:
    Internal implementation MUST NOT create unnecessary public dependencies.

___

# 132. Core API Evolution Model

RULE COREEVOLVE-001:
    Public stable APIs MUST evolve additively.

RULE COREEVOLVE-002:
    Existing consumers MUST remain compatible.

RULE COREEVOLVE-003:
    Deprecated interfaces MAY remain as compatibility layers.

RULE COREEVOLVE-004:
    New behavior SHOULD normally be introduced through additions.

RULE COREEVOLVE-005:
    AI MUST NOT remove an existing interface merely because a newer interface exists.

___

# 133. AI Modification Procedure

Before modification:

STEP 001:
    Identify the requested task.

STEP 002:
    Identify the exact files/components affected.

STEP 003:
    Identify applicable architecture contracts.

STEP 004:
    Identify applicable API/ABI contracts.

STEP 005:
    Identify ownership and lifetime contracts.

STEP 006:
    Identify dependency and concurrency constraints.

STEP 007:
    Classify the requested change.

STEP 008:
    Identify unknown information.

STEP 009:
    Do not infer unresolved project-specific facts.

During modification:

STEP 010:
    Preserve unrelated behavior.

STEP 011:
    Preserve stable interfaces.

STEP 012:
    Preserve ownership semantics.

STEP 013:
    Preserve lifetime semantics.

STEP 014:
    Preserve error semantics.

STEP 015:
    Preserve dependency direction.

STEP 016:
    Use the minimum necessary implementation change.

STEP 017:
    Do not introduce speculative abstraction.

STEP 018:
    Do not introduce unrelated refactoring.

After modification:

STEP 019:
    Check compilation-relevant correctness.

STEP 020:
    Check public API compatibility.

STEP 021:
    Check ABI compatibility where applicable.

STEP 022:
    Check ownership.

STEP 023:
    Check lifetime.

STEP 024:
    Check aliasing.

STEP 025:
    Check error propagation.

STEP 026:
    Check error cleanup.

STEP 027:
    Check resource destruction.

STEP 028:
    Check dependency direction.

STEP 029:
    Check external-input validation.

STEP 030:
    Check internal invariants.

STEP 031:
    Check concurrency requirements where applicable.

STEP 032:
    Check naming and formatting.

STEP 033:
    Check task-scope compliance.

STEP 034:
    Report unresolved issues.

___

# 134. AI Validation Procedure

RULE VALIDATE-001:
    AI MUST validate the modified code against applicable MUST rules.

RULE VALIDATE-002:
    AI MUST inspect every modified public interface.

RULE VALIDATE-003:
    AI MUST inspect every changed resource path.

RULE VALIDATE-004:
    AI MUST inspect both success and failure paths.

RULE VALIDATE-005:
    AI MUST inspect callers when function contracts change.

RULE VALIDATE-006:
    AI MUST inspect callees when resource or status semantics change.

RULE VALIDATE-007:
    AI MUST check for new dependency cycles.

RULE VALIDATE-008:
    AI MUST check for new public implementation leakage.

RULE VALIDATE-009:
    AI MUST check whether any existing consumer can observe an incompatible change.

RULE VALIDATE-010:
    AI MUST NOT declare compliance based solely on compilation.

___

# 135. AI Conflict Handling

RULE CONFLICT-001:
    If an existing implementation conflicts with an explicit higher-authority contract, AI MUST follow the higher-authority contract.

RULE CONFLICT-002:
    If two explicit contracts conflict and precedence is undefined, AI MUST report the conflict.

RULE CONFLICT-003:
    AI MUST NOT silently choose one conflicting contract.

RULE CONFLICT-004:
    AI MUST NOT rewrite project architecture to resolve an unresolved documentation conflict unless explicitly authorized.

RULE CONFLICT-005:
    If safe implementation is impossible because of unresolved conflict, AI MUST NOT fabricate a resolution.

___

# 136. AI Prohibited Behavior

AI MUST NOT:

* invent project requirements
* invent architecture
* invent API semantics
* invent ABI guarantees
* invent ownership semantics
* invent lifetime guarantees
* invent aliasing guarantees
* silently alter error semantics
* silently alter public behavior
* silently alter stable interfaces
* remove public APIs without authorization
* break ABI without authorization
* add speculative APIs
* add speculative abstractions
* add unrelated dependencies
* perform unrelated refactoring
* rely on transitive dependencies
* suppress recoverable errors
* convert failure into success
* use assertions for ordinary external input errors
* use `void` to hide meaningful failure
* silently transfer ownership
* destroy borrowed resources
* use expired resources
* introduce undocumented aliasing
* introduce hidden global state
* introduce architectural dependency cycles
* expose implementation details unnecessarily
* widen symbol visibility unnecessarily
* assume NULL semantics
* assume thread safety
* assume struct layout stability
* assume system-specific behavior is automatically part of the TWR API
* replace project-specific rules with generic programming advice
* treat generated code as architectural authority
* claim compliance without validation
* claim that unknown information is known
* silently ignore contradictions
* silently weaken rules
* silently expand task scope

___

# 137. Final Compliance Rules

Before reporting a completed TWR modification, AI MUST verify:

CHECK 001:
    Task scope was preserved.

CHECK 002:
    Applicable architecture contracts were identified.

CHECK 003:
    Existing project decisions were preserved.

CHECK 004:
    Unknown information was not invented.

CHECK 005:
    Public interfaces remain compatible where required.

CHECK 006:
    ABI remains compatible where required.

CHECK 007:
    Function responsibilities remain coherent.

CHECK 008:
    Input semantics remain valid.

CHECK 009:
    Output semantics remain valid.

CHECK 010:
    Ownership remains explicit.

CHECK 011:
    Lifetime remains valid.

CHECK 012:
    Aliasing remains valid.

CHECK 013:
    Resource cleanup remains complete.

CHECK 014:
    Failure paths remain correct.

CHECK 015:
    Errors are not silently discarded.

CHECK 016:
    Programmer failures remain distinguishable from runtime failures.

CHECK 017:
    Assertions are not used for external input validation.

CHECK 018:
    Dependencies remain explicit.

CHECK 019:
    No architectural dependency cycle was introduced.

CHECK 020:
    Headers do not expose unnecessary implementation details.

CHECK 021:
    Public namespace rules remain satisfied.

CHECK 022:
    Required integer-type rules remain satisfied.

CHECK 023:
    Thread-safety requirements remain satisfied.

CHECK 024:
    System interface behavior remains contract-compatible.

CHECK 025:
    No unrelated modification was introduced.

CHECK 026:
    No prohibited AI behavior occurred.

RULE FINAL-001:
    If every applicable mandatory rule is satisfied, AI MAY report the modification as compliant.

RULE FINAL-002:
    If any applicable mandatory rule is violated, AI MUST NOT report the modification as compliant.

RULE FINAL-003:
    If compliance cannot be determined because required information is unavailable, AI MUST report compliance as undetermined.

RULE FINAL-004:
    "Compiles" MUST NOT be used as a synonym for "compliant".

RULE FINAL-005:
    "Tests pass" MUST NOT be used as a synonym for "architecturally compliant".

RULE FINAL-006:
    AI MUST distinguish:
    - verified compliance
    - unresolved compliance
    - known violation

RULE FINAL-007:
    AI MUST preserve human/developer authority over project decisions.

RULE FINAL-008:
    AI suggestions MUST NOT become project requirements merely because AI generated them.

RULE FINAL-009:
    AI MUST treat this document as a constraint system, not as permission to redesign TWR.

RULE FINAL-010:
    The simplest implementation satisfying all applicable contracts and mandatory rules SHOULD be preferred.

___

# End of TWR AI Coding Rules
