# Tayku Web Client Installer Architecture

This document defines the architecture, responsibilities, interfaces, and boundaries of the Tayku Web Client Installer Module.

---

# 1. Scope

* **In-scope:**

  * This page defines the contract of `installer.js`.
  * This page defines the responsibilities and boundaries of the Installer Module.
  * This page explains how `installer.js` receives and installs packages from the Registry Module.
  * This page defines the handling of package files and their required installation data.

* **Out-of-scope:**

  * This page does not define how packages are searched or resolved.
  * This page does not define registry source selection or communication with the Tayku Web Registry.
  * This page does not define package signature generation or verification.
  * This page does not define dependency resolution.
  * This page does not define package metadata validation.

---

# 2. Architecture

This section explains the architecture of the Tayku Web Client Installer Module.

## 2.1 Definitions

* **Installer:** The module responsible for downloading and installing a package provided by the Registry Module.

* **Package:** A Tayku Web package that has been resolved by the Registry Module and is ready to be downloaded and installed.

* **Installation Configuration:** Configuration data required to correctly install a package. The Installer uses this information to perform the installation but does not determine or resolve it.

* **Registry Module:** The module responsible for finding and resolving packages, communicating with registry sources, verifying package integrity, and providing the Installer with the package to install.

## 2.2 Responsibilities

The Installer Module has a deliberately narrow responsibility.

When a user requests a package, the CLI parses the command and passes the request to the Registry Module. The Registry Module searches the configured registry sources, resolves the requested package and its required dependencies, and asks the Verifier Module to verify the package signature.

After successful verification, the Registry Module passes the resolved package and its installation information to the Installer Module.

The Installer Module then:

1. Downloads the resolved package.
2. Downloads the required installation data, if provided.
3. Installs the package into the appropriate location.
4. Returns a `TAYKU_STATUS_CODE`.

The Installer Module does not search for packages, select registry sources, verify signatures, or resolve dependencies.

## 2.3 Logic

The Installer Module is called only after the Registry Module has successfully resolved and verified a package.

The Registry Module is responsible for determining **what** must be installed. The Installer Module is responsible for determining **how** the resolved package is written to the local system.

If the resolved package contains dependencies, the Registry Module resolves those dependencies before calling the Installer Module. Therefore, the Installer Module does not independently resolve dependencies.

From the Installer's perspective, a request to install a package may already contain all required dependency packages.

---

# 3. Interface

* **Inputs**

  * Resolved package information provided by the Registry Module.
  * Package download source.
  * Installation configuration, if required.
  * Target installation path.

* **Outputs**

  * `TAYKU_STATUS_CODE`
  * Installed package files.

## 3.1 Dependencies

* `fs`: Node.js filesystem API used to create, read, write, and modify package files.
* `https` / `http`: Node.js networking APIs used to download package data when required.

## 3.2 Contract

```js
/**
 * Installs a resolved package.
 *
 * The package MUST have been resolved and verified by the Registry
 * Module before this function is called.
 *
 * The Installer MUST NOT resolve dependencies or verify package
 * signatures.
 *
 * If the package contains required dependencies resolved by the
 * Registry Module, they MUST be included in the installation request.
 *
 * @param {object} package_info Resolved package information.
 * @param {object} installation_config Installation configuration.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function install_package(package_info, installation_config) {...}
```

---

# 4. Data Flow

* **Package Installation**

```text
User
   │
   │ package request
   ▼
CLI
   │
   │ parsed command
   ▼
Registry Module
   │
   ├──► Registry Sources
   │       │
   │       ▼
   │   Resolved Package
   │       │
   │       ▼
   │    Verifier
   │       │
   │       ▼
   │   Verified Package
   │
   │ package_info + installation_config
   ▼
Installer Module
   │
   ├──► Download Package
   │
   ├──► Download Installation Data
   │
   ├──► Install Dependencies
   │
   └──► Install Package
            │
            ▼
     Installed Package
```

> [!NOTE]
> Dependency resolution is performed by the Registry Module. The Installer Module only installs dependencies that have already been resolved and provided as part of the installation request.

* **Dependency Installation**

```text
Registry Module
   │
   │ Resolved Package
   │      + Resolved Dependencies
   ▼
Installer Module
   │
   ├──► Dependency A
   ├──► Dependency B
   ├──► Dependency C
   │
   └──► Main Package
            │
            ▼
      Installed Package
```

---

# 5. Error Handling

The Installer Module reports installation failures through `TAYKU_STATUS_CODE`.

* `NOT FOUND`: The requested package, installation data, or required source could not be found.

  * **Solution:** Verify that the resolved package and its required installation resources are available.

* `ALREADY EXISTS`: A package or required installation file already exists and cannot be safely overwritten.

  * **Solution:** Remove the existing package or use an installation path that does not contain the conflicting files.

* `PERMISSION DENIED`: The Installer does not have sufficient permissions to create, modify, or write the required files.

  * **Solution:** Check the permissions of the target installation path and ensure that the current user can modify it.

* `IO ERROR`: An unexpected input/output error occurred while downloading, creating, reading, writing, or modifying package files.

  * **Solution:** Check the filesystem and target path, then retry the installation.

* `NETWORK ERROR`: The package or required installation data could not be downloaded because of a network failure.

  * **Solution:** Check the network connection and the availability of the package source, then retry the installation.

* `NOT ENOUGH SPACE`: There is not enough free disk space to complete the installation.

  * **Solution:** Free sufficient disk space and retry the installation.

---

# 6. Security

* **Package Verification**

  * Package verification is performed by the Verifier Module before installation.
  * The Installer Module MUST NOT install a package that has not been successfully verified by the Registry Module.

* **Dependency Security**

  * Dependencies are resolved and verified by the Registry Module before they are passed to the Installer Module.
  * The Installer Module does not independently trust or resolve dependency sources.

* **File System Security**

  * The Installer MUST write package files only to the target installation location determined by the installation configuration.
  * Package paths MUST NOT be allowed to escape the configured installation directory.

* **Installation Integrity**

  * The Installer MUST report a failure if a required package file cannot be written completely.
  * Partial installations SHOULD be detected and handled according to the installation policy.

---

# 7. Design Revision History (Changelog)

This section records the architectural evolution of the Installer Module.

### v0.0.1 — 21 SEP 2026

**Author:** Ali Emre Arlı

### Initial Architecture

* Established the initial architecture of `installer.js`.
* Defined the Installer Module as the package installation layer.
* Established the boundary between the Registry, Verifier, and Installer Modules.
* Defined package installation as the final step after package resolution and verification.

· · ·

> *It downloads things. That's pretty much it.*

