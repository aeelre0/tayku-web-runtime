# TWR User Configuration Guide

This document is the official operational guide for system administrators, DevOps engineers, and operators configuring and deploying the Tayku Web Runtime (TWR) environment or TWR Applications on servers and workstations.

It explains how to locate, edit, and manage configuration settings using TOML files, environment variables, and CLI command-line options.

---

# 1. Purpose

The purpose of this document is to provide practical instructions for system operators to manage TWR configuration files under `.config/twr/`, apply runtime settings, override defaults, and resolve configuration loading issues.

---

# 2. Table of Contents

- 1. Purpose
- 2. Table of Contents
- 3. Definitions
- 4. Scope
  * in-scope
  * out-of-scope
- 5. Configuration Directory Structure
  * 5.1 System-Wide vs Core Directories
  * 5.2 Application Configuration Directories
- 6. Managing Configuration Overrides
  * 6.1 Configuration Precedence for Operators
  * 6.2 Overriding via Environment Variables
  * 6.3 Overriding via CLI Arguments
- 7. TOML Configuration File Syntax
  * 7.1 Value Types
  * 7.2 Structuring Tables and Subsections
- 8. Operator Configuration Examples
  * 8.1 Core Environment Configuration Example (`core/twr.toml`)
  * 8.2 Application Deployment Configuration Example (`<app_name>/twr.toml`)
- 9. Operational Error Handling and Recovery
  * 9.1 Invalid Syntax and File Corruption
  * 9.2 Missing Required Keys
- 10. Questions
  * 10.1 How Do I Verify Which Configuration Settings Are Loaded?
  * 10.2 Can I Move the Configuration Directory to Another Path?
- 11. Related Resources

---

# 3. Definitions

- **System Administrator / Operator:** The user responsible for installing, configuring, running, and maintaining TWR services on a server or workstation.
- **Core Configuration:** Global framework settings affecting all TWR operations, stored in `.config/twr/core/twr.toml`.
- **Application Configuration:** Settings specific to a single deployed TWR application, stored in `.config/twr/<app_name>/twr.toml`.
- **Precedence:** The rule that determines which value wins when a setting is defined in multiple places.

---

# 4. Scope

- **in-scope:**
  * Filesystem layout for TWR configuration under Unix user environments (`.config/twr/`).
  * Rules for editing TOML configuration files.
  * Operational procedures for overriding settings using environment variables (`TWR_*`) and command-line options.
  * Troubleshooting configuration parsing errors and missing settings.

- **out-of-scope:**
  * Architecture specifications and internal parser boundaries (see `config.architecture.md`).
  * Low-level C AST structures and memory allocation contracts (see `twr-config.dev.md`).
  * Public C API header specifications and code integration (see `twr-config.api.md`).

---

# 5. Configuration Directory Structure

TWR organizes system and application configurations under the user's hidden configuration directory (`.config/twr/`).

```text
.config/
└── twr/
    ├── core/
    │   └── twr.toml         # System-wide framework and runtime defaults
    └── <app_name>/
        └── twr.toml         # Deployment configuration for a specific application
```

· · ·

## 5.1 System-Wide vs Core Directories
The `core/twr.toml` file contains global server environment settings. These settings control framework-level defaults that apply across TWR applications running under the user environment.

· · ·

## 5.2 Application Configuration Directories
Each TWR application installed on the server uses a dedicated directory under `.config/twr/<app_name>/`, where `<app_name>` corresponds to the binary or module name. The `twr.toml` file within this directory manages application-specific parameters.

---

# 6. Managing Configuration Overrides

Operators can adjust runtime behavior without modifying original configuration files by using precedence rules.

· · ·

## 6.1 Configuration Precedence for Operators
When TWR resolves a setting at startup, it evaluates configuration sources in the following descending order:

1. **Command-Line Interface (CLI) Flags:** Highest priority. Options supplied directly to process execution.
2. **Environment Variables:** System variables prefixed with `TWR_`.
3. **Application TOML File:** Settings in `.config/twr/<app_name>/twr.toml`.
4. **Core TOML File:** Settings in `.config/twr/core/twr.toml`.
5. **In-Code Defaults:** Lowest priority. Fallback defaults embedded within the software.

If a setting exists at a higher priority level, it completely replaces any value set at a lower level.

· · ·

## 6.2 Overriding via Environment Variables
To temporarily override a parameter without modifying `.config/twr/` files, export an environment variable prefixed with `TWR_`. Key names convert to uppercase:

```bash
# Temporarily override an application configuration variable
export TWR_LOG_LEVEL=debug
```

· · ·

## 6.3 Overriding via CLI Arguments
To override a setting for a single execution, pass explicit CLI arguments directly when invoking the application process according to the application's supported flags:

```bash
twr run my_app -- <app_flags>
```

---

# 7. TOML Configuration File Syntax

TWR configuration files use standard TOML format. Keys must be written in lowercase using `snake_case`.

· · ·

## 7.1 Value Types
Operators can configure settings using standard TOML data types:

- **Strings:** Enclosed in double quotes (`"info"`, `"127.0.0.1"`).
- **Integers:** Whole numbers (`8080`, `4`).
- **Booleans:** Lowercase keywords (`true`, `false`).
- **Arrays:** Lists enclosed in square brackets (`["/var/log/twr.log", "stdout"]`).

· · ·

## 7.2 Structuring Tables and Subsections
Group related operational options into section headers using square brackets:

```toml
[server]
host = "0.0.0.0"
port = 8080

[logging]
level = "warn"
```

---

# 8. Operator Configuration Examples

The following examples are illustrative TOML files provided for reference. Specific configuration keys and sections are defined by individual TWR Core and TWR Application contracts.

· · ·

## 8.1 Core Environment Configuration Example (`core/twr.toml`)

```toml
# Illustrative core framework settings
[runtime]
worker_threads = 4

[logging]
default_level = "info"
```

· · ·

## 8.2 Application Deployment Configuration Example (`<app_name>/twr.toml`)

```toml
# Illustrative application deployment settings
[server]
host = "127.0.0.1"
port = 3000

[database]
max_connections = 20

[features]
enable_telemetry = false
```

---

# 9. Operational Error Handling and Recovery

When a TWR process parses configuration files at startup, any encountered error prevents successful configuration resolution and returns a diagnostic error code.

· · ·

## 9.1 Invalid Syntax and File Corruption
If a TOML file contains a syntax error (e.g., missing quotation mark, invalid key structure), configuration loading fails with error code `TWR_ERROR_CONFIG_SYNTAX_INVALID`.

**Recovery Procedure:**
1. Check standard error or diagnostic logs for syntax failure details.
2. Validate the `.config/twr/` TOML file syntax using a standard TOML validator or editor.
3. Correct the formatting and re-run the operation.

· · ·

## 9.2 Missing Required Keys
If a mandatory setting is missing from all configuration layers and has no lower-precedence fallback, configuration lookup returns `TWR_ERROR_CONFIG_KEY_NOT_FOUND`.

**Recovery Procedure:**
1. Identify the missing key required by the application contract.
2. Add the missing key and its value to `.config/twr/<app_name>/twr.toml` or supply it via a corresponding `TWR_` environment variable.
3. Re-run the operation.

---

# 10. Questions

This section provides answers to common operational questions.

· · ·

## 10.1 How Do I Verify Which Configuration Settings Are Loaded?
TWR resolves configuration values by evaluating options in the established precedence hierarchy: CLI flags, environment variables (`TWR_*`), application TOML (`.config/twr/<app_name>/twr.toml`), core TOML (`.config/twr/core/twr.toml`), and in-code defaults. Operators can verify active values by reviewing the configured TOML files or checking exported environment variables.

· · ·

## 10.2 Can I Move the Configuration Directory to Another Path?
TWR follows standard Unix environment conventions, storing configuration strictly under `.config/twr/` within the executing user's home or environment directory.

---

# 11. Related Resources

- `config.architecture.md`
- `twr-config.dev.md`
- `twr-config.api.md`

___ 

# 12. Notes

> [!Note] AI Usage
> This document has been transcribed by an AI in accordance with the rules in the `documentation-guide-rules-ai.dev.md` file, based on the developer's architectural decisions.

___
