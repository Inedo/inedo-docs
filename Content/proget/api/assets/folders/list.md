---
title: "List Asset Folders"
order: 2
---

*List Asset Folders* is available as both a `pgutil` command and an HTTP Request, and will list Asset "items" (files and folders) in the specified path.

:::(Info) (🚀 Quick Example: Listing Asset Files and Folders with pgutil)
This example will list the files in the folder `production-files` in the asset directory `myAssetDirectory`:

```bash
pgutil assets list --feed=myAssetDirectory --path=production-files
```
:::

## Command Specification (CLI)
The `assets list` command is used to list files and folders in an asset directory.

The `--feed` option is always required, and the `--path` option is required for folders within the asset directory.

**Listing the contents of the root folder of the asset directory** requires the asset directory (e.g. `myAssetDirectory`):

```bash
pgutil assets list --feed=myAssetDirectory
```

**Listing the contents of a folder of the asset directory** requires the asset directory (e.g. `myAssetDirectory`) and the folder (e.g. `test-files`):

```bash
pgutil assets list --feed=myAssetDirectory --path=test-files
```

## HTTP Request Specification
To list item data, simply `GET` to the URL with the `AssetDirectoryName` and path to the folder. 

```plaintext
GET /endpoints/«AssetDirectoryName»/dir/«path-to-folder»?[?recursive=«true/false»]
```

* When `recursive` is `false` or not specified, only data of items directly contained in the specified path are returned. When `recursive` is `true`, data of all items within the folders of the specified path are also returned. 

* Omitting the `«path_to_folder»` will return items located in the root folder of the asset directory.

## HTTP Response Specification
A JSON array of `AssetDirectoryItem` (see [AssetDirectoryItem.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/AssetDirectories/AssetDirectoryItem.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/assets#authentication).

Note that querying a nonexistent path (i.e. a folder that does not exist) will not return a `404`, but will instead return an empty array. To check if a folder exists, you can use the [Get Metadata](/docs/proget/api/assets/metadata/get) endpoint.

