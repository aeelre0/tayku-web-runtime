# Tayku Web Client Update Command Architecture

The "`update`" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for updating Tayku packages and projects.

The command determines the update target from the supplied identifier and the current filesystem location.

Each Tayku package contains information identifying the registry from which it was obtained. The "`update`" command uses this registry information to locate the package's source and retrieve the changes made since the currently installed version.

The update mechanism follows the same general model used by systems such as Git, where only the files changed between versions are retrieved when possible.

The command supports updating packages installed inside a Tayku project and updating standalone Tayku projects from a general filesystem location.

No separate update behavior is currently defined for a standalone "`tayku-package.json`" location.

---

# 1. In-Scope
* **In-Scope**  
	* Determine the update target from the command arguments.
	* Determine the update context from the current filesystem location.
	* Update packages installed in a Tayku project.
	* Update a Tayku project from a general filesystem location.
	* Resolve the registry associated with a package.
	* Contact the package's source registry.
	* Determine the changes between the installed and available package versions.
	* Download files changed since the installed version.
	* Update an already installed package.
	* Resolve initialized Tayku projects when updating from a general location.
	* Support updating a project and a package together from a general location.
	* Return the appropriate "`TAYKU-STATUS-CODE`".

* **Out-of-Scope**  
	* Implementing registry communication.
	* Implementing package version comparison.
	* Implementing file-difference calculation.
	* Implementing package installation.
	* Creating Tayku projects.
	* Initializing Tayku projects.
	* Defining package registry protocols.
	* Managing standalone "`tayku-package.json`" update behavior.
	* Rendering command output.
	* Parsing command-line arguments.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "`update`" command.

## 2.1 Logic

The "`update`" command determines its behavior from the current filesystem location and the supplied identifiers.

When executed inside a Tayku project containing a "`tayku-project.json`", the command operates in **project context**.

In project context, a package identifier refers to a package used by the current project.

For example:

```sh
npx tayku-web update example-package
```

The command resolves the package within the current Tayku project and updates it.

If the package has already been installed, the command updates the existing installed package rather than treating the operation as a new installation.

Each package contains information identifying its source registry.

The "`update`" command uses this registry information to locate the package's source.

The command then determines the changes between the currently installed package and the available package version and retrieves the changed files.

· · ·

When the "`update`" command is executed from a general location that does not contain a "`tayku-project.json`", a project identifier can be used to update a Tayku project.

For example:

```sh
npx tayku-web update foo
```

The command resolves the initialized Tayku project named "`foo`" and updates it.

A project located below the current directory does not change the current location into project context.

For example, if the current location is:

```text
project/x/
```

and "`project/x/`" is not itself a Tayku project containing "`tayku-project.json`", the command does not use a parent or child Tayku project to implicitly change the update context.

· · ·

When the command is executed from a general location, a project and package may be specified together.

For example:

```sh
npx tayku-web update foo example-package
```

The command resolves the initialized Tayku project "`foo`" and the requested package within that project.

The Tayku Client maintains information about initialized projects, allowing projects to be resolved from a general location.

· · ·

The update operation retrieves only the files that have changed between the installed and available versions where the package update mechanism supports this operation.

The "`update`" command orchestrates the operation but does not implement the registry, package-difference, or file-transfer mechanisms itself.

· · ·

## 2.2 Definitions

* **Update Target:** The package or project selected for the update operation.

* **Package Context:** An update operation executed from a directory containing a "`tayku-project.json`", where package identifiers refer to packages used by that project.

* **General Context:** An update operation executed from a location that does not contain a "`tayku-project.json`".

* **Package Update:** The operation of updating a package already installed in a Tayku project.

* **Project Update:** The operation of updating an initialized Tayku project.

* **Package Source Registry:** The registry identified by the package as its source.

* **Installed Package:** A package currently present in a Tayku project.

* **Changed File:** A file whose contents differ between the installed package version and the available package version.

* **Initialized Project:** A Tayku project registered in the Tayku Client's local project state.

* **Project Registry:** The local state maintained by the Tayku Client that allows initialized projects to be resolved from a general location.

* **Update Context:** The interpretation of the command target based on the current filesystem location.

---

# 3. Interface

## 3.1 Inputs

The "`update`" command accepts one or more identifiers.

The general command form is:

```sh
npx tayku-web update --<identifier>
```

In project context, a package may be specified directly:

```sh
npx tayku-web update <package-name>
```

In general context, a project may be specified:

```sh
npx tayku-web update <project-name>
```

A project and package may also be specified together:

```sh
npx tayku-web update <project-name> <package-name>
```

The meaning of the identifier is determined by the current update context.

· · ·

## 3.2 Outputs

The "`update`" command returns a "`TAYKU-STATUS-CODE`".

The CLI layer may additionally report the progress and result of the update operation.

The exact output format is handled by the CLI layer.

· · ·

## 3.3 Dependencies

The "`update`" command depends on the following components:

* **CLI Parser**  
	* Parses the command-line arguments before they are passed to the "`update`" command.

* **Registry Module**  
	* Resolves and communicates with package source registries.
	* Provides access to package versions and package changes.

* **Package Module**  
	* Resolves installed packages.
	* Provides package update information.
	* Applies package file changes.

* **Project Module**  
	* Resolves Tayku projects.
	* Provides project update information.
	* Applies project file changes.

* **Project Registry**
	* Resolves initialized Tayku projects from a general filesystem location.

* **Filesystem**
	* Provides access to the current project and installed package files.
	* `fs`

* **Runtime Environment of JS**
	* `nodejs`

The "`update`" command does not implement registry communication, package-difference calculation, or file transfer itself.

· · ·

## 3.4 Source Contract

The "`update`" command receives the update identifiers from the CLI layer.

A conceptual interface is defined as follows:

```js
/**
 * Updates a Tayku package or project.
 *
 * Resolves the update context, determines the update target,
 * and invokes the appropriate Tayku modules.
 *
 * @param {string[]} identifiers The update target identifiers.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function update(identifiers) {...}
```

The current filesystem location determines whether the command operates in project context or general context.

The supplied identifiers determine the package or project to update.

The "`update`" command delegates registry access and update application to the appropriate modules.

· · ·

## 3.5 Command Contract

### Update a Package

When executed from a Tayku project:

```sh
npx tayku-web update <package-name>
```

Updates the specified package used by the current project.

### Update a Project

When executed from a general location:

```sh
npx tayku-web update <project-name>
```

Updates the specified initialized Tayku project.

### Update a Package in a Project

When executed from a general location:

```sh
npx tayku-web update <project-name> <package-name>
```

Updates the specified package within the specified initialized Tayku project.

· · ·

## 3.6 Command Behavior

**What happens when "`update <package-name>`" is executed inside a Tayku project?**

The command detects the "`tayku-project.json`" in the current location.

The command interprets the supplied identifier as a package identifier.

The package is resolved from the current project.

The package's source registry information is resolved.

The source registry is contacted to determine whether an updated package version is available.

If an update is available, the changes between the installed and available versions are determined.

Changed files are downloaded and applied to the installed package.

The resulting "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

· · ·

**What happens when the package is already installed?**

If the requested package is already installed in the current project, the command performs an update operation on the existing package.

The package is not treated as a new installation.

The package's source registry information is used to locate the package update.

· · ·

**What happens when "`update <project-name>`" is executed from a general location?**

The command determines that the current location is not a Tayku project.

The supplied identifier is interpreted as an initialized project identifier.

The Tayku Client's project state is searched for the requested project.

The resolved project is updated.

The update operation is performed against the resolved project.

· · ·

**What happens when "`update <project-name> <package-name>`" is executed from a general location?**

The command determines that the current location is not a Tayku project.

The first identifier is interpreted as the initialized project identifier.

The second identifier is interpreted as the package identifier.

The Tayku Client resolves the project from its local initialized-project state.

The package is resolved within that project.

The package's source registry is contacted and the available update is determined.

The changed files are downloaded and applied to the package.

· · ·

**What happens when the current location contains "`tayku-project.json`"?**

The command enters project context.

A package identifier supplied directly to the command is interpreted as a package belonging to the current project.

The command does not use the initialized-project registry to resolve another project for the same operation.

For example, from a directory containing "`tayku-project.json`":

```sh
npx tayku-web update foo
```

resolves "`foo`" as a package identifier rather than as another project identifier.

· · ·

**What happens when a Tayku project exists below the current location?**

The command does not automatically enter the child project's context.

Project context is determined from the current location.

A project located in a child directory is not implicitly selected merely because it contains a "`tayku-project.json`".

· · ·

**What happens when a package has a different source registry?**

The package's own source registry information is used.

The command does not assume that the package belongs to the registry currently being used for other operations.

The update request is sent to the registry recorded by the package.

· · ·

**What happens when no update is available?**

The package or project remains unchanged.

The command reports the resulting operation status to the CLI layer.

· · ·

**What happens when the source registry cannot be reached?**

The update operation cannot retrieve the package changes from the source registry.

The corresponding "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

· · ·

**What happens when a changed file cannot be downloaded?**

The update operation reports the failure.

The corresponding "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

The exact rollback or partial-update behavior is defined by the Package and Project architectures.

---

# 4. Error Handling

The "`update`" command may return errors generated by the CLI argument layer, update target resolution, registry access, package operations, project operations, or filesystem operations.

* **Invalid Arguments**  
	* "`INVALID ARGUMENT`": The supplied identifiers do not form a valid update operation.

	* **Solution**: Use one of the supported command forms:

```sh
npx tayku-web update <package-name>
npx tayku-web update <project-name>
npx tayku-web update <project-name> <package-name>
```

* **Project Not Found**  
	* "`PROJECT NOT FOUND`": The requested project could not be resolved from the Tayku Client's initialized-project state.

	* **Solution**: Check the project identifier and verify that the project has been initialized.

* **Package Not Found**  
	* "`PACKAGE NOT FOUND`": The requested package could not be resolved in the selected project.

	* **Solution**: Check the package identifier and verify that the package is available in the selected project.

* **Registry Not Found**  
	* "`REGISTRY NOT FOUND`": The registry recorded as the package's source could not be resolved.

	* **Solution**: Check the package source registry information and the local registry state.

* **Registry Error**  
	* "`REGISTRY ERROR`": The source registry could not provide the requested update information.

	* **Solution**: Check the source registry and retry the update operation.

* **Permission Error**  
	* "`PERMISSION DENIED`": The update operation cannot modify the required project or package files because of insufficient permissions.

	* **Solution**: Check the permissions of the relevant files and directories.

* **Filesystem Error**  
	* "`IO ERROR`": An unexpected filesystem error occurred while reading or updating the package or project.

	* **Solution**: Check the filesystem and retry the update operation.

* **Update Error**  
	* "`UPDATE ERROR`": The update operation could not be completed.

	* **Solution**: Check the affected package, project, and source registry and retry the operation.

Errors generated by lower-level modules are propagated to the CLI layer through the command's "`TAYKU-STATUS-CODE`".

---

# 5. Data Flow

## 5.1 Package Update in Project Context

```text
User
   │
   │ update <package-name>
   ▼
CLI
   │
   ▼
Update Command
   │
   │ detect tayku-project.json
   ▼
Project
   │
   ▼
Package
   │
   │ source registry
   ▼
Registry Module
   │
   │ available version
   ▼
Package Update
   │
   │ changed files
   ▼
Filesystem
   │
   ▼
Updated Package
   │
   ▼
Update Command
   │
   ▼
CLI
```

· · ·

## 5.2 Project Update from General Context

```text
User
   │
   │ update <project-name>
   ▼
CLI
   │
   ▼
Update Command
   │
   ▼
Project Registry
   │
   │ resolve initialized project
   ▼
Project
   │
   ▼
Registry / Project Modules
   │
   │ project changes
   ▼
Filesystem
   │
   ▼
Updated Project
   │
   ▼
Update Command
   │
   ▼
CLI
```

· · ·

## 5.3 Package Update from General Context

```text
User
   │
   │ update <project-name> <package-name>
   ▼
CLI
   │
   ▼
Update Command
   │
   ├──► Project Registry
   │         │
   │         ▼
   │      Project
   │         │
   │         ▼
   │      Package
   │
   └───────────────┐
                   ▼
             Source Registry
                   │
                   │ changed files
                   ▼
             Package Update
                   │
                   ▼
              Filesystem
                   │
                   ▼
             Updated Package
```

· · ·

## 5.4 Package Source Registry

```text
Installed Package
       │
       │ source registry
       ▼
Package Registry Information
       │
       ▼
Registry Module
       │
       │ request update
       ▼
Source Registry
       │
       │ changed files
       ▼
Package Update
```

---

# 6. Security

* **Source Registry Integrity**
	* The source registry used for a package update must be resolved from the package's own registry information.
	* The command must not silently replace the package's source registry with another registry.

* **Project Resolution**
	* Projects resolved from a general location must originate from the Tayku Client's initialized-project state.
	* The command must not implicitly select an unrelated project based only on filesystem proximity.

* **Filesystem Integrity**
	* Updates must be applied only to the resolved package or project.
	* The command must not modify unrelated filesystem resources.

* **Update Isolation**
	* A package update must operate only on the selected package.
	* A project update must operate only on the selected project.

* **Responsibility Isolation**
	* Registry communication remains the responsibility of the Registry module.
	* Package update operations remain the responsibility of the Package module.
	* Project update operations remain the responsibility of the Project module.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Update Command**
	* Defined the initial architecture of the "`update`" command.
	* Defined package updates inside Tayku projects.
	* Defined project updates from general filesystem locations.
	* Defined project and package updates from general filesystem locations.
	* Defined package source registry resolution.
	* Defined changed-file retrieval.
	* Defined initialized-project resolution.
	* Defined context-dependent interpretation of update identifiers.

---

# 8. ToDo List

* [ ] Define the exact Registry module interface used by "`update`".
* [ ] Define the exact Package module update interface.
* [ ] Define the exact Project module update interface.
* [ ] Define the exact initialized-project registry format.
* [ ] Define the exact package source registry field.
* [ ] Define the exact package version comparison mechanism.
* [ ] Define the exact changed-file calculation mechanism.
* [ ] Define the exact file transfer mechanism.
* [ ] Define whether unchanged files are ever downloaded.
* [ ] Define behavior when the package source registry has changed.
* [ ] Define behavior when a source registry no longer exists.
* [ ] Define behavior when a package is missing from its source registry.
* [ ] Define behavior when no update is available.
* [ ] Define partial-update behavior.
* [ ] Define rollback behavior after a failed update.
* [ ] Define the exact "`TAYKU-STATUS-CODE`" values.
* [ ] Define behavior when no initialized projects are available.
* [ ] Define behavior when multiple projects have the same identifier.
* [ ] Test package updates inside project context.
* [ ] Test project updates from general context.
* [ ] Test project and package updates from general context.
* [ ] Test updates for already installed packages.
* [ ] Test unreachable source registries.
* [ ] Test changed-file retrieval.
* [ ] Test failed file transfers.
