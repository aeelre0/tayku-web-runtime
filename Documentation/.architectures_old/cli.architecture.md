# Tayku Client Architecture

Tayku provides official modules just like unofficial package distrubition system for the users who uses our ecosystem. According to this goal, we need to provide a client interface for distrubition, visual feedbacks, etc...

Tayku Client gets a solution for these purposes. 

> [!Info]
> In this article, the meaning of module is official Tayku Packages, while package word represantating the unofficial user packages.

___

# 1. Scope
- **in-scope:**
    - Tayku Client contains a security layer for validating the modules and package (according to user config for packages).
    - Tayku Client allows to publish your own package for Tayku ecosystem.
    - Tayku Client allows to download published modules and packages by automatic configration of your projects.
    - Tayku Client provides an interface for searching a package or a module.
    - Tayku Client can initialize your project with you own configrations automatically (only `nodejs` for now.).
    - This module manages the communication between Unix Socket and inner modules.

- **out-of-scope:**
    - Tayku Client doesn't interests the internal security of unofficial packages.
    - Tayku Client doesn't provides a runtime environment (for now).

___

# 2. Architecture

Tayku Client embraces the Unix philosopy in its own codes. Therefore, it contains different modules inside and these communicate each others by a source. These modules are;
- [[commands.architecture.md]]
- [[init.architecture.md]]
- [[installer.architecture.md]]
- [[registry.architecture.md]]
- [[utils.architecture.md]]
- [[verifier.architecture.md]]

These modules communicate each others by Unix Sockets using a protocol named Common Message Protocol (see [[common-message.protocol.md]]). Even thoug modules were not working, these can start by the help of `systemd socket activation`.

___ 

# 3. Interfaces 

- **Inputs:**
    - `tayku-web <command> <parameter> -<subparameter>`
- **Outputs:**
    - `TAYKU-STATUS-CODE` by a message. 

## 3.1 Command List
- `install` : This command downloads the thing you want if exists. Its parameters are defines the type of the item.
- `remove` : This command deletes the thing downloaded.
- `list` : This command lists the thing you want. Its parameters are specifies the type of the item.
- `man` : This command shows the detailed informations of commands, utils, stautus-codes, packages etc...
- `search` : This command searchs the thing you want in the sources. Its parameters are specifies the type of the items.
- `init` : initializes a project by the config you specified.
- `add-registry` : This command adds the registry source if its meets the requirements.
- `publish` : This command publishes the package in the registry that you choose if its meets the requirements.

> [!Info]
> Parameters of the commands are specified in its own pages.

## 3.2 Dependencies 
- `nodejs` : Runtime environment of Tayku Client because we are using JavaScript for now. If Tayku Client starts to using `C` or other languages in the future, this dependency would be removed.
- `git` : This dependency serves the downloading package or module purposes of Tayku Client.

- **Distrubition of Tayku Client:**
    - `npm`

## 3.3 Contract

``` js
/**
 * Parse command-line arguments.
 *
 * @param {number} argc
 * @param {string[]} argv
 * @param {object} out
 * @returns {number} TAYKU_STATUS_CODE
 */
function parseArgs(argc, argv, out) {}

/**
 * Dispatch a parsed command.
 *
 * @param {object} command
 * @param {object} out
 * @returns {number} TAYKU_STATUS_CODE
 */
function dispatchCommand(command, out) {}

/**
 * Establish a connection to a Tayku component.
 *
 * @param {string} endpoint
 * @param {object} out
 * @returns {number} TAYKU_STATUS_CODE
 */
function connect(endpoint, out) {}

/**
 * Send a Common Message to a Tayku component.
 *
 * @param {object} connection
 * @param {object} message
 * @returns {number} TAYKU_STATUS_CODE
 */
function sendMessage(connection, message) {}

/**
 * Receive a Common Message from a Tayku component.
 *
 * @param {object} connection
 * @param {object} out
 * @returns {number} TAYKU_STATUS_CODE
 */
function receiveMessage(connection, out) {}

/**
 * Close a connection to a Tayku component.
 *
 * @param {object} connection
 * @returns {number} TAYKU_STATUS_CODE
 */
function close(connection) {}

/**
 * Print a response to the user.
 *
 * @param {object} response
 * @returns {number} TAYKU_STATUS_CODE
 */
function printOutput(response) {}

/**
 * Terminate the CLI process.
 *
 * @param {number} code
 */
function exit(code) {}
```

___

# 4. Data Flow

1. Whenever `commands.js` detects a command, runs the file that belongs to its command.
- **add** 
    2. A requiest to submitted to `registry.js` for finding the package (or module), wanted.
    3. If registry can not find the package, then returns the `NOT FOUND` error code (`-1`).
    4. If registry can find the package, then submit another requiest to `verifier.js` for validating process.
    5. Verifier return its deciding by scans the signature and compares the hash. If verifier wouldn't accept the package, then returns the `NOT SECURE` error code (`-2`).
    6. After verifying process, registry resumes again and submit a requiest to `installer.js`.
    7. Whenever installer gets the package (or module) url, it downloads and places it to project.
- **list**
    2. A requiest to submitted to `registry.js`.
    3. Whenever registry was resumed, it reads the `.json` file which has the package list, inside.
    4. If registry coudln't find the `.json` file, returns `NOT FOUND` error code (`-1`).
    5. Registry prints this list to console.
- **man**
    2. A requiest to submitted to `man.js` for finding the command that wanted.
    3. Man module search the command page inside the folder of `man/`.
    4. If man couldn't find the command page inside, then returns the `NOT FOUND` error code.
    5. Whenever the man finds the command page, prints it to console.
- **search**
    2. A requiest to submitted to `registry.js`.
    3. Whenever registry was resumed, it search the thing given, in the `.json` file inside.
    4. If registry can find the thing, then returns the `FOUND` success code (`1`).

___ 

# 5. Error Handling

- If `add` command returns `NOT FOUND`, this means that, package couldn't find in the registry source. You can change the source from the config file or try to control if there is spelling mistake and try again.
- If `add` command returns `NOT SECURE`, this menas that, the package that you want to download, is not signed. If you are using the official source, you can contact us because we couldn't contains unsigned packages inside.
    - If you are using custom repository, you have to sign your package by instructions (see [[verifier.architecture.md]]).
    - Even we don't recommend, you can just instal them by accepting risks. If you want to download it despite everything, try yo add `--not-secure` tag to command.

___

# 6. Security
- **Thread Safety**
    - Tayku Cli designed synchronous due to data flow architecture.
- **Malware Protection**
    - Tayku Cli has own verifier inside and this module accepts only packages that signed by its developer. Even though, we couldn't control the inside of all packages. Therefore, you have to inspect the package that you want to download yourself.

___ 

# 7. Devlog
- **v0.0.0 - DATE**
    - Unix philosopy was embraced.
    - Filesystem of client was organised.
    - Base architecture was decided.

___ 

# 8. TO-DO & Roadmap 
* [ x ] Create the folder structure of CLI.
* [ x ] Decide which files will be created later.
* [ ] Write a C based web runtime environment just like `nodejs` (future goal)
* [ ] Sets up to config system for users.
* [ ] Finish up the architectures
* [ ] Test 

___

