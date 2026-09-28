# Tayku Web Client Init Command Architecture

The `init` command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for initializing different types of Tayku entities.

The command acts as an abstraction layer between the CLI layer and the corresponding Secondary Branch of the Init Module.

---

# 1. In-Scope
* **In-Scope**
  * Determine the requested initialization type from the parsed command-line arguments.
  * Select the appropriate Secondary Branch for the requested type.
  * Resolve the Secondary Branch using the Init Module branch mapping.
  * Determine the configuration file path to use.
  * Use the default configuration when no configuration file is explicitly provided.
  * Pass the selected configuration path to the corresponding Secondary Branch.
  * Return the status code returned by the Secondary Branch to the CLI layer.
  * Define the abstraction interface between the CLI layer and the Init Module Secondary Branches.
  * Support extensibility for new initialization types.

* **Out-of-Scope**
  * Parsing command-line arguments.
  * Parsing configuration files.
  * Validating configuration contents.
  * Defining entity-specific configuration structures.
  * Creating files or directories.
  * Resolving dependencies.
  * Downloading dependencies.
  * Installing packages.
  * Applying templates.
  * Executing entity-specific initialization logic.
  * Implementing the Main Branch or Secondary Branch logic.
  * Managing the internal structure of initialized entities.

---

# 2. Architecture

This section examines the architecture and responsibilities of the `init` command.

## 2.1 Logic

The `init` command acts as an abstraction layer between the CLI layer and the Secondary Branches of the Init Module.

The command receives already-parsed arguments from the CLI layer. It determines the requested entity type and resolves the corresponding Secondary Branch through the Init Module branch mapping.

The branch mapping is defined in `src/init/init.json`. Each supported initialization type is associated with its corresponding Secondary Branch.

For example:

```json
{
    "project": "./project-init.js",
    "package": "./package-init.js",
    "developer": "./developer-init.js"
}
```

The command then passes the selected configuration path to the resolved Secondary Branch.

The Secondary Branch is responsible for calling the Main Branch and performing the actual initialization process according to the entity type.

The `init` command does not interpret the contents of the configuration file. Configuration parsing and validation are responsibilities of the Main Branch.

The initialization process creates files and directories, resolves dependencies, downloads required resources, applys templates, and performs other operations required by the selected entity type. These operations are performed by the Init Module and its corresponding Secondary Branch, not by the `init` command.

> [!Note] Initializer Architecture
> If you want to inspect the initializer architecture detailed, you can look at `tayku-web/docs/init.architecture.md` file.

· · ·

## 2.2 Definitions

* **Initialization Type:** The type of Tayku entity to be initialized, such as `project`, `package`, or `developer`.

* **Secondary Branch:** A type-specific initializer responsible for initializing a particular Tayku entity type.

* **Main Branch:** The common initialization layer located at `src/init.js`. It provides shared low-level initialization primitives used by Secondary Branches.

* **Branch Mapping:** The mapping defined in `src/init/init.json` that associates an initialization type with its corresponding Secondary Branch.

* **Configuration Path:** The path of the configuration file provided to the selected Secondary Branch.

* **Default Configuration:** The configuration file provided by the Tayku Web Client when the user does not explicitly specify a configuration file.

* **Initializer:** The Secondary Branch responsible for initializing the requested entity type.

---

# 3. Interface

## 3.1 Inputs

The `init` command accepts the following command-line arguments:

* `--project`

  * Selects the project initialization type.

* `--package`

  * Selects the package initialization type.

* `--developer`

  * Selects the developer initialization type.

* `-c <CONFIG>`

  * Specifies the configuration file to use during initialization.

* `--config <CONFIG>`

  * Specifies the configuration file to use during initialization.

The `-c` and `--config` options are equivalent.

If no configuration file is explicitly provided, the command uses the default configuration associated with the selected initialization type.

The currently supported default configuration files are:

```text
tayku-web/config/project-config.json
tayku-web/config/package-config.json
tayku-web/config/developer-config.json
```

Additional initialization types may be introduced in future versions.

· · ·

## 3.2 Outputs

The `init` command returns a `TAYKU-STATUS-CODE`.

The status code is returned by the selected Secondary Branch and propagated to the CLI layer without modification.

The initialized entity is created by the corresponding Secondary Branch and the Main Branch.

· · ·

## 3.3 Dependencies

The `init` command depends on the following components:

* **CLI Parser**

  * Parses the command-line arguments before they are passed to the `init` command.

* **Init Branch Mapping**

  * `src/init/init.json`
  * Maps initialization types to their corresponding Secondary Branches.

* **Init Secondary Branches**

  * Provides type-specific initialization logic.
  * Calls the Main Branch when common initialization primitives are required.

The command does not directly depend on the internal implementation of the Main Branch or the initialization logic of individual entity types.

· · ·

## 3.4 Source Contract

The `init` command receives parsed command-line arguments from the CLI layer.

The command resolves the appropriate Secondary Branch using the initialization type and passes the selected configuration path to that branch.

The command does not parse or validate the configuration file.

A conceptual interface is defined as follows:

```js
/**
 * Initializes the requested Tayku entity type.
 *
 * The initialization type is resolved through the Init Module
 * branch mapping before the corresponding Secondary Branch
 * is called.
 *
 * @param {string} type The initialization type.
 * @param {string|null} config_path Path of the configuration file,
 * or null to use the default configuration.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function init(type, config_path) {...}
```

· · ·

## 3.5 Command Contract

### Initialize Project

```bash
npx tayku-web init --project
```

Initializes a Tayku project using the default project configuration.

```bash
npx tayku-web init --project -c <CONFIG>
```

Initializes a Tayku project using the specified configuration file.

```bash
npx tayku-web init --project --config <CONFIG>
```

Initializes a Tayku project using the specified configuration file.

### Initialize Package

```bash
npx tayku-web init --package
```

Initializes a Tayku package using the default package configuration.

```bash
npx tayku-web init --package -c <CONFIG>
```

Initializes a Tayku package using the specified configuration file.

### Initialize Developer

```bash
npx tayku-web init --developer
```

Initializes a developer entity using the default developer configuration.

```bash
npx tayku-web init --developer -c <CONFIG>
```

Initializes a developer entity using the specified configuration file.

### Argument Rules

```text
--project
--package
--developer

--project + -c
--package + -c
--developer + -c
```

The `--config` form is equivalent to `-c`.

Only one initialization type may be selected in a single invocation.

An invocation containing no initialization type is invalid.

An invocation containing multiple initialization types is invalid.

· · ·

## 3.6 Command Behavior

### What happens if no configuration file is provided?

If the user does not provide `-c` or `--config`, the command selects the default configuration associated with the requested initialization type.

For example:

```text
--project   → project-config.json
--package   → package-config.json
--developer → developer-config.json
```

The default configuration is then passed to the corresponding Secondary Branch.

· · ·

### What happens if a custom configuration file is provided?

If the user provides `-c` or `--config`, the specified configuration path is passed to the corresponding Secondary Branch.

The `init` command does not inspect or modify the contents of the configuration file.

· · ·

### What happens if the requested initialization type is not registered?

If the requested initialization type does not have an entry in `src/init/init.json`, the command cannot resolve a Secondary Branch for the requested type.

The initialization operation is not started and an appropriate status code is returned.

· · ·

### What happens if a new initialization type is added?

A new initialization type can be introduced by adding the corresponding Secondary Branch and registering it in `src/init/init.json`.

For example:

```json
{
    "project": "./project-init.js",
    "package": "./package-init.js",
    "developer": "./developer-init.js",
    "service": "./service-init.js"
}
```

The command architecture does not require a new initialization implementation inside `commands/init.js` for each new type.

· · ·

### What happens if the configuration file does not exist?

The command passes the specified configuration path to the Secondary Branch.

Configuration file existence, readability, parsing, and validation are handled by the Main Branch.

If the configuration cannot be processed, the resulting status code is propagated back through the Secondary Branch and the `init` command.

· · ·

### What happens if initialization fails?

The `init` command does not handle the internal initialization failure itself.

The status code returned by the Secondary Branch is propagated to the CLI layer.

The specific failure is handled by the responsible Init Module component.

---

# 4. Error Handling

The `init` command may return errors generated by the CLI argument layer or by the selected Init Module Secondary Branch.

* `INVALID ARGUMENT`: The provided arguments do not form a valid `init` command invocation.

  * **Solution:** Check the command syntax and provide exactly one initialization type with an optional configuration path.

* `NOT FOUND`: The requested initialization type could not be resolved through the Init Module branch mapping.

  * **Solution:** Check `src/init/init.json` and make sure that the requested initialization type is registered.

* `INVALID CONFIG`: The specified configuration file is invalid or cannot be processed by the Main Branch.

  * **Solution:** Check the configuration file and make sure that it complies with the contract defined by the corresponding entity type.

* `ALREADY EXISTS`: A required file, directory, or entity already exists and cannot be safely created.

  * **Solution:** Remove or rename the existing entity, or initialize the entity in another location.

* `PERMISSION DENIED`: The initializer does not have sufficient permissions to perform a required operation.

  * **Solution:** Check the permissions of the target path and retry the initialization.

* `IO ERROR`: An unexpected input/output error occurred during initialization.

  * **Solution:** Check the filesystem and target path, then retry the initialization.

* `NOT ENOUGH SPACE`: There is not enough free disk space to complete the initialization process.

  * **Solution:** Free sufficient disk space and retry the initialization.

Errors returned by the Secondary Branch are propagated to the CLI layer without being reimplemented by the `init` command.

---

# 5. Data Flow

## 5.1 Initialization

```text
User
   │
   │ initialization type + optional config path
   ▼
CLI
   │
   │ parsed arguments
   ▼
Init Command
   │
   ├──► Determine Initialization Type
   │
   ├──► Determine Configuration Path
   │
   │
   ▼
src/init/init.json
   │
   │ type → initializer
   ▼
<type>-init.js
   │
   │ config_path
   ▼
src/init.js
   │
   │ validate_config()
   ▼
Configuration Object
   │
   ▼
Type-Specific Initialization
   │
   ├──► Create Files / Directories
   ├──► Resolve Dependencies
   ├──► Apply Templates
   ├──► Execute Initialization Commands
   └──► Other Type-Specific Operations
   │
   ▼
Initialized Entity
   │
   │ TAYKU-STATUS-CODE
   ▼
Init Command
   │
   ▼
CLI
```

· · ·

## 5.2 Secondary Branch Resolution

```text
Parsed Arguments
       │
       │ type
       ▼
Init Command
       │
       ▼
src/init/init.json
       │
       ├── project ─────► project-init.js
       │
       ├── package ─────► package-init.js
       │
       └── developer ──► developer-init.js
                              │
                              ▼
                       src/init.js
```

· · ·

## 5.3 Configuration Selection

```text
                Initialization Type
                        │
                        ▼
                 Configuration?
                   /         \
                 yes          no
                  │            │
                  ▼            ▼
          User-provided     Default Config
             Config              │
                  │              │
                  └──────┬───────┘
                         ▼
                  <type>-init.js
                         │
                         ▼
                    src/init.js
```

---

# 6. Security

* **Configuration Security**

  * Configuration files defines files, directories, dependencies, framework sources, and commands to be executed during initialization.
  * The `init` command does not inspect the contents of configuration files.
  * Configuration validation and security-sensitive handling are performed by the corresponding Init Module components.
  * Users should only use configuration files from trusted sources.

* **Path Security**

  * The initialization process creates or modifies files and directories according to the selected configuration.
  * Path validation must be performed by the responsible Init Module components before filesystem operations are executed.
  * Initialization must prevent configured paths from escaping the intended target directory.

* **Command Execution Security**

  * Entity-specific configuration may define commands to be executed during initialization.
  * These commands may perform arbitrary operations on the user's system.
  * Users should inspect configuration files and their configured commands before initialization.

* **Dependency Security**

  * Initialization resolves and obtains dependencies from external sources.
  * Dependency resolution does not by itself guarantee that a dependency is free from malicious or harmful code.
  * Dependency security is handled by the corresponding dependency and registry mechanisms.

* **Responsibility Isolation**

  * The `init` command must not implement its own configuration parsing, dependency resolution, filesystem operations, or security mechanisms.
  * Security-sensitive operations remain within their respective Init Module components.

* **Thread Safety**

  * The Tayku Web Client `init` command operates synchronously in this version.
  * The command does not perform concurrent dispatch of multiple initialization branches.

---

# 7. Revision History

## v0.0.1 — 2026-09-21

**Author:** Ali Emre Arlı

### Init Command

* Defined the initial architecture of the `init` command.
* Defined project, package, and developer initialization types.
* Established the Secondary Branch resolution mechanism through `src/init/init.json`.
* Defined default configuration selection.
* Defined the interface between the CLI layer and the Init Module Secondary Branches.
* Established the responsibility boundary between the `init` command and the Init Module.

---

> *The command chooses the road. The initializer does the walking.*

---

# 8. ToDo List

* [ ] Define the exact parsed argument structure received by the `init` command.
* [ ] Define the exact interface between `commands/init.js` and Secondary Branches.
* [ ] Define the exact structure and validation rules of `src/init/init.json`.
* [ ] Define behavior when a registered Secondary Branch cannot be loaded.
* [ ] Define behavior for missing default configuration files.
* [ ] Define the complete list of supported initialization types.
* [ ] Define CLI help and documentation behavior for dynamically registered initialization types.
* [ ] Define initialization cancellation behavior.
* [ ] Define initialization rollback behavior after partial failure.
* [ ] Test dynamic Secondary Branch resolution.
* [ ] Test default and user-provided configuration selection.
