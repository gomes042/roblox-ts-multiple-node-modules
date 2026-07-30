# roblox-ts-multiple-node-modules

A `roblox-ts` patch that adds support for using multiple `node_modules` folders across different Roblox services.

This is intended as a temporary workaround while the official `roblox-ts` PR for multiple `node_modules` support is still open:

https://github.com/roblox-ts/roblox-ts/pull/3008

## ❓ Why?

By default, `roblox-ts` expects dependencies to be placed in the shared `rbxts_include` folder, usually under `ReplicatedStorage`.

This package allows you to define additional service-specific `node_modules` folders, such as:

- Server-only dependencies in `ServerScriptService`
- Client-only dependencies in `StarterPlayerScripts`
- Shared dependencies in `ReplicatedStorage`
- Developer-only tooling that should not replicate to every player

## ⚙️ Install
`npm install roblox-ts-multiple-node-modules`

### ✅ Requirements

- Override the default `rbxts_include` path in `ReplicatedStorage`.
- Pass `--includePath node_modules/roblox-ts-multiple-node-modules/include` to every `rbxtsc` command, including watch mode.
- `@rbxts-js` must only exist under `ReplicatedStorage`.
- The `rbxts_include.$path` property is only required in `ReplicatedStorage`.
- `services` is required and must be located at:

```txt
ReplicatedStorage/node_modules
```

## 🧩 Basic Rojo Example

```diff
{
    "ReplicatedStorage": {
        "rbxts_include": {
-           "$path": "include",
+           "$path": "node_modules/roblox-ts-multiple-node-modules/include",

            "node_modules": {
                "$className": "Folder",

                "@rbxts": {
                    "$path": "node_modules/@rbxts"
                },

                "@rbxts-js": {
                    "$path": "node_modules/@rbxts-js"
                }
            }
        }
    },

    "ServerScriptService": {
+       "rbxts_include": {
+           "node_modules": {
+               "$className": "Folder",
+
+               "@my_server_modules": {
+                   "$path": "node_modules/@my_server_modules"
+               }
+           }
+       }
    }
}
```

### Configure `rbxtsc`

Changing `rbxts_include.$path` in the Rojo project is not enough by itself. `roblox-ts` also needs to know where `RuntimeLib.lua` is located.

Pass the package's `include` directory using `--includePath` in every command that runs `rbxtsc`:

```sh
rbxtsc --noInclude --includePath node_modules/roblox-ts-multiple-node-modules/include --rojo default.project.json --type game
```

Use the same option in watch mode:

```sh
rbxtsc --verbose --noInclude --includePath node_modules/roblox-ts-multiple-node-modules/include --rojo default.project.json --type game --watch
```

Without `--includePath`, `roblox-ts` continues looking in the default local `include` folder and compilation can fail with:

```txt
Rojo project contained no data for include folder!
```

## 💡 Usage Ideas

### 1. Server-only or client-only modules

You can define specific `@rbxts` modules manually for each service instead of mapping the entire `node_modules` folder.

For example:

- Shared dependencies can stay in `ReplicatedStorage`.
- Server-only dependencies can be placed in `ServerScriptService`.
- Client-only dependencies can be placed in a client service such as `StarterPlayerScripts`.

This helps avoid replicating unnecessary packages to places where they are not needed.

### 2. UI Labs

If this UI Labs PR is accepted:

https://github.com/PepeElToro41/ui-labs/pull/105

You can keep the UI Labs package only inside `ServerScriptService`.

If your stories are still located in shared folders, you can remove them in production by using a server script that deletes story scripts when the server starts.

### 3. Developer-specific modules

You can keep developer-only modules, such as Iris, out of the default replicated tree.

This allows you to manually replicate them only when needed, instead of making them visible to every player by default.

## 📝 Notes

- Keep shared runtime dependencies in `ReplicatedStorage`.
- Keep server-only dependencies in server services.
- Avoid placing `@rbxts-js` outside `ReplicatedStorage.rbxts_include.node_modules`.
- Make sure `services` exists under ReplicatedStorage's node modules (`ReplicatedStorage.rbxts_include.node_modules.@rbxts.services`).

## 🚧 Status

This package is a workaround until official multiple `node_modules` support is available in `roblox-ts`. [PR #3008](https://github.com/roblox-ts/roblox-ts/pull/3008
)
