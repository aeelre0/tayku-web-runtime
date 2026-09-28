# Tayku Web Client Verify Command Architecture

The "`verify`" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for verifying signed Tayku packages and projects.

The command determines the verification target from the command arguments and the current filesystem location.

The verification operation determines whether a package or project has remained unchanged since it was signed by its publisher.

The "`verify`" command acts as a wrapper around the Verifier module.

The cryptographic verification mechanism, signature format, key derivation, and verification procedure are defined by the Verifier architecture.

The command supports two verification types:

```sh
npx tayku-web verify --package
npx tayku-web verify --project
```

---

# 1. In-Scope
* **In-Scope**  
	* Determine the verification type from the command arguments.
	* Determine the verification target from the current filesystem location and command arguments.
	* Resolve packages and projects that can be verified.
	* Invoke the Verifier module with the resolved target.
	* Return the verification result to the CLI layer.
	* Return the appropriate "`TAYKU-STATUS-CODE`".

* **Out-of-Scope**  
	* Implementing cryptographic verification.
	* Implementing signature validation.
	* Implementing key derivation.
	* Implementing publisher identity verification.
	* Defining the signature format.
	* Defining the verification algorithm.
	* Signing packages or projects.
	* Modifying verified packages or projects.
	* Parsing command-line arguments.
	* Rendering command output.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "`verify`" command.

## 2.1 Logic

The "`verify`" command acts as a wrapper around the Verifier module.

The command determines whether a package or project is being verified and resolves the corresponding verification target.

The supported verification types are:

* **Package**
	* Verifies a package and determines whether it has remained unchanged since it was signed by its publisher.

* **Project**
	* Verifies a project and determines whether it has remained unchanged since it was signed by its publisher.

The "`verify`" command does not implement the verification mechanism itself.

The resolved target is passed to the Verifier module.

The Verifier module performs the verification according to the Verifier architecture and returns the resulting verification status.

The "`verify`" command then propagates the resulting "`TAYKU-STATUS-CODE`" to the CLI layer.

· · ·

## 2.2 Definitions

* **Verification Target:** The Tayku package or project selected for verification.

* **Package Verification:** The operation of determining whether a package has remained unchanged since it was signed by its publisher.

* **Project Verification:** The operation of determining whether a project has remained unchanged since it was signed by its publisher.

* **Publisher:** The entity that signed the package or project.

* **Verifier Module:** The module responsible for cryptographic verification according to the Verifier architecture.

* **Verification Result:** The result returned by the Verifier module indicating whether the verification operation succeeded or failed.

* **General Context:** A filesystem location that does not contain a "`tayku-project.json`" or "`tayku-package.json`" defining the current verification context.

* **Project Context:** A filesystem location containing a "`tayku-project.json`".

* **Project Registry:** The local project state maintained by the Tayku Client, used to resolve initialized projects from a general location.

---

# 3. Interface

## 3.1 Inputs

The "`verify`" command accepts a verification identifier.

The general command form is:

```sh
npx tayku-web verify --<identifier>
```

The supported identifiers are:

```text
package
project
```

The exact target resolution depends on the current filesystem location.

· · ·

## 3.2 Outputs

The "`verify`" command returns a "`TAYKU-STATUS-CODE`".

The CLI layer may additionally display the verification result to the user.

For example, the CLI may communicate that the selected package or project passed or failed verification.

The exact output format is handled by the CLI layer.

· · ·

## 3.3 Dependencies

The "`verify`" command depends on the following components:

* **CLI Parser**  
	* Parses the command-line arguments before they are passed to the "`verify`" command.

* **Verifier Module**  
	* Provides the cryptographic verification mechanism.
	* Validates package and project signatures.
	* Defines the verification contract.

* **Project Registry**
	* Resolves initialized Tayku projects from a general filesystem location.

* **Filesystem**
	* Provides access to local package and project data.
	* `fs`

* **Runtime Environment of JS**
	* `nodejs`

The "`verify`" command does not implement cryptographic verification itself.

· · ·

## 3.4 Source Contract

The "`verify`" command receives the verification type and target information from the CLI layer.

A conceptual interface is defined as follows:

```js
/**
 * Verifies a Tayku package or project.
 *
 * Resolves the verification target and invokes the Verifier module.
 *
 * @param {string} type The verification type.
 * @param {string} target The verification target.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function verify(type, target) {...}
```

The "`verify`" command resolves the target according to the current filesystem context.

The resolved target is passed to the Verifier module.

The "`verify`" command does not perform cryptographic verification itself.

· · ·

## 3.5 Command Contract

### Verify a Package

When executed from a Tayku project:

```sh
npx tayku-web verify --package <name>
```

Verifies the specified package within the current Tayku project.

When executed from a location containing the appropriate project state, the package may be resolved from that project's package map.

### Verify a Project

When executed from a general location:

```sh
npx tayku-web verify --project <name>
```

Resolves the initialized Tayku project and verifies it.

### Verify a Package from General Context

When executed from a Tayku project context:

```sh
npx tayku-web verify --package <name>
```

The package is resolved from the current project's package map.

When executed from a general location, the project and package may be specified together:

```sh
npx tayku-web verify <project> <package>
```

The Tayku Client resolves the project from its initialized-project state and resolves the package from that project.

· · ·

## 3.6 Command Behavior

**What happens when "`--project`" is specified?**

The command resolves the requested project.

If the command is executed from a general location, the project is resolved from the Tayku Client's initialized-project state.

The resolved project is passed to the Verifier module.

The Verifier module performs the verification according to the Verifier architecture.

The resulting "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

· · ·

**What happens when "`--package`" is specified?**

The command resolves the requested package from the current project context.

The package is passed to the Verifier module.

The Verifier module performs the verification according to the Verifier architecture.

The resulting "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

· · ·

**What happens when "`verify --project <name>`" is executed from a general location?**

The command resolves the initialized project named "`<name>`" from the Tayku Client's project state.

The resolved project is passed to the Verifier module.

The project is verified according to the Verifier architecture.

· · ·

**What happens when "`verify --package <name>`" is executed from a location containing "`tayku-project.json`"?**

The command uses the current project context.

The package named "`<name>`" is resolved from the project's package map.

The resolved package is passed to the Verifier module.

· · ·

**What happens when "`verify --package <name>`" is executed from a location containing "`tayku-package.json`"?**

The command does not provide a standalone package verification mode for this context.

No separate verification behavior is currently defined for a location containing only "`tayku-package.json`".

· · ·

**What happens when "`verify <project> <package>`" is executed from a general location?**

The first identifier is interpreted as the initialized project name.

The second identifier is interpreted as the package name.

The Tayku Client resolves the project from its initialized-project state.

The package is then resolved from the project's package map.

The resolved package is passed to the Verifier module.

· · ·

**What does successful verification mean?**

Successful verification means that the Verifier module has determined that the signed package or project still satisfies the signature and integrity conditions defined by the Verifier architecture.

The "`verify`" command does not independently determine whether the target was modified.

· · ·

**What happens when verification fails?**

The Verifier module returns the corresponding failure result.

The "`verify`" command propagates the resulting "`TAYKU-STATUS-CODE`" to the CLI layer.

The "`verify`" command does not attempt to repair or modify the failed target.

---

# 4. Error Handling

The "`verify`" command may return errors generated by the CLI argument layer, target resolution, project resolution, package resolution, or the Verifier module.

* **Invalid Arguments**  
	* "`INVALID ARGUMENT`": The supplied verification identifier or target combination is invalid.

	* **Solution**: Use one of the supported verification forms:

```sh
npx tayku-web verify --package <name>
npx tayku-web verify --project <name>
npx tayku-web verify <project> <package>
```

* **Project Not Found**  
	* "`PROJECT NOT FOUND`": The requested project could not be resolved.

	* **Solution**: Check the project name and verify that the project exists in the Tayku Client's initialized-project state.

* **Package Not Found**  
	* "`PACKAGE NOT FOUND`": The requested package could not be resolved from the selected project.

	* **Solution**: Check the package name and verify that the package exists in the selected project's package map.

* **Invalid Location**  
	* "`INVALID LOCATION`": The current filesystem location does not provide the context required for the requested verification operation.

	* **Solution**: Execute the command from the appropriate project context or provide the required project and package identifiers.

* **Verification Error**  
	* "`VERIFY ERROR`": The Verifier module could not complete the verification operation.

	* **Solution**: Check the Verifier module result and retry the verification operation.

* **Permission Error**  
	* "`PERMISSION DENIED`": The required package, project, or verification data cannot be accessed.

	* **Solution**: Check the permissions of the relevant files and directories.

* **Filesystem Error**  
	* "`IO ERROR`": An unexpected filesystem error occurred while resolving or reading the verification target.

	* **Solution**: Check the filesystem and retry the verification operation.

Errors generated by the Verifier module are propagated to the CLI layer through the command's "`TAYKU-STATUS-CODE`".

---

# 5. Data Flow

## 5.1 Project Verification

```text
User
   │
   │ verify --project <name>
   ▼
CLI
   │
   ▼
Verify Command
   │
   ▼
Project Registry
   │
   │ resolve project
   ▼
Project
   │
   ▼
Verifier Module
   │
   │ verification
   ▼
Verification Result
   │
   ▼
Verify Command
   │
   ▼
CLI
   │
   ▼
User
```

· · ·

## 5.2 Package Verification in Project Context

```text
User
   │
   │ verify --package <name>
   ▼
CLI
   │
   ▼
Verify Command
   │
   ▼
Current Project
   │
   │ package map
   ▼
Package
   │
   ▼
Verifier Module
   │
   │ verification
   ▼
Verification Result
   │
   ▼
Verify Command
   │
   ▼
CLI
```

· · ·

## 5.3 Package Verification from General Context

```text
User
   │
   │ verify <project> <package>
   ▼
CLI
   │
   ▼
Verify Command
   │
   ▼
Project Registry
   │
   │ resolve project
   ▼
Project
   │
   │ package map
   ▼
Package
   │
   ▼
Verifier Module
   │
   │ verification
   ▼
Verification Result
   │
   ▼
Verify Command
   │
   ▼
CLI
```

---

# 6. Security

* **Signature Integrity**
	* The "`verify`" command must delegate all cryptographic verification to the Verifier module.
	* The command must not implement an alternative verification mechanism.

* **Target Integrity**
	* The verification target must be resolved from the intended package or project context.
	* The command must not verify an unintended filesystem resource.

* **No Modification**
	* Verification must not modify the package or project being verified.
	* A failed verification must not trigger an automatic repair or update operation.

* **Project Resolution**
	* Projects resolved from a general location must originate from the Tayku Client's initialized-project state.

* **Responsibility Isolation**
	* Target resolution remains the responsibility of the "`verify`" command.
	* Cryptographic verification remains the responsibility of the Verifier module.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Verify Command**
	* Defined the initial architecture of the "`verify`" command.
	* Defined package verification.
	* Defined project verification.
	* Defined project-context package resolution.
	* Defined general-context project resolution.
	* Defined general-context project and package resolution.
	* Established the Verifier module as the owner of the verification mechanism.

---

# 8. ToDo List

* [ ] Define the exact Verifier module interface.
* [ ] Define the exact verification target interface.
* [ ] Define the exact "`TAYKU-STATUS-CODE`" values returned by verification.
* [ ] Define the exact CLI output for successful verification.
* [ ] Define the exact CLI output for failed verification.
* [ ] Define the exact project registry interface.
* [ ] Define behavior when a project is not initialized.
* [ ] Define behavior when multiple projects have the same identifier.
* [ ] Define behavior when a package is not present in the selected project's package map.
* [ ] Define behavior when a package has no valid signature.
* [ ] Define behavior when a package has been modified after signing.
* [ ] Define behavior when a project has been modified after signing.
* [ ] Define behavior when signature data is malformed.
* [ ] Define behavior when the publisher cannot be resolved.
* [ ] Test package verification.
* [ ] Test project verification.
* [ ] Test package verification inside a project context.
* [ ] Test project verification from a general location.
* [ ] Test project and package verification from a general location.
* [ ] Test verification of modified packages.
* [ ] Test verification of modified projects.
