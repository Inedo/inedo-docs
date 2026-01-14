---
title: "Delete Asset Folder"
order: 3
---

*Delete Asset Folder* is available as both a `pgutil` command and an HTTP Request, and will delete a folder at the specified path. 

:::(Info) (🚀 Quick Example: Deleting a folder with pgutil)
This example will delete a folder `old-folder` in the asset directory `myAssetDirectory`:

```bash
pgutil assets delete --feed=myAssetDirectory --path=old-folder
```
:::

## Command Specification (CLI)
The `assets delete` command is used to delete a folder in the asset directory.

The `--path` options is always required. The `--feed` option is required if there is no default feed configured. 

**Deleting a folder** requires the asset directory (e.g. `MyAssetDirectory`) and folder name (e.g. `old-folder`):

```bash
pgutil assets delete --feed=myAssetDirectory --path=old-folder --force
```
* The `--force` option is optional, and when used will delete a folder even if it is not empty. When not specified, the specified folder will only be deleted if empty.

## HTTP Request Specification
To delete a folder, simply `POST` to the URL with the `AssetDirectoryName` and path to the folder.

```plaintext
POST /endpoints/«AssetDirectoryName»/delete/«path»?recursive=«true/false»
```

* When `recursive` is `true`, the folder and all of its contents will be deleted. It is not an error if the folder does not exist. When `false` or not specified and the path refers to a folder, it will only be deleted if empty.

## HTTP Response Specification

A successful `200` response will indicate the item (file/folder) has been deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication).

