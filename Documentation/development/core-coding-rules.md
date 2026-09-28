# Core Coding Rules

This document defines the core coding rules of the Tayku Web Runtime Environment (TWR).

These rules describe how TWR software is expected to be written, structured, exposed, maintained, and evolved. They define coding-level invariants and conventions without unnecessarily restricting implementation details.

The fundamental principle of this document is that TWR MUST provide strong and stable contracts while allowing implementations to remain replaceable.

___

# 0. Table of Contents

* Scope
* Core Philosophy
* TWR Apps
* Functions and Responsibilities
* Encapsulation
* Stable Interfaces
* TWR libc
* Public API Design
* Deprecated APIs
* API Evolution
* Function Naming
* Return Values
* TWR_STATUS_CODE
* Error Propagation
* Error Logging
* Function Contracts
* Inputs and Outputs
* Input Immutability
* Const Input Parameters
* Input Parameter Representation
* Output Parameters
* Output Validity
* Output Parameters on Error
* Output Initialization
* NULL Parameters
* Boolean Values
* Void Functions
* Ownership
* Ownership Transfer
* Resource Destruction
* Resource Lifetime
* Borrowed Resources
* Aliasing
* Input/Output Aliasing
* Resource Acquisition and Cleanup
* Cleanup and goto
* Allocation Failure
* Assertions
* Assertion Failure
* Let-it-Crash
* Crash Isolation
* Error vs Programmer Failure
* Error Context
* Dependencies
* Dependency Direction
* Forward Declarations
* Includes
* Header and Source Separation
* Public and Private Headers
* Structs
* Struct Behavior and Layout
* Struct Initialization
* Data and Behavior
* Enumerations
* Naming Conventions
* Public Namespace
* File Names
* C Style
* C++ Style
* Formatting
* Comments
* Macros
* Constants and Magic Values
* Casts
* Pointer Arithmetic
* Integer Types
* Strings
* Memory Allocation
* Static Storage and Global State
* Thread Safety
* Concurrency Contracts
* System Interfaces and errno
* System Calls
* Stable Syscall ABI
* Implementation Freedom
* Contract Over Mechanism
* Single Exit and Early Return
* Control Flow
* Internal Contracts
* Validation Boundaries
* Function Preconditions
* Resource Independence
* API and Ownership Compatibility
* API and Lifetime Compatibility
* API and Aliasing Compatibility
* Minimal Interfaces
* Composition
* Small Interfaces
* Implementation Abstraction
* Interface Stability and Internal Change
* Source Compatibility
* ABI Compatibility
* Function Signature Evolution
* Internal Helpers
* Static Internal Functions
* Source-Level Visibility
* Public Implementation Leakage
* Error Cleanup
* Successful Cleanup
* Failed Operations
* Resource Creation
* Resource Destruction Interfaces
* Memory Safety
* Stack Usage
* Heap Usage
* Memory Abstractions
* Logging Interface
* User Output
* Diagnostics
* General Error Taxonomy
* Boolean Status vs Error Status
* Internal Assertions and External Input
* Invariants
* Crash Philosophy
* Recovery
* Thread Safety and Ownership
* Mutable Global State and Concurrency
* Explicit Dependencies and Includes
* Circular Dependency Handling
* Developer Choice
* SHOULD vs MUST
* Avoiding Artificial Rules
* Unix and POSIX as Default
* Stable Interface, Replaceable Implementation
* Core Ownership Model
* Core Error Model
* Core Resource Model
* Core Data Flow Model
* Core Dependency Model
* Core API Evolution Model
* Final Principles
* Revision History
* ToDo List
* Notes

___

# 1. Scope

These rules apply to all source code that is part of the Tayku Web Runtime Environment (TWR), including:

* TWR Apps
* TWR libraries
* TWR runtime components
* TWR libc facilities
* TWR command implementations
* internal implementation modules
* public interfaces
* private interfaces
* C source code
* C++ source code

These rules apply whenever code is created, modified, extended, refactored, or otherwise maintained.

These rules are coding rules rather than a complete architectural specification. Architectural documents define the complete architecture and public contracts of individual components.

When a specific architectural or API contract defines behavior more precisely than this document, the specific contract takes precedence for that component.

___

# 2. Core Philosophy

TWR adopts the Unix and POSIX philosophy as its fundamental software engineering philosophy.

TWR software SHOULD therefore favor:

* small components
* simple interfaces
* explicit dependencies
* composability
* stable interfaces
* replaceable implementations
* clear ownership
* explicit data flow
* predictable failure behavior
* minimal unnecessary abstraction
* strong separation of interface and implementation

TWR MUST NOT introduce complexity merely for the sake of abstraction.

A mechanism SHOULD exist because it solves a real problem, preserves an invariant, or provides a useful interface.

Implementation details SHOULD remain implementation details whenever they do not affect the externally observable contract.

The primary distinction throughout TWR is:

```text
Stable Contract
      |
      v
Public Interface
      |
      v
Replaceable Implementation
```

The public behavior of a component is stable.

The implementation behind that behavior MAY change.

___

# 3. TWR Apps

Every TWR module that provides an independently meaningful responsibility is treated as a TWR App.

A TWR App is defined by its responsibility rather than by its size.

There is no fixed minimum or maximum size for a TWR App.

A TWR App MUST have one fundamental responsibility.

A TWR App MUST NOT become a collection of unrelated responsibilities merely because they are convenient to implement together.

TWR Apps SHOULD be composable.

Small TWR Apps SHOULD be capable of being composed into larger applications, which MAY themselves be composed into services or higher-level systems.

The size of a TWR App MUST NOT be used as the primary criterion for determining whether its design is valid.

Responsibility is the primary criterion.

___

# 4. Functions and Responsibilities

Every function SHOULD perform one clearly identifiable task.

A function MUST NOT become a God Function.

A function SHOULD be divided when its implementation becomes difficult to understand as a single unit.

There is no artificial line-count limit for functions.

Function size is determined by:

* readability
* responsibility
* control-flow complexity
* conceptual cohesion
* maintainability

A function that has become large enough to overflow the developer's working view and can no longer be understood as a single coherent operation SHOULD be divided.

A function SHOULD NOT be divided merely to satisfy an arbitrary line count.

Splitting a function MUST NOT be used to hide complexity.

The resulting functions SHOULD each have meaningful responsibilities.

___

# 5. Encapsulation

Every TWR App MUST encapsulate its internal implementation.

Other TWR Apps MUST NOT directly access another TWR App's internal implementation.

This rule applies even when the Apps are located in the same repository.

A TWR App MUST expose a public interface through which external users interact with it.

The public interface of a TWR App consists of the interfaces explicitly defined by its architecture and contracts.

A TWR App SHOULD provide both:

* a command interface
* a library interface

Process-to-process interaction MAY use the command interface and Unix-style mechanisms such as pipes.

Library consumers MAY directly use another TWR App's public library interface when a dependency exists.

A consumer MUST NOT access the dependency's internal implementation merely because the implementation is physically available in the same source tree.

How encapsulation is implemented is an implementation detail.

The following MAY be used to enforce encapsulation:

* `static`
* private headers
* internal translation units
* symbol visibility mechanisms
* build-system restrictions
* opaque types
* other suitable language or build mechanisms

The mechanism is not normative.

The preservation of encapsulation is normative.

___

# 6. Stable Interfaces

TWR treats interfaces as stable contracts.

The following are stable TWR boundaries when exposed as public interfaces:

* command interfaces
* library function interfaces
* syscall interfaces
* other explicitly defined public contracts

A stable interface MUST NOT depend on implementation details that are not part of its contract.

Implementation details MAY change without requiring consumers to change, provided that the public contract remains satisfied.

TWR facilities SHOULD be preferred when an equivalent TWR facility exists because they provide a continuity path through the stable TWR interface.

Underlying system facilities MAY still be used when appropriate.

Using an underlying Linux or system facility directly is not inherently forbidden.

However, direct dependence on system-specific implementation facilities does not provide the same TWR continuity guarantee as using the corresponding TWR abstraction.

___

# 7. TWR libc

TWR MAY provide its own libc abstraction, including facilities such as `twr_malloc` and other TWR-specific interfaces.

The purpose of TWR libc is to provide a stable TWR-facing interface rather than to unnecessarily reproduce every system facility from the beginning.

An initial TWR libc implementation MAY forward directly to the underlying system libc.

For example:

```c
void *
twr_malloc(
    size_t size
)
{
    return malloc(size);
}
```

The implementation MAY later be replaced by a TWR-native implementation without changing the public TWR interface.

The interface is stable.

The implementation is replaceable.

TWR code SHOULD use TWR-provided facilities when doing so provides a meaningful stable abstraction.

Direct use of system facilities MAY remain valid where no TWR abstraction is appropriate or where the architecture explicitly permits it.

___

# 8. Public API Design

A public API SHOULD expose only functionality necessary for the responsibility of its component.

A public API SHOULD NOT be expanded merely because a functionality might become useful in the future.

Unnecessary public functionality increases the long-term compatibility burden.

Public APIs MUST be treated as long-lived contracts.

Once a public API is released, it MUST be append-only.

Existing public functions MUST NOT be removed merely because a newer implementation or API exists.

Existing public functions MUST NOT be changed in a way that breaks their established contract.

New functionality SHOULD be introduced by adding new interfaces.

Existing interfaces SHOULD remain available when they are deprecated.

___

# 9. Deprecated APIs

A deprecated public function MUST remain available to existing consumers.

Deprecation MUST NOT be used as a mechanism for silently breaking existing consumers.

A deprecated function MAY delegate to a newer function.

For example:

```c
TWR_STATUS_CODE
twr_foo_old(
    const foo_t *foo
)
{
    return twr_foo_new(foo);
}
```

This is valid when `twr_foo_old()` continues to satisfy its original contract.

A deprecated function MAY use a completely different internal implementation as long as its established public behavior remains valid.

The introduction of a new implementation MUST NOT be used as justification for breaking an existing deprecated interface.

A deprecated interface SHOULD direct new development toward the replacement interface where appropriate.

___

# 10. API Evolution

TWR API evolution MUST preserve existing contracts.

New functions MAY be added.

New command forms MAY be added.

New facilities MAY be added.

Existing public behavior MUST remain compatible unless a separate compatibility mechanism is explicitly defined by the architecture.

API evolution SHOULD prefer addition over modification.

When a new behavior cannot be represented without breaking an existing contract, the new behavior SHOULD be exposed through a new interface rather than silently changing the old behavior.

___

# 11. Function Naming

Function names MUST follow the TWR namespace convention:

```text
[module/app]_[submodule]_[function]()
```

The submodule portion MAY be omitted when no submodule exists.

Examples:

```c
twr_http_request();
twr_http_listen();
twr_http_parser_init();
twr_http_parser_next();
```

Public function names MUST use the appropriate TWR namespace.

TWR MUST NOT expose generic public function names that unnecessarily risk namespace collisions.

___

# 12. Return Values

Every function SHOULD return `TWR_STATUS_CODE`.

For TWR functions, the return value represents operation status rather than the function's primary data result.

The standard success value is:

```text
0
```

Error values are negative.

Positive values MUST NOT be used as success status values.

Functions SHOULD NOT return primary data directly through the return value when the function participates in the TWR status-code model.

Data SHOULD be provided through output parameters or explicitly defined output structures.

Example:

```c
TWR_STATUS_CODE
twr_foo_read(
    foo_t *foo,
    void *buffer,
    size_t size,
    size_t *read_size
);
```

A raw value such as:

```c
return -1;
```

SHOULD NOT be used as a TWR error result.

Named TWR status values SHOULD be used instead.

___

# 13. TWR_STATUS_CODE

TWR uses `TWR_STATUS_CODE` as its standard error and status mechanism.

TWR SHOULD maintain a relatively small set of general-purpose status codes.

TWR SHOULD NOT create a unique status code for every possible failure condition.

Detailed error meaning belongs to the function contract.

Detailed diagnostic information MAY be provided through the TWR logging system.

A function MAY translate a lower-level status into another TWR status when required by its own abstraction.

The standard behavior, however, is to propagate a received `TWR_STATUS_CODE` upward unchanged.

For example:

```c
TWR_STATUS_CODE
twr_a_do_something(...)
{
    TWR_STATUS_CODE status;

    status = twr_b_do_something(...);

    if (status != TWR_SUCCESS)
        return status;

    return TWR_SUCCESS;
}
```

Status translation SHOULD be performed only when the abstraction or contract gives the translation meaningful semantic value.

___

# 14. Error Propagation

Errors SHOULD propagate from lower layers toward higher layers.

The standard error flow is:

```text
low-level operation
        |
        v
TWR_STATUS_CODE
        |
        v
caller
        |
        v
higher-level caller
```

A function receiving an error SHOULD propagate it unchanged unless there is a valid abstraction-level reason to translate it.

Error propagation MUST NOT silently convert a failure into success.

A function MUST NOT ignore an error merely because it does not currently know how to handle it, unless the contract explicitly permits the error to be ignored.

___

# 15. Error Logging

Error logging SHOULD occur at the highest appropriate layer that has sufficient context to interpret and report the failure.

Lower-level functions SHOULD generally return their `TWR_STATUS_CODE` without independently logging the same error.

A chain of functions propagating the same error SHOULD NOT produce duplicate logs at every layer.

The highest appropriate layer MAY add contextual information before reporting the failure.

Logging MUST NOT replace error propagation.

A function SHOULD NOT print directly to the user merely because it encountered an error.

User-facing output is normally the responsibility of the caller or appropriate presentation layer.

___

# 16. Function Contracts

Every externally relevant function behavior MUST be defined by its contract.

A function contract SHOULD define all externally observable semantics relevant to correct use of the function.

This includes, where applicable:

* purpose
* inputs
* outputs
* valid input values
* invalid input values
* `NULL` validity
* output validity
* ownership
* lifetime
* side effects
* resource behavior
* status codes
* failure behavior
* preconditions
* postconditions
* thread-safety
* synchronization requirements
* aliasing requirements
* other externally observable behavior

A function contract MUST NOT depend on unnecessary implementation details.

The internal algorithm, local control flow, helper structure, and other implementation mechanisms MAY change as long as the contract remains satisfied.

Large function contracts SHOULD be documented in architecture or documentation files rather than being embedded as large source-code comment blocks.

___

# 17. Inputs and Outputs

Input and output roles MUST be distinguishable.

When a function requires both an input and an output representation, input and output parameters MUST be separate whenever both roles are required.

The same parameter SHOULD NOT simultaneously serve as both input and output when the operation can reasonably be expressed using separate parameters.

Example:

```c
TWR_STATUS_CODE
twr_foo_process(
    const foo_t *input,
    foo_t *output
);
```

is preferred over using one mutable pointer to represent both roles.

This distinction exists so that the data flow of a function can be understood directly from its signature.

___

# 18. Input Immutability

Input values MUST NOT be modified by a function.

If an operation requires modifying an input value, the input MUST first be copied.

The operation MUST then be performed on the copy.

For example:

```c
TWR_STATUS_CODE
twr_foo_process(
    const foo_t *input,
    foo_t *output
);
```

is preferred when processing requires a modified representation.

The function MUST NOT modify the object represented by an input parameter.

Input immutability is a semantic requirement, not merely an optimization preference.

___

# 19. Const Input Parameters

Input parameters MUST be declared `const` where the language permits the semantic distinction.

The purpose of this rule is to make input and output roles visible in the function signature.

For pointer inputs:

```c
const foo_t *input
```

indicates that the pointed value is input data and MUST NOT be modified.

Output pointers use the corresponding mutable form:

```c
foo_t *output
```

The semantic requirement is that input values remain unchanged.

The use of `const` MUST NOT be interpreted as permission to modify an input through another alias.

___

# 20. Input Parameter Representation

The representation of an input parameter MAY be chosen by the developer.

Inputs MAY be passed:

* by value
* by pointer
* through another explicitly defined representation

The choice SHOULD be based on appropriate considerations such as:

* semantics
* object size
* ABI
* performance
* lifetime
* ownership
* readability

Regardless of representation, the input value MUST remain immutable.

___

# 21. Output Parameters

Output parameters are used when a function needs to produce data while returning a `TWR_STATUS_CODE`.

Output parameters MUST be distinguishable from input parameters.

A function MAY produce multiple outputs using:

* multiple output parameters
* an output structure
* another explicitly defined output representation

The choice is left to the developer.

The function contract MUST define the resulting output representation and semantics.

___

# 22. Output Validity

If a function returns success, every output defined by its contract MUST contain valid data unless the contract explicitly permits `NULL` or another non-value state.

A successful function MUST NOT leave an output in an unspecified state when the contract defines that output as valid.

A `NULL` output MAY be valid when explicitly permitted by the contract.

The standard behavior is that successful outputs are valid and non-`NULL` unless otherwise specified.

___

# 23. Output Parameters on Error

If a function returns an error, output parameters MUST be considered incomplete.

Output data produced before the failure MUST NOT be treated as a complete or valid result unless the function contract explicitly defines otherwise.

For example, if a read operation partially fills a buffer and then fails, the caller MUST treat the resulting output as incomplete.

The status code determines whether the output represents a valid completed result.

The standard model is:

```text
TWR_SUCCESS
    |
    +--> output is valid

TWR_ERROR
    |
    +--> output is incomplete / invalid
```

___

# 24. Output Initialization

A caller MAY initialize an output pointer to `NULL` before passing it to a function.

This is standard practice for pointer outputs but is not mandatory.

For example:

```c
foo_t *foo = NULL;

status = twr_foo_create(&foo);
```

is valid and recommended as a common initialization pattern.

A caller MUST NOT be required to initialize an output unless the function contract explicitly requires such initialization.

A successful function MUST still produce a valid output according to its contract regardless of whether the caller initialized the output beforehand.

___

# 25. NULL Parameters

`NULL` is not universally invalid.

Whether a parameter may be `NULL` is determined by the function contract.

If the contract permits `NULL`, the function MUST treat it as a valid input condition according to that contract.

If the contract does not permit `NULL`, receiving `NULL` MAY result in an appropriate `TWR_STATUS_CODE`.

Functions MUST NOT mechanically reject every pointer merely because it is `NULL`.

Validity is contract-dependent.

___

# 26. Boolean Values

Boolean values MAY be used as ordinary output data.

For example:

```c
TWR_STATUS_CODE
twr_foo_is_valid(
    const foo_t *foo,
    bool *valid
);
```

is a valid TWR interface.

The `bool` represents output data.

The `TWR_STATUS_CODE` represents operation status.

These concepts MUST NOT be confused.

___

# 27. Void Functions

Functions SHOULD return `TWR_STATUS_CODE`.

A `void` return type MAY be used when returning a status is not meaningful or useful.

Examples include suitable callback interfaces and other explicitly defined cases where no status result is required.

`void` SHOULD NOT be used merely out of convenience when an operation can meaningfully fail or report status.

The use of `void` MUST NOT become a mechanism for hiding error conditions.

___

# 28. Ownership

Ownership is established by the party that initiates the creation of a resource.

The fact that a lower-level function physically allocates or initializes the resource does not automatically make that function the owner.

For example:

```c
twr_foo_create(&foo);
```

means that the caller that initiated the creation owns the resulting resource, even if `twr_foo_create()` performs the actual allocation internally.

The fundamental ownership model is:

```text
Creation initiated by caller
        |
        v
Caller owns resource
```

Passing a resource to another function does not transfer ownership.

Using a resource does not transfer ownership.

Modifying a resource does not transfer ownership.

Calling a function with a resource does not by itself transfer ownership.

___

# 29. Ownership Transfer

Ownership transfer is permitted when necessary.

Ownership transfer SHOULD be avoided whenever it is not required.

Ownership transfer MUST be explicitly defined by the relevant function or interface contract.

A function MUST NOT silently assume ownership merely because it received a resource.

When ownership is transferred, the contract MUST make the new ownership responsibility clear.

The new owner becomes responsible for the resource according to the resource's destruction and lifetime rules.

___

# 30. Resource Destruction

The owner of a resource is responsible for releasing or destroying it.

A function MUST NOT destroy or release a resource it does not own.

A function using a borrowed resource MUST leave its ownership unchanged.

For example:

```c
twr_foo_use(foo);
```

does not grant `twr_foo_use()` permission to destroy `foo` when `foo` is owned by its caller.

Ownership transfer is the explicit exception and MUST be defined by contract.

___

# 31. Resource Lifetime

A function MUST NOT extend the lifetime of an input resource.

If a function needs to retain an input after the function call returns, it MUST create and own an independent copy.

For example, a function receiving:

```c
const char *name
```

MUST NOT retain the pointer after returning unless the contract explicitly defines an independent lifetime mechanism that satisfies the input lifetime rule.

The normal mechanism for retaining input data is copying it into independently owned storage.

This rule exists to prevent hidden lifetime dependencies and dangling references.

___

# 32. Borrowed Resources

A borrowed resource MAY be used by a function during the lifetime guaranteed by the caller and contract.

A borrowed resource MUST NOT be destroyed by the borrowing function.

A borrowed resource MUST NOT have its lifetime extended implicitly.

If a function needs a resource beyond the lifetime guaranteed for the input, it MUST create an independent owned representation.

___

# 33. Aliasing

A resource MAY have multiple aliases.

Aliasing is not forbidden.

A resource MUST have a single ownership holder at any given time.

Aliases do not become owners merely by existing.

An alias MAY be used according to the permissions defined by the resource's contract.

An alias MUST NOT release or destroy the resource unless ownership has explicitly been transferred to that alias.

___

# 34. Input/Output Aliasing

Input and output aliasing SHOULD be avoided.

A function MAY support input/output aliasing when necessary or appropriate.

If input/output aliasing is supported, its behavior MUST be explicitly defined by the function contract.

A function MUST NOT leave aliasing behavior ambiguous when aliasing can affect the result.

The standard design SHOULD use separate input and output objects where practical.

___

# 35. Resource Acquisition and Cleanup

A function MUST release every resource it owns before returning from a normal recoverable error path.

Resource leaks MUST NOT be accepted merely because the function is returning an error.

Cleanup MUST follow reverse acquisition order.

If resources are acquired in:

```text
A
B
C
```

they MUST be released in:

```text
C
B
A
```

This is the standard TWR resource lifetime model.

___

# 36. Cleanup and goto

`goto` is permitted.

TWR does not impose a restriction that `goto` may only be used for cleanup.

However, `goto` SHOULD be used in a manner that preserves readable and understandable control flow.

A cleanup label is a natural use of `goto` when multiple error paths must release resources in reverse acquisition order.

Example:

```c
status = twr_a_create(&a);
if (status != TWR_SUCCESS)
    goto cleanup_a;

status = twr_b_create(&b);
if (status != TWR_SUCCESS)
    goto cleanup_b;

status = twr_c_create(&c);
if (status != TWR_SUCCESS)
    goto cleanup_c;

return TWR_SUCCESS;

cleanup_c:
twr_b_destroy(&b);

cleanup_b:
twr_a_destroy(&a);

cleanup_a:
return status;
```

The exact implementation MAY differ, provided that ownership and cleanup rules are satisfied.

___

# 37. Allocation Failure

Allocation failure is a runtime failure.

Allocation failure MUST NOT be treated as a programmer-error assertion merely because the allocation was expected to succeed.

If an allocation fails, the function MUST clean up resources it already owns on the current error path before returning.

Previously acquired resources MUST be released in reverse acquisition order.

For example:

```text
allocate A
allocate B
allocate C -> failure
```

requires:

```text
cleanup B
cleanup A
return TWR_STATUS_CODE
```

The failed allocation itself does not create an owned resource.

___

# 38. Assertions

`assert` is mandatory for programmer errors and violated internal invariants where an assertion is appropriate.

An impossible internal state MUST be detected using `assert` rather than being silently converted into an ordinary runtime error.

Assertions MUST NOT be used as normal external-input validation.

External or runtime-invalid input SHOULD result in `TWR_STATUS_CODE`.

The distinction is:

```text
External invalid input
        |
        v
TWR_STATUS_CODE

Internal invariant violation
        |
        v
assert
        |
        v
crash
```

___

# 39. Assertion Failure

An assertion failure MUST result in immediate failure of the affected execution context.

TWR does not require special recovery behavior after an assertion failure.

An assertion failure is a programmer or invariant failure rather than a recoverable runtime error.

TWR therefore adopts direct crash behavior for assertion failures.

Normal error-path cleanup rules apply to recoverable errors.

They do not require a separate recovery system for an assertion-triggered crash.

___

# 40. Let-it-Crash

TWR strongly adopts the let-it-crash philosophy.

Not every failure MUST be recovered from.

A failure that cannot be meaningfully recovered from MAY terminate the affected execution context.

The system SHOULD be designed so that the failure of one component does not unnecessarily destroy unrelated components.

Isolation and stable boundaries SHOULD allow the larger system to continue when possible.

Possible recovery mechanisms MAY include:

* retry
* restart
* caller-level detection
* error propagation
* replacement of the failed component

No particular recovery mechanism is mandatory unless required by the relevant architecture or contract.

Let-it-crash MUST NOT be interpreted as permission to leak resources on ordinary error paths.

Normal recoverable error paths MUST still perform required cleanup.

___

# 41. Crash Isolation

TWR components SHOULD be isolated sufficiently that the failure of one component does not unnecessarily cause a chain reaction.

A crash MAY propagate when the architecture requires it.

However, unnecessary crash chains SHOULD be avoided.

The system SHOULD allow higher-level components to detect component failure and decide whether to:

* retry
* restart
* report an error
* terminate
* continue without the failed component

The correct response is architecture-dependent.

___

# 42. Error vs Programmer Failure

TWR distinguishes between environmental/runtime failures and violations of programmer assumptions.

Runtime failures include conditions such as:

* allocation failure
* unavailable resources
* invalid external input
* unavailable system facilities
* other conditions defined by the contract as runtime failures

These SHOULD be represented using `TWR_STATUS_CODE`.

Programmer errors and violated internal invariants SHOULD use `assert`.

The two mechanisms MUST NOT be confused.

___

# 43. Error Context

Low-level functions SHOULD provide status rather than presentation.

Higher-level functions SHOULD provide contextual interpretation when necessary.

An error code SHOULD represent a general failure category.

Detailed context MAY be provided by the logger.

A lower-level implementation SHOULD NOT need to know how an error will ultimately be presented to a user.

___

# 44. Dependencies

Dependencies MUST be explicit.

If a component directly uses another component, it MUST directly depend on that component.

A component MUST NOT rely on a transitive dependency merely because another dependency currently provides it.

For example:

```text
twr-a -> twr-b
twr-b -> twr-c
```

does not mean that `twr-a` may use `twr-c` without declaring a direct dependency.

If `twr-a` directly uses `twr-c`, the dependency graph MUST explicitly represent:

```text
twr-a -> twr-c
```

This rule applies to both source-level and component-level dependencies where applicable.

___

# 45. Dependency Direction

Dependencies SHOULD form a directed acyclic structure.

Circular dependencies between TWR components are forbidden.

A component MUST NOT depend directly or indirectly on itself.

Dependency direction SHOULD remain understandable from the component structure.

When two components appear to require one another, the design SHOULD be reconsidered before introducing mutual coupling.

Shared abstractions MAY be extracted when appropriate.

Forward declarations MAY be used to solve language-level declaration cycles when they do not indicate an architectural dependency cycle.

___

# 46. Forward Declarations

Forward declarations SHOULD be preferred when only a declaration is required and the complete definition is unnecessary.

For example:

```c
struct TwrFoo;
struct TwrBar;

TWR_STATUS_CODE
twr_foo_use_bar(
    struct TwrBar *bar
);
```

is preferable to including an entire definition when the implementation does not require it.

A complete header MUST be included when the complete definition is required.

Forward declarations MUST NOT be used to conceal a real architectural dependency.

They are a tool for reducing unnecessary compile-time coupling and resolving language-level declaration dependencies.

___

# 47. Includes

Source files and headers SHOULD include the declarations they directly use.

A file SHOULD NOT depend on another header merely because that header happens to include the required declaration transitively.

Direct use SHOULD correspond to a direct include where the complete declaration or definition is required.

Unused includes SHOULD be removed.

Include dependencies SHOULD remain as small and explicit as reasonably possible.

TWR SHOULD follow established Unix/Linux header organization conventions rather than inventing an unnecessary independent include philosophy.

___

# 48. Header and Source Separation

Headers SHOULD primarily contain:

* public declarations
* type declarations
* public structures
* enumerations
* constants
* macros
* required compile-time definitions
* interface definitions

Implementation details SHOULD remain in source files whenever practical.

A header MAY contain implementation mechanisms such as:

* `static inline` functions
* macros
* compile-time helpers
* other mechanisms required by the language or design

Such mechanisms SHOULD be used only when they provide a meaningful reason to exist in the header.

Public headers SHOULD NOT unnecessarily expose implementation details.

Private headers MAY contain internal implementation declarations.

___

# 49. Public and Private Headers

Public headers define public interfaces.

Private headers define implementation-level interfaces.

Internal declarations SHOULD remain private whenever external consumers do not require them.

A public header MUST NOT expose internal implementation merely for convenience.

An implementation MAY reorganize private headers without affecting consumers as long as the public contract remains stable.

___

# 50. Structs

TWR C code SHOULD follow established Unix/Linux conventions for structures.

Structures SHOULD normally be declared with an explicit struct tag:

```c
struct TwrHttpRequest {
    int value;
};
```

rather than relying on an automatic `typedef struct` convention.

The structure MAY then be used explicitly:

```c
struct TwrHttpRequest request;
```

Public structure naming MUST follow TWR namespace conventions.

___

# 51. Struct Behavior and Layout

The externally observable behavior and semantics of a public structure MAY be part of its public contract.

The physical memory layout of a public structure is not automatically a stable TWR interface.

TWR MUST NOT assume that public structure layout is stable merely because the structure is publicly visible.

Implementation details such as:

* field ordering
* padding
* alignment
* internal representation
* storage strategy

MAY change when doing so does not violate the relevant public contract.

If a specific ABI requires layout stability, that stability MUST be explicitly defined by that ABI contract.

___

# 52. Struct Initialization

Data structures and behavior SHOULD remain conceptually separate.

TWR uses free functions rather than methods attached to structures.

Stateful structures SHOULD be initialized through an explicit initialization function when initialization represents behavior or lifecycle management.

For example:

```c
TWR_STATUS_CODE
twr_foo_init(
    struct TwrFoo *foo
);
```

Simple data-only structures MAY use ordinary C initialization where appropriate.

The rule is not that structure literals are forbidden.

The rule is that lifecycle behavior SHOULD be represented explicitly as behavior rather than being hidden inside arbitrary data initialization.

___

# 53. Data and Behavior

Data and behavior SHOULD be kept separate as much as practical.

TWR C interfaces use:

* data structures for data
* free functions for behavior

Structures SHOULD NOT contain methods.

Function pointers MAY exist inside structures when they represent data or callback interfaces.

The presence of a function pointer does not turn a structure into a method-bearing object.

Opaque structures MAY be used when stronger implementation encapsulation is required.

___

# 54. Enumerations

Enumerations SHOULD follow Unix/Linux C conventions.

TWR SHOULD use explicit enum tags rather than relying on a general `typedef enum` pattern.

Example:

```c
enum TwrState {
    TWR_STATE_INIT,
    TWR_STATE_RUNNING,
    TWR_STATE_STOPPED
};
```

Public enum values MUST follow the TWR public namespace convention.

___

# 55. Naming Conventions

TWR uses established Unix/Linux naming conventions as the general C baseline, with explicit TWR-specific naming rules.

The following conventions apply:

```text
Structs      -> PascalCase
Functions    -> camel_case
Variables    -> camel_case
Files        -> kebab-case
Macros       -> UPPER_CASE
Constants    -> UPPER_CASE
Enum values  -> TWR_* style
```

Examples:

```c
struct TwrHttpRequest;

TWR_STATUS_CODE
twr_http_request(
    ...
);

size_t buffer_size;
```

TWR public identifiers MUST use the appropriate TWR namespace.

___

# 56. Public Namespace

The `twr_*` namespace SHOULD be used for public functions and lower-case identifiers.

The `TWR_*` namespace SHOULD be used for:

* macros
* constants
* status identifiers
* public enum values
* other upper-case public identifiers

Struct tags MUST follow the TWR namespace convention.

Internal identifiers do not require an additional naming scheme beyond appropriate encapsulation.

___

# 57. File Names

TWR source and documentation files MAY use `kebab-case`.

File naming does not need to reproduce the exact naming conventions used by the Linux kernel.

File names SHOULD remain descriptive and predictable.

A file SHOULD represent a meaningful implementation or interface unit rather than becoming an arbitrary collection of unrelated code.

___

# 58. C Style

TWR C code follows established Unix/Linux/BSD conventions unless TWR explicitly defines another behavior.

TWR SHOULD NOT invent a separate formatting philosophy when an established Unix/Linux convention already provides an appropriate solution.

C code SHOULD prioritize:

* readability
* explicitness
* predictable control flow
* simple interfaces
* minimal abstraction
* direct data flow

___

# 59. C++ Style

TWR C++ code follows orthodox C++ conventions and the Google C++ style approach.

C++ source files use the `.cc` extension.

The `.cpp` extension is not the standard TWR C++ source extension.

C++ code MUST NOT be written as if it were C merely because both languages are used by TWR.

C and C++ conventions SHOULD remain appropriately separated.

___

# 60. Formatting

C formatting SHOULD follow Unix/Linux conventions.

C++ formatting SHOULD follow Google conventions.

TWR does not require a separate formatting philosophy where an established standard already satisfies the project's needs.

Formatting tools MAY be used to enforce the relevant language standard.

Formatting SHOULD remain deterministic and consistent within a language.

___

# 61. Comments

Comments SHOULD explain information that cannot be understood directly from the code.

Good comments MAY explain:

* why an unusual decision exists
* why a non-obvious algorithm is required
* why a workaround exists
* important local invariants
* constraints that are not obvious from the code

Comments SHOULD NOT merely restate obvious code.

For example, this is generally unnecessary:

```c
/* Increment i. */
i++;
```

Source comments SHOULD remain concise.

Large architectural or API contracts SHOULD be documented in separate architecture or documentation files.

TWR does not require JSDoc-style or Doxygen-style large contract blocks in ordinary source code.

English SHOULD be preferred for source-code comments.

___

# 62. Macros

Macros MAY be used.

TWR does not impose a blanket prohibition against macros.

Macros SHOULD be used when they provide meaningful compile-time semantics or are appropriate for the C implementation model.

Examples include:

```c
#define TWR_MAX_BUFFER_SIZE 4096
#define TWR_PROTOCOL_VERSION 1
```

Macros SHOULD have meaningful names.

Macros MUST NOT be used merely to obscure simple code.

___

# 63. Constants and Magic Values

Values with meaningful semantic identity SHOULD be named.

Meaningful numeric and string constants SHOULD NOT be scattered throughout an implementation as unexplained literals.

For example:

```c
#define TWR_MAX_BUFFER_SIZE 4096
```

is preferable when `4096` represents a defined protocol or system limit.

Not every literal MUST be converted into a macro.

Simple values with no independent semantic identity MAY remain literals.

The purpose of this rule is to eliminate unexplained magic values, not to maximize the number of macros.

___

# 64. Casts

Casts MAY be used when necessary.

Unnecessary casts SHOULD be avoided.

A cast MAY be appropriate when it:

* satisfies a real type conversion
* expresses required intent
* interfaces with a system API
* handles a known representation boundary

A cast MUST NOT be added merely to silence a warning without understanding the underlying type issue.

___

# 65. Pointer Arithmetic

Normal C pointer arithmetic is permitted.

TWR does not impose an independent blanket restriction on pointer arithmetic.

Pointer arithmetic SHOULD remain readable and correct according to ordinary C semantics.

Additional restrictions MAY be introduced by a specific component contract when required by that component.

___

# 66. Integer Types

`size_t` MUST be used for quantities representing:

* size
* byte count
* memory quantity
* buffer length
* object count
* other non-negative size-like quantities

The use of `size_t` SHOULD communicate semantically that the value represents a size or quantity.

Platform-dependent integer types such as `long` SHOULD NOT be used when a more semantically appropriate type exists.

Unsigned integer types SHOULD NOT be selected merely because a value is not expected to become negative.

Fixed-width integer types MAY be used when a fixed width is semantically required.

Examples include:

```c
uint32_t
int32_t
uint64_t
```

The type should represent the semantic requirements of the value rather than merely its current range.

___

# 67. Strings

TWR MAY provide a `TWR_STRING` or `twr_string` abstraction.

The TWR string abstraction SHOULD retain a practical C-style usage model.

The existence of a TWR string abstraction does not imply that all dynamic allocation is forbidden.

TWR strings MAY be dynamically allocated.

The contract of a string type MUST define relevant:

* ownership
* lifetime
* mutability
* allocation behavior
* destruction behavior
* representation requirements

Classic C strings MAY still be used where appropriate.

___

# 68. Memory Allocation

Stack allocation SHOULD be preferred where practical.

Heap allocation MAY be used.

Heap allocation is not forbidden.

Heap allocation SHOULD be used when the lifetime, size, ownership model, or other requirements make it appropriate.

TWR-provided allocation and memory facilities SHOULD be preferred when an appropriate TWR facility exists.

Direct system allocation MAY be used where permitted.

Direct use of system allocation does not automatically provide the continuity guarantees of the corresponding TWR abstraction.

___

# 69. Static Storage and Global State

Global mutable state SHOULD generally be avoided.

Global mutable state is not universally forbidden.

It MAY be used when it is justified by the component's design.

When global mutable state is used, its:

* ownership
* lifetime
* access rules
* synchronization behavior
* initialization behavior

SHOULD be clear.

The `static` keyword itself is not forbidden.

`static` MAY be used for internal linkage and encapsulation.

The primary concern is uncontrolled mutable global state, not the `static` keyword.

___

# 70. Thread Safety

Thread safety MUST NOT be assumed unless specified by the function or component contract.

A function is thread-safe only to the extent guaranteed by its contract.

When thread safety is required, the relevant contract SHOULD define:

* whether concurrent calls are supported
* which resources may be shared
* synchronization requirements
* atomicity requirements
* ownership requirements
* caller responsibilities

Synchronization mechanisms are implementation details unless their observable behavior affects the contract.

Developers MAY use:

* mutexes
* atomics
* locks
* thread-local storage
* other synchronization mechanisms

as appropriate.

___

# 71. Concurrency Contracts

Concurrency behavior MUST be defined when it affects correct use of an interface.

A function SHOULD NOT silently imply thread safety merely because its implementation currently happens to be safe under a particular runtime configuration.

If an interface is intentionally not thread-safe, the contract SHOULD state that limitation.

If concurrent access is supported, the contract SHOULD define the supported concurrency model.

___

# 72. System Interfaces and errno

TWR implementations MAY use system-level error mechanisms such as `errno`.

`errno` MAY be read and interpreted internally.

TWR public interfaces SHOULD expose errors through `TWR_STATUS_CODE` rather than requiring callers to depend on implementation-specific system error mechanisms.

For example:

```text
Linux/system call
       |
       v
     errno
       |
       v
TWR implementation
       |
       v
TWR_STATUS_CODE
       |
       v
TWR caller
```

The use of `errno` internally does not make `errno` part of the public TWR contract unless explicitly specified.

This allows the implementation to replace Linux-specific mechanisms without unnecessarily changing the TWR interface.

___

# 73. System Calls

TWR MAY directly use system calls internally.

TWR MAY wrap system calls through TWR facilities.

The choice is an implementation detail unless exposed by the public contract.

TWR-provided abstractions SHOULD be preferred when they provide a stable interface or meaningful portability/continuity benefit.

Direct system-call usage MUST NOT unnecessarily leak system-specific behavior into a stable TWR public interface.

___

# 74. Stable Syscall ABI

When TWR exposes a stable syscall ABI, that ABI MUST be append-only.

Existing syscall interfaces MUST remain valid according to their established contracts.

New syscall functionality MAY be added.

Existing syscall behavior MUST NOT be silently broken by new additions.

Internal implementation behind a stable syscall ABI MAY change freely as long as the ABI contract remains satisfied.

___

# 75. Implementation Freedom

TWR coding rules intentionally leave implementation choices open where those choices do not affect public behavior.

Developers MAY choose different:

* algorithms
* helper functions
* internal data structures
* allocation strategies
* synchronization mechanisms
* internal header organization
* cleanup implementation
* control-flow structures
* compiler-supported mechanisms

provided that all relevant contracts and coding invariants remain satisfied.

A rule SHOULD constrain behavior only when the behavior is important to the TWR model.

Implementation details SHOULD NOT be turned into artificial coding laws without a concrete reason.

___

# 76. Contract Over Mechanism

The contract is normative.

The mechanism is not normative unless explicitly stated.

For example, TWR requires encapsulation, but does not require a particular mechanism such as `static`.

TWR requires input immutability, but the exact internal copying mechanism is an implementation detail.

TWR requires resource cleanup, but the exact cleanup control flow may use structured branching or `goto`.

TWR requires stable APIs, but the implementation behind the APIs may change.

The general rule is:

```text
Required behavior
       |
       v
Contract / invariant
       |
       v
Developer chooses implementation
```

___

# 77. Single Exit and Early Return

TWR does not require a single exit point.

Early return SHOULD be used when continuing execution would be invalid, unsafe, or unnecessary.

Invalid input MAY be rejected immediately.

A failed operation MAY return immediately after performing the required cleanup.

The goal is clear control flow rather than adherence to a single-exit ideology.

Example:

```c
if (input == NULL)
    return TWR_ERROR_INVALID_ARGUMENT;
```

is valid when `NULL` is not permitted by the contract.

___

# 78. Control Flow

Control flow SHOULD remain understandable.

Early returns MAY be used.

`goto` MAY be used.

Loops MAY use ordinary C control-flow mechanisms.

No particular control-flow structure is universally mandatory.

The chosen control flow MUST preserve:

* correctness
* cleanup requirements
* ownership rules
* contract behavior
* readability

___

# 79. Internal Contracts

Internal functions SHOULD have clear contracts even when they are not public.

An internal function MAY assume preconditions guaranteed by its caller.

Internal functions MUST rely on those guaranteed preconditions rather than redundantly validating the same conditions at every layer.

Redundant validation SHOULD be avoided when the caller's contract already guarantees the condition.

Internal invariants SHOULD be protected with `assert` where appropriate.

The standard model is:

```text
External boundary
        |
        v
validate external conditions
        |
        v
internal contract
        |
        v
trust guaranteed preconditions
        |
        v
assert internal invariants
```

___

# 80. Validation Boundaries

Validation SHOULD occur at the boundary where untrusted or externally supplied data enters a component.

Once a contract has established a precondition, downstream internal functions SHOULD rely on that contract.

Internal functions SHOULD NOT repeatedly perform the same external validation without a concrete reason.

This reduces duplicated logic and preserves the meaning of function contracts.

A lower-level function MAY still validate conditions that belong to its own contract.

___

# 81. Function Preconditions

Preconditions MUST be respected by callers.

A function MAY assume a precondition when that precondition is explicitly established by its contract.

Callers MUST NOT violate documented preconditions.

Internal functions SHOULD NOT compensate indefinitely for invalid caller behavior.

When a caller violates an internal invariant or programmer contract, `assert` MAY be appropriate.

When an external input violates a runtime input contract, `TWR_STATUS_CODE` SHOULD be used.

___

# 82. Resource Independence

A function SHOULD avoid creating hidden dependencies on resources that it does not own.

A function SHOULD operate on resources according to the ownership and lifetime defined by its contract.

A function MUST NOT silently assume that an input will remain alive beyond the lifetime guaranteed by the caller.

If persistent use is required, the function SHOULD establish independent ownership through copying or another explicitly defined ownership mechanism.

___

# 83. API and Ownership Compatibility

Public APIs MUST make ownership behavior understandable.

An API that creates a resource MUST define who owns the resulting resource.

An API that transfers ownership MUST define the transfer.

An API that borrows a resource SHOULD preserve the caller's ownership.

An API MUST NOT require users to infer ownership from implementation behavior.

Ownership MUST be a contract-level property.

___

# 84. API and Lifetime Compatibility

Public APIs MUST define relevant lifetime assumptions.

A function SHOULD NOT require callers to keep an input alive longer than necessary unless this requirement is explicitly part of the contract.

If an API stores a reference to caller-owned data, the lifetime requirement MUST be explicitly documented.

The default TWR behavior is that input lifetime is not extended.

When persistence is necessary, independent storage SHOULD be created.

___

# 85. API and Aliasing Compatibility

If an API supports aliases, its contract SHOULD define the relevant mutation and ownership semantics.

If input/output aliasing affects the result, the contract MUST explicitly define whether aliasing is supported.

Undefined aliasing behavior SHOULD be avoided.

Separate input and output parameters SHOULD be preferred where aliasing would otherwise create ambiguity.

___

# 86. Minimal Interfaces

A component SHOULD expose the smallest public interface that satisfies its responsibility.

Internal helpers SHOULD remain internal.

Public APIs SHOULD NOT expose implementation details merely because they already exist.

A public interface SHOULD provide capabilities rather than exposing internal structures unnecessarily.

A new public function SHOULD have a clear reason to exist.

___

# 87. Composition

TWR Apps are the fundamental units of composition.

Each TWR App SHOULD perform one small, fundamental responsibility.

Multiple TWR Apps MAY be composed into a System.

Multiple Systems MAY be composed into larger Systems or Services.

The purpose of this model is to allow complexity to emerge through composition rather than through increasingly large individual Apps.

A TWR App SHOULD NOT absorb another App's responsibility merely to reduce the number of dependencies.

Composition SHOULD preserve explicit dependency relationships.

Therefore:

```text
TWR App
    ↓
System
    ↓
Larger System
    ↓
Service / Runtime
```

___

# 88. Small Interfaces

TWR library interfaces SHOULD favor small Unix-like primitives.

A function SHOULD perform a clear operation rather than becoming a universal entry point for unrelated functionality.

Large behavior MAY be composed from multiple functions.

The existence of multiple small functions is not itself a design problem.

The functions SHOULD remain coherent and composable.

___

# 89. Implementation Abstraction

Abstraction SHOULD occur at stable boundaries.

TWR SHOULD NOT expose an abstraction merely to hide a trivial implementation when doing so provides no meaningful benefit.

Conversely, implementation details SHOULD be hidden when exposing them would create unnecessary coupling.

The objective is not maximum abstraction.

The objective is stable, understandable interfaces.

___

# 90. Interface Stability and Internal Change

Internal implementation MAY change without requiring API changes.

A refactor MAY replace:

* algorithms
* memory managers
* internal structures
* system calls
* helper functions
* synchronization mechanisms
* source organization

provided that the public contract remains valid.

This is a fundamental TWR principle:

```text
Implementation
      |
      | may change
      v
Stable Interface
      |
      | remains
      v
Consumer
```

___

# 91. Source Compatibility

Source-level compatibility SHOULD be preserved for stable public APIs.

Existing callers SHOULD continue to compile and behave according to the established contract.

If a breaking change is unavoidable, the old public interface SHOULD remain available through deprecation or another compatibility mechanism whenever possible.

___

# 92. ABI Compatibility

Every TWR App MUST expose both:

* a Command ABI
* a Library ABI

Both interfaces MUST be append-only.

Existing commands and library functions MUST remain compatible with existing consumers.

New commands and library functions MAY be added.

Neither the Command ABI nor the Library ABI may be modified in a
breaking manner merely to introduce new functionality.

ABI stability is determined by the specific interface contract.

A public function signature MUST NOT be changed in a way that breaks existing consumers of a stable interface.

A new parameter MUST NOT simply be appended to an existing function if doing so changes the existing ABI.

New behavior SHOULD normally be represented by a new interface.

Public structure layout is not automatically considered a stable ABI unless explicitly defined by the ABI contract.

___

# 93. Function Signature Evolution

Existing function signatures MUST NOT be changed in a breaking manner after the interface becomes stable.

For example:

```c
foo(int value);
```

MUST NOT simply become:

```c
foo(int value, int mode);
```

as an incompatible replacement.

Instead, a new function MAY be introduced:

```c
foo_with_mode(int value, int mode);
```

The original function SHOULD remain available when compatibility requires it.

___

# 94. Internal Helpers

Internal helper functions SHOULD remain private.

Internal helper functions MAY use implementation-specific conventions that are not appropriate for public interfaces.

Internal helpers MUST still obey relevant ownership, lifetime, cleanup, and correctness rules.

Internal helpers MAY assume preconditions guaranteed by their callers.

___

# 95. Static Internal Functions

Internal functions SHOULD use appropriate internal linkage mechanisms when this improves encapsulation.

For example:

```c
static TWR_STATUS_CODE
foo_helper(...)
{
    ...
}
```

is an appropriate implementation technique for a translation-unit-local helper.

The exact mechanism is not mandatory when another mechanism provides equivalent encapsulation.

The purpose is to prevent unnecessary exposure of internal symbols.

___

# 96. Source-Level Visibility

Symbols SHOULD have the narrowest visibility required by their use.

A symbol that is only required within one translation unit SHOULD remain local to that translation unit where practical.

A symbol required by multiple internal source files MAY be exposed through a private header.

A symbol required by external consumers MUST be exposed through the appropriate public interface.

___

# 97. Public Implementation Leakage

Public headers and interfaces SHOULD NOT unnecessarily expose:

* private helper functions
* internal allocation strategy
* internal synchronization
* internal storage mechanisms
* private state
* implementation-only dependencies

The public interface SHOULD expose what consumers need to use the component correctly.

___

# 98. Error Cleanup

Every normal error path MUST respect resource ownership.

If a function owns resources when an error occurs, it MUST release those resources before returning.

Cleanup MUST occur before returning the error status.

Owned resources MUST be released in reverse acquisition order.

The function MAY use `goto` or another control-flow mechanism to implement cleanup.

___

# 99. Successful Cleanup

Resources MUST NOT be released prematurely merely because they were passed to another function.

Passing a resource to another function does not end the owner's responsibility.

The owner remains responsible until:

* the resource is destroyed
* ownership is explicitly transferred
* another explicitly defined lifetime mechanism applies

___

# 100. Failed Operations

A failed operation MUST NOT silently leave owned resources unmanaged.

If a lower-level operation fails, the caller MUST either:

* handle the error according to its contract
* propagate the error
* translate the error when required
* perform another explicitly defined recovery action

A function MUST NOT return success merely to avoid handling an error.

___

# 101. Resource Creation

When a function creates a resource at the request of a caller, ownership belongs to the caller that initiated the creation unless the contract explicitly defines another ownership model.

The implementation function MAY physically allocate the resource.

Physical allocation does not by itself determine ownership.

Ownership is a semantic contract.

___

# 102. Resource Destruction Interfaces

Destruction functions SHOULD make ownership responsibilities clear.

A destruction function SHOULD normally be invoked by the owner.

A function MUST NOT destroy resources it merely borrowed.

A resource MAY have multiple aliases, but only the owner has destruction responsibility unless the contract explicitly defines another mechanism.

___

# 103. Memory Safety

TWR code MUST respect:

* ownership
* lifetime
* input immutability
* output validity
* aliasing rules
* resource cleanup

The rules in this document are intended to prevent hidden ownership and lifetime behavior.

Memory safety SHOULD be achieved through clear contracts and explicit responsibility rather than through unnecessary abstraction.

___

# 104. Stack Usage

Stack allocation SHOULD be preferred when the lifetime and size of the object make it appropriate.

Large or dynamically sized objects MAY require heap allocation or another storage mechanism.

Stack preference MUST NOT override correctness.

The appropriate storage mechanism is determined by:

* lifetime
* size
* ownership
* recursion
* concurrency
* architecture

___

# 105. Heap Usage

Heap allocation MAY be used.

Heap allocation SHOULD NOT be used merely because it is convenient when stack allocation provides a simpler and safer lifetime model.

When heap allocation is used, ownership MUST remain clear.

The owner MUST eventually release the resource according to its contract.

___

# 106. Memory Abstractions

TWR memory abstractions SHOULD provide stable interfaces.

Internal allocator implementation MAY change.

A memory abstraction MAY initially forward to the underlying operating system and later use a different implementation.

Consumers SHOULD depend on the TWR memory interface when the architecture provides one.

___

# 107. Logging Interface

TWR provides its own logging interface.

TWR code SHOULD use the TWR logger rather than coupling user-facing behavior directly to a specific system logging implementation.

The current implementation MAY use system facilities such as Linux logging infrastructure.

The implementation of the logger MAY change without changing the public TWR logging interface.

Logging MUST NOT replace `TWR_STATUS_CODE` error propagation.

___

# 108. User Output

A library or low-level function SHOULD NOT directly decide how an error is presented to the end user.

Functions SHOULD return status and provide defined output.

Higher-level callers MAY decide whether to:

* log
* print
* display
* retry
* ignore
* terminate

The responsibility for presentation SHOULD remain at the appropriate layer.

___

# 109. Diagnostics

Diagnostic detail MAY be more specific than the public status code.

The status code SHOULD remain general.

The logger MAY provide:

* contextual information
* resource identifiers
* operation names
* system error information
* debugging information

Diagnostic information SHOULD NOT be required for correct interpretation of the basic TWR status contract.

___

# 110. General Error Taxonomy

TWR SHOULD avoid excessive error-code specialization.

A small set of meaningful general errors SHOULD be preferred over a large taxonomy of narrowly differentiated codes.

The function contract MAY provide additional semantic detail.

The logger MAY provide diagnostic detail.

The status code SHOULD remain suitable for stable API use.

___

# 111. Boolean Status vs Error Status

Boolean values MUST NOT replace `TWR_STATUS_CODE` when the operation itself can fail.

For example:

```c
TWR_STATUS_CODE
twr_foo_is_valid(
    const foo_t *foo,
    bool *valid
);
```

is preferred over:

```c
bool
twr_foo_is_valid(
    const foo_t *foo
);
```

when determining validity can itself fail.

The boolean represents the result.

The status code represents whether the operation successfully produced that result.

___

# 112. Internal Assertions and External Input

Assertions MUST NOT be used as a substitute for ordinary validation of external input.

For example, an externally supplied invalid parameter SHOULD produce:

```c
return TWR_ERROR_INVALID_ARGUMENT;
```

rather than:

```c
assert(parameter != NULL);
```

when the invalid condition is an expected runtime possibility.

Assertions are for conditions that should be impossible if the program and its contracts are correct.

___

# 113. Invariants

Internal invariants SHOULD be explicit.

When an invariant is required for correctness and violation represents a programmer or implementation error, the invariant SHOULD be asserted.

An invariant MUST NOT be silently ignored.

An invariant MUST NOT be converted into a generic runtime error merely to avoid crashing when the state is genuinely impossible under the contract.

___

# 114. Crash Philosophy

TWR considers a crash an acceptable outcome for unrecoverable programmer or invariant failures.

A crash MUST NOT be treated as an ordinary error-return mechanism for expected runtime conditions.

Expected runtime conditions SHOULD use `TWR_STATUS_CODE`.

Unexpected impossible states MAY crash through `assert` or another appropriate fatal mechanism.

___

# 115. Recovery

Recovery SHOULD be proportional to the failure.

A function SHOULD NOT create elaborate recovery logic for an unrecoverable invariant violation.

A runtime failure MAY be recoverable when the contract permits it.

A component MAY terminate and allow a higher-level supervisor or caller to decide what happens next.

TWR does not require every failure to be converted into successful execution.

___

# 116. Thread Safety and Ownership

Ownership rules remain valid under concurrency.

If multiple threads can access a resource, the contract MUST define the relevant synchronization and ownership behavior.

Thread safety MUST NOT implicitly transfer ownership.

A thread using a resource does not become its owner merely by accessing it.

___

# 117. Mutable Global State and Concurrency

Global mutable state SHOULD be minimized.

When global mutable state is necessary, concurrent access behavior SHOULD be explicitly defined.

Synchronization MUST be provided when required by the contract.

Thread safety MUST NOT be assumed merely because the state is globally accessible.

___

# 118. Explicit Dependencies and Includes

Component dependencies and source includes SHOULD reflect actual use.

A source file SHOULD NOT include unrelated headers merely because they are available.

A component SHOULD NOT declare dependencies that it does not need merely for convenience.

Dependencies SHOULD remain explicit and minimal.

___

# 119. Circular Dependency Handling

Architectural circular dependencies MUST NOT be introduced.

Language-level declaration cycles MAY be resolved using forward declarations when possible.

A forward declaration is appropriate when the complete type definition is not required.

If the complete type is required, the necessary definition MUST be included.

A forward declaration MUST NOT be used to bypass a genuine architectural dependency problem.

___

# 120. Developer Choice

Where these rules explicitly permit developer choice, the developer MAY choose the implementation that best satisfies the component's requirements.

Examples include:

* by-value vs pointer inputs
* separate outputs vs output structures
* use of `goto`
* use of macros
* pointer arithmetic
* stack vs heap where both are valid
* implementation of encapsulation
* header-level `static inline`
* internal synchronization mechanisms

Developer choice MUST remain within the boundaries defined by contracts and mandatory invariants.

___

# 121. SHOULD vs MUST

The terms used in this document have precise intent.

`MUST` means that the rule is normative and code violating it is considered non-compliant unless a specific higher-level contract explicitly defines an exception.

`SHOULD` means that the behavior is the standard TWR practice and deviations require a meaningful reason.

`MAY` means that the behavior is explicitly permitted.

A `SHOULD` rule MUST NOT be interpreted as an absolute prohibition.

A `MAY` rule MUST NOT be interpreted as a requirement.

___

# 122. Avoiding Artificial Rules

TWR coding rules SHOULD describe meaningful invariants.

TWR MUST NOT create arbitrary restrictions merely because another project uses them.

A coding convention SHOULD exist when it provides one or more of:

* correctness
* interface stability
* readability
* maintainability
* explicit ownership
* explicit data flow
* predictable behavior
* compatibility
* composability
* reduced coupling

Rules that do not provide meaningful value SHOULD NOT be introduced merely for stylistic uniformity.

___

# 123. Unix and POSIX as the Default

When TWR has not explicitly defined a different behavior, established Unix, POSIX, Linux, or BSD conventions SHOULD be treated as the default reference for C implementation decisions.

TWR-specific rules override those conventions where TWR has a deliberate reason to differ.

This allows TWR to inherit decades of established systems-programming practice without unnecessarily reproducing every convention as an independent TWR rule.

___

# 124. Stable Interface, Replaceable Implementation

The following principle summarizes the TWR coding model:

```text
Public Contract
      |
      v
Stable Interface
      |
      v
Replaceable Implementation
```

A developer SHOULD optimize implementation freedom behind stable boundaries.

A consumer SHOULD depend on the stable interface rather than implementation details.

A refactor SHOULD NOT require consumer changes when the public contract remains unchanged.

___

# 125. Core Ownership Model

The TWR ownership model can be summarized as:

```text
Creation initiated by caller
        |
        v
Caller owns resource
        |
        +--> resource may be passed to other functions
        |
        +--> ownership remains unchanged
        |
        +--> aliases may exist
        |
        +--> borrowed functions MUST NOT destroy it
        |
        +--> lifetime MUST NOT be implicitly extended
        |
        +--> owner releases resource
```

Ownership transfer is permitted but SHOULD be avoided.

When ownership transfer occurs, it MUST be explicit in the contract.

___

# 126. Core Error Model

The TWR error model can be summarized as:

```text
External/runtime failure
        |
        v
TWR_STATUS_CODE
        |
        v
propagate upward
        |
        v
highest appropriate layer
        |
        +--> log/context
        |
        +--> recover
        |
        +--> report
        |
        +--> terminate
```

Programmer errors and impossible internal states follow a different path:

```text
Internal invariant violation
        |
        v
assert
        |
        v
crash
```

___

# 127. Core Resource Model

The TWR resource model can be summarized as:

```text
Acquire A
    |
    v
Acquire B
    |
    v
Acquire C
    |
    v
failure
    |
    v
Release C
    |
    v
Release B
    |
    v
Release A
    |
    v
return TWR_STATUS_CODE
```

Normal error paths MUST clean up owned resources.

Cleanup MUST follow reverse acquisition order.

___

# 128. Core Data Flow Model

TWR function data flow follows:

```text
const input
    |
    v
function
    |
    +----> mutable output
    |
    +----> TWR_STATUS_CODE
```

Input values MUST remain unchanged.

Outputs MUST be explicit.

Input/output roles MUST be distinguishable.

A successful function MUST produce valid outputs according to its contract.

An errored function MUST be assumed to have produced incomplete outputs unless the contract explicitly defines another behavior.

___

# 129. Core Dependency Model

TWR dependency flow follows:

```text
Consumer
   |
   +----> Direct dependency
               |
               +----> Public interface
```

A consumer MUST NOT rely on transitive dependencies.

Circular component dependencies are forbidden.

Declaration-level cycles MAY be resolved through forward declarations where the language permits.

___

# 130. Core API Evolution Model

TWR public APIs evolve through addition:

```text
API v1
├── function_a
└── function_b

API v2
├── function_a
├── function_b
└── function_c

API v3
├── function_a
├── function_b
├── function_c
└── function_d
```

Existing interfaces remain.

Deprecated interfaces remain available.

New functionality is added.

Implementation may change independently.

This append-only model is fundamental to TWR interface continuity.

___

# 131. Final Principles

TWR coding SHOULD be guided by the following principles:

* Keep components small and composable.
* Give every TWR App one fundamental responsibility.
* Never create God Functions.
* Keep implementation encapsulated.
* Expose stable interfaces.
* Keep implementations replaceable.
* Prefer explicit dependencies.
* Do not rely on transitive dependencies.
* Avoid circular dependencies.
* Prefer forward declarations when only declarations are required.
* Keep input and output roles distinct.
* Never modify input values.
* Use `const` to represent input semantics.
* Make ownership explicit.
* Keep ownership with the party that initiated creation unless explicitly transferred.
* Avoid ownership transfer unless necessary.
* Never destroy resources that are not owned.
* Never extend input lifetime implicitly.
* Allow aliases while maintaining a single owner.
* Avoid input/output aliasing unless explicitly supported.
* Return `TWR_STATUS_CODE` as the normal function status mechanism.
* Keep status codes small and general.
* Propagate errors upward by default.
* Log errors at the highest appropriate layer.
* Treat outputs on error as incomplete.
* Produce valid outputs on success.
* Clean up owned resources on normal error paths.
* Release resources in reverse acquisition order.
* Use `goto` when it improves cleanup or control flow.
* Use `assert` for programmer errors and impossible internal states.
* Let unrecoverable failures crash.
* Do not confuse let-it-crash with permission to leak resources.
* Prefer stack allocation where practical.
* Use heap allocation when required.
* Prefer TWR-provided facilities where they provide a stable abstraction.
* Avoid unnecessary global mutable state.
* Define thread safety through contracts.
* Use `size_t` for sizes, lengths, counts, and quantities.
* Use fixed-width integers when fixed width is semantically required.
* Do not use unsigned types merely because negative values are undesirable.
* Keep data and behavior separate.
* Use free functions rather than methods in TWR C structures.
* Follow Unix/Linux conventions for C.
* Follow Google-style conventions for C++.
* Use `.cc` for TWR C++ source.
* Keep public APIs minimal.
* Evolve public APIs append-only.
* Keep deprecated APIs available.
* Allow deprecated APIs to delegate to newer implementations when compatibility is preserved.
* Treat the contract as normative and the implementation as replaceable.
* Do not introduce artificial rules without a meaningful engineering reason.

The central TWR principle is:

```text
Strong Contracts
      +
Explicit Ownership
      +
Explicit Data Flow
      +
Explicit Dependencies
      +
Stable Interfaces
      +
Replaceable Implementations
      +
Unix Philosophy
      +
Let-it-Crash
      =
TWR Coding Model
```

___

# 132. Revision History

## 132.1 Initial Version

- [ ] Finalize the initial `core-coding-rules.md` version.
- [ ] Review the rules against all current TWR architecture documents.
- [ ] Review the rules against existing TWR source code.
- [ ] Verify consistency with the TWR documentation rules.
- [ ] Verify consistency with the TWR API and ABI model.
- [ ] Verify that no implementation detail has accidentally become a mandatory architectural requirement.

___

# 133. ToDo List

- [ ] Review this document whenever the TWR public interface model changes.
- [ ] Add rules only when a recurring engineering problem justifies them.
- [ ] Keep this document focused on coding invariants and conventions.
- [ ] Move component-specific rules into their respective architecture or contract documents.
- [ ] Preserve the distinction between normative behavior and implementation freedom.

___ 

# 134. Notes

> [!Note] AI Usage
> This document has been transcribed by an AI in accordance with the rules in the `documentation-guide-rules-ai.md` file, based on the developer's architectural decisions.

> [!Note] AI Specific File 
> `tayku-ai-design-principles.md` file was designed for AI agents by using this file.

___ 
