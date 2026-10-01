# TWR Configuration Subsystem API Specification

This document defines the C11 public C API specification for the Tayku Web Runtime (TWR) configuration subsystem.

It specifies the public header interfaces, opaque handles, data types, function signatures, error status codes, and memory ownership rules for embedding and consuming the TWR configuration library within C/C++ applications and modules.

---

# 1. Purpose

The purpose of this specification is to establish a stable, append-only C11 public API contract for loading, parsing, querying, and managing TWR configuration settings in C applications.

---

# 2. Table of Contents

- 1. Purpose
- 2. Table of Contents
- 3. Definitions
- 4. Scope
  * in-scope
  * out-of-scope
- 5. C11 Public Header Contract
- 6. Data Types and Handles
  * 6.1 Opaque Configuration Context
  * 6.2 Configuration Value Types
- 7. Lifecycle and Loading Primitives
  * 7.1 Architecture and Precedence Boundary
  * 7.2 `twr_config_create`
  * 7.3 `twr_config_destroy`
  * 7.4 `twr_config_load_file`
  * 7.5 `twr_config_load_env`
- 8. Value Query APIs
  * 8.1 `twr_config_get_string`
  * 8.2 `twr_config_get_int`
  * 8.3 `twr_config_get_bool`
  * 8.4 `twr_config_has_key`
- 9. Status Codes and Error Handling
- 10. Memory Ownership and Lifetime Contracts
- 11. Related Resources

---

# 3. Definitions

- **Configuration Context:** An opaque structure (`struct TwrConfig`) encapsulating runtime settings populated by low-level configuration loading primitives or explicit programmatic operations.
- **Opaque Handle:** A pointer to an incomplete struct type (`struct TwrConfig *`) that enforces strict encapsulation by preventing direct struct member access.
- **`TWR_STATUS_CODE`:** The standard integer status code returned by all fallible TWR functions (`0` for `TWR_SUCCESS`, negative values for error conditions).

---

# 4. Scope

- **in-scope:**
  * C11 public header declarations for the `twr_config_*` C API primitives.
  * Opaque handles, data types, and status code references.
  * Explicit ownership, memory safety, input immutability, and lifetime rules.
  * Lower-level primitives for context lifecycle, TOML file parsing, environment loading, and typed key querying.
  * Architectural boundaries defining component responsibilities between `twr-config` primitives and higher-level TWR runtime components.

- **out-of-scope:**
  * System administrator deployment guides (see `config.user.md`).
  * Architecture specifications and multi-layer precedence resolution models (see `config.architecture.md`).
  * Higher-level CLI option parsing and CLI flag injection (managed by the TWR runtime/command execution layer).
  * Internal C AST structures, memory allocators, and translation units (see `twr-config.dev.md`).

---

# 5. C11 Public Header Contract

In accordance with active TWR API decisions:
- Public C API headers strictly adhere to the **C11 standard** (`ISO/IEC 9899:2011`).
- All functions are declared within the **`twr_config_*`** public function namespace.
- Public structures use explicit tags (`struct TwrConfig`) following **PascalCase** naming.
- All fallible public functions return **`TWR_STATUS_CODE`**. Infallible resource cleanup functions (`twr_config_destroy`) return `void`.
- Primary data results are passed through explicit mutable output parameters.
- Input parameters are declared as **`const`** pointers to guarantee immutability.

---

# 6. Data Types and Handles

## 6.1 Opaque Configuration Context

```c
/* Opaque handle to a TWR configuration instance */
struct TwrConfig;
```

External consumers interact with configuration contexts strictly through pointers to `struct TwrConfig`. The internal implementation details and member layout remain fully encapsulated within internal translation units.

· · ·

## 6.2 Configuration Value Types

```c
/* Enumeration of supported configuration value types */
enum TwrConfigValueType {
    TWR_CONFIG_TYPE_NONE = 0,
    TWR_CONFIG_TYPE_STRING,
    TWR_CONFIG_TYPE_INT,
    TWR_CONFIG_TYPE_BOOL,
    TWR_CONFIG_TYPE_ARRAY,
    TWR_CONFIG_TYPE_TABLE
};
```

*Note on Value Types:* `TWR_CONFIG_TYPE_ARRAY` and `TWR_CONFIG_TYPE_TABLE` represent parser-supported structural node types present in the internal TOML representation. The public query API explicitly exposes scalar lookups (`string`, `int`, `bool`), while structural traversal of nested tables and arrays remains encapsulated or handled by higher-level configuration abstractions.

---

# 7. Lifecycle and Loading Primitives

## 7.1 Architecture and Precedence Boundary

The `twr-config` C API provides fundamental, lower-level configuration loading and storage primitives.

- **Multi-Layer Precedence Resolution Boundary:** Multi-layer precedence resolution (evaluating CLI flags > Environment Variables > Application TOML > Core TOML > In-Code Defaults as specified in `config.architecture.md`) is orchestrated by higher-level TWR runtime components.
- **File Ingestion Behavior:** `twr_config_load_file()` and `twr_config_load_env()` execute direct value ingestion into the specified `struct TwrConfig` context. When invoked sequentially on a single context, keys loaded from subsequent operations overwrite existing keys in that specific context. Higher-level components maintain distinct contexts or explicitly control call order to implement established layer precedence.
- **File Path Policy Boundary:** `twr_config_load_file()` operates strictly as a low-level TOML ingestion primitive that accepts any filesystem path provided by the caller. Enforcing the TWR configuration file namespace (`.config/twr/`) and selecting appropriate file paths are the explicit responsibility of higher-level TWR runtime components.
- **CLI Overrides Responsibility:** Command-Line Interface (CLI) argument parsing and override resolution belong to higher-level TWR runtime components. CLI options are evaluated outside `twr-config` and do not pass through a dedicated CLI loading primitive in this C API.

· · ·

## 7.2 `twr_config_create`

Allocates and initializes a new, empty configuration context.

```c
TWR_STATUS_CODE
twr_config_create(
    struct TwrConfig **config
);
```

- **Parameters:**
  - `config`: Output pointer that will receive the pointer to the newly allocated `struct TwrConfig`. Must not be `NULL`.
- **Preconditions:** `config` pointer itself must be non-NULL.
- **Postconditions:** On `TWR_SUCCESS`, `*config` contains a valid handle to an empty configuration context owned by the caller.
- **Return Status:**
  - `TWR_SUCCESS`: Context allocated successfully.
  - `TWR_ERROR_INVALID_ARGUMENT`: `config` output parameter is `NULL`.
  - `TWR_ERROR_OUT_OF_MEMORY`: Memory allocation failed.

· · ·

## 7.3 `twr_config_destroy`

Frees all resources associated with a configuration context.

```c
void
twr_config_destroy(
    struct TwrConfig *config
);
```

- **Parameters:**
  - `config`: Pointer to the `struct TwrConfig` context to destroy. If `config` is `NULL`, the function performs no operation.
- **Ownership:** Reclaims memory owned by the context. Any borrowed pointers previously returned by lookup functions become invalid.

· · ·

## 7.4 `twr_config_load_file`

Parses a TOML configuration file at the specified file path and ingests its settings into the configuration context.

```c
TWR_STATUS_CODE
twr_config_load_file(
    struct TwrConfig *config,
    const char *file_path
);
```

- **Parameters:**
  - `config`: Pointer to an initialized configuration context.
  - `file_path`: Null-terminated string representing the absolute or relative path to the TOML file.
- **Semantics & Collision Rules:** Ingests keys from the specified TOML file into `config`. If a key path already exists within `config`, its value is overwritten by the newly loaded file's value. Higher-level orchestration components enforce architectural layer order when building unified contexts.
- **Failure Atomicity:** Failure atomicity is unspecified by this C API contract. The behavior on partial ingestion after a file-reading or parsing failure is not defined by this API.
- **Return Status:**
  - `TWR_SUCCESS`: File parsed and loaded successfully.
  - `TWR_ERROR_INVALID_ARGUMENT`: `config` or `file_path` is `NULL`.
  - `TWR_ERROR_CONFIG_SYNTAX_INVALID`: TOML parsing failure or syntax corruption.
  - `TWR_ERROR_FILE_NOT_FOUND`: Specified `file_path` does not exist or cannot be opened.

· · ·

## 7.5 `twr_config_load_env`

Scans the system environment for variables matching the `TWR_*` prefix and applies them as overrides to the specified configuration context.

```c
TWR_STATUS_CODE
twr_config_load_env(
    struct TwrConfig *config
);
```

- **Parameters:**
  - `config`: Pointer to an initialized configuration context.
- **Semantics:** Scans environment variables matching the `TWR_*` prefix and ingests them into `config`. Loaded environment values overwrite matching keys previously present in `config`. The precise key path normalization and mapping convention (such as handling nested sections, underscores, or casing) is unspecified by this C API specification and is governed by the authoritative TWR configuration architecture.
- **Return Status:**
  - `TWR_SUCCESS`: Environment variables processed successfully.
  - `TWR_ERROR_INVALID_ARGUMENT`: `config` is `NULL`.

---

# 8. Value Query APIs

## 8.1 `twr_config_get_string`

Queries a string setting by its key path.

```c
TWR_STATUS_CODE
twr_config_get_string(
    const struct TwrConfig *config,
    const char *key_path,
    const char **out_value
);
```

- **Parameters:**
  - `config`: Immutable pointer to the configuration context.
  - `key_path`: Null-terminated key path string (e.g., `"server.host"`).
  - `out_value`: Output pointer to receive a borrowed pointer to the string value.
- **Lifetime:** `*out_value` points to internal storage owned by `config`. The caller MUST NOT free `*out_value`. The string pointer remains valid only while `config` remains alive and unmodified.
- **Return Status:**
  - `TWR_SUCCESS`: Key found and string value returned.
  - `TWR_ERROR_INVALID_ARGUMENT`: Any required argument is `NULL`.
  - `TWR_ERROR_CONFIG_KEY_NOT_FOUND`: Specified key path does not exist in the context.
  - `TWR_ERROR_CONFIG_TYPE_MISMATCH`: Key exists but is not a string type.

· · ·

## 8.2 `twr_config_get_int`

Queries an integer setting by its key path.

```c
TWR_STATUS_CODE
twr_config_get_int(
    const struct TwrConfig *config,
    const char *key_path,
    int64_t *out_value
);
```

- **Parameters:**
  - `config`: Immutable pointer to the configuration context.
  - `key_path`: Null-terminated key path string (e.g., `"server.port"`).
  - `out_value`: Output pointer to receive the 64-bit signed integer value.
- **Return Status:**
  - `TWR_SUCCESS`: Key found and integer written to `*out_value`.
  - `TWR_ERROR_INVALID_ARGUMENT`: Any required argument is `NULL`.
  - `TWR_ERROR_CONFIG_KEY_NOT_FOUND`: Specified key path does not exist in the context.
  - `TWR_ERROR_CONFIG_TYPE_MISMATCH`: Key exists but is not an integer type.

· · ·

## 8.3 `twr_config_get_bool`

Queries a boolean setting by its key path.

```c
TWR_STATUS_CODE
twr_config_get_bool(
    const struct TwrConfig *config,
    const char *key_path,
    bool *out_value
);
```

- **Parameters:**
  - `config`: Immutable pointer to the configuration context.
  - `key_path`: Null-terminated key path string (e.g., `"features.telemetry"`).
  - `out_value`: Output pointer to receive the boolean value.
- **Return Status:**
  - `TWR_SUCCESS`: Key found and boolean written to `*out_value`.
  - `TWR_ERROR_INVALID_ARGUMENT`: Any required argument is `NULL`.
  - `TWR_ERROR_CONFIG_KEY_NOT_FOUND`: Specified key path does not exist in the context.
  - `TWR_ERROR_CONFIG_TYPE_MISMATCH`: Key exists but is not a boolean type.

· · ·

## 8.4 `twr_config_has_key`

Checks if a key exists in the specified configuration context.

```c
TWR_STATUS_CODE
twr_config_has_key(
    const struct TwrConfig *config,
    const char *key_path,
    bool *out_exists
);
```

- **Parameters:**
  - `config`: Immutable pointer to the configuration context.
  - `key_path`: Null-terminated key path string.
  - `out_exists`: Output pointer to receive `true` if the effective key path currently exists in `config`, or `false` otherwise.
- **Semantics:** Checks the existence of the key within the active state of `config` after any prior loading or overwrite operations.
- **Return Status:**
  - `TWR_SUCCESS`: Check completed successfully.
  - `TWR_ERROR_INVALID_ARGUMENT`: `config`, `key_path`, or `out_exists` is `NULL`.

---

# 9. Status Codes and Error Handling

Public API functions return standard TWR status codes declared in `twr-status-codes.h`:

| Status Code Identifier | Description |
| :--- | :--- |
| `TWR_SUCCESS` | Operation completed successfully. |
| `TWR_ERROR_INVALID_ARGUMENT` | An invalid argument or `NULL` pointer was passed. |
| `TWR_ERROR_OUT_OF_MEMORY` | Failed to allocate heap memory. |
| `TWR_ERROR_CONFIG_SYNTAX_INVALID` | TOML file syntax or parsing error. |
| `TWR_ERROR_FILE_NOT_FOUND` | Configuration file could not be opened. |
| `TWR_ERROR_CONFIG_KEY_NOT_FOUND` | Requested configuration key does not exist. |
| `TWR_ERROR_CONFIG_TYPE_MISMATCH` | Requested type does not match stored value type. |

*Note on Status Codes:* Numeric integer values assigned to each status macro are defined authoritatively in `core/twr-status-codes.h`. The C API contract relies on canonical macro identifiers rather than hardcoded literal numbers.

---

# 10. Memory Ownership and Lifetime Contracts

1. **Context Creation:** `twr_config_create` transfers ownership of the allocated `struct TwrConfig` handle to the caller. The caller MUST call `twr_config_destroy` when finished.
2. **Borrowed Values:** Pointers returned via `twr_config_get_string` point to memory managed internally by the configuration context. The caller MUST NOT attempt to `free()` or modify these strings.
3. **Lifetime Bound:** Borrowed string pointers are valid only as long as the parent `struct TwrConfig` context remains alive and unmodified. Destroying or mutating the context invalidates all previously borrowed pointers.
4. **Input Immutability:** All input string paths (`key_path`, `file_path`) passed to API functions are read-only (`const char *`) and are never modified or retained after the function returns.

---

# 11. Related Resources

- `config.architecture.md`
- `config.user.md`
- `twr-config.dev.md`
