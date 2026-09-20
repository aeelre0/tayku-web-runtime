# Tayku Client Registry Architecture

Tayku client is not designed as the classical `npm` package logic, instead Tayku Client is only package-manager-like system for Tayku Web Utilities. This means that, users will be install tehir packages and modules from at least a soruce (just like package managers of Linux).

Therefore we have to design a source system named registry.

___

# 1. Scope
- **in-scope:**
    * Explain how the Tayku Client communicates with registry sources.
    * Explain which configurations are required by the Tayku Client when creating or adding a registry source.
    * Explain the package discovery logic of the Tayku Client.
    * Explain the difference between a package and a module.
    * Explain the package retrieval process from a registry source.
    * Explain the requirements for publishing a package to a registry source.

- **out-of-scope:**
    * This document describes only the registry logic of the Tayku Client. The configuration and architecture of the Tayku Web Registry (Backend) are out of scope.

___ 

# 2. Architecture 

This section explains the Architecture of Tayku Web Client Registry module in a detailed way.

## 2.1 Definitions 

- **Tayku Web Module:**
    - Official repositories published by Tayku Web Developers. These are located in the native registry source.

- **Tayku Web Inner Module:**
    - Tayku Web Client Modules that using while developing of client. These modules don't publish in the official registry soruce(s).

- **Tayku Web Package:**
    - Un-official repositories of Tayku Web Environment. These repositories are located in an external registry source. 

- **Tayku Web Registry:**
    - The source that contains packages or modules. These sources have to obey some configuration rules for being a registry soruce.

## 2.2 Logic 

Tayku Client Registry Module has own configuration file in the config directory, named `registry.config.json`. These file defines the registry sources inside. In this version, Tayku Web Client has two official registry soruce;
    1. Tayku Web Frontend Registry Source = Covers the frontend modules inside just like `hero`, `header`, etc...
    2. Tayku Web Backend Registry Source = Covers the backend modules inside just like `CRM`, `CMS`, etc...

Users can add external repositories inside of the `registry.config.json` file. Whenever new registry soruce added, Tayku Web Client Registry Module can search packages inside these sources just like official sources.

Tayku Web Client Registry Module handle its functions by communicating with these registries from `registry.config.json` file. These functions contains install new package to project, remove a package from project, publish new package, listing all available packages, look at the man file of package and etc...

___ 

# 3. Interface 
- **inputs**
    - `package-name` : The name of package that you want to download, remove or view its man page (or etc...).
    - `registry-source` : The source of the registry that you want to access.
    - `tayku-package.json` : A file that you have to define if you want to publish new package.

- **outputs**
    - `TAYKU-STATUS-CODE` 
    - `package-list` : An object which is listing two or more packages inside.

## 3.1 Dependencies 
    - `registry.config.json` file for viewing its own registry sources.

## 3.2 Contract

```js
/**
 * Initializes the registry source list in RAM.
 *
 * The registry sources are loaded into memory when the client starts.
 * This function manages the initial loading process.
 *
 * If a new registry source is added while the client is running,
 * the registry source list MUST be reinitialized.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function initialize_registry_list() {...}


/**
 * Contains the configured registry sources.
 *
 * @type {string[]}
 */
let registry_source_list = []


/**
 * Returns a copy of the current registry source list.
 *
 * The function MUST NOT modify registry_source_list.
 * The returned list is written to the provided output parameter.
 *
 * @param {string[]} out Output parameter for the registry source list.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function get_registry_sources(out) {...}


/**
 * Adds a new registry source to the registry source list.
 *
 * The new registry source will be persisted to the registry
 * configuration before the client exits.
 *
 * @param {string} src Source link of the registry.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function add_registry_source(src) {...}


/**
 * Removes a registry source from the registry source list.
 *
 * The registry source will be removed from the registry
 * configuration before the client exits.
 *
 * @param {string} src Source link of the registry.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function remove_registry_source(src) {...}


/**
 * Searches the configured registry sources for a package.
 *
 * The function searches each configured registry source
 * until the requested package is found.
 *
 * @param {string} package_name Name of the package to search for.
 * @param {object} out Output parameter for the search result.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function search_package(package_name, out) {...}


/**
 * Retrieves a package from its registry source.
 *
 * The function resolves the package location and provides
 * the required package information to the caller.
 *
 * @param {string} package_name Name of the package to retrieve.
 * @param {object} out Output parameter for the package information.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function get_package(package_name, out) {...}


/**
 * Lists all packages available from the configured registry sources.
 *
 * @param {object} out Output parameter for the package list.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function list_packages(out) {...}


/**
 * Publishes a package to the selected registry source.
 *
 * @param {string} package_path Path of the package to publish.
 * @param {string} registry_source Registry source where the package will be published.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function publish_package(package_path, registry_source) {...}
```

# 3.3 Registry Contract

A registry is a source from which the Tayku Web Client discovers and retrieves packages and modules.

This section defines the contract between the Tayku Web Client and a registry. It defines the registry structure, package representation, developer publishing requirements, and package signing requirements.

A registry MUST NOT be required to use Git or any particular storage, database, server, or directory structure.

## 3.3.1 Registry Manifest

Every registry MUST provide a `manifest.json` file.

The manifest MUST define:

* registry name
* registry version
* available packages
* available modules

Example:
```json
{
    "name": "tayku-web-frontend-registry",
    "version": "1.0.0",
    "packages": [],
    "modules": []
}
```

The manifest is the authoritative index of the packages and modules provided by the registry.

The Tayku Web Client MUST use the information provided by the manifest to discover packages and modules.

## 3.3.2 Registry Metadata

A registry MUST define a name and version.

A registry MAY provide additional metadata describing the registry or its operator.

Such metadata can include:

* display name
* description
* website
* contact information

Registry operator metadata is informational and MUST NOT be treated as proof of real-world identity.

A registry operated by Tayku Web is an official Tayku registry.

A registry operated by another party is an external registry.

## 3.3.3 Registry Layout

A registry MUST NOT be required to follow a particular directory structure.

The registry MAY organize packages and modules in any layout.

The manifest MUST provide sufficient information for the Tayku Web Client to locate each package or module.

The Tayku Web Client MUST NOT construct package paths based on assumptions about the registry's internal layout.

## 3.3.4 Package Entries

Every package exposed by a registry MUST have an entry in the `packages` array of `manifest.json`.

Each package entry MUST define:

* package name
* package version
* package source

The `source` field MUST contain a URI from which the package can be retrieved.

Example:
```json
{
    "name": "tayku-web-frontend-registry",
    "version": "1.0.0",
    "packages": [
        {
            "name": "hero",
            "version": "1.0.0",
            "source": "https://example.com/packages/hero"
        }
    ],
}
```

The source URI MAY point to a resource hosted by the registry or to another location.

The package MUST be retrievable from the specified source.

## 3.3.5 Package Metadata

Every Tayku package MUST contain a `tayku-package.json` file.

The file MUST define:

* package name
* package version
* package type

Example:

```json
{
    "name": "hero",
    "version": "1.0.0",
    "type": "package"
}
```

The package metadata MAY contain additional package information such as:

* description
* license
* homepage
* documentation

Publisher identity MUST NOT be required in `tayku-package.json`.

The publisher's cryptographic identity is established by the package signature and the signing identity associated with that signature.

## 3.3.6 Package Identity and Version

The package name and version defined in `tayku-package.json` MUST exactly match the corresponding values in `manifest.json`.

A registry MUST NOT expose two different packages with the same name and version.

Different releases of the same package MUST use different version values.

The package name and version together identify a specific package release within a registry.

## 3.3.7 Manifest Consistency

A registry MUST maintain consistency between its manifest and the packages it provides.

For every package listed in `manifest.json`:

1. The `source` URI MUST resolve to the corresponding package.
2. The package MUST be retrievable.
3. The package name MUST match the manifest entry.
4. The package version MUST match the manifest entry.
5. The package metadata MUST be valid according to this specification.

A registry MUST NOT advertise a package that does not satisfy these requirements.

## 3.3.8 Developer Account

A developer MUST have a Tayku Web Developer account to publish a package through the Tayku Web package ecosystem.

A developer account MUST NOT require a real-world identity.

A developer MAY use a pseudonymous identity.

The developer account represents the developer namespace and its associated signing identities.

The developer account MAY contain informational profile data, including:

* display name
* description
* website
* contact information

This information is not used as the cryptographic identity of the developer.

The cryptographic identity of a developer is established by its registered signing identities.

## 3.3.9 Developer Signing Identity

Tayku Web package signing uses Ed25519.

A developer signing identity consists of:

* a 32-byte secret seed
* the corresponding Ed25519 public key

The secret seed MUST remain under the control of the developer.

Tayku Web MUST NOT store the secret seed on its servers.

The Tayku Web Client MUST provide the following command to initialize a developer account and create a local signing identity:

```text
tayku-web init --developer-user
```

The command MUST create a 32-byte secret seed using a cryptographically secure random source.

The secret seed MUST be stored locally under the developer's control.

The default secret storage location is:

```text
tayku-web/config/secrets/.seed_<id>
```

The corresponding public key MUST be associated with the developer account.

A developer MUST be able to create multiple signing identities under the same developer account.

Signing identities MUST NOT be restricted to one identity per device.

A developer MAY maintain multiple signing identities on the same device and MAY use different identities on different devices or automated environments.

This allows a developer to separate signing identities for different devices, environments, projects, or security boundaries.

The developer MUST be able to replace or revoke a signing identity without replacing the developer account itself.

## 3.3.10 Package Initialization

The Tayku Web Client MUST provide a command for creating a new Tayku package:

```text
tayku-web init --package
```

The command MUST initialize the package source tree and create the required initial package metadata.

The resulting package MUST contain a valid `tayku-package.json`.

The package MUST be ready for development and subsequent signing or publication.

## 3.3.11 Package Signing

A developer MUST be able to sign a package using:

```text
tayku-web sign --package
```

The command MUST sign the package using one of the developer's registered signing identities.

The resulting signature MUST be associated with the signing identity used to sign the package.

The exact signing procedure is defined by `verifier.architecture.md`.

This registry contract does not define:

* package hashing
* package canonicalization
* key derivation
* signature encoding
* signature file format
* signature verification algorithms

Those mechanisms belong to the verifier architecture.

If any content covered by the package signature changes, the package MUST be signed again.

## 3.3.12 Package Publication

A developer MUST be able to publish a package using:

```text
tayku-web publish --package
```

Before publication, the Tayku Web Client MUST validate:

* package structure
* `tayku-package.json`
* package name
* package version
* package type

A package MAY be published without a signature.

An unsigned package MUST be identified as unsigned and MUST NOT receive the cryptographic guarantees provided by package signing.

A developer SHOULD sign a package before publication.

A registry MUST preserve the content of a signed package.

A registry MUST NOT modify the content covered by a package signature after publication.

If a published package is modified, the modified package MUST be treated as a different package release and MUST receive a new signature.

## 3.3.13 Package Verification

The Tayku Web Client MUST verify the signature of a signed package before accepting the package as cryptographically valid.

An invalid signature MUST cause signature verification to fail.

A missing signature MUST be treated as an unsigned package.

A valid signature establishes the cryptographic identity of the signing key and the integrity of the signed package content according to the verifier architecture.

A valid signature MUST NOT, by itself, establish that the package is safe, useful, or trusted.

The complete verification procedure is defined in:

```text
verifier.architecture.md
```

## 3.3.14 External Registries

A registry MAY be operated independently of Tayku Web.

An external registry claiming Tayku Web compatibility MUST implement the registry and package contracts defined in this specification.

An external registry operator MUST NOT be required to disclose a real-world identity.

The Tayku Web Client MUST distinguish between:

* the registry operator
* the package developer
* the developer's signing identity

A registry operator and a package developer are independent identities.

A package MAY be hosted by one registry while being signed by a developer who does not operate that registry.

A valid package signature establishes the cryptographic identity of the signer and the integrity of the signed package content. It does not establish that the registry itself is trusted by Tayku Web.

## 3.3.15 Publishing and Registry Independence

A registry is a package distribution source, not necessarily a package publishing service.

A registry MUST expose packages according to this contract.

A registry MAY implement its own publishing interface or accept packages through another mechanism.

The absence of a publishing interface does not prevent a source from being a valid Tayku Web registry.

The mechanism used to transfer a package into a registry is separate from the mechanism used by the Tayku Web Client to retrieve that package.

___ 

# 4. Data Flow 

Data flow is designed as one-directional;
`Caller -> Tayku Web Client Registry Module -> Registry Source -(Handle process)-> Tayku Web Client Registry Module`

___

# 5. Error Handling 

- If `registry.js` returns `NOT FOUND` during the package operations, this means that the package that you want, couldn't found. You can try to control if there is spelling mistake and try again.
- If `registry.js` returns `NOT FOUND` during the registry source operations, this means that the source that you want, couldn't found and accessed. Generally the problem is the connection of URL.
- If `registry.js` returns `NOT VALID` during the registry source operations, this means that even the url is accessible, its structure is not valid for being a registry source. You have to fix this registry according to our standardizations.
- If `registry.js` return `NOT SECURE` during the downloading packag, this means that, the package that you want to download, is not signed or their hashes is not matches with expected. Even thoug you can continue if you want, we don't recommend that.

___

# 6. Security
- **Malware Security**
    - Tayku Web Registry has own security rules for publishing packages. Even thoug, it's not means that, packages are fully secure. We coulddespiten't interfere codes of all of the packages. Therefore,you have to inspect (thanks to GPLv3 Licensing) the code and publisher carefully. 

    - Also beside the signature security mechaism of Tayku Web Registry, users can continue by their own free will.

    - Despite these warnings, Tayku Web guarentees the security of official packages (means that modules).

- **Thread Safety** 
    - Tayku Web Client Registry Module was designed as synchronously due to the verifying mechanism, in this version.

___ 

# 7. Devlog

- v0.0.0 - 18 SEP 2026
    - Base of contract was designed.
    - Primitive Architecture was designed.

___ 

# 8. TO-DO & Roadmap

* [x] Create a protocol for `registry.config.json`.
* [x] Create a protocol for `manifest.json`.
* [x] Create a protocol for `tayku-package.json`.
* [x] Decide the architecture of registry sources.
* [x] Setup the primitive architecture. 
* [ ] Import the logic of the function in the `registry.js` file. 
* [ ] Test 

___
