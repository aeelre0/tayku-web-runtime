# Tayku Web Client Man Command Architecture

The "man" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for accessing Tayku manual pages.

The command locates the requested manual entry within the Tayku Web Client manual page hierarchy and passes the resulting document to the CLI layer for presentation.

The manual page hierarchy follows the traditional Unix manual section structure.

---

# 1. In-Scope
* **In-Scope**  
  * Determine the requested manual entry from the command arguments.
  * Search the Tayku manual page directories for the requested entry.
  * Resolve the appropriate manual section containing the requested entry.
  * Locate the corresponding manual page file.
  * Read the requested manual page.
  * Pass the manual page content to the CLI layer.
  * Return the appropriate "TAYKU-STATUS-CODE".
  * Support additional manual sections without requiring changes to the command interface.

* **Out-of-Scope**  
  * Parsing command-line arguments.
  * Rendering manual pages.
  * Formatting manual page content for the terminal.
  * Defining the contents of manual pages.
  * Generating manual pages.
  * Modifying manual pages.
  * Executing commands described by manual pages.
  * Implementing command-specific logic.
  * Implementing package-specific logic.
  * Providing search functionality across the contents of manual pages.
  * Managing the manual page directory structure.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "man" command.

## 2.1 Logic

The "man" command acts as an abstraction layer between the CLI layer and the Tayku manual page hierarchy.

The command receives the requested manual entry from the CLI layer.

Manual pages are stored under:

`tayku-web/src/man/`

Each manual section is represented by a directory following the traditional Unix manual section convention:

`tayku-web/src/man/manX/
`
where "`x`" is the manual section number.

The meaning of each manual section follows the Unix manual system.

The currently supported sections are:

```
man1/
man5/
man7/
```

For a requested entry such as:

`list`

the command searches the appropriate manual section directories for the corresponding manual page:

```
list.1
list.5
list.7
```

The section determines the type and purpose of the manual entry according to the Unix manual section convention.

When the requested manual page is found, its contents are passed to the CLI layer.

The "man" command does not interpret the semantic contents of the manual page. The manual page is treated as documentation data.

The command does not execute or modify anything described by a manual page.

· · ·

## 2.2 Definitions

* **Manual Page:** A documentation file describing a Tayku command, configuration format, interface, system component, or other documented entity.

* **Manual Section:** A numbered category of manual pages following the traditional Unix manual section convention.

* **Manual Section Directory:** A directory containing manual pages belonging to a specific manual section, such as "`man1`", "`man5`", or "`man7`".

* **Manual Entry:** The named documentation entity requested by the user.

* **Manual Page File:** The file containing the documentation for a manual entry. Its filename consists of the entry name and its manual section extension.

* **Manual Root:** The root directory containing all Tayku manual sections:

`tayku-web/src/man/`

* **Section Number:** The numeric identifier associated with a manual section and its corresponding file extension.

---

# 3. Interface

## 3.1 Inputs

The "`man`" command accepts a single manual entry as its command argument.

`<ENTRY>`

The entry identifies the manual page requested by the user.

For example:
`npx tayku-web man list`

requests the manual entry associated with "`list`".

The command does not currently expose additional command-line options for selecting a manual section.

· · ·

## 3.2 Outputs

The "`man`" command returns a "`TAYKU-STATUS-CODE`".

When the requested manual page is found successfully, its contents are passed to the CLI layer.

When the requested manual page cannot be found or cannot be read, an appropriate failure status code is returned.

The "`man`" command does not define the presentation format of the manual page.

· · ·

## 3.3 Dependencies

The "`man`" command depends on the following components:

* **CLI Parser**  
	* Parses the command-line arguments before they are passed to the "man" command.

* **Manual Page Hierarchy**  
	* Provides the manual sections and manual page files.

* **Filesystem**
	* Provides access to the manual page files.
	* `fs`
- **Runtime Environment of JS**
	- `nodejs`

The command does not depend on the implementation of the commands or systems documented by the manual pages.

· · ·

## 3.4 Source Contract

The "`man`" command receives a manual entry name from the CLI layer.

The command searches the manual page hierarchy for the requested entry and, when found, provides the corresponding manual page content to the CLI layer.

A conceptual interface is defined as follows:

``` js
/**
 * Retrieves a Tayku manual page.
 *
 * Searches the registered manual section directories for
 * the requested manual entry and passes the resulting
 * manual page content to the CLI layer.
 *
 * @param {string} entry The requested manual entry.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function man(entry) {...}
```

The command does not expose the manual page contents through its return value.

The manual page content is provided through the command's output mechanism to the CLI layer, while the function return value communicates operation status.

· · ·

## 3.5 Command Contract

Display a Manual Entry

`npx tayku-web man <ENTRY>`

Retrieves and presents the manual page associated with "`<ENTRY>`".

For example:

`npx tayku-web man list`

requests the "`list`" manual entry.

· · ·

## 3.6 Command Behavior

**What happens when the requested manual page exists?**

The command resolves the requested entry within the manual page hierarchy.

The corresponding manual page is read and its contents are passed to the CLI layer.

The operation returns a successful "TAYKU-STATUS-CODE".

· · ·

**What happens when the requested manual page does not exist?**

If no corresponding manual page can be found in the supported manual sections, the command does not produce a manual entry.

An appropriate "NOT FOUND" status code is returned.

· · ·

**What happens when multiple sections contain the same entry?**

A manual entry may exist in more than one manual section.

For example:

```
man1/list.1
man5/list.5
```

may coexist.

The "`man`" command resolves the manual entry according to the Tayku manual section search order.

The section search order must remain deterministic.

The default search order is defined by the manual subsystem and must not depend on filesystem enumeration order.

· · ·

**What happens when a manual page cannot be read?**

If the requested manual page exists but cannot be read, the command returns the corresponding filesystem or I/O failure status.

The command does not attempt to reconstruct, regenerate, or modify the manual page.

· · ·

**What happens when a new manual section is added?**

A new manual section can be added under the manual root:

`tayku-web/src/man/manX/`

where "`X`" represents the new Unix-compatible manual section.

The "`man`" command interface remains unchanged.

The command architecture is designed so that adding a manual section does not require a new user-facing command.

· · ·

**What happens when a new manual page is added?**

A new manual page can be added to the appropriate manual section directory.

For example:

`tayku-web/src/man/man1/add.1`

Once present, the manual entry can be requested through:

`npx tayku-web man add`

No additional implementation is required in "`commands/man.js`" solely because a new manual page is added.

---

# 4. Error Handling

The "`man`" command may return errors generated by the CLI argument layer or by the manual page subsystem.

* "`INVALID ARGUMENT`": The provided arguments do not form a valid "man" command invocation.

  * **Solution**: Provide exactly one manual entry.

* "`NOT FOUND`": The requested manual entry could not be found in the supported manual sections.
  
  * **Solution**: Check the manual entry name and the available manual pages.

* "`PERMISSION DENIED`": The manual page exists but cannot be accessed because of insufficient filesystem permissions.
  
  * **Solution**: Check the permissions of the manual page and its parent directories.

* "`IO ERROR`": An unexpected input/output error occurred while accessing the manual page.
  
  * **Solution**: Check the filesystem and retry the operation.

Errors generated while locating or reading manual pages are propagated to the CLI layer through the command's "`TAYKU-STATUS-CODE`".

---

# 5. Data Flow

## 5.1 Manual Page Retrieval

```
User
   │
   │ man <ENTRY>
   ▼
CLI
   │
   │ parsed entry
   ▼
Man Command
   │
   │ entry
   ▼
Manual Root
   │
   ├──► man1/
   │       │
   │       └──► <entry>.1
   │
   ├──► man5/
   │       │
   │       └──► <entry>.5
   │
   └──► man7/
           │
           └──► <entry>.7
                │
                ▼
          Manual Page Content
                │
                ▼
               CLI
                │
                ▼
             Presentation
```

· · ·

## 5.2 Manual Section Resolution

```
             Requested Entry
                    │
                    ▼
              Man Command
                    │
                    ▼
             Manual Root
                    │
                    ▼
            Section Search Order
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        man1/     man5/     man7/
          │         │         │
          ▼         ▼         ▼
      entry.1   entry.5   entry.7
          │         │         │
          └─────────┼─────────┘
                    │
                    ▼
              Resolved Entry
                    │
                    ▼
              Manual Content
                    │
                    ▼
                   CLI
```

---

# 6. Security

* **Path Security**
	* The requested manual entry must be resolved within the Tayku manual root.
	* Entry resolution must not allow path traversal outside the manual root.
	* User-provided entry names must not be interpreted as arbitrary filesystem paths.

* **File Access**
	  * Manual pages are read-only resources from the perspective of the "man" command.
	  * The command must not modify or execute manual page files.

* **Content Handling**  
	  * Manual page contents are treated as documentation.
	  * The "man" command must not execute commands, scripts, or other instructions contained within a manual page.

* **Responsibility Isolation** 
	  * The "man" command is responsible only for locating and retrieving documentation.
	  * Rendering, presentation, and terminal-specific behavior remain outside the command's responsibility.

* **Thread Safety**
	  * The Tayku Web Client "man" command operates synchronously in this version.
	  * The command does not perform concurrent manual page resolution.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Man Command**
	* Defined the initial architecture of the "`man`" command.
	* Defined the manual page hierarchy under "`src/man/`".
	* Established Unix-compatible manual section semantics.
	* Defined the currently supported "`man1`", "`man5`", and "`man7`" sections.
	* Defined manual entry resolution.
	* Defined the interface between the "man" command and the CLI layer.
	* Established manual page retrieval as the sole responsibility of the command.

---

# 8. ToDo List

* [ ] Define the exact manual section search order.
* [ ] Define the exact parsed argument structure received by "commands/man.js".
* [ ] Define the exact output interface between "commands/man.js" and the CLI layer.
* [ ] Define whether section selection will be exposed to the user in a future version.
* [ ] Define behavior when identical entries exist across multiple manual sections.
* [ ] Define the complete list of supported manual sections.
* [ ] Test manual entry resolution.
* [ ] Test duplicate manual entries across sections.
* [ ] Test missing manual entries.
* [ ] Test unreadable manual pages.
* [ ] Test path traversal protection.