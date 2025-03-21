---
title: "Create Asset Folder"
order: 1
---

*Create Asset Folder* is available as both a `pgutil` command and an HTTP Request, and will create a folder at the specified path.

:::(Info) (🚀 Quick Example: Creating a folder with pgutil)
This example will create a folder `new-folder` in the asset directory `myAssetDirectory`:

```bash
pgutil assets folder create --feed=myAssetDirectory --path=new-folder
```
:::

## Command Specification (CLI)
The `assets folder create` command is used to create a folder in the asset directory.

The `--path` option is always required. The `--feed` option is required if there is no default feed configured.

**Creating a folder** requires the asset directory (e.g. `MyAssetDirectory`) and new folder name (e.g. `new-folder`):

```bash
pgutil assets folder create --feed=myAssetDirectory --path=new-folder
```

## HTTP Request Specification
To create a folder simply `POST` to the URL with the `AssetDirectoryName` and path to the folder.

```plaintext
POST /endpoints/«AssetDirectoryName»/dir/«path_to_folder»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **201 (Success)** | the folder is created |
| **401 (Authentication Required)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-assets#authentication) |