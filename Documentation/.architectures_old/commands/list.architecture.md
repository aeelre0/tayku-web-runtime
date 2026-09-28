# Tayku Web Client List Command Architecture

---

# 1. Scope

The List Command provides a read-only interface for listing packages and registry sources available to the Tayku Web Client.

The command does not modify packages, registry configuration, or project state.

The List Command is responsible only for determining what should be listed from the parsed command arguments and forwarding the request to the appropriate module.

---

# 2. Architecture

## 2.1 Logic

The List Command acts as a thin abstraction layer between the CLI parser and the modules responsible for providing package and registry information.

The command determines the requested listing operation from the parsed arguments and calls the appropriate module interface.

The List Command MUST NOT implement package or registry discovery itself.

The supported listing operations are:

```text
list --package
    → list packages from all configured registry sources

list --package --available
    → list packages currently available to the project

list --package --registry <REGISTRY>
    → list packages available from the specified registry source

list --registry --available
    → list configured registry sources
```

The `--available` option refers to resources currently available to the project or client, depending on the selected target.

When `--package` is used without `--available` or `--registry`, the command MUST list packages from all configured registry sources.

The `--registry` option used with `--package` selects a specific registry source.

The `--available` and `--registry` options therefore represent different listing scopes and MUST NOT be treated as interchangeable.

· · ·

## 2.2 Definitions

### Package

A Tayku package that can be listed either from configured registry sources or from the packages currently available to the project.

### Registry Source

A registry source configured in the Tayku Web Client.

### Available Package

A package that has been downloaded and is currently available to the project.

### Available Registry

A registry source currently configured in the Tayku Web Client.

---

# 3. Interface

## 3.1 Inputs

The List Command receives parsed CLI arguments.

Relevant arguments are:

```text
--package
--registry
--available
```

The following aliases are supported:

```text
-a → --available
-r → --registry
```

A registry identifier is required when `--registry` is used as a package listing scope.

· · ·

## 3.2 Outputs

The List Command returns the status code provided by the module responsible for the requested listing operation.

The command does not transform the returned listing data unless required by the CLI output layer.

The function return value MUST be a `TAYKU_STATUS_CODE`.

· · ·

## 3.3 Dependencies

The List Command directly depends on the module interfaces required to retrieve the requested information.

The List Command MUST NOT directly depend on lower-level package discovery, registry resolution, verification, or installation logic.

The exact module responsible for each listing operation is defined by the corresponding module architecture.

· · ·

## 3.4 Source Contract

The List Command source interface is responsible for dispatching a parsed listing request.

```js
/**
 * Lists the requested packages or registry sources.
 *
 * The operation is determined from the parsed command arguments.
 *
 * @param {object} args Parsed command arguments.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function list(args) {...}
```

The `list()` function MUST:

1. Determine the requested listing target.
2. Determine the requested listing scope.
3. Call the appropriate module interface.
4. Return the resulting `TAYKU_STATUS_CODE`.

The `list()` function MUST NOT:

* resolve registry sources itself,
* discover packages itself,
* modify package state,
* modify registry configuration,
* install or download packages,
* verify package signatures,
* implement registry communication logic.

· · ·

## 3.5 Command Contract

### 3.5.1 Package Listing

```bash
npx tayku-web list --package
```

Lists packages available from all configured registry sources.

```bash
npx tayku-web list --package --available
```

Lists packages currently available to the project.

```bash
npx tayku-web list --package --registry <REGISTRY>
```

Lists packages available from the specified registry source.

The `<REGISTRY>` value identifies the registry source to query.

### 3.5.2 Registry Listing

```bash
npx tayku-web list --registry --available
```

Lists registry sources currently configured in the Tayku Web Client.

· · ·

## 3.6 Command Behavior

### 3.6.1 Default Package Listing

When `--package` is provided without `--available` or a specific `--registry`, the command MUST query all configured registry sources.

The command MUST NOT require the user to specify a registry in this case.

### 3.6.2 Available Package Listing
 
When `--package --available` is provided, the command MUST list packages currently available to the project.

Only packages currently available to the project are included in the result.

Packages that are not available to the project MUST NOT be included.

### 3.6.3 Registry Package Listing

When `--package --registry <REGISTRY>` is provided, the command MUST list packages available from the specified registry source.

The command MUST NOT list packages that are not provided by the selected registry source.

### 3.6.4 Registry Listing

When `--registry --available` is provided, the command MUST list the registry sources currently configured in the Tayku Web Client.

The operation is read-only.

· · ·

## 3.7 Argument Validation

The command MUST reject invalid or incomplete argument combinations.

At minimum, the following combinations are invalid:

```text
--registry
```

without a valid registry-listing scope.

```text
--package --registry
```

without a registry identifier.

Invalid argument combinations MUST return the appropriate `TAYKU_STATUS_CODE`.

The List Command MUST NOT silently reinterpret an invalid argument combination as another operation.

___

# 4. Error Handling

Errors generated while retrieving packages or registry sources MUST be propagated from the responsible module.

The List Command MUST NOT replace module-specific errors with unrelated errors.

If no matching packages or registry sources exist, the responsible module SHOULD return an empty result rather than treating the absence of entries as an error.

---

# 5. Data Flow

## 5.1 Package Listing

```text
CLI Parser
    │
    │ parsed arguments
    ▼
list.js
    │
    ├── --package
    │      │
    │      ├── --available
    │      │      ▼
    │      │   Project Packages
    │      │
    │      ├── --registry <REGISTRY>
    │      │      ▼
    │      │   Registry Module
    │      │
    │      └── no scope
    │             ▼
    │         All Configured Registries
    │
    ▼
Listing Result
```

· · ·

## 5.2 Registry Listing

```text
CLI Parser
    │
    │ parsed arguments
    ▼
list.js
    │
    │ --registry --available
    ▼
Registry Module
    │
    ▼
Configured Registry Sources
```

---

# 6. Security

The List Command does not perform security verification itself.

When package or registry information is retrieved from an external registry, security-related validation remains the responsibility of the module providing that information.

The List Command MUST NOT bypass security checks implemented by lower-level modules.

Because the List Command is read-only, it MUST NOT modify package or registry state as a side effect of listing an item.

---

# 7. Revision History

## v1.00

**Author:** Tayku Development Team

### Added

* Initial List Command architecture.
* Package listing from all configured registries.
* Package listing from a specific registry.
* Listing of packages currently available to the project.
* Listing of configured registry sources.
* `--available` / `-a`.
* `--registry` / `-r`.

### Changed

None.

### Removed

None.

---

# 8. ToDo List

* [ ] Define the exact module interfaces used by the List Command.
* [ ] Define the output format for package listings.
* [ ] Define the output format for registry listings.
* [ ] Define duplicate handling when the same package exists in multiple registries.
* [ ] Define behavior when a configured registry is unreachable.
* [ ] Define ordering rules for package and registry listings.
* [ ] Define pagination or output limits if required.
* [ ] Define complete argument validation rules.

