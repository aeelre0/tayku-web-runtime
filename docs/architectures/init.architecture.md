# Tayku Web Client Init Architecture

Generally, creating a project gets a lot times. The configurations, designing of layers, set up backend and frontend, etc...
Tayku Client Init Module provides a quick way of creating a project by your decisions and configuration.

___ 

# 1. Scope 
* **In-scope:**
    * This page describes the initialization architecture of a Tayku Project.
    * This page describes the preffered file structure of Tayku Web Client Init Module.
    * This page explains the configuration choices.
    * This page gives an idea about the working principle of Tayku Web Client.
    * This page provides some tricks for the way of saving your time.
    * This page provides the difference between `node_modules/` and `tayku_packages/`.
    * This page describes the project generation flow from configuration to a complete Tayku Web project.
    * This page describes the project configuration structure and its available options.
    * This page describes the template selection and application process.
    * This page describes the dependency resolution process.
    * This page describes the initialization commands and their execution.
    * This page describes the metadata and special files generated during initialization.
 
* **Out-of-scope:**
    * This page doesn't describe the installation process of packages.
    * This page doesn't describe the implementation details of individual packages.
    * This page doesn't describe the development workflow after project initialization.
    * This page doesn't describe deployment or production configuration.
    * This page doesn't describe the implementation details of backend or frontend features.
    * This page doesn't describe the configuration of external services beyond the initialization stage.

___ 

# 2. Architecture 

This section explains the logic, definitions, methods and other things that interests with architecture of this module.

## 2.1 Definitions

- **Project Structure:** The file and folder structre of your project.

- **Project Configuration:** The collection of settings that determines how a Tayku project is initialized and structured.

- **Project Template:** A predefined structure and configuration used as the starting point for a new Tayku project.

- **Module:** A Tayku Package, published by Tayku Web officially.

- **Client:** The frontend-side application of a Tayku Web project.

- **Backend:** The server-side part of a Tayku Web project responsible for handling application logic, data, and external communication.

- **Package:** A Tayku Package, published by users. 

- **Tayku Package:** A package managed or provided specifically by the Tayku ecosystem.

- **Initialization:** The process of creating and configuring a new Tayku project according to the user's selected options.

- **Initializer:** The component responsible for creating the project structure and applying the selected configuration during initialization.

- **Configuration Option:** A choice presented to the user that affects the structure, modules, or behavior of the initialized project.

- **Layer:** A logical separation of responsibilities within a project, such as the client, backend, data, or infrastructure layer.

## 2.2 Logic 

The Tayku Web Init Module is responsible for initializing Tayku Web entities according to a user-defined configuration. The module does not directly represent a specific type of entity; instead, it applies the initialization logic appropriate to the requested entity type.

The module can initialize different types of Tayku entities, such as projects and packages. The initialization process may differ between entity types, but the general principle remains the same: the user defines the desired structure and configuration, and the Init Module creates the corresponding entity.

For a project initialization, the module is responsible for creating the project structure, applying the selected templates and configurations, preparing the required dependencies, and generating the files and metadata required by the Tayku project.

For a package initialization, the module follows the same general principle while using a package-specific configuration. Package initialization may therefore have a different structure and set of configuration options from project initialization.

The Init Module is responsible for performing initialization according to the provided configuration. It does not define the internal implementation of the initialized entity. For example, it may prepare a frontend according to a selected framework and template, but it does not implement the frontend framework itself.

The module also does not assume that every entity has the same configuration. Configuration options are determined by the type of entity being initialized and may be extended as new Tayku entity types are introduced.

The initialization logic is therefore designed to be extensible. New initialization targets can be added without changing the fundamental purpose of the module: interpreting the appropriate configuration and constructing the requested Tayku entity accordingly.

___ 

# 3. Interface 
* **Inputs**
    * `config.json` : A config file that contains the user specific settings for a project or package (or etc..). The projects initializes by these file. If the user does not provide a valid configuration, the standard configuration is used.
    * `type` : The type of the entity that will be initialized.

* **Outputs**
    * `<directory-name>/` : The entity folder of the initialized entity. This directory covers some specific packages for Tayku Web Environment and user configurations. These files and folders were declared in the `## 3.2 Config Contract` section.

## 3.1 Dependencies 

* `nodejs` : Tayku Web Client uses `JavaScript` in its modules. Therefore we need to a runtime environment. Tayku Web uses `nodejs`.

* `npm` : The Nodejs Package Manager for installing the external dependencies of user.

* `twr` : The Tayku Web Registry for installing the packages user wanted.

* `config.json` : A configuration file that includes the settings about entity.

* `tayku-web/src/templates/` : The templates to make the user's job easier.

> [!NOTE] User Dependencies
> The dependencies listed above are the packages required by `init.js`. However, if the user specifies external dependencies (for example, `React`), the Tayku Web Initializer Module will install them.

## 3.2 Contract 

The Tayku Web Client Initializer is designed as a flexible structure due to the variable nature of the type parameter. New types may be added in the future. Therefore, this module follows an interface-based architecture.

Tayku Web Client has an `src/init.js` file that provides common utilities for specific initializer types. This is called the Main Branch of the initializer, while `src/init/init-<type>.js` files are called Secondary Branches.

A Secondary Branch manages type-specific initialization through the same interface defined below.

### 3.2.1 Main Branch Contract 

The Main Branch provides common low-level operations used by Secondary Branches during initialization.

```js
/**
 * Reads, validates, and parses a configuration file.
 *
 * @param {string} config_path Path of the configuration file.
 * @param {object} out Output parameter for the parsed configuration object.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function validate_config(config_path, out) {...}


/**
 * Creates a directory.
 *
 * @param {string} path Path of the directory to create.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function create_directory(path) {...}


/**
 * Creates a file.
 *
 * @param {string} path Path of the file to create.
 * @param {string} content Content to write to the file.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function create_file(path, content) {...}


/**
 * Resolves a dependency from its specified source and type.
 *
 * @param {object} dependency Dependency definition to resolve.
 * @param {object} out Output parameter for the resolved dependency.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function resolve_dependency(dependency, out) {...}
```

### 3.2.2 Secondary Branch Contract

Each Secondary Branch implements the initialization interface for a specific entity type. The implementation may differ between entity types.

```js
/**
 * Initializes an entity using the provided configuration.
 *
 * @param {object} config Parsed entity configuration.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function initialize(config) {...}

// External functions can make additions specific to the constructor's type.
```


## 3.3 Configuration Contract

The initialization configuration defines the structure, components, configuration, and dependencies of the entity to be initialized.

The configuration is provided as a JSON file and is parsed by the Main Branch before being passed to the corresponding Secondary Branch.

### 3.3.1 Project Configuration

A project configuration may contain the following fields:

```json
{
    "project-base": {
        "name": "...",
        "desc": "..."
    },

    "frontend": {
        "framework": {
            "src": "...",
            "language": "...",
            "commands": []
        },

        "template": "...",

        "config": {
            "color-palette": "...",
            "typography": "...",
            "spacing": "...",
            "radius": "...",
            "shadows": "...",
            "breakpoints": "...",
            "other": "..."
        },

        "needed": [
            {
                "source": "...",
                "type": "...",
                "language": "..."
            }
        ]
    },

    "backend": {
        "framework": {
            "src": "...",
            "language": "...",
            "commands": []
        },

        "template": "...",

        "needed": [
            {
                "source": "...",
                "type": "...",
                "language": "..."
            }
        ]
    },

    "needed": [
        {
            "source": "...",
            "type": "...",
            "language": "..."
        }
    ]
}
```

#### `project-base`

Defines the basic metadata and properties of the project.

* `name`: The name of the project. This value is used as the project directory name.
* `desc`: The description of the project.

#### `frontend`

Defines the frontend layer of the project.

* `framework`: Defines the framework and environment used by the frontend.

  * `src`: The source of the frontend framework.
  * `language`: The programming language used by the frontend.
  * `commands`: Commands to be executed during frontend initialization.

* `template`: Specifies a predefined template to be used for the frontend. If omitted, no predefined template is applied.

* `config`: Defines the configuration of the frontend. It may contain design, layout, styling, and other frontend-specific configuration options.

  * `color-palette`: Defines the color palette used by the frontend.
  * `typography`: Defines the typography configuration used by the frontend.
  * `spacing`: Defines the spacing scale used by the frontend.
  * `radius`: Defines the border-radius configuration used by the frontend.
  * `shadows`: Defines the shadow configuration used by the frontend.
  * `breakpoints`: Defines the responsive breakpoints used by the frontend.
  * Additional configuration fields may be supported as the frontend configuration system evolves.

* `needed`: Defines dependencies required by the frontend.

#### `backend`

Defines the backend layer of the project.

* `framework`: Defines the framework and environment used by the backend.

  * `src`: The source of the backend framework.
  * `language`: The programming language used by the backend.
  * `commands`: Commands to be executed during backend initialization.

* `template`: Specifies a predefined template to be used for the backend. If omitted, no predefined template is applied.

* `needed`: Defines dependencies required by the backend.

#### `needed`

Defines dependencies required by the project or one of its layers.

Each dependency is represented by an object containing:

* `source`: The source from which the dependency is resolved.
* `type`: The type of dependency.
* `language`: The programming language or language ecosystem associated with the dependency.

The `type` field determines how the dependency is resolved. Supported dependency types may include:

* `tayku-package`: A package provided by the Tayku ecosystem.
* `node-package`: A package provided through the Node.js package ecosystem.
* `source`: A dependency resolved directly from its source.

The `language` field allows the initializer to distinguish dependencies belonging to different language ecosystems. For example, a dependency may be associated with `javascript`, `typescript`, or another supported language.

> [!NOTE]
> Currently, JavaScript and TypeScript are supported as programming languages. Support for C and C++ is planned for future releases.

Dependencies defined at the project level are available to the project as a whole, while dependencies defined under `frontend.needed` or `backend.needed` are specific to their respective layers.

___ 

# 4. Data Flow

* **Initialization**

```text
User
   │
   │ config_path + entity_type
   ▼
init command
   │
   ▼
Secondary Branch
   │
   │ config_path
   ▼
validate_config()
   │
   ├──► Configuration File
   │       │yanlış yöne işaret ediyor
   │       ▼
   │   Parse JSON
   │       │
   │       ▼
   │   Configuration Object
   │
   └──────────────► config
                       │
                       ▼
                  initialize()
                       │
             ┌─────────┼─────────┐
             │         │         │
             ▼         ▼         ▼
      create_directory()  create_file()  resolve_dependency()
             │         │         │
             │         │         └──► Resolved Dependency
             │         │
             │         └──► Project File
             │
             └──► Project Directory
                       │
                       ▼
                 Initialized Entity
```

* **Dependency Resolution**

```text
Dependency Definition
   │
   │ source + type + language
   ▼
resolve_dependency()
   │
   ├──► Dependency Source
   │
   ├──► Dependency Type
   │
   └──► Dependency Language
             │
             ▼
       Resolved Dependency
             │
             ▼
        installer.js
```

___ 

# 5. Error Handling

> [!NOTE]
> This module depends on the TWR layer of the client. Therefore, it may propagate errors returned by the TWR layer. See [registry.architecture.md](registry.architecture.md).

## 5.1 TWR Errors

* `NOT FOUND`: The requested resource could not be found.

  * A required dependency of the Tayku Web Client Initializer could not be found.
  * A dependency specified in `config.json` under `needed` or `framework` points to a non-existent source.
  * **Solution:** Check the specified source and make sure that the required dependency is available.

* `NOT VALID`: The specified registry source is accessible, but its structure does not comply with the Tayku Web Registry specifications.

  * **Solution:** Fix the registry source according to the TWR specifications.

* `NOT SECURE`: A dependency or registry source does not satisfy the required security requirements.

  * **Solution:** Verify the dependency's integrity and security information, or use a trusted source.

## 5.2 Init Errors

* `INVALID CONFIG`: The provided `config.json` is invalid or does not comply with the configuration contract.

  * **Solution:** Validate the configuration against the Configuration Contract and fix the invalid fields.

* `ALREADY EXISTS`: A directory or file required for initialization already exists and cannot be safely overwritten.

  * **Solution:** Remove or rename the existing entity, or initialize the entity in another location.

* `PERMISSION DENIED`: The initializer does not have sufficient permissions to create, read, write, or modify a required file or directory.

  * **Solution:** Check the permissions of the target path and ensure that the current user can modify it.

* `IO ERROR`: An unexpected input/output error occurred while reading, writing, creating, or modifying a file or directory.

  * **Solution:** Check the filesystem and target path, then retry the initialization.

* `NOT ENOUGH SPACE`: There is not enough free disk space to complete the initialization process.

  * **Solution:** Free sufficient disk space and retry the initialization; at least `1 GiB` of free space is recommended.

___ 

# 6. Security

* **Configuration Security**

  * The configuration file may define directories, files, dependencies, framework sources, and commands to be executed during initialization. Therefore, users should only use configuration files from trusted sources.
  * The Initializer must validate configuration values before using them and must prevent paths from escaping the intended project directory.
  * **Solution:** Inspect the configuration file and its sources carefully before starting the initialization process.

* **Dependency Security**

  * The Initializer may resolve dependencies from external sources. Dependency resolution does not guarantee that the resolved dependency is completely secure.
  * The security and integrity of dependencies are handled according to the security mechanisms of the corresponding source, such as the Tayku Web Registry.
  * **Solution:** Use trusted dependency sources and verify the dependency information before proceeding.

* **Command Execution Security**

  * The configuration may specify commands to be executed during initialization. These commands may execute arbitrary operations on the user's system.
  * **Solution:** Inspect all configured commands before running the Initializer, especially when using configuration files obtained from external sources.

* **Filesystem Security**

  * The Initializer creates files and directories according to the provided configuration. Invalid or malicious paths may cause files to be created or modified outside the intended project directory if they are not properly validated.
  * **Solution:** The Initializer must normalize and validate target paths before performing filesystem operations.

* **Thread Safety**

  * The Tayku Web Client Initializer is designed to operate synchronously in this version. This avoids concurrent initialization operations and simplifies filesystem and dependency-resolution consistency.

___

# 7. Devlog

- v0.0.0 - 20 SEP 2026
    - Base of contract was designed.
    - Primitive Architecture was designed.
    - `config.json` file was bound by a contract.

___ 

# 8. TO-DO & Roadmap

* [x] Create a protocol for `config.json`.
* [x] Decide the architecture of folder structure and package managment.
* [x] Setup the primitive architecture. 
* [ ] Import the logic of the Main and Second Branches.
* [ ] Test 

___
