# Tayku Web Client Search Command Architecture

The "`search`" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for searching Tayku projects, packages, and registries.

The command determines the requested search type and search scope from the command arguments and invokes the appropriate underlying Tayku Web Client module to perform the search operation.

The "`search`" command acts as an abstraction and orchestration layer. It does not implement the search mechanisms itself.

The command supports three search types:

```sh
npx tayku-web search --project <NAME>
npx tayku-web search --package <NAME>
npx tayku-web search --registry <NAME>
```

The "`--local`" option can be used to restrict the search to local resources where supported:

```sh
npx tayku-web search --project --local <NAME>
npx tayku-web search --package --local <NAME>
npx tayku-web search --registry --local <NAME>
```

---

# 1. In-Scope
* **In-Scope**  
	* Determine the requested search type from the command arguments.
	* Determine whether the search is local or web-based.
	* Validate the search type and search scope.
	* Determine the requested search name.
	* Invoke the appropriate underlying Tayku Web Client module.
	* Search for projects locally or on the web.
	* Search for packages on the web or within the current project when "`--local`" is specified.
	* Search for registries locally or on the web.
	* Return the appropriate "`TAYKU-STATUS-CODE`".
	* Pass search results to the CLI layer.

* **Out-of-Scope**  
	* Parsing command-line arguments.
	* Implementing project search logic.
	* Implementing package search logic.
	* Implementing registry search logic.
	* Managing search indexes.
	* Managing remote registries.
	* Installing packages.
	* Initializing projects.
	* Modifying project configuration.
	* Modifying package configuration.
	* Modifying registry configuration.
	* Rendering search results.
	* Defining the contents of search results.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "`search`" command.

## 2.1 Logic

The "`search`" command acts as an abstraction layer between the CLI layer and the underlying Tayku Web Client modules responsible for searching Tayku resources.

The command determines the search type from the supplied command arguments.

The supported search types are:

* **Project**
	* Searches for Tayku projects.
	* Supports both local and web searches.

* **Package**
	* Searches for Tayku packages on the web.
	* When "`--local`" is specified, searches for the package within the current project.

* **Registry**
	* Searches for Tayku registries.
	* Supports both local and web searches.

The "`--local`" option changes the search scope.

Without "`--local`", the command performs a web search where the selected search type supports web searching.

With "`--local`", the command restricts the search to local resources.

For "`project`" searches, a local search searches projects available on the local system.

For "`package`" searches, a local search is project-specific.

The package local search requires the search to be performed from a location containing:

`tayku-package.json`

The package is searched within the project associated with that "`tayku-package.json`".

For "`registry`" searches, a local search searches registries available in the local Tayku Web Client registry configuration.

The "`search`" command does not implement the actual search mechanism.

Instead, it determines the appropriate search operation and invokes the corresponding underlying module.

· · ·

## 2.2 Definitions

* **Search Type:** The resource category selected through "`--project`", "`--package`", or "`--registry`".

* **Search Scope:** The location in which the search is performed. The supported scopes are local and web.

* **Local Search:** A search restricted to resources available on the local system.

* **Web Search:** A search performed against the appropriate Tayku web-based resource.

* **Project Search:** A search for Tayku projects matching the requested name.

* **Package Search:** A search for Tayku packages matching the requested name.

* **Registry Search:** A search for Tayku registries matching the requested name.

* **Project Context:** The current Tayku project identified by "`tayku-package.json`" when performing a local package search.

* **Search Target:** The name supplied by the user to identify the requested resource.

---

# 3. Interface

## 3.1 Inputs

The "`search`" command accepts a search type, an optional search scope, and a search target.

### Project

Web search:

```sh
npx tayku-web search --project <NAME>
```

Local search:

```sh
npx tayku-web search --project --local <NAME>
```

The "`--project`" search type supports both local and web searches.

### Package

Web search:

```sh
npx tayku-web search --package <NAME>
```

Local search:

```sh
npx tayku-web search --package --local <NAME>
```

A web package search searches for the package independently of the current project.

A local package search is performed within the current project.

The local package search requires a "`tayku-package.json`" file to identify the project context.

### Registry

Web search:

```sh
npx tayku-web search --registry <NAME>
```

Local search:

```sh
npx tayku-web search --registry --local <NAME>
```

The "`--registry`" search type supports both local and web searches.

· · ·

## 3.2 Outputs

The "`search`" command returns a "`TAYKU-STATUS-CODE`".

When matching resources are found successfully, the search results are passed to the CLI layer.

When no matching resources are found, an appropriate failure status code is returned.

When the search operation fails, the corresponding failure status code is returned.

The "`search`" command does not define the presentation format of search results.

· · ·

## 3.3 Dependencies

The "`search`" command depends on the following components:

* **CLI Parser**  
	* Parses the command-line arguments before they are passed to the "`search`" command.

* **Project Module**  
	* Provides the functionality required to search for projects.

* **Package Module**  
	* Provides the functionality required to search for packages.

* **Registry Module**  
	* Provides the functionality required to search for registries.

* **Filesystem**
	* Provides access to local project and registry resources.
	* `fs`

* **Runtime Environment of JS**
	* `nodejs`

The "`search`" command depends on the interfaces exposed by these modules rather than their internal implementations.

· · ·

## 3.4 Source Contract

The "`search`" command receives a search type, search scope, and search target from the CLI layer.

A conceptual interface is defined as follows:

```js
/**
 * Searches for a Tayku resource.
 *
 * Resolves the requested search type and scope and invokes
 * the corresponding Tayku Web Client module.
 *
 * @param {string} type The search type.
 * @param {boolean} local Whether the search is local.
 * @param {string} target The requested search target.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function search(type, local, target) {...}
```

The "`type`" parameter determines which resource type is searched.

The "`local`" parameter determines whether the search is restricted to local resources.

The "`target`" parameter identifies the resource being searched for.

The command does not implement the search mechanism itself.

Search results are provided through the command's output mechanism to the CLI layer, while the function return value communicates operation status.

· · ·

## 3.5 Command Contract

### Search for a Project

Web:

```sh
npx tayku-web search --project <NAME>
```

Searches for the requested project on the web.

Local:

```sh
npx tayku-web search --project --local <NAME>
```

Searches for the requested project on the local system.

### Search for a Package

Web:

```sh
npx tayku-web search --package <NAME>
```

Searches for the requested package on the web.

Local:

```sh
npx tayku-web search --package --local <NAME>
```

Searches for the requested package within the current project.

### Search for a Registry

Web:

```sh
npx tayku-web search --registry <NAME>
```

Searches for the requested registry on the web.

Local:

```sh
npx tayku-web search --registry --local <NAME>
```

Searches for the requested registry in the local registry configuration.

· · ·

## 3.6 Command Behavior

**What happens when "`--project`" is specified without "`--local`"?**

The command resolves a web project search.

The Project module is invoked with the requested project name.

The resulting search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when "`--project`" and "`--local`" are specified?**

The command resolves a local project search.

The Project module is invoked with the requested project name and local search scope.

The resulting local search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when "`--package`" is specified without "`--local`"?**

The command resolves a web package search.

The Package module is invoked with the requested package name.

The resulting search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when "`--package`" and "`--local`" are specified?**

The command resolves a local package search.

The command determines the current project context using "`tayku-package.json`".

The Package module is invoked with the requested package name and the current project context.

The package search is restricted to the current project.

The resulting search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when "`--package --local`" is used without a project context?**

If "`tayku-package.json`" cannot be resolved from the current location, the command cannot determine the project context required for the local package search.

The search operation is not performed.

An appropriate failure status code is returned.

· · ·

**What happens when "`--registry`" is specified without "`--local`"?**

The command resolves a web registry search.

The Registry module is invoked with the requested registry name.

The resulting search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when "`--registry`" and "`--local`" are specified?**

The command resolves a local registry search.

The Registry module is invoked with the requested registry name and local search scope.

The resulting local search results are passed to the CLI layer.

The operation returns the resulting "`TAYKU-STATUS-CODE`".

· · ·

**What happens when no matching resource is found?**

If the selected search operation completes successfully but no matching resource is found, the command does not produce a search result.

An appropriate "`NOT FOUND`" status code is returned.

· · ·

**What happens when an unsupported search type is provided?**

If the supplied search type is not supported, the search operation is not performed.

An appropriate "`INVALID ARGUMENT`" status code is returned.

· · ·

**What happens when an underlying module fails?**

If the module responsible for the selected search operation reports a failure, the "`search`" command propagates the resulting "`TAYKU-STATUS-CODE`" to the CLI layer.

The command does not reproduce or replace the functionality of the underlying module.

---

# 4. Error Handling

The "`search`" command may return errors generated by the CLI argument layer, project context resolution, or the underlying search modules.

* **Invalid Arguments**  
	* "`INVALID ARGUMENT`": The provided search type, search scope, or search target is invalid.

	* **Solution**: Use one of the supported forms:

```sh
npx tayku-web search --project <NAME>
npx tayku-web search --project --local <NAME>
npx tayku-web search --package <NAME>
npx tayku-web search --package --local <NAME>
npx tayku-web search --registry <NAME>
npx tayku-web search --registry --local <NAME>
```

* **Resource Not Found**  
	* "`NOT FOUND`": No matching project, package, or registry could be found.

	* **Solution**: Check the search target and search scope.

* **Project Context Not Found**  
	* "`PROJECT NOT FOUND`": A local package search was requested, but the current project context could not be resolved.

	* **Solution**: Execute the command from a location containing "`tayku-package.json`".

* **Permission Error**  
	* "`PERMISSION DENIED`": The requested local resource cannot be accessed because of insufficient permissions.

	* **Solution**: Check the permissions of the relevant files and directories.

* **Filesystem Error**  
	* "`IO ERROR`": An unexpected input/output error occurred while accessing local search resources.

	* **Solution**: Check the filesystem and retry the operation.

* **Registry Error**  
	* "`REGISTRY ERROR`": An error occurred while accessing the local or web registry search mechanism.

	* **Solution**: Check the registry configuration and retry the operation.

Errors generated by underlying modules are propagated to the CLI layer through the command's "`TAYKU-STATUS-CODE`".

---

# 5. Data Flow

## 5.1 Project Search

```text
User
   │
   │ search --project [--local] <NAME>
   ▼
CLI
   │
   │ parsed type + scope + target
   ▼
Search Command
   │
   ├──► Web
   │      │
   │      ▼
   │   Project Module
   │
   └──► Local
          │
          ▼
      Project Module
          │
          ▼
     Search Results
          │
          ▼
         CLI
```

· · ·

## 5.2 Package Search

```text
User
   │
   │ search --package [--local] <NAME>
   ▼
CLI
   │
   │ parsed type + scope + target
   ▼
Search Command
   │
   ├──► Web
   │      │
   │      ▼
   │   Package Module
   │
   └──► Local
          │
          ▼
   tayku-package.json
          │
          ▼
    Current Project
          │
          ▼
    Package Module
          │
          ▼
     Search Results
          │
          ▼
         CLI
```

· · ·

## 5.3 Registry Search

```text
User
   │
   │ search --registry [--local] <NAME>
   ▼
CLI
   │
   │ parsed type + scope + target
   ▼
Search Command
   │
   ├──► Web
   │      │
   │      ▼
   │   Registry Module
   │
   └──► Local
          │
          ▼
   Local Registry Configuration
          │
          ▼
      Registry Module
          │
          ▼
     Search Results
          │
          ▼
         CLI
```

---

# 6. Security

* **Argument Validation**
	* The search type must be validated before an operation is selected.
	* The search scope must be validated against the capabilities of the selected search type.
	* User-provided search targets must not be interpreted as arbitrary commands or options.

* **Local Path Security**
	* Local package and project searches must resolve resources according to their respective module contracts.
	* User-provided search targets must not allow unintended filesystem access.
	* Local package search must remain restricted to the current project context.

* **Registry Security**
	* Local registry searches must operate only on the registry configuration managed by the Tayku Web Client.
	* A registry search target must not be interpreted as an arbitrary filesystem path.

* **Web Search Security**
	* Web search requests must be handled by the responsible underlying module.
	* The "`search`" command must not directly execute data returned from web search operations.

* **Responsibility Isolation**
	* The "`search`" command is responsible only for selecting the search type and scope and invoking the appropriate search operation.
	* Search implementation and resource-specific behavior remain the responsibility of the corresponding modules.

* **Thread Safety**
	* The Tayku Web Client "`search`" command operates synchronously in this version.
	* The command does not perform concurrent search operations itself.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Search Command**
	* Defined the initial architecture of the "`search`" command.
	* Defined project search.
	* Defined package search.
	* Defined registry search.
	* Defined local and web search scopes.
	* Defined local package search within the current project.
	* Defined the "`--local`" command option.
	* Defined the interface between the "`search`" command and the underlying search modules.
	* Established the "`search`" command as an abstraction and orchestration layer.

---

# 8. ToDo List

* [ ] Define the exact Project module search interface.
* [ ] Define the exact Package module search interface.
* [ ] Define the exact Registry module search interface.
* [ ] Define the exact parsed argument structure received by "`commands/search.js`".
* [ ] Define the exact search result structure.
* [ ] Define the exact output interface between "`commands/search.js`" and the CLI layer.
* [ ] Define the exact "`TAYKU-STATUS-CODE`" values used by each search operation.
* [ ] Define the exact web search mechanism.
* [ ] Define the exact local project search mechanism.
* [ ] Define the exact local registry search mechanism.
* [ ] Define the exact project context resolution mechanism for local package searches.
* [ ] Test project web search.
* [ ] Test project local search.
* [ ] Test package web search.
* [ ] Test package local search.
* [ ] Test registry web search.
* [ ] Test registry local search.
* [ ] Test missing project context.
* [ ] Test missing search results.
* [ ] Test invalid search types.
* [ ] Test invalid search scopes.