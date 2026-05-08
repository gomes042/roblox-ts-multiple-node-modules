# roblox-ts-multiple-node-modules
Roblox TS Patch for Multiple Node Modules Support

Since Opened PR [#3009](https://github.com/roblox-ts/roblox-ts/pull/3008) (roblox-ts official repository) is still open

# Why?
If you want to ensure some modules or specific codes are specific for client or server, so you don't need to keep everything at ReplicatedStorage. (While you can still use ReplicatedStorage for shared dependencies).

# Setup

-  Override default include at `ReplicatedStorage` to `node_modules/roblox-ts-multiple-node-modules/include`.
-  `@rbxts-js` should be at **ReplicatedStorage only**.
-  rbxts_include's `path` property is **only needed at the ReplicatedStorage**.
- `services` is **REQUIRED** and should be at `ReplicatedStorage/node_modules`.

Rojo Config Basic Example:
```diff
"ReplicatedStorage": {
    "rbxts_include": {
-		"$path": "include"
+        "$path": "node_modules/roblox-ts-multiple-node-modules/include",
        "node_modules": {
        "$className": "Folder",
           "@rbxts": {
                "$path": "node_modules/@rbxts"
            },
            "@rbxts-js": {
                "$path": "node_modules/@rbxts-js"
            },
        }
},
"ServerScriptService": {
+        "rbxts_include": {
+            "node_modules": {
+                "$className": "Folder",
+                "@my_modules": {
+                   "$path": "node_modules/@my_modules"
+                },
+            }
}
```

# Examples
TODO
