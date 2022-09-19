# vscode

## SFTP配置

```json
{
    "name": "My Server",
    "host": "10.20.7.149",
    "protocol": "sftp",
    "port": 22,
    "username": "uos",
    "password": "uos123!!",
    "remotePath": "/home/uos/Desktop/code",
    "uploadOnSave": true,
    "useTempFile": false,
    "openSsh": false,
    "watcher": {
        "files": "*",
        "autoUpload": true,
        "autoDelete": true
    },
    "ignore": [
        ".vscode",
        ".git",
        ".DS_Store",
        "build",
        ".cache",
        "obj-x86_64-linux-gnu"
    ]
}
```

## clangd

```json
"clangd.arguments": [
        "--all-scopes-completion",
        "--completion-style=bundled",
        "--cross-file-rename",
        "--header-insertion=never",
        "--header-insertion-decorators",
        "--background-index",
        "--clang-tidy",
        "--clang-tidy-checks=performance-*,bugprone-*",
        "-j=15",
        "--pch-storage=disk",
        "--function-arg-placeholders=false",
        "--compile-commands-dir=build"
    ]
```
