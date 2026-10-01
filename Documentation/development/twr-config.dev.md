# TWR Configuration Development Specification

The Tayku Web Runtime Environment (TWR) utilizes a configuration-driven architecture to manage runtime options, application parameters, and module initialization. This document defines the internal development specification and implementation guidelines for the TWR configuration subsystem (`twr-config`) using the TOML standard.

This specification is targeted exclusively at TWR core developers and maintainers responsible for implementing, extending, and maintaining the core configuration parsing and storage engine within the runtime.

---

# 1. Purpose

The purpose of this document is to define the low-level data structures, parsing strategy, memory management guidelines, and error handling mechanisms for the `twr-config` component inside TWR Core.

---

# 2. Table of Contents

- 1. Purpose
- 2. Table of Contents
- 3. Definitions
- 4. Scope
  * in-scope
  * out-of-scope
- 5. Parser Architecture and Data Structures
  * 5.1 Internal AST Representation
  * 5.2 Memory Allocation Guidelines
  * 5.3 TOML Parsing Pipeline
- 6. Precedence Resolution Mechanism
- 7. Error Handling and Status Codes
- 8. Questions
  * 8.1 Why Is TOML Used for Core Configuration?
  * 8.2 How Are Unknown Keys Handled in Dev Mode?
- 9. Related Resources

---

# 3. Definitions

This section defines terms used normatively throughout this internal development specification.

- **Config Tree:** The in-memory hierarchical structure representing parsed configuration keys and values.
- **Parser Engine:** The C11-compliant TOML parsing component integrated into TWR Core.
- **Precedence Cascade:** The deterministic order in which configuration overrides (CLI flags, environment variables, user config files, defaults) are evaluated.
- **Config Node:** A single key-value entry or nested table node within the configuration AST.

---

# 4. Scope

- **in-scope:**
  * C/C++ internal data structures for storing parsed TOML data within TWR Core.
  * Standard memory allocation, lifetime rules, and ownership models for configuration nodes.
  * Integration mechanisms between `twr-config` and `twr-string`.
  * Status code generation during parse failures.

- **out-of-scope:**
  * Public C API headers exposed to external application developers (see `twr-config.api.md`).
  * End-user `.config/twr/` directory configuration guides (see `config.user.md`).

---

# 5. Parser Architecture and Data Structures

This section defines the internal engineering specifications for the TOML parser engine in TWR Core.

· · ·

## 5.1 Internal AST Representation

The `twr-config` module MUST store parsed TOML data in an opaque hierarchy of tagged nodes. The core data structure MUST NOT expose raw pointers directly to non-core modules.

```c
typedef enum TwrConfigType {
    TWR_CONFIG_TYPE_NIL = 0,
    TWR_CONFIG_TYPE_BOOL,
    TWR_CONFIG_TYPE_INT,
    TWR_CONFIG_TYPE_FLOAT,
    TWR_CONFIG_TYPE_STRING,
    TWR_CONFIG_TYPE_ARRAY,
    TWR_CONFIG_TYPE_TABLE
} TwrConfigType;

struct TwrConfigNode {
    TwrConfigType type;
    char *key;
    union {
        bool bool_val;
        int64_t int_val;
        double float_val;
        struct twr_string *str_val;
        struct TwrConfigArray *array_val;
        struct TwrConfigTable *table_val;
    } value;
};
```

· · ·

## 5.2 Memory Allocation Guidelines

All dynamic memory allocations within the config tree MUST use established C library memory management interfaces (`malloc` / `free`).

- Dynamic structures allocated during file parsing MUST be tracked and released via `free` upon context destruction.
- Lifetime ownership of parsed nodes MUST remain with the `TwrConfigContext` instance.
- Borrowed references retrieved by getter functions MUST NOT be freed by callers.

· · ·

## 5.3 TOML Parsing Pipeline

The parsing pipeline MUST execute sequentially in three distinct stages:

1. **Lexical Analysis and Tokenization:** Ingests raw TOML bytes from `.config/twr/` configuration files into a stream of typed tokens.
2. **AST Construction:** Validates TOML table hierarchies, key-value syntax, and array types, building the `TwrConfigTable` tree.
3. **Type Binding and Invariant Checking:** Maps parsed values to internal core structs and validates mandatory framework fields.

---

# 6. Precedence Resolution Mechanism

`twr-config` MUST evaluate configuration values according to the TWR configuration precedence order. When a key is queried, the engine MUST resolve values in the following descending priority order:

1. **Explicit CLI Flags:** Arguments passed directly to `twr run` or `twr build`.
2. **Environment Variables:** Variables prefixed with `TWR_` (e.g., `TWR_LOG_LEVEL`).
3. **Application Configuration:** Local app TOML files under `.config/twr/<app_name>/twr.toml`.
4. **Core Configuration:** Global settings under `.config/twr/core/twr.toml`.
5. **Hardcoded Defaults:** In-code fallback constants defined within TWR Core modules.

---

# 7. Error Handling and Status Codes

In accordance with TWR error handling standards (`core-coding-rules.dev.md`), `twr-config` functions MUST return `TWR_STATUS_CODE`.

- Syntax errors in TOML files MUST return `TWR_ERROR_CONFIG_SYNTAX_INVALID`.
- Missing mandatory fields MUST return `TWR_ERROR_CONFIG_KEY_NOT_FOUND`.
- Type mismatches during retrieval MUST return `TWR_ERROR_CONFIG_TYPE_MISMATCH`.
- Memory allocation failures during parsing MUST return `TWR_ERROR_OUT_OF_MEMORY`.

---

# 8. Questions

This section clarifies developer questions regarding internal configuration mechanics.

· · ·

## 8.1 Why Is TOML Used for Core Configuration?
TOML provides explicit key-value mappings that translate directly to C structures with zero ambiguity, while offering native support for comments (`#`) which are crucial for server-side maintenance in Unix environments.

· · ·

## 8.2 How Are Unknown Keys Handled in Dev Mode?
To prevent silent typos in core configuration files, `twr-config` SHOULD emit a warning log via `twr-log` when encountering unknown top-level keys during development initialization.

---

# 9. Related Resources

- `config.architecture.md`
- `config.user.md`
- `twr-config.api.md`
- `core-coding-rules.dev.md`

___

# 10. Notes

> [!Note] AI Usage
> This document has been transcribed by an AI in accordance with the rules in the `documentation-guide-rules-ai.dev.md` file, based on the developer's architectural decisions.

___
