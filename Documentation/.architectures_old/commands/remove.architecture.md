# Tayku Web Client Remove Command Architecture

The "remove" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for removing Tayku packages, projects, or registries.

The command determines the removal target from the command arguments and invokes the appropriate underlying Tayku Web Client module to perform the requested removal operation.

The "remove" command acts as an abstraction and orchestration layer. It does not implement package, project, or registry removal logic itself.

The command supports three removal types:

```sh
npx tayku-web remove --package <NAME>
npx tayku-web remove --project <NAME>
npx tayku-web remove --registry <SOURCE>
```

---

# 1. In-Scope
* **In-Scope**  
	* Determine the removal type from the command arguments.
	* Determine the requested package, project, or registry.
	* Validate the arguments required by the selected removal type.
	* Invoke the appropriate underlying Tayku Web Client module.
	* Return the appropriate "TAYKU-STATUS-CODE".

* **Out-of-Scope**  
	* Parsing command-line arguments.
	* Implementing package removal logic.
	* Implementing project removal logic.
	* Implementing registry removal logic.
	* Managing package contents.
	* Managing project contents.
	* Managing registry contents.
	* Removing packages from remote registries.
	* Removing projects from remote registries.
	* Defining package configuration.
	* Defining project configuration.
	* Defining registry configuration.
	* Rendering command output.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "remove" command.

## 2.1 Logic

The "remove" command acts as an abstraction layer between the CLI layer and the underlying Tayku Web Client modules responsible for removal operations.

The command determines the removal operation from the supplied command arguments.

The supported removal types are:

* **Package**
	* Removes the requested package from the current Tayku application.

* **Project**
	* Removes the requested project.

* **Registry**
	* Removes the requested registry from the Tayku Web Client registry configuration.

The "remove" command does not implement the actual removal logic.

Instead, it resolves the requested removal type and invokes the corresponding module.

The package removal operation operates on the package associated with the current Tayku application.

The project removal operation operates on the requested Tayku project.

The registry removal operation operates on the registry configuration maintained by the Tayku Web Client.

The command interface remains independent from the internal implementation of these modules.

· · ·

## 2.2 Definitions

* **Removal Type:** The resource category selected through "`--package`", "`--project`", or "`--registry`".

* **Package:** A Tayku package managed by a Tayku application.

* **Project:** A Tayku project managed by the Tayku Web Client.

* **Registry:** A registry configured for use by the Tayku Web Client.

* **Registry Source:** The source identifying a registry.

* **Removal Target:** The package name, project name, or registry source supplied to the command.

---

# 3. Interface

## 3.1 Inputs

The "`remove`" command accepts a removal type and a corresponding removal target.

### Package

```sh
npx tayku-web remove --package <NAME>
```

"`<NAME>`" identifies the package to be removed.

### Project

```sh
npx tayku-web remove --project <NAME>
```

"`<NAME>`" identifies the project to be removed.

### Registry

```sh
npx tayku-web remove --registry <SOURCE>
```

"`<SOURCE>`" identifies the registry to be removed.

The command does not currently expose additional removal types.

· · ·

## 3.2 Outputs

The "`remove`" command returns a "`TAYKU-STATUS-CODE`".

When the requested removal operation is completed successfully, a successful status code is returned.

When the requested removal operation fails, the corresponding failure status code is returned.

The command does not define the internal result of the removal operation.

· · ·

## 3.3 Dependencies

The "`remove`" command depends on the following components:

* **CLI Parser**  
	* Parses the command-line arguments before they are passed to the "remove" command.

* **Package Module**  
	* Provides the functionality required to remove a package.

* **Project Module**  
	* Provides the functionality required to remove a project.

* **Registry Module**  
	* Provides the functionality required to remove a registry.

* **Runtime Environment of JS**
	* `nodejs`

The command depends on the interfaces exposed by these modules rather than their internal implementations.

· · ·

## 3.4 Source Contract

The "`remove`" command receives a removal type and a removal target from the CLI layer.

A conceptual interface is defined as follows:

```js
/**
 * Removes a Tayku resource.
 *
 * Resolves the requested removal type and invokes
 * the corresponding Tayku Web Client module.
 *
 * @param {string} type The removal type.
 * @param {string} target The removal target.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function remove(type, target) {...}
```

The "`type`" parameter determines which removal operation is performed.

The "`target`" parameter identifies the resource to be removed.

The command does not implement the removal operation itself.

The function return value communicates operation status to the CLI layer.

· · ·

## 3.5 Command Contract

### Remove a Package

```sh
npx tayku-web remove --package <NAME>
```

Removes the specified package from the current Tayku application.

### Remove a Project

```sh
npx tayku-web remove --project <NAME>
```

Removes the specified Tayku project.

### Remove a Registry

```sh
npx tayku-web remove --registry <SOURCE>
```

Removes the specified registry from the Tayku Web Client registry configuration.

· · ·

## 3.6 Command Behavior

**What happens when "`--package`" is specified?**

The command resolves the package removal operation.

The package module is invoked with the requested package name.

The package module performs the package removal according to its own contract.

The resulting "TAYKU-STATUS-CODE" is returned to the CLI layer.

· · ·

**What happens when "`--project`" is specified?**

The command resolves the project removal operation.

The project module is invoked with the requested project name.

The project module performs the project removal according to its own contract.

The resulting "TAYKU-STATUS-CODE" is returned to the CLI layer.

· · ·

**What happens when "`--registry`" is specified?**

The command resolves the registry removal operation.

The registry module is invoked with the requested registry source.

The registry module performs the registry removal according to its own contract.

The resulting "TAYKU-STATUS-CODE" is returned to the CLI layer.

· · ·

**What happens when the requested resource does not exist?**

If the underlying module cannot resolve the requested resource, the command does not perform the removal operation.

The corresponding failure status code is returned to the CLI layer.

· · ·

**What happens when the underlying module fails?**

If the module responsible for the selected removal operation reports a failure, the "remove" command propagates the resulting "TAYKU-STATUS-CODE" to the CLI layer.

The command does not reproduce or replace the functionality of the underlying module.

· · ·

**What happens when an unsupported removal type is provided?**

If the supplied removal type is not supported, the removal operation is not performed.

An appropriate "INVALID ARGUMENT" status code is returned.

---

# 4. Error Handling

The "`remove`" command may return errors generated by the CLI argument layer or by the underlying removal modules.

* **Invalid Arguments**  
	* "`INVALID ARGUMENT`": The provided removal type or removal target is invalid.

	* **Solution**: Use one of the supported forms:

```sh
npx tayku-web remove --package <NAME>
npx tayku-web remove --project <NAME>
npx tayku-web remove --registry <SOURCE>
```

* **Resource Not Found**  
	* "`NOT FOUND`": The requested package, project, or registry could not be found.

	* **Solution**: Check the resource identifier and retry the operation.

* **Permission Error**  
	* "`PERMISSION DENIED`": The requested removal operation could not be performed because the required resource cannot be modified.

	* **Solution**: Check the permissions of the relevant files and directories.

* **Filesystem Error**  
	* "`IO ERROR`": An unexpected input/output error occurred during the removal operation.

	* **Solution**: Check the filesystem and retry the operation.

* **Registry Error**  
	* "`REGISTRY ERROR`": An error occurred while accessing or modifying the registry configuration.

	* **Solution**: Check the registry configuration and retry the operation.

Errors generated by underlying modules are propagated to the CLI layer through the command's "`TAYKU-STATUS-CODE`".

---

# 5. Data Flow

## 5.1 Package Removal

```text
User
   │
   │ remove --package <NAME>
   ▼
CLI
   │
   │ parsed arguments
   ▼
Remove Command
   │
   │ package name
   ▼
Package Module
   │
   │ remove package
   ▼
Package Removal Result
   │
   │ TAYKU-STATUS-CODE
   ▼
Remove Command
   │
   ▼
CLI
```

· · ·

## 5.2 Project Removal

```text
User
   │
   │ remove --project <NAME>
   ▼
CLI
   │
   │ parsed arguments
   ▼
Remove Command
   │
   │ project name
   ▼
Project Module
   │
   │ remove project
   ▼
Project Removal Result
   │
   │ TAYKU-STATUS-CODE
   ▼
Remove Command
   │
   ▼
CLI
```

· · ·

## 5.3 Registry Removal

```text
User
   │
   │ remove --registry <SOURCE>
   ▼
CLI
   │
   │ parsed arguments
   ▼
Remove Command
   │
   │ registry source
   ▼
Registry Module
   │
   │ remove registry
   ▼
Registry Removal Result
   │
   │ TAYKU-STATUS-CODE
   ▼
Remove Command
   │
   ▼
CLI
```

---

# 6. Security

* **Argument Validation**
	* The removal type must be validated before an operation is selected.
	* The removal target must be passed to the corresponding module without being interpreted as an unrelated command or option.

* **Path Security**
	* Package and project removal paths must be resolved according to the contracts of their respective modules.
	* User-provided targets must not allow unintended filesystem access.

* **Registry Security**
	* Registry removal must operate only on registries known to the Tayku Web Client registry configuration.
	* The command must not interpret a registry source as an arbitrary local filesystem path.

* **Responsibility Isolation**
	* The "remove" command is responsible only for selecting and invoking the appropriate removal operation.
	* Resource-specific validation and removal remain the responsibility of the corresponding modules.

* **Thread Safety**
	* The Tayku Web Client "remove" command operates synchronously in this version.
	* The command does not perform concurrent removal operations.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Remove Command**
	* Defined the initial architecture of the "`remove`" command.
	* Defined package removal.
	* Defined project removal.
	* Defined registry removal.
	* Defined the command interface for the three removal types.
	* Defined the interface between the "remove" command and the underlying modules.
	* Established the "remove" command as an abstraction and orchestration layer.

---

# 8. ToDo List

* [ ] Define the exact package module interface.
* [ ] Define the exact project module interface.
* [ ] Define the exact registry module interface.
* [ ] Define the exact parsed argument structure received by "commands/remove.js".
* [ ] Define the exact "TAYKU-STATUS-CODE" values used by each removal operation.
* [ ] Define package removal behavior.
* [ ] Define project removal behavior.
* [ ] Define registry removal behavior.
* [ ] Define behavior when a package is required by another package.
* [ ] Define behavior when a project contains uncommitted or modified data.
* [ ] Define registry removal validation.
* [ ] Test invalid removal types.
* [ ] Test missing removal targets.
* [ ] Test non-existent resources.
* [ ] Test package removal.
* [ ] Test project removal.
* [ ] Test registry removal.
* [ ] Test permission failures.
* [ ] Test filesystem failures.