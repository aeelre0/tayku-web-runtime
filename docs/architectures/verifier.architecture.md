# Tayku Web Client Verifier Architecture

We mentioned that the signature mechanism of Tayku Web Registry Packages. This page explains the verifying and signaturing mechanism in a detailed way.

___ 

# 1. Scope 
- **in-scope:**
    - This page defines the contract of `verifier.js`.
    - This page explains the proved method of signaturing and verifying.

- **out-of-scope:**
    - This page doesn't provide new methods for signaturing and verifying.
    - This page doesn't explains the mechanism of communication between Tayku Web Client Registry Module and Tayku Web Registry.
    - This page doesn't explians the machanism of communication between Tayku Web Client Registry Module and `verifier.js`

___

# 2. Architecture

This section explains the Tayku Web Client Verifier Module architecture.

## 2.1 Definitions

* **Signature:** After a developer finishes developing a package, the package can be signed to verify that it has not been modified during distribution. This is important because the package could be changed by someone else before it reaches the user. The signature mechanism verifies the integrity of the package.

* **Secret Seed:** The preffered method of Tayku Web Client Registry is Ed25519. This mechanism works by public and private keys. Tayku Web Client Verifier produce them from a secret seed, created as user specific. This key 32 bytes long and stores under `tayku-web/config/secret/.seed_<id>`.

* **Secret Key:** Ed25519 works with a key pair consisting of a secret key and a public key. The secret key signs the package, while the public key verifies whether the signature is valid.

* **Public Key:** The key that stored in the `<package-name>/secret/.key_<id>`.

* **Canonicalization:** Canonicalization is the process of converting a package into a deterministic byte representation before signing or verifying. The same package MUST always produce the same canonical representation regardless of file system ordering or environment.

## 2.2 Methods

* **Ed25519:** The default signaturing mechanism of Tayku Web Registry Packages. (see [doc](https://ed25519.cr.yp.to/)).

* **getrandom(...):** The default secret seed derrived method of Tayku Web Developers.(see [doc](https://man7.org/linux/man-pages/man2/getrandom.2.html?utm_source=chatgpt.com)).

## 2.3 Logic

Tayku Web Client Verifier Module is responsible for generating signing keys, signing packages, and verifying package signatures. When a developer wants to sign a package but does not have a signing seed, the developer must first create a developer identity by using `tayku-web init --developer-user`. This command generates a 32-byte secret seed and stores it locally.

After creating the developer identity, the developer can sign a package by using `tayku-web sign --package <PACKAGE_NAME>` from the directory containing the package's `tayku-package.json`. The Verifier Module finds the available signing seeds and, if there is more than one seed, asks the developer to select which signing identity will be used.

The Verifier Module uses the selected seed to generate the Ed25519 secret key and public key. The public key is stored inside the package at `<package_name>/secret/.key_<id>`. The Verifier Module then signs the package, including all files that belong to the package, and stores the generated signature in the package's `.sign` file.

After the signing process is completed, the generated secret key is removed from memory and is not stored inside the package or any other project file. The original secret seed remains stored locally so it can be used for future signing operations.

When a package is downloaded, the Verifier Module reads the public key and signature from the package and verifies the package contents using Ed25519. This allows the Tayku Web Client to detect whether the package has been modified after it was signed.

___

# 3. Interface
* **Inputs**
    * `tayku-package.json`: The manifest document of a package.
    * `seed`: The secret seed that created as user specific.
    * `.sign`: The file that covers the signature of a package.
    * `.key_<id>`: The public key which belongs to the developer user who signed a package.

* **Outputs**
    * `TAYKU-STATUS-CODE`
    * `.sign`: The file that covers the signature of a package after signing operation.

## 3.1 Dependencies

* `crypto` : The library of `nodejs` for managing the signature and producing operations.

## 3.2 Contract 

```js 
/**
 * Initializes the developer signing identity.
 *
 * The function generates a cryptographically secure 32-byte secret seed
 * and stores it in the local developer secrets directory.
 *
 * The generated seed is used to derive the Ed25519 signing key pair
 * when a package is signed.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function initialize_developer_identity() {...}


/**
 * Contains the available developer signing identities.
 *
 * The list contains only signing identity metadata.
 * Secret seeds MUST NOT be stored in this list.
 *
 * @type {object[]}
 */
let signing_identity_list = []


/**
 * Signs a package using the selected developer signing identity.
 *
 * The function reads the package from the provided package path,
 * creates the required signing representation, and generates an
Canonicalization is the process of converting a package into a deterministic byte representation before signing or verifying. The same package MUST always produce the same canonical representation regardless of file system ordering or environment. * Ed25519 signature for the package.
 *
 * The package MUST contain a valid tayku-package.json file.
 *
 * The generated public key is stored inside the package and the
 * generated signature is stored in the package's .sign file.
 *
 * The secret key MUST NOT be persisted to the package or project.
 *
 * @param {string} package_path Path of the package to sign.
 * @param {string} identity_id Identifier of the signing identity.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function sign_package(package_path, identity_id) {...}


/**
 * Verifies the signature of a package.
 *
 * The function reads the public key and signature from the package
 * and verifies the package contents using Ed25519.
 *
 * The package MUST contain the required public key and signature
 * information.
 *
 * @param {string} package_path Path of the package to verify.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function verify_package(package_path) {...}
```

___ 

# 4. Data Flow

* **Signing** 

```text
Developer
   │
   │ package_path + identity_id
   ▼
sign_package()
   │
   ├──► .seed_<id>
   │       │
   │       ▼
   │   Secret Seed
   │       │
   │       ▼
   │   Ed25519 Signing Key
   │
   ├──► Package Files
   │       │
   │       ▼
   │   Canonical Package Representation
   │
   │       + Signing Key
   │             │
   │             ▼
   │          Signature
   │
   ├──► Public Key ─────► secret/.key_<id>
   │
   └──► Signature ──────► .sign
```

* **Verifying**

```text
Package
   │
   ├──► secret/.key_<id> ──► Public Key
   │
   ├──► .sign ─────────────► Signature
   │
   └──► Package Files
             │
             ▼
   Canonical Package Representation
             │
             ▼
       Ed25519 Verify
             │
             ▼
       TAYKU_STATUS_CODE
```

___ 

# 5. Error Handling

* If `verifier.js` returns the `NOT FOUND` error code, the required seed, package, public key, or signature could not be found.

  * **Solution:** Check the provided package path and make sure that the required signing identity or package files exist.

* If `verifier.js` returns the `NOT SECURE` error code, the package signature is missing or does not match the package contents.

  * **Solution:** Verify the package again using a valid signature, or obtain the package from a trusted source.

___

# 6. Security

* **Thread Safety**

  * Tayku Web Client Verifier Module operates synchronously in this version.

* **Package Integrity**

  * The Verifier Module can verify the integrity of a package by validating its signature against its canonical representation and public key.
  * A valid signature indicates that the verified package contents correspond to the contents that were signed. However, signature verification alone does not guarantee that the package is free from malware or other malicious behavior.

* **Malware Security**

  * Tayku Web Client's verification mechanism guarantees package integrity, but it does not guarantee that the package itself is safe to execute.
  * Tayku Web guarantees the security of official Tayku Modules. Packages published by third parties should be inspected carefully before use, including their source code and publisher information.
  * The free and open-source nature of Tayku packages allows users to inspect their source code before use.
  
___

# 7. Devlog

* v0.0.0 - 20 SEP 2026
    * Base of contract was designed.
    * Primitive Architecture was designed.

___ 

# 8. TO-DO & Roadmap

* [x] Finish the primitive architecture of verifier.
* [ ] Implement the logic of code to `verifier.js`.
* [ ] Test

___ 

