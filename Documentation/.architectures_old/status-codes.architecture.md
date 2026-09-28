# Tayku Web Status Codes Architecture

The Tayku Web Client uses a status code system to represent the result of operations.

The status code system is the common error and result reporting mechanism of the Tayku Web Client.

The system is conceptually similar to the "`errno`" mechanism used by Unix-like systems.

Each operation may return a "`TAYKU-STATUS-CODE`" representing the result of the operation.

The meaning of each status code is defined by this architecture.

Commands and internal modules may use status codes to communicate operation results without requiring the caller to depend on implementation-specific error handling.

Status codes are numerical values.

The exact numerical values and their meanings are defined in the versioned Status Code Contract.

---

# 1. In-Scope
* **In-Scope**
	* Define the Tayku Web status code system.
	* Define the format of status codes.
	* Define the Status Code Contract.
	* Define the meaning of individual status codes.
	* Provide a common result mechanism for Tayku Web commands and modules.
	* Maintain the historical meaning of previously defined status codes.
* **Out-of-Scope**
	* Defining command-specific behavior.
	* Defining the internal implementation of modules.
	* Defining exceptions or language-specific error mechanisms.
	* Defining the implementation details of individual commands.

---

# 2. Architecture

This section examines the architecture and responsibilities of the Tayku Web Status Code system.

## 2.1 Status Code Logic

A Tayku Web operation returns a "`TAYKU-STATUS-CODE`" representing its result.

Conceptually:

```text
Operation
 |
 v
TAYKU-STATUS-CODE
 |
 +----> Success
 |
 +----> Failure
 |
 +----> Other Defined Result
```

The caller may inspect the returned status code and determine the result according to the Status Code Contract.

The status code itself does not define how the operation is implemented.

· · ·
## 2.2 Status Code Contract

The Status Code Contract defines the numerical values and meanings of Tayku Web status codes.

Each defined status code must have a documented meaning.

The contract is versioned independently from the internal implementation.

The following is a placeholder demonstrating the intended format:

### 3.x.x — Status Code Contract

* "`TAYKU-STATUS-CODE`": **Dummy Status Code**
	* **Meaning**: This entry is a placeholder for a status code definition.
	* **Result**: This entry will be replaced with the actual status code meaning.

Future status code definitions will describe what each code represents.

For example, a future entry may define a specific code as representing a particular error or successful operation.

· · ·
## 2.3 Status Code Stability

Once a status code and its meaning are included in the Status Code Contract, its established meaning must not be changed incompatibly.

A new status code may be introduced when a new result needs to be represented.

Existing status codes must retain their established meaning.

If the internal implementation changes, the status code returned to the caller may remain unchanged when the externally observable result remains the same.

· · ·
## 2.4 Implementation Independence

The Status Code system must not depend on a specific implementation language.

The current implementation may represent a "`TAYKU-STATUS-CODE`" as a JavaScript number.

A future implementation may represent the same status code in C, Go, or another language.

The numerical contract remains independent from the language used to implement it.

---

# 3. Interface

## 3.1 Inputs

A "`TAYKU-STATUS-CODE`" is produced as the result of a Tayku Web operation.

The status code does not require a specific input representation.

· · ·
## 3.2 Outputs

Operations return a "`TAYKU-STATUS-CODE`".

The caller may use the returned value to determine whether the operation succeeded or failed according to the Status Code Contract.

· · ·
## 3.3 Dependencies

The Status Code system must not depend on:

* A specific programming language.
* A specific internal module.
* A specific function.
* A specific data structure.
* A specific exception mechanism.
* A specific internal ABI.
* A specific internal API.

· · ·
## 3.4 Source Contract

The conceptual source contract is:

```js
/**
 * Returns the result of a Tayku Web operation.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function operation() {
	/* implementation-defined */
}
```

The exact function name, implementation, and source language are not part of the Status Code Contract.

· · ·
## 3.5 Command Contract

Commands may use "`TAYKU-STATUS-CODE`" values to communicate operation results.

For example:

```text
Command
 |
 v
Module
 |
 v
TAYKU-STATUS-CODE
 |
 v
Command
```

The command may then report or process the result according to its defined behavior.

· · ·
## 3.6 Status Code Behavior

A status code must represent the condition defined by its Status Code Contract entry.

The implementation must not return an existing status code to represent a different condition merely because the numerical value is convenient.

New conditions requiring a distinct externally meaningful result must receive a separately defined status code.

---

# 4. Error Handling

Status codes provide the common error reporting mechanism for Tayku Web operations.

An operation may return a failure status code instead of relying exclusively on language-specific exceptions.

* **Invalid Status Code**
	* "`INVALID STATUS CODE`": An undefined or invalid status code was produced.
	* **Solution**: Return a status code defined by the Status Code Contract.

---

# 5. Data Flow

## 5.1 Operation Result

```text
Operation
 |
 v
Result
 |
 v
TAYKU-STATUS-CODE
 |
 v
Caller
```

The caller interprets the status code according to the Status Code Contract.

· · ·
## 5.2 Command Result

```text
User
 |
 v
Command
 |
 v
Internal Module
 |
 v
TAYKU-STATUS-CODE
 |
 v
Command
 |
 v
User
```

The command may translate the internal operation result into the appropriate user-facing behavior.

· · ·
## 5.3 Contract Evolution

```text
New Requirement
 |
 v
New Status Code
 |
 v
Status Code Contract
 |
 v
Implementation
 |
 v
User / Caller
```

Existing status code meanings remain unchanged when a new status code is introduced.

---

# 6. Security

Status codes must not expose sensitive internal information.

Error reporting must provide enough information for the caller to determine the operation result without unnecessarily exposing internal implementation details.

Security-sensitive failures must use appropriate status codes defined by the Status Code Contract.

Internal debugging information may be handled separately from the stable status code interface.

---

# 7. Revision History

## v0.0.1 — 2026-09-22
**Author**: Ali Emre Arlı
**Status Codes**
	* Defined the "`TAYKU-STATUS-CODE`" mechanism.
	* Defined the Status Code Contract.
	* Defined the status code system as conceptually similar to Unix "`errno`".
	* Added a dummy Status Code Contract entry for future definitions.

---

# 8. ToDo List
* [ ] Define the complete Status Code Contract.
* [ ] Define the numerical range of status codes.
* [ ] Define success status codes.
* [ ] Define failure status codes.
* [ ] Define reserved status code ranges.
* [ ] Define whether status codes are globally unique across all Tayku Web modules.
* [ ] Define versioning rules for the Status Code Contract.
* [ ] Define the exact behavior for unknown status codes.
* [ ] Define language-specific representations for JavaScript, C, and future implementations.
