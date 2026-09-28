# Tayku Web Client Add Command Architecture

The `add` command is one of the commands available in the Tayku Web Client. This command manages the addition of packages and registry sources to the Tayku Web environment.

___ 

# 1. Scope
* **In-Scope**
  * Determine the requested operation from the parsed arguments provided to the `add` command.
  * Call the appropriate Registry Module interface based on the requested operation.
  * Pass the required arguments to the Registry Module.
  * Return the status code returned by the Registry Module to the CLI layer.
  * Define the abstraction interface between the CLI layer and the Registry Module.

* **Out-of-Scope**
  * Parsing command-line arguments.
  * Searching registry sources.
  * Resolving packages.
  * Resolving package dependencies.
  * Adding registry sources.
  * Verifying package signatures.
  * Downloading packages.
  * Installing packages.
  * Managing package installation state.
  * Implementing registry or package management logic.

---

# 2. Architecture

This section examines the architecture of this command in a detailed way.

## 2.1 Logic

The `add` command acts as an abstraction layer between the CLI layer and the Registry Module.

The command determines the requested operation from the provided parsed arguments and calls the corresponding Registry Module interface.

1. **Package Addition**

   * `--package <PACKAGE>` requests the Registry Module to search all configured registry sources for the specified package.
   * `--package <PACKAGE> --registry <REGISTRY>` requests the Registry Module to search only the specified registry source.

2. **Registry Addition**

   * `--registry <REGISTRY>` requests the Registry Module to add the specified registry source to the configured registry sources.

The `add` command does not perform any package or registry operation itself. It only selects and calls the appropriate Registry Module interface, passes the required arguments, and returns the resulting status code to the CLI layer.

The internal processing of the request, including package resolution, dependency resolution, package verification, package downloading, and package installation, is outside the responsibility of the `add` command.

· · ·

## 2.2 Definitions

* **Package:** A Tayku package that can be resolved and installed by the Tayku Web Client.

* **Registry Source:** A source used by the Registry Module to search for and resolve packages.

* **Configured Registry:** A registry source currently available to the Tayku Web Client.

* **Package Addition:** The process of requesting a package through the Registry Module and passing the resolved package through verification and installation.

* **Registry Addition:** The process of adding a registry source to the client's configured registry sources.

* **Package Identifier:** The identifier used to identify a package.

* **Registry Identifier:** The identifier used to identify a registry source.

---

# 3. Interface

## 3.1 Inputs

The `add` command accepts the following command-line arguments:

* `--package <PACKAGE>`

  * Specifies the package to be added.

* `--registry <REGISTRY>`

  * Specifies the registry source to search or add.

At least one of `--package` or `--registry` must be provided.

· · ·

## 3.2 Outputs

The `add` command returns a `TAYKU-STATUS-CODE` indicating whether the requested operation was completed successfully.

For package addition, the resulting package is installed by the Installer Module.

For registry addition, the specified registry source is added to the client's configured registry sources.

· · ·

## 3.3 Dependencies

The `add` command depends on the following Tayku Web Client modules:

* **Registry Module**

  * Searches registry sources.
  * Resolves requested packages.
  * Resolves package dependencies.
  * Validates registry sources.

* **Verifier Module**

  * Verifies the integrity and authenticity of resolved packages.

* **Installer Module**

  * Installs resolved and verified packages.

The command also depends on the client's registry configuration for managing configured registry sources.

· · ·

## 3.4 Source Contract

```js 
/**
 * Adds a package using the specified registry source or,
 * if no registry is specified, all configured registry sources.
 *
 * @param {string} package_name The identifier of the package to add.
 * @param {string|null} registry_id The registry source to use,
 * or null to search all configured registry sources.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function add_package(package_name, registry_id) {...}

/**
 * Adds a registry source to the configured registry sources.
 *
 * The Registry Module validates the specified registry source
 * before adding it to the client's registry configuration.
 *
 * @param {string} registry_url The URL of the registry source.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function add_registry(registry_url) {...}
```

· · ·

## 3.5 Command Contract

### Add Package

```bash
npx tayku-web add --package <PACKAGE>
```

Searches all configured registry sources for the specified package.

```bash
npx tayku-web add --package <PACKAGE> --registry <REGISTRY>
```

Searches only the specified registry source for the requested package.

### Add Registry

```bash
npx tayku-web add --registry <REGISTRY>
```

Adds the specified registry source to the client's configured registry sources.

### Argument Rules

```text
--package
--package + --registry
--registry
```

These are the only valid argument combinations defined by the current command contract.

An invocation containing neither `--package` nor `--registry` is invalid.

· · ·

## 3.6 Command Behavior

### What happens if multiple packages with the same identifier are found?

If multiple matching packages are found across the searched registry sources, the user is prompted to select the package to add.

The selected package is then passed to the Registry Module for the remaining operations.

· · ·

### What happens if the package is already installed?

If the requested package is already installed, the user is prompted to confirm whether the package should be installed again.

If the user declines, the operation is cancelled.

· · ·

### What happens if the package fails security verification?

If the requested package fails the required security verification, the user is informed of the security failure and prompted to confirm whether the operation should continue.

If the user declines, the operation is cancelled.

· · ·

### What happens if the registry source has already been added?

If the specified registry source is already present in the configured registry sources, the operation is skipped.

The existing registry source remains unchanged.

· · ·

### What happens if the registry source is invalid?

If the specified registry source does not comply with the Tayku Web Registry specifications, the Registry Module returns an appropriate error code.

The registry source is not added.

· · ·

### What happens if the registry source fails security validation?

If the specified registry source fails the required security validation, the user is informed of the security failure and prompted to confirm whether the registry source should still be added.

If the user declines, the operation is cancelled.

---

# 4. Error Handling

The `add` command may propagate errors returned by the Registry, Verifier, and Installer Modules.

* `INVALID ARGUMENT`: The provided arguments do not match a valid `add` command invocation.

  * **Solution:** Check the command syntax and provide a valid combination of `--package` and `--registry`.

* `NOT FOUND`: The requested package or registry source could not be found.

  * **Solution:** Check the package or registry identifier and make sure the requested resource exists.

* `NOT VALID`: The specified registry source does not comply with the required registry structure.

  * **Solution:** Verify the registry source and make sure it complies with the Tayku Web Registry specifications.

* `NOT SECURE`: The requested package failed the required security verification.

  * **Solution:** Do not install the package and obtain a valid package from a trusted source.

* `ALREADY EXISTS`: The package or registry source already exists in the target environment.

  * **Solution:** Use the existing package or registry source, or remove it before adding it again.

* `PERMISSION DENIED`: The client does not have sufficient permissions to modify the required files or directories.

  * **Solution:** Check the permissions of the target path and retry the operation.

* `IO ERROR`: An unexpected input/output error occurred while modifying the required files.

  * **Solution:** Check the filesystem and target path, then retry the operation.

---

# 5. Data Flow

## 5.1 Package Addition

```text
User
   │
   │ package + optional registry
   ▼
CLI
   │
   │ parsed arguments
   ▼
Add Command
   │
   ├── package only ───────────────► Registry Module
   │                                  │
   │                                  ▼
   │                            Search Registries
   │                                  │
   │                                  ▼
   │                            Resolved Package
   │
   └── package + registry ─────────► Registry Module
                                      │
                                      ▼
                               Search Registry
                                      │
                                      ▼
                                Resolved Package
                                      │
                                      ▼
                                   Verifier
                                      │
                                      ▼
                                  Installer
                                      │
                                      ▼
                              Installed Package
```

· · ·

## 5.2 Registry Addition

```text
User
   │
   │ registry
   ▼
CLI
   │
   │ parsed arguments
   ▼
Add Command
   │
   ▼
Registry Module
   │
   ├──► Validate Registry Source
   │
   ▼
Configured Registry Sources
```

---

# 6. Security

* **Package Integrity**

  * Packages are verified against their signatures before installation.
  * Successful verification indicates that the package contents have not been modified after being signed by the developer.
  * This mechanism helps protect against unauthorized modification, including potential man-in-the-middle attacks.
  * Signature verification does not guarantee that a package is free from malicious or harmful code.

* **User Choice**

  * If a package fails security verification, the user is informed of the failure and may decide whether to continue.
  * The `add` command must not make a security judgment about the package beyond the results provided by the Verifier Module.

* **Official Tayku Modules**

  * Tayku guarantees the security of official Tayku Modules.
  * No equivalent security guarantee is provided for third-party packages.

* **Responsibility Isolation**

  * The `add` command must not implement its own package verification or security mechanisms.
  * Security-sensitive operations remain within their respective modules.

---

# 7. Revision History

## v0.0.1 — 2026-09-21

**Author:** Ali Emre Arlı

### Add Command

* Defined the initial architecture of the `add` command.
* Defined package addition through all configured registries.
* Defined package addition through a specific registry.
* Defined registry source addition.
* Established the initial command contract.
* Defined the command's interaction with the Registry, Verifier, and Installer Modules.

---

# 8. ToDo List

* [ ] Define the exact registry source configuration format.
* [ ] Define registry source validation behavior.
* [ ] Define package identifier format.
* [ ] Define duplicate package handling.
* [ ] Define duplicate registry source handling.
* [ ] Define package resolution behavior when multiple registries contain the same package.
* [ ] Define package verification failure behavior.
* [ ] Define partial installation behavior.
* [ ] Define the exact interface between the Add Command and the Registry Module.
* [ ] Define the exact interface between the Add Command and the Installer Module.

