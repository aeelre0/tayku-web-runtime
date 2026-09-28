# Tayku Web Client Sync Command Architecture

The "`sync`" command is one of the commands available in the Tayku Web Client. It provides the user-facing interface for synchronizing the local registry state.

The command checks the configured registries, stores newly discovered registry links in local registry memory, and reports the status of each registry.

The "`sync`" command does not implement registry communication itself.

The registry communication and registry state management are defined by the Registry architecture.

The command is executed using:

```sh
npx tayku-web sync
```

The command does not require any arguments.

---

# 1. In-Scope
* **In-Scope**  
	* Locate the configured registries.
	* Check the accessibility of each registry.
	* Store newly discovered registry links in local registry memory.
	* Determine the status of each registry.
	* Report registry statuses to the CLI layer.
	* Allow the user to remove unreachable registries.
	* Return the appropriate "`TAYKU-STATUS-CODE`".

* **Out-of-Scope**  
	* Implementing registry communication.
	* Implementing registry protocols.
	* Creating registries.
	* Publishing packages.
	* Downloading packages.
	* Synchronizing package contents.
	* Managing package data.
	* Rendering command output.
	* Parsing command-line arguments.

---

# 2. Architecture

This section examines the architecture and responsibilities of the "`sync`" command.

## 2.1 Logic

The "`sync`" command acts as an abstraction layer between the CLI layer, the local registry memory, and the Registry module.

The command does not require a target registry or additional arguments.

The command loads the configured registry information and requests the Registry module to check each registry.

When a registry is newly configured and has not yet been stored in local registry memory, the "`sync`" command causes its registry link to be stored.

For each registry, the command obtains its current accessibility status.

A successful registry check may be presented in a form similar to:

```text
registry.example.org ... OK
```

An unavailable registry may be presented in a form similar to:

```text
registry.example.org ... 404
```

The exact output format is handled by the CLI layer.

If an unreachable registry is detected, the user may be given the option to remove the registry from local registry memory.

An unreachable registry must not be removed automatically.

The "`sync`" command does not implement registry communication or registry storage itself.

· · ·

## 2.2 Definitions

* **Registry:** A Tayku package registry known to or configured for the Tayku Web Client.

* **Registry Memory:** The local state containing known registry links.

* **Registry Check:** The operation of checking whether a registry can be reached and used.

* **Reachable Registry:** A registry that can be successfully contacted.

* **Unreachable Registry:** A registry that cannot be successfully contacted or returns an unusable response.

* **Registry Removal:** The removal of an unreachable registry from local registry memory.

* **Registry Module:** The module responsible for registry communication and registry state management.

* **Registry Link:** The address used to access a registry.

---

# 3. Interface

## 3.1 Inputs

The "`sync`" command does not accept command-specific arguments.

```sh
npx tayku-web sync
```

The command operates on the registries known to the Tayku Web Client.

· · ·

## 3.2 Outputs

The "`sync`" command returns a "`TAYKU-STATUS-CODE`".

The CLI layer may additionally display the status of each checked registry.

Example output:

```text
registry.example.org ... OK
registry.example.net ... 404
registry.example.dev ... OK
```

The exact output format is defined by the CLI architecture.

· · ·

## 3.3 Dependencies

The "`sync`" command depends on the following components:

* **CLI Parser**  
	* Executes the "`sync`" command after command-line parsing.

* **Registry Module**  
	* Provides registry discovery.
	* Provides registry accessibility checks.
	* Provides registry state management.
	* Provides registry removal functionality.

* **Filesystem**
	* Provides access to local registry memory.
	* `fs`

* **Runtime Environment of JS**
	* `nodejs`

The "`sync`" command does not implement registry communication itself.

· · ·

## 3.4 Source Contract

The "`sync`" command does not require input arguments.

A conceptual interface is defined as follows:

```js
/**
 * Synchronizes the local registry state.
 *
 * Checks configured registries, stores newly discovered registry
 * links, and returns the resulting TAYKU_STATUS_CODE.
 *
 * @returns {number} TAYKU_STATUS_CODE
 */
function sync() {...}
```

The "`sync`" command invokes the Registry module to perform registry operations.

The return value communicates the overall operation status to the CLI layer.

· · ·

## 3.5 Command Contract

```sh
npx tayku-web sync
```

Checks the configured registries and synchronizes the local registry memory.

The command does not require additional arguments.

· · ·

## 3.6 Command Behavior

**What happens when "`sync`" is executed?**

The command loads the configured registry information.

The Registry module is invoked to check each registry.

The status of each registry is returned to the command.

Newly discovered registry links are stored in local registry memory.

The resulting registry statuses are passed to the CLI layer for presentation.

The resulting "`TAYKU-STATUS-CODE`" is returned to the CLI layer.

· · ·

**What happens when a new registry is configured?**

If a registry has been configured but its link is not yet present in local registry memory, "`sync`" checks the registry.

If the registry is successfully discovered, its link is stored in local registry memory.

This allows subsequent Tayku Web Client operations to use the registry as a known registry.

· · ·

**What happens when a registry is reachable?**

The Registry module reports the successful registry check.

The CLI layer may display a status similar to:

```text
registry.example.org ... OK
```

The command continues checking the remaining registries.

· · ·

**What happens when a registry is unreachable?**

The Registry module reports the failure status of the registry.

The CLI layer may display the returned status, for example:

```text
registry.example.org ... 404
```

The command continues checking the remaining registries.

The user may then be given the option to remove the unreachable registry from local registry memory.

· · ·

**What happens when the user chooses to remove an unreachable registry?**

The "`sync`" command requests the Registry module to remove the selected registry from local registry memory.

The registry is not removed unless the user explicitly selects the removal operation.

· · ·

**What happens when the user does not remove an unreachable registry?**

The registry remains in local registry memory.

The command completes without removing the registry.

---

# 4. Error Handling

The "`sync`" command may return errors generated by the Registry module, local registry state management, or the CLI argument layer.

* **Invalid Arguments**  
	* "`INVALID ARGUMENT`": Arguments were provided to the "`sync`" command.

	* **Solution**: Execute the command without additional arguments:

```sh
npx tayku-web sync
```

* **Registry Error**  
	* "`REGISTRY ERROR`": The Registry module could not complete a registry operation.

	* **Solution**: Check the affected registry and retry the "`sync`" operation.

* **Permission Error**  
	* "`PERMISSION DENIED`": The local registry memory cannot be accessed or modified because of insufficient permissions.

	* **Solution**: Check the permissions of the relevant registry state files and directories.

* **Filesystem Error**  
	* "`IO ERROR`": An unexpected filesystem error occurred while accessing local registry memory.

	* **Solution**: Check the filesystem and retry the "`sync`" operation.

Errors generated while checking an individual registry must not prevent the remaining registries from being checked.

---

# 5. Data Flow

## 5.1 Registry Synchronization

```text
User
   │
   │ npx tayku-web sync
   ▼
CLI
   │
   ▼
Sync Command
   │
   ▼
Registry Module
   │
   ├──► Registry A ──► Status
   │
   ├──► Registry B ──► Status
   │
   └──► Registry C ──► Status
   │
   ▼
Registry State
   │
   ├──► Store new registry links
   │
   └──► Keep existing registry links
   │
   ▼
Sync Command
   │
   ▼
CLI
   │
   ▼
User
```

· · ·

## 5.2 New Registry

```text
New Registry
      │
      ▼
Sync Command
      │
      ▼
Registry Module
      │
      ├──► Check Registry
      │
      └──► Store Registry Link
                    │
                    ▼
           Local Registry Memory
```

· · ·

## 5.3 Unreachable Registry

```text
Registry
   │
   ▼
Registry Module
   │
   ▼
Unreachable
   │
   ▼
Sync Command
   │
   ▼
CLI
   │
   ▼
User
   │
   ├──► Remove ──► Registry Module
   │
   └──► Keep
```

---

# 6. Security

* **Registry Integrity**
	* Registry links must not be removed without explicit user confirmation.
	* Registry state must not be modified by the "`sync`" command beyond the operations defined by the Registry architecture.

* **Registry Isolation**
	* A failure in one registry must not prevent other registries from being checked.

* **User Confirmation**
	* Unreachable registries must not be removed automatically.

* **Registry State**
	* Registry links stored in local registry memory must originate from the Registry configuration or discovery process.

* **Responsibility Isolation**
	* Registry communication and registry state management remain the responsibility of the Registry module.

---

# 7. Revision History

## v0.0.1 — 2026-09-22

**Author**: Ali Emre Arlı

**Sync Command**
	* Defined the initial architecture of the "`sync`" command.
	* Defined registry accessibility checking.
	* Defined local registry memory synchronization.
	* Defined newly discovered registry link storage.
	* Defined unreachable registry handling.
	* Defined user-controlled registry removal.

---

# 8. ToDo List

* [ ] Define the exact Registry module interface.
* [ ] Define the exact registry memory format.
* [ ] Define the exact registry discovery mechanism.
* [ ] Define when a registry is considered newly discovered.
* [ ] Define the exact registry status representation.
* [ ] Define the exact CLI output format.
* [ ] Define whether registry checks are performed sequentially or concurrently.
* [ ] Define the exact user interaction for removing unreachable registries.
* [ ] Define the exact "`TAYKU-STATUS-CODE`" values.
* [ ] Define behavior when no registries are configured.
* [ ] Define behavior when all registries are unreachable.
* [ ] Define behavior when a newly discovered registry cannot be stored.
* [ ] Test reachable registries.
* [ ] Test unreachable registries.
* [ ] Test new registry synchronization.
* [ ] Test registry removal.
* [ ] Test registry memory failures.
