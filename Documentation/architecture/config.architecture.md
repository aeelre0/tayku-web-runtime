# TWR Configuration Architecture Specification

The Tayku Web Runtime Environment (TWR) provides a standardized, file-driven configuration architecture to manage system settings, module parameters, and application behavior. This document defines the system-level architecture, directory layout, parsing boundaries, component boundaries, and resolution semantics for the TWR configuration subsystem.

This specification applies to TWR core architects, TWR core developers, and application developers designing configurable TWR Applications and modules within the TWR ecosystem. It establishes the rules required to build compliant, configurable components.

---

# 1. Purpose

The purpose of this document is to establish the architectural boundary, file hierarchy conventions, precedence cascade, namespace isolation, and integration guidelines for configuration management in TWR Core and TWR Applications.

---

# 2. Table of Contents

- 1. Purpose
- 2. Table of Contents
- 3. Definitions
- 4. Scope
  * in-scope
  * out-of-scope
- 5. Architectural Principles and Design Goals
  * 5.1 Hardcoding Avoidance Principles
  * 5.2 Parser Integration Boundary
- 6. Configuration Boundaries and Namespaces
  * 6.1 Core Configuration Boundary
  * 6.2 Application Configuration Boundary
- 7. Precedence and Resolution Semantics
  * 7.1 Precedence Hierarchy
  * 7.2 Resolution Cascade Rules
- 8. Developer Integration and Rules
  * 8.1 Configurable Application Parameters
  * 8.2 Architectural Restrictions
- 9. Error Isolation and Failure Semantics
- 10. Questions
  * 10.1 Why Are Configuration Directories Hidden?
  * 10.2 Can Applications Override Core Configuration Keys?
- 11. Related Resources

---

# 3. Definitions

This section defines terms used normatively throughout this architectural specification.

- **Core Configuration:** Framework-level runtime settings located under `.config/twr/core/`.
- **Application Configuration:** Application-specific settings located under `.config/twr/<app_name>/`.
- **Precedence Cascade:** The deterministic order of priority used by `twr-config` to resolve key collisions across runtime layers.
- **In-Code Fallback:** Universal default values defined directly within C/C++ module source files as a last-resort fallback.

---

# 4. Scope

- **in-scope:**
  * System-wide configuration directory structure under Unix paths (`.config/twr/`).
  * Architectural boundary between Core and Application namespaces.
  * Integration architecture of the `utils/twr-tomlc99` (`tomlc99`) parsing utility.
  * Deterministic precedence cascade rules for CLI flags, environment variables, TOML configs, and defaults.
  * Rules governing configurable parameters and `twr-config` module interaction for developers.

- **out-of-scope:**
  * Low-level C AST structures and internal parser memory allocation (see `twr-config.dev.md`).
  * Public C API header exports and function signatures (see `twr-config.api.md`).
  * End-user operational tutorials, deployment guides, and TOML editing instructions (see `config.user.md`).

---

# 5. Architectural Principles and Design Goals

The TWR configuration subsystem provides a unified mechanism to control runtime environments without requiring binary re-compilation.

· · ·

## 5.1 Hardcoding Avoidance Principles

To maintain operational flexibility across deployment environments:

- Module and application developers SHOULD expose configurable runtime parameters, timeouts, memory bounds, network options, and feature toggles via TOML configuration keys rather than embedding static constants in code.
- Hardcoded in-code constants SHOULD be limited to safe, universal fallback defaults.
- Configuration-controlled runtime options MUST NOT require binary re-compilation to change behavior across different deployment environments.

· · ·

## 5.2 Parser Integration Boundary

TWR Core integrates the open-source C11 TOML parser (`tomlc99`) under the `utils/twr-tomlc99/` utility boundary.

- The `utils/twr-tomlc99` parser MUST ingest raw TOML bytes directly from filesystem files or buffers.
- The `twr-config` core module acts as the sole architectural wrapper mapping `tomlc99` outputs into TWR-compliant runtime structures.
- External TWR Applications and modules MUST NOT invoke `utils/twr-tomlc99` directly; all configuration requests MUST route exclusively through `twr-config`.

---

# 6. Configuration Boundaries and Namespaces

All TWR configuration files MUST follow standard Unix directory structures under `.config/twr/`.

```text
.config/
└── twr/
    ├── core/
    │   └── twr.toml         # Framework-wide default settings
    └── <app_name>/
        └── twr.toml         # Application-specific configuration
```

· · ·

## 6.1 Core Configuration Boundary
The core directory (`.config/twr/core/twr.toml`) defines system-wide framework invariants, global log defaults, shared memory boundaries, and execution parameters. Core configuration keys belong exclusively to the `core` namespace.

· · ·

## 6.2 Application Configuration Boundary
The application directory (`.config/twr/<app_name>/twr.toml`) contains parameters specific to an individual TWR Application or business module[cite: 9]. Applications MUST operate within their own isolated namespace matching their registered executable or module name (`<app_name>`).

---

# 7. Precedence and Resolution Semantics

When a configuration key is requested at runtime, `twr-config` MUST evaluate sources using a deterministic priority model.

· · ·

## 7.1 Precedence Hierarchy
Resolution MUST proceed in the following descending priority order:

1. **Explicit CLI Flags:** Arguments supplied directly at process invocation (e.g., `--log-level=debug`).
2. **Environment Variables:** System environment variables prefixed with `TWR_` (e.g., `TWR_LOG_LEVEL`).
3. **Application Configuration:** Local application settings loaded from `.config/twr/<app_name>/twr.toml`.
4. **Core Configuration:** Global settings loaded from `.config/twr/core/twr.toml`.
5. **In-Code Defaults:** Universal fallback constants defined within C/C++ module implementations.

· · ·

## 7.2 Resolution Cascade Rules
The first source containing a valid, typed match for the requested key MUST win. If a higher-precedence source defines a key, it completely overrides values present in lower-precedence sources for that key.

---

# 8. Developer Integration and Rules

Developers designing TWR Core modules or TWR Applications MUST adhere to the following integration constraints:

· · ·

## 8.1 Configurable Application Parameters
- Application configuration keys MUST be documented in the application's design specification.
- Key names MUST use lowercase alphanumeric characters formatted in `snake_case`.
- Application-specific keys SHOULD be grouped into logical TOML sections (e.g., `[server]`, `[database]`).

· · ·

## 8.2 Architectural Restrictions
- TWR Applications MUST NOT attempt to read, write, or override configuration keys outside their designated `.config/twr/<app_name>/` namespace.
- TWR Applications MUST access TWR configuration through `twr-config` and MUST NOT read or parse TWR configuration files directly.
- Applications MUST correctly handle configuration lookup failures according to the applicable configuration contract, rather than assuming that fallback defaults exist for all parameters.

---

# 9. Error Isolation and Failure Semantics

Configuration operations MUST strictly adhere to deterministic status-code contracts defined in `twr-config.dev.md`.

- If a configuration file contains invalid syntax or structural formatting errors during parsing, `twr-config` MUST fail immediately and return `TWR_ERROR_CONFIG_SYNTAX_INVALID`.
- If a requested configuration parameter is missing and has no lower-precedence fallback, `twr-config` MUST return `TWR_ERROR_CONFIG_KEY_NOT_FOUND`.
- If a configuration value cannot be converted into the requested type, `twr-config` MUST return `TWR_ERROR_CONFIG_TYPE_MISMATCH`.
- If a dynamic memory allocation fails during configuration processing, `twr-config` MUST return `TWR_ERROR_OUT_OF_MEMORY`.
- Parsing or loading failures MUST NOT be silently swallowed or implicitly converted into fallback operations. Error context MUST be propagated to the caller via `TWR_STATUS_CODE`[cite: 1, 2].

---

# 10. Questions

This section clarifies architectural queries regarding the configuration design.

· · ·

## 10.1 Why Are Configuration Directories Hidden?
TWR adopts the `.config/twr/` directory path as its established configuration convention under Unix environments, aligning with standard user environment layout expectations to keep application working spaces organized.

· · ·

## 10.2 Can Applications Override Core Configuration Keys?
No. Core configuration keys under `.config/twr/core/` define framework-level invariants. Application configurations under `.config/twr/<app_name>/` MAY only define or override keys within their application namespace.

---

# 11. Related Resources

- `twr-config.dev.md`
- `twr-config.api.md`
- `config.user.md`
- `core-coding-rules.dev.md`

___ 

# 12. Notes

> [!Note] AI Usage
> This document has been transcribed by an AI in accordance with the rules in the `documentation-guide-rules-ai.dev.md` file, based on the developer's architectural decisions.

___
