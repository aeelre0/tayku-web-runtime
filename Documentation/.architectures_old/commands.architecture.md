# Tayku Client Commands Architecture

Tayku Client is and wrapper of the modules due to abstraction policy. Therefore, client can't control the operation of commadns inside except for defining base contract. This file explains the inner contrcts and architecture of commands interface.

___ 

# 1. Scope 
- **in-scope:**
    - This module dispatches commands to their specified files. 

- **out-of-scope:**
    - This module doesn't handle parsing process.
    - This module doesn't access to Unix Socket without client.

___ 

# 2. Architecture 

Whenever commands module called by client, module search the command and its specified file in the `commands/` folder by the help of a `.json` file.

___ 

# 3. Interface 
- **Inputs:**
    - `command` : The parsed command from user's console.
    - `parameters` : The parsed parameters from user's console.
- **Output:**
    - `TAYKU-STATUS-CODE` : Was dispatching successfull or not?

# 3.1 Dependencies
- `commands.json` file for managing commands and their file locations (`tayku-web/commands/commands.json`).
- Relavent executable files of commands (`tayku-web/commands/...`).

# 3.2 Contract 

``` js 
/**
 * Dispatch a Tayku command.
 *
 * Resolves the command definition, loads its implementation,
 * executes it, and returns its result.
 *
 * @param {object} command Parsed command and its arguments.
 * @param {object} out Command execution output.
 * @returns {number} TAYKU_STATUS_CODE
 */
function dispatch(command, out) {...}
```

> [!Note] 
> Whenever `commands.js` calls the relavent code file, it calls a function that named command_main(...) as standard.

- **Protocol of commands.json**

``` json 
{
    {
        "command-name" : "name",
        "command-man-source" : "source", // native = tayku-web/src/man/man1
        "command-executable-file" : "file-location" // native = tayku-web/src/commands/
    },
    {
        "command-name" : "name",
        "command-man-source" : "source", // native = tayku-web/src/man/man1
        "command-executable-file" : "file-location" // native = tayku-web/src/commands/
    },
...
}
```

___ 

# 4. Data Flow 

Data flow of `commands.js`, simply consists of transfer the parameters from `cli.js` to relavent command file.

```
User -(Writes the command and paramter to console)-> cli.js -(Parses the command and parameters)-> commands.js -(finds relavent executable by the help of command.json)-> executable
```

___

# 5. Error Handling

- If `command.js` returns `NOT FOUND`, this means that, the command that typed couldn't find. You can control the location of command file and if there isn't any file, you have to create it.
- If `command.js` returns `NOT PERMITTED`, this means that, the command file doesn't have exec permissions. You should give the necessity permissions these files in your operating system.

___ 

# 6. Security

This module is not control the commands according to safety. Its purpose only call the relavent command file by searching them in the `commands.json` file.

___ 

# 7. Devlog
- **v0.0.0 - 17 SEP 2026**
    * Base of contract was designed.
    * Primitive Architecture was designed.
___ 

# 8. TO-DO & Roadmap 
* [ x ] Create a protocol for `commands.json`.
* [ ] Create `commands.json` and save the file locations and commands.
* [ ] Write the logic of `dispatch(...)` function.
* [ ] Test 

___

